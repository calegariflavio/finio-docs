# Finio — Deployment Guide

*Target: Ubuntu VPS (24.04 LTS). Last updated: 2026-07-25*

---

## Overview

The deployment runs:
- Spring Boot JAR behind Nginx (reverse proxy on port 443 → 8080)
- React SPA as static files served by Nginx
- PostgreSQL on the same VPS
- Certbot (Let's Encrypt) for SSL

---

## Prerequisites on the VPS

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y openjdk-21-jdk nginx postgresql postgresql-contrib certbot python3-certbot-nginx nodejs npm
node -v   # should be 20+
java -version  # should be 21
```

---

## 1. PostgreSQL setup

```bash
sudo -u postgres psql
```

```sql
CREATE USER finio WITH PASSWORD 'your-strong-password';
CREATE DATABASE "finio-db" OWNER finio;
GRANT ALL PRIVILEGES ON DATABASE "finio-db" TO finio;
\q
```

---

## 2. Environment variables

Create `/etc/finio.env` (owned by root, readable by the finio service user):

```env
DB_PASSWORD=your-strong-postgres-password
JWT_SECRET=at-least-64-random-chars-here
JWT_EXPIRATION_MS=86400000

MAIL_PASSWORD=your-gmail-app-password
ADMIN_EMAIL=your-admin@email.com

STRIPE_SECRET_KEY=sk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Update these to your actual domain after DNS is pointed
APP_BASE_URL=https://finio.yourdomain.com
```

> **SMTP note:** Gmail requires an App Password (not your account password). If 2FA is on: Google Account → Security → App passwords. The SMTP from address in `application.yml` must match the Gmail account.

> **Stripe webhook secret:** Register the webhook endpoint in the Stripe dashboard under `Developers → Webhooks → Add endpoint`. URL: `https://finio.yourdomain.com/api/stripe/webhook`. Events to listen for: `checkout.session.completed`, `customer.subscription.deleted`, `invoice.payment_failed`.

---

## 3. Build the backend

On your local machine or the VPS:

```bash
cd backend
mvn clean package -DskipTests
```

Copy the JAR to the VPS:

```bash
scp target/finio-*.jar user@your-vps:/opt/finio/finio.jar
```

---

## 4. Backend systemd service

Create `/etc/systemd/system/finio-backend.service`:

```ini
[Unit]
Description=Finio Spring Boot Backend
After=network.target postgresql.service

[Service]
User=finio
WorkingDirectory=/opt/finio
EnvironmentFile=/etc/finio.env
ExecStart=/usr/bin/java -jar /opt/finio/finio.jar \
  --spring.datasource.url=jdbc:postgresql://localhost:5432/finio-db \
  --spring.datasource.username=finio \
  --spring.datasource.password=${DB_PASSWORD} \
  --app.jwt.secret=${JWT_SECRET} \
  --app.jwt.expiration-ms=${JWT_EXPIRATION_MS} \
  --spring.mail.password=${MAIL_PASSWORD} \
  --app.admin.email=${ADMIN_EMAIL} \
  --stripe.secret-key=${STRIPE_SECRET_KEY} \
  --stripe.webhook-secret=${STRIPE_WEBHOOK_SECRET} \
  --stripe.success-url=https://finio.yourdomain.com/upgrade/success \
  --stripe.cancel-url=https://finio.yourdomain.com/upgrade/cancel \
  --app.mail.base-url=https://finio.yourdomain.com
Restart=always
RestartSec=10
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable finio-backend
sudo systemctl start finio-backend
sudo journalctl -u finio-backend -f   # watch logs
```

---

## 5. Build the frontend

Update `frontend/.env` (or set at build time) before building:

```env
VITE_API_BASE_URL=https://finio.yourdomain.com
```

Build locally:

```bash
cd frontend
npm install
npm run build
```

Copy the `dist/` folder to the VPS:

```bash
scp -r dist/ user@your-vps:/var/www/finio/
```

---

## 6. Nginx configuration

Create `/etc/nginx/sites-available/finio`:

```nginx
server {
    listen 80;
    server_name finio.yourdomain.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name finio.yourdomain.com;

    # SSL managed by Certbot (filled in automatically)
    ssl_certificate /etc/letsencrypt/live/finio.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/finio.yourdomain.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    # Frontend SPA (React)
    root /var/www/finio;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api/ {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Swagger (optional — disable in production if you want)
    location /swagger-ui/ {
        proxy_pass http://localhost:8080;
    }

    location /api-docs/ {
        proxy_pass http://localhost:8080;
    }

    # Stripe webhook (larger body allowed)
    location /api/stripe/webhook {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        client_max_body_size 1M;
    }
}
```

Enable it:

```bash
sudo ln -s /etc/nginx/sites-available/finio /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 7. SSL with Certbot

```bash
sudo certbot --nginx -d finio.yourdomain.com
```

Certbot auto-renews via a cron job. Verify: `sudo certbot renew --dry-run`.

---

## 8. Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

Port 8080 should NOT be open to the public — Nginx proxies to it internally.

---

## 9. Deployment checklist

Before going live:

- [ ] DNS A record for `finio.yourdomain.com` points to VPS IP
- [ ] PostgreSQL running and `finio-db` created
- [ ] `/etc/finio.env` written with all secrets
- [ ] JAR deployed and backend service running (`systemctl status finio-backend`)
- [ ] Flyway migrations ran on startup (check logs for `Successfully applied N migration(s)`)
- [ ] Frontend `dist/` in `/var/www/finio/`
- [ ] Nginx config valid (`nginx -t`) and reloaded
- [ ] SSL certificate issued by Certbot
- [ ] Firewall configured (8080 not exposed)
- [ ] `ADMIN_EMAIL` set — first login with that email auto-elevates to ROLE_ADMIN
- [ ] Stripe webhook endpoint registered with correct events
- [ ] Stripe webhook secret matches `STRIPE_WEBHOOK_SECRET` in env
- [ ] Test end-to-end: register → verify email → login → add transaction → upgrade to premium → cancel subscription

---

## 10. Updating

```bash
# Backend
mvn clean package -DskipTests
scp target/finio-*.jar user@vps:/opt/finio/finio.jar
ssh user@vps "sudo systemctl restart finio-backend"

# Frontend
npm run build
scp -r dist/ user@vps:/var/www/finio/
```

---

## Known issues / pre-deploy to-dos

See [[Known Issues]] for the full list. The most critical:

- Gmail SMTP app password needs refreshing (local password expired — not blocking prod as long as a valid one is set in `VITE_API_BASE_URL` env)
- `application.yml` has hardcoded `app.mail.from` with Flavio's personal email — this should remain consistent with the Gmail SMTP account used
- Stripe `premium-price-id` in `application.yml` is hardcoded as `price_1Tdi3KJwgr92nN6mg1qX56yL` — verify this is the correct live price ID before deploying to production
