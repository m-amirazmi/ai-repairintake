# Pricing & Business Model

## Philosophy

This document covers two phases:
1. **POC Phase** (Months 1–6): Built for brother's 5-outlet chain. No charges to brother.
2. **SaaS Phase** (Month 6+): Extracted platform for Malaysian repair shop market.

---

## POC Phase: Costs Borne by Developer (You)

During the 6-month build, all infrastructure and API costs are absorbed by you as the developer. This is an investment in proving the product works before asking for payment.

### Monthly Costs (POC)

| Service | Monthly Cost (RM) | Notes |
|---------|------------------|-------|
| Domain + DNS | RM4 | RM50/year amortized |
| Supabase Cloud | RM0 | Free tier (500MB DB, 1GB storage) |
| OpenAI API (dev + testing) | RM50–100 | GPT-4o Vision + GPT-4o calls |
| 360dialog WhatsApp API | RM0–30 | Sandbox → low volume production |
| Vercel Hosting | RM0 | Hobby tier |
| **Total Monthly (POC)** | **~RM55–135** | |

### 6-Month POC Total Cash Outlay

| Category | Amount (RM) |
|----------|-------------|
| One-time (domain, initial API credits) | ~RM200 |
| Monthly costs × 6 months | ~RM330–810 |
| **Total POC Investment** | **~RM530–1,010** |

Your sweat equity (6 months full-time development) is the real investment — valued at RM30,000–60,000 of market-rate engineering time.

---

## When to Start Charging

### Brother's Shops: 6-Month Free Trial

Rather than incremental cost-sharing, brother gets a clean **6-month free trial** for the first 3 outlets. The goal: let the system prove itself as their main tool for repair tracking and WhatsApp customer communication before asking for payment.

| Milestone | Action |
|-----------|--------|
| **Month 1–3** | Build MVP. Brother's shops not onboarded yet. |
| **Month 4–6** | Pilot phase: 3 outlets fully onboarded, all features, zero cost to brother. |
| **Month 7–9** | Full rollout: remaining 2 outlets join. Still free. Monitor usage and dependency. |
| **Month 10** | Evaluation: is this their main system for repair tracking + WhatsApp communication? If yes → negotiate paid license. If no → extend trial 3 more months and fix gaps. |

**Success criteria before charging:**
- System is their primary tool for ticket creation (not paper/WhatsApp manually)
- WhatsApp customer notifications are flowing through the platform
- Staff at all outlets are using it daily without hand-holding
- Queue management and payment capture are happening in-app

**Post-evaluation pricing (Month 10+):**
- RM250/outlet/month for the first 3 outlets = RM750/month
- Remaining 2 outlets at same rate = RM1,250/month total for all 5
- Grandfathered at this rate for 24 months from first payment
- No setup fee, no contract minimum
- Annual prepay: 10% discount

This is substantially below the public rate (RM300/outlet on Basic plan) in exchange for being the reference customer and providing ongoing feedback.

---

## SaaS Phase: Public Pricing (Month 6+)

When extracting into a multi-tenant SaaS for other repair shops.

### Target Market
- Small-medium phone repair shops (1–10 outlets)
- Malaysia-focused initially (MYR, Bahasa Melayu, WhatsApp)
- Single-brand or multi-brand shops

### Pricing Tiers

| Plan | Outlets | Monthly Price | Annual Price (Save 2 mo) | Best For |
|------|---------|---------------|-------------------------|----------|
| **Starter** | 1 | RM199 | RM1,990 | Solo shop, 1 owner-technician |
| **Basic** | 2–3 | RM399 | RM3,990 | Growing shop, 2–3 staff |
| **Pro** | 4–10 | RM799 | RM7,990 | Multi-outlet chain |
| **Enterprise** | 10+ | Custom | Custom | Large chains, franchises |

### What's Included (All Plans)

- Generous monthly ticket allowance (see limits below)
- AI device identification (Vision)
- AI diagnosis suggestions
- Photo quality gate
- Pre-existing damage detection
- Voice-to-structured notes
- WhatsApp Business API integration
- Bahasa Melayu + English
- Web PWA (mobile + desktop)
- Real-time queue
- Basic reporting
- Staff accounts (up to plan limit)

