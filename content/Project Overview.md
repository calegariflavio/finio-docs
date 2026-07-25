# Finio — Project Overview

*Last updated: 2026-07-25*

---

## What is Finio?

Finio is a full-stack personal finance tracker built as a portfolio project. It lets users record income and expenses, set monthly budgets, track savings goals, manage recurring bills, monitor net worth, and generate monthly reports — all behind a clean dark-mode dashboard.

It is not a toy. It has real auth, a real payment wall (Stripe), a real email flow (Gmail SMTP), and is being prepared for VPS deployment.

---

## Tech Stack

### Backend
| Concern | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 3.x |
| Auth | Stateless JWT (BCrypt passwords) |
| Database | PostgreSQL 15+ via Spring Data JPA / Hibernate |
| Migrations | Flyway (`V1__` → `V20__`) |
| Object mapping | MapStruct |
| Email | Spring Mail → Gmail SMTP |
| Payments | Stripe (Checkout, Webhooks, Subscriptions) |
| Rate limiting | Bucket4j (10 req/min per IP on auth endpoints) |
| API docs | SpringDoc OpenAPI / Swagger UI |
| Scheduling | Spring `@Scheduled` |
| Build | Maven 3.9+ |

### Frontend
| Concern | Technology |
|---|---|
| Language | TypeScript |
| Framework | React 18 + Vite |
| Routing | React Router v6 |
| State / data fetching | React Query (TanStack Query v5) |
| Forms | React Hook Form + Zod |
| UI components | shadcn/ui (Radix primitives + Tailwind) |
| Charts | Recharts |
| HTTP client | Axios (with JWT interceptor) |
| Notifications | Sonner (toast) |
| Animations | canvas-confetti (goal completion) |
| Testing (unit) | Vitest + Testing Library |
| Testing (E2E) | Playwright |

---

## Who is this for?

- **Portfolio audience** — demonstrates a production-grade full-stack system with real auth, payments, scheduling, and E2E tests.
- **Personal use** — Flavio's own finance tracking tool post-launch.

---

## Project goals

1. Ship a deployed, publicly accessible URL before end of 2026.
2. Demonstrate: stateless JWT auth, Stripe integration, scheduled jobs, GDPR-style data export + deletion, Playwright E2E testing.
3. Keep the codebase clean enough to walk through in a technical interview.

---

## Repository

`C:\Users\flavi\fullstack-scaffold\`

```
fullstack-scaffold/
├── backend/          Spring Boot API
├── frontend/         React + Vite SPA
└── CLAUDE.md         AI coding guidelines + architecture reference
```

---

## Links

- Swagger UI (local): http://localhost:8080/swagger-ui.html
- Frontend dev (local): http://localhost:5173
- Stripe dashboard: https://dashboard.stripe.com
