# Deployment Strategy

## Environments

| Environment | Database | Hosting | Purpose |
|---|---|---|---|
| **Local** | Docker (Postgres) | `localhost:3000` | Development |
| **Staging** | Supabase (staging project) | Vercel Preview Deploy | Internal testing |
| **Production** | Supabase (production project) | Vercel Production | Live shop operations |

## CI/CD Pipeline (GitHub Actions)

```
Developer pushes to branch
        │
        ▼
┌──────────────────────────────────────┐
│  CI (on every push)                  │
│  ├── pnpm install --frozen-lockfile  │
│  ├── pnpm lint (ESLint + Prettier)   │
│  ├── pnpm typecheck (tsc)            │
│  └── pnpm test (Vitest)              │
└──────────────┬───────────────────────┘
               │
       ┌───────┴───────┐
       ▼               ▼
┌─────────────┐  ┌──────────────┐
│   STAGING   │  │  PRODUCTION  │
│             │  │              │
│ Merge to    │  │ Manual       │
│ `main` →    │  │ trigger      │
│ Auto-deploy │  │ from `main`  │
│ to Vercel   │  │ to Vercel    │
│ Preview URL │  │ Production   │
└─────────────┘  └──────────────┘
```

## Mobile App Distribution

| Stage | Method | Audience | Setup Time |
|---|---|---|---|
| Development | Expo Go (QR code scan) | You (developer) | Instant |
| Internal Testing | EAS Build → TestFlight | Brother's 5 shops | ~1 day |
| Production | EAS Build → App Store | Public (SaaS phase) | ~2 weeks review |

**Internal Testing Setup:**
1. Register Apple Developer account (US$99/year)
2. Add staff Apple IDs to App Store Connect
3. `eas build --profile preview --platform ios`
4. Upload to TestFlight
5. Staff install via TestFlight app

**OTA Updates (Expo):**
For minor changes (UI tweaks, bug fixes) that don't change native code:
1. `npx expo export` (builds JS bundle)
2. `eas update --branch production`
3. All installed apps auto-update on next launch
4. No App Store review needed

## Database Migrations

```bash
# Development: push schema directly (Drizzle push)
pnpm db:push

# Staging / Production: generate and run migrations
pnpm db:generate  # Creates migration files from schema changes
pnpm db:migrate    # Runs migrations against target database
```

**Production migration rules:**
1. Never run destructive migrations (DROP COLUMN, DROP TABLE) without review
2. Add columns as nullable first, backfill, then make NOT NULL
3. Have rollback SQL ready before running migration
4. Run during low-traffic hours (after 8 PM Malaysian time)
5. Take database snapshot before migration

## Backup Strategy

| Level | Frequency | Retention |
|---|---|---|
| Supabase auto-backup | Daily | 7 days (free), 30 days (Pro) |
| Manual pg_dump | Before each migration | 30 days |
| CSV export | Monthly | Indefinite (cold storage) |

## Custom Domain (SaaS Phase)

1. Purchase domain: e.g., `repairintake.my` or `intake.ai`
2. Configure DNS in Vercel domains
3. For tenant white-label: `shop.repairintake.my` or custom domain support later
4. SSL auto-provisioned by Vercel

## Monitoring & Alerts

| What | How | Alert Threshold |
|---|---|---|
| App errors | Sentry (free tier) | New error in production |
| API latency | Vercel Analytics | > 2s P95 latency |
| OpenAI failures | Custom counter | > 5% error rate |
| Database CPU | Supabase dashboard | > 80% sustained |
| WhatsApp failures | Notification log query | > 10% delivery failure |

## Initial Setup Checklist

- [ ] Create Supabase project (production)
- [ ] Create Supabase project (staging)
- [ ] Register 360dialog account + verify WhatsApp number
- [ ] Get OpenAI API key
- [ ] Deploy to Vercel (connect GitHub repo)
- [ ] Set up environment variables in Vercel
- [ ] Run initial database migration
- [ ] Seed device catalog (50 models)
- [ ] Create owner account
- [ ] Create 5 outlet records
- [ ] Create staff accounts for brother's team
- [ ] Build iOS app via EAS
- [ ] Distribute via TestFlight
- [ ] Test WhatsApp message templates
- [ ] Training session with brother's staff
