---
title: Finio
description: Personal finance tracker built with Spring Boot and React. Portfolio project by Flavio Calegari.
---

# Finio

A full-stack personal finance tracker built as a portfolio project.

**Stack:** Java 21 · Spring Boot 3 · PostgreSQL · React 18 · TypeScript · Vite

**Source code:** [github.com/calegariflavio/fullstack-scaffold](https://github.com/calegariflavio/fullstack-scaffold)

---

## What it does

Finio lets users track income and expenses, set budgets, define savings goals, and monitor net worth — with a FREE tier and a PREMIUM tier unlocked via Stripe.

| Area | Features |
|---|---|
| Auth | Register, email verification, login, forgot password, profile, GDPR export/delete |
| Transactions | CRUD, filtering & search, analytics, calendar view, CSV export/import |
| Budgets | Monthly budgets with automated alert emails |
| Goals | Savings goals (PREMIUM) |
| Recurring | Recurring transaction templates with daily scheduler (PREMIUM) |
| Net Worth | Asset/liability tracking with monthly snapshots (PREMIUM) |
| Reports | Auto-generated monthly financial reports |
| Payments | Stripe Checkout + webhooks + subscription management |
| Admin | User management and platform stats |

---

## Build status

| Area | Status |
|---|---|
| Backend API | ✅ Complete |
| Frontend SPA | ✅ Complete |
| E2E tests (Playwright) | ✅ 20/20 passing |
| Stripe integration | ✅ Complete |
| Admin panel | ✅ Complete |
| Deployment | 🔲 Not done yet |

---

## Documentation

- [[Project Overview]] — tech stack, goals
- [[Architecture]] — backend layers, auth flow, scheduled jobs, frontend structure
- [[Domain Model]] — entities, enums, Flyway migrations
- [[Feature Inventory]] — built features, FREE vs PREMIUM limits
- [[API Contract]] — every endpoint with request/response shapes
- [[Known Issues]] — active bugs and tech debt
- [[Testing Strategy]] — Playwright E2E suite, Vitest unit tests, selector reference
- [[Future Roadmap]] — planned features, permanently deferred items
- [[Use Cases/index|Use Cases]] — all 20 use cases with spec, implementation plan, and task list
