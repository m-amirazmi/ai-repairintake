# Pricing Strategy

> **DEPRECATED** — Superseded by `13-pricing.md`. This file was an early draft with placeholder numbers. The authoritative pricing document is at `/apps/docs/13-pricing.md`.

**Status:** Draft — will be validated after MVP with real usage data.

---

## Internal (Brother's 5 Outlets)

Free during MVP development and testing. In exchange: real-world feedback, domain expertise, reference customer for SaaS launch.

---

## SaaS Pricing (Month 6+)

### Model: Per-Outlet, Monthly

| Tier | Outlets | Price (RM/mo) | Features |
|---|---|---|---|
| **Starter** | 1 | RM129 | All intake tiers, WhatsApp, AI, 100 tickets/mo |
| **Pro** | Up to 5 | RM249 | Unlimited tickets, reports, advanced AI |
| **Business** | Up to 10 | RM449 | Multi-outlet dashboard, inventory, staff roles |
| **Enterprise** | Unlimited | Custom | White-label, custom WhatsApp number, priority support |

### Why Per-Outlet Pricing

1. **Aligned with value** — More outlets = more revenue for shop = more value from system
2. **Predictable** — Unlike per-ticket pricing (variable, hard to budget)
3. **Simple** — Easy to understand, easy to sell
4. **Expansion incentive** — Adding an outlet costs the same per-outlet, encouraging growth

### Competitive Pricing Context

| Competitor | Entry Price | Notes |
|---|---|---|
| RepairDesk | $99/mo (~RM450) | 1 outlet, US pricing |
| Orderry | ~$79/mo (~RM350) | 1 outlet |
| RepairShopr | ~$99/mo (~RM450) | 1 outlet |
| **Our Starter** | **RM129/mo (~$29)** | **1 outlet, targeted at Malaysian shops** |

Our pricing is deliberately lower than US competitors because:
1. Malaysian shop revenue is lower (RM50-350 per repair vs $100-400 in US)
2. Lower cost of living = lower willingness to pay
3. Early-stage SaaS needs adoption velocity over revenue maximization
4. Can raise prices as feature set and brand strengthen

### AI Cost Per Ticket (Our Margin)

| Intake Type | AI Calls | Cost/Ticket | RM129 Covers |
|---|---|---|---|
| Quick Repair | 0 | RM0.00 | Infinite |
| Voice Intake | Whisper + GPT-4o parse | ~RM0.03 | 4,300 tickets/mo |
| Full AI | GPT-4o Vision + diagnosis | ~RM0.06 | 2,150 tickets/mo |

At 100 tickets/mo (Starter limit), AI costs ~RM5/mo total. At scale, costs drop with caching and model improvements. Margins are healthy.

---

## Stripe Billing (Malaysia)

- Supported currencies: MYR
- Payment methods: FPX online banking, credit/debit card, GrabPay
- Stripe fee: 2.9% + RM2.00 per transaction (Malaysia)
- 14-day free trial, no credit card required

---

## What We Don't Charge For

- AI features (included in all tiers — our moat)
- WhatsApp messaging (bundled, costs us ~RM0.05/msg)
- Device catalog updates (automatic)
- Support (tiered by plan, Starter = community forum, Pro+ = email/chat)
- Staff accounts (unlimited per outlet)

---

## Upsell Path

1. **Starter** (1 outlet, RM129) → shop owner sees value in AI intake + WhatsApp
2. **Pro** (expansion: opened 2nd outlet) → needs multi-outlet dashboard
3. **Business** (growing chain, 5+ outlets) → needs reports, staff roles, inventory
4. **Enterprise** (10+ outlets, white-label) → custom WhatsApp number, branded experience

The product grows with the business. No forced migration — upgrade when ready.