### Add-Ons

| Add-On | Price | Notes |
|--------|-------|-------|
| Extra staff seats (beyond 5) | RM49/user/month | |
| Advanced reporting | RM99/month | Technician performance, demand forecasting |
| Inventory management | RM99/month | Stock tracking, low-stock alerts |
| White-label branding | RM199/month | Custom logo, colors, WhatsApp profile |
| API access | RM299/month | For shops building their own integrations |
| Priority support | RM149/month | WhatsApp support, 4-hour response |

### Limits Per Plan

| Feature | Starter | Basic | Pro | Enterprise |
|---------|---------|-------|-----|------------|
| Monthly tickets | 500 | 1,500 | 5,000 | Unlimited |
| AI Vision calls | 600 | 2,000 | 6,000 | Unlimited |
| WhatsApp conversations | 1,000 | 3,000 | 10,000 | Unlimited |
| Storage (photos) | 5GB | 20GB | 100GB | Custom |
| Outlets | 1 | 3 | 10 | Custom |
| Staff users | 3 | 8 | 20 | Custom |

### Why This Pricing?

- **Starter at RM199**: Lower than most POS systems (RM300–500), competitive with simple ticket apps
- **Per-outlet model**: Aligns with how repair shops think ("I have 3 branches")
- **Fair-use ticket limits**: Predictable infrastructure costs, no surprise bills. 500+ tickets/mo is generous for a single-outlet shop (covers 16+ repairs/day).
- **AI included**: Differentiator — competitors charge extra for "premium features"

### Competitor Comparison (Malaysia Market)

| Product | Price | AI? | WhatsApp? | Mobile PWA? |
|---------|-------|-----|-----------|-------------|
| Simple ticket book (manual) | RM0 | No | No | No |
| Google Sheets / Excel | RM0 | No | No | No |
| Generic POS (e.g., StoreHub) | RM300–500/mo | No | No | No |
| RepairShopr (US) | $99–169/mo | No | SMS only | No |
| **Repair Intake (ours)** | **RM199–799/mo** | **Yes** | **Yes** | **Yes** |

---

## Revenue Projection (SaaS)

### Conservative Scenario (Year 1)

| Month | Shops | Outlets | MRR (RM) | Notes |
|-------|-------|---------|----------|-------|
| 6 | 0 | 0 | 0 | 2–3 friendly shops on free trial |
| 7 | 2 | 4 | 800 | First external paid conversions |
| 8 | 4 | 8 | 2,000 | Word of mouth in repair community |
| 9 | 6 | 12 | 3,200 | |
| 10 | 1 (brother) + 8 | 3 + 16 | 6,400 | Brother evaluation complete, 3 outlets paid |
| 11 | 1 + 12 | 3 + 25 | 8,750 | |
| 12 | 1 + 18 | 3 + 35 | 12,000 | Brother adds 2 more outlets |

