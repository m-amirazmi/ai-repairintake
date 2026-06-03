# Repair Intake System — Documentation

AI-native mobile-first repair intake system for smartphone repair shops.
Built for a 5-outlet chain in Dungun/Kerteh, Malaysia, with a clear path to SaaS.

## Index

| File | Content |
|---|---|
| [01-overview.md](./01-overview.md) | Project vision, core philosophy, scope |
| [02-architecture.md](./02-architecture.md) | System architecture, data flows, role flows |
| [03-tech-stack.md](./03-tech-stack.md) | Technology choices and rationale |
| [04-database-schema.md](./04-database-schema.md) | Complete database schema with ER diagram |
| [05-api-design.md](./05-api-design.md) | REST API endpoints and contracts |
| [06-mobile-flows.md](./06-mobile-flows.md) | Mobile app UI wireframes (ASCII) |
| [07-mermaid-diagrams.md](./07-mermaid-diagrams.md) | Flow diagrams (Mermaid) |
| [08-admin-dashboard.md](./08-admin-dashboard.md) | Admin web dashboard wireframes (ASCII) |
| [09-project-structure.md](./09-project-structure.md) | Monorepo folder structure |
| [10-roadmap.md](./10-roadmap.md) | 6-month development roadmap |
| [11-edge-cases.md](./11-edge-cases.md) | Edge cases and error handling |
| [12-security.md](./12-security.md) | Security, authentication, PDPA compliance |
| [13-deployment.md](./13-deployment.md) | Deployment strategy and CI/CD |
| [14-appendix.md](./14-appendix.md) | Seed data, translations, environment variables |
| [15-summary.md](./15-summary.md) | Executive summary and open questions |

## Quick Start

1. Read `01-overview.md` for the big picture
2. Read `02-architecture.md` for system design
3. Read `10-roadmap.md` for the build plan
4. Read `15-summary.md` for open decisions

## Stack Summary

- **Mobile**: Expo (React Native) + NativeWind
- **Web**: Next.js 15 + Tailwind + shadcn/ui
- **Backend**: Next.js API Routes + Drizzle ORM
- **Database**: PostgreSQL (Supabase)
- **AI**: OpenAI GPT-4o Vision + GPT-4o
- **Messaging**: WhatsApp Business API (360dialog)
- **Language**: Bahasa Melayu + English