**Year 1 Total Revenue: ~RM33,000** (lower due to brother's extended free trial)

### Moderate Scenario (Year 1)

| Month | Shops | Outlets | MRR (RM) |
|-------|-------|---------|----------|
| 6 | 0 | 0 | 0 |
| 7 | 5 | 10 | 2,500 |
| 8 | 10 | 20 | 5,000 |
| 9 | 18 | 35 | 10,000 |
| 10 | 1 (brother) + 25 | 3 + 50 | 15,750 |
| 11 | 1 + 35 | 5 + 70 | 23,500 |
| 12 | 1 + 50 | 5 + 100 | 33,000 |

**Year 1 Total Revenue: ~RM90,000**

### Costs at Scale (50 shops, ~100 outlets)

| Service | Monthly Cost (RM) | Notes |
|---------|------------------|-------|
| Supabase (scaled) | 1,500 | Pro/Team tier |
| OpenAI API (all tenants) | 500 | ~30K tickets/mo, 60% Quick Repair (free), 25% Voice (RM0.03), 15% Full AI (RM0.06) |
| 360dialog WhatsApp | 4,000 | ~30K conversations/mo |
| Vercel (Pro) | 90 | $20/mo Pro tier |
| Stripe fees (3.4%) | 1,120 | On ~RM33K MRR |
| Customer support (part-time) | 2,000 | |
| **Total Monthly Costs** | **~RM9,210** | |
| **MRR (RM33,000)** | |
| **Gross Margin** | **~72%** | |

### Breakeven Analysis

At small scale, infra costs are negligible (free tiers, founder support):

| Stage | Shops | MRR | Infra Costs | Margin |
|-------|-------|-----|-------------|--------|
| **Minimal** (2 shops on Basic) | 2 | RM798 | ~RM300 | **62%** |
| **Breakeven** | 2–3 | RM1,197 | ~RM500 | **58%** |
| **Healthy** (10 shops) | 10 | RM4,500 | ~RM1,500 | **67%** |
| **Scale** (50 shops) | 50 | RM33,000 | ~RM9,210 | **72%** |

Breakeven happens almost immediately — at just **2–3 shops on Basic plan**, monthly subscription revenue already covers all infrastructure costs. The real investment is engineering time, not infrastructure.

---

## POC → SaaS Transition Plan

| Phase | Timeline | Brother Pays | Other Shops | Your Focus |
|-------|----------|--------------|-------------|------------|
| **Build** | Month 1–3 | Nothing | N/A | Build MVP |
| **Pilot** | Month 4–6 | Nothing (3 outlets free) | N/A | Polish, fix bugs, measure adoption |
| **Full Rollout** | Month 7–9 | Nothing (all 5 outlets free) | 2–3 free trials | Monitor dependency, collect testimonials |
| **Evaluate** | Month 10 | Revenue starts if criteria met | Begin paid conversions | Negotiate license, extract SaaS |
| **Launch** | Month 10+ | RM250/outlet/mo (3 outlets) | First paid customers | Growth |

### Brother's Pricing Guarantee

As the first customer and real-world testing ground, brother gets:
- **6 months free**: 3+ outlets fully featured, zero cost, no strings
- **Grandfathered pricing**: RM250/outlet/month locked for 24 months (vs. RM300+ public rate)
- **Feature priority**: His shop needs get first priority in development
- **Zero setup fee**: Waived RM500 onboarding fee
- **Free white-label**: RM199/month add-on included free

---

## Key Metrics to Track

| Metric | Target | Why |
|--------|--------|-----|
| **CAC** (Customer Acquisition Cost) | < RM500 | Must recover in <3 months |
| **LTV** (Lifetime Value) | > RM3,600 | 12+ month retention at RM300/mo |
| **LTV:CAC Ratio** | > 3:1 | Healthy SaaS benchmark |
| **Churn** | < 5%/month | Critical for Malaysian market |
| **NRR** (Net Revenue Retention) | > 100% | Upsell add-ons, expand outlets |
| **Payback Period** | < 6 months | From CAC to revenue recovery |

---

## Risks & Mitigations

| Risk | Impact | Mitigation |
|------|--------|------------|
| Brother doesn't adopt system as main tool | High | 6-month free trial with clear success criteria; system must integrate deeply into daily workflow |
| Shops prefer free alternatives | Medium | AI differentiation; 10x better than spreadsheets |
| Price sensitivity in Malaysia | Medium | Offer monthly; annual discount; starter plan at RM199 |
| OpenAI costs rise | Low | Implement rate limiting; cache results; swap models |
| 360dialog price increases | Medium | Build abstraction layer; can swap to other WhatsApp BSPs |

---

## Summary

- **POC is free for brother** (you invest ~RM530–1,010 cash + 6 months time)
- **6-month free trial**: 3+ outlets, all features, zero cost to brother
- **Month 10**: Brother starts paying RM250/outlet/month if system is their main tool
- **SaaS pricing**: RM199–799/month per shop, AI included, fair-use ticket limits
- **Year 1 target**: RM33,000–90,000 revenue from 18–50 shops (lower due to brother free trial)
- **Gross margin target**: 72% at scale (corrected from inflated AI + infra estimates)
- **Breakeven**: ~2–3 shops — infra costs are minimal at small scale

The AI features are not just product differentiators — they justify the price premium over manual systems. A shop paying RM399/month saves 10+ staff-hours, which at Malaysian wages is worth RM300–500/month. The ROI is immediate.
