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
| Apple Developer Program | RM38 | RM450/year amortized |
| Domain + DNS | RM4 | RM50/year amortized |
| Supabase Cloud | RM0 | Free tier (500MB DB, 1GB storage) |
| OpenAI API (dev + testing) | RM50–100 | GPT-4o Vision + GPT-4o calls |
| 360dialog WhatsApp API | RM0–30 | Sandbox → low volume production |
| Vercel Hosting | RM0 | Hobby tier |
| Expo EAS Build | RM0 | Free tier |
| **Total Monthly (POC)** | **~RM90–170** | |

### 6-Month POC Total Cash Outlay

| Category | Amount (RM) |
|----------|-------------|
| One-time (Apple Dev, domain, initial credits) | ~RM750 |
| Monthly costs × 6 months | ~RM600–1,020 |
| **Total POC Investment** | **~RM1,350–1,770** |

Your sweat equity (6 months full-time development) is the real investment — valued at RM30,000–60,000 of market-rate engineering time.

---

## When to Start Charging

### Brother's Shops: Month 4+ (Post-MVP)

Once the system is handling real tickets daily and saving staff time, transition to a fair operational cost recovery:

| Milestone | Action |
|-----------|--------|
| **Month 1–3** | Free. Focus on adoption and feedback. |
| **Month 4** | Introduce "cost sharing": Brother covers WhatsApp API + Supabase Pro (~RM300/month). You continue covering AI costs. |
| **Month 5** | Full operational cost split: Brother covers all infra costs (~RM435/month). You cover nothing. |
| **Month 6** | Negotiate license fee: RM200–500/outlet/month for software + AI. This is your first revenue. |

**Suggested brother pricing (Month 6):**
- RM300/outlet/month = RM1,500/month for 5 outlets
- Includes: software license, all AI features, WhatsApp messaging, support
- 12-month minimum contract
- Annual prepay: 10% discount (RM16,200/year)

This is reasonable because:
- Each outlet handles ~300 tickets/month
- Cost per ticket: RM1.00 (vs. saving 5+ minutes staff time per ticket)
- Competing software (non-AI) in Malaysia charges RM500–800/outlet/month

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

- Unlimited tickets
- AI device identification (Vision)
- AI diagnosis suggestions
- Photo quality gate
- Pre-existing damage detection
- Voice-to-structured notes
- WhatsApp Business API integration
- Bahasa Melayu + English
- iOS mobile app
- Web admin dashboard
- Real-time queue
- Basic reporting

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
- **No per-ticket fees**: Predictable monthly cost, no surprise bills during busy months
- **AI included**: Differentiator — competitors charge extra for "premium features"

### Competitor Comparison (Malaysia Market)

| Product | Price | AI? | WhatsApp? | Mobile App? |
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
| 6 | 1 (brother) | 5 | 1,500 | Brother paying full license |
| 7 | 3 | 8 | 2,500 | 2 friendly shops on trial |
| 8 | 5 | 12 | 3,800 | First paid conversions |
| 9 | 8 | 18 | 5,500 | Word of mouth in repair community |
| 10 | 12 | 25 | 7,500 | |
| 11 | 15 | 30 | 9,000 | |
| 12 | 20 | 40 | 12,000 | |

**Year 1 Total Revenue: ~RM55,000**

### Moderate Scenario (Year 1)

| Month | Shops | Outlets | MRR (RM) |
|-------|-------|---------|----------|
| 6 | 1 | 5 | 1,500 |
| 7 | 5 | 10 | 3,500 |
| 8 | 10 | 20 | 7,000 |
| 9 | 18 | 35 | 12,000 |
| 10 | 25 | 50 | 17,000 |
| 11 | 35 | 70 | 23,000 |
| 12 | 50 | 100 | 32,000 |

**Year 1 Total Revenue: ~RM140,000**

### Costs at Scale (50 shops, 100 outlets)

| Service | Monthly Cost (RM) |
|---------|------------------|
| Supabase (scaled) | 1,500 |
| OpenAI API (all tenants) | 3,000 |
| 360dialog WhatsApp | 4,000 |
| Vercel (Pro) | 400 |
| Expo EAS (Teams) | 300 |
| Stripe fees (3.4% + RM1) | 1,100 |
| Customer support (part-time) | 2,000 |
| **Total Monthly Costs** | **~RM12,300** |
| **MRR (RM32,000)** | |
| **Gross Margin** | **~62%** |

---

## POC → SaaS Transition Plan

| Phase | Timeline | Brother Pays | Other Shops | Your Focus |
|-------|----------|--------------|-------------|------------|
| **Build** | Month 1–3 | Nothing | N/A | Build MVP |
| **Pilot** | Month 4–5 | Infra costs only (~RM300/mo) | N/A | Polish, fix bugs |
| **License** | Month 6 | RM1,500/mo | N/A | Extract SaaS |
| **Beta** | Month 6–7 | RM1,500/mo | 2–3 free trials | Onboarding |
| **Launch** | Month 8+ | RM1,500/mo | First paid customers | Growth |

### Brother's Pricing Guarantee

As the first customer and investor (through shop adoption), brother gets:
- **Grandfathered pricing**: RM250/outlet/month locked for 24 months (vs. RM300 public rate)
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
| Brother refuses to pay Month 6 | High | Written agreement Month 1; "free for 5 months, then license" |
| Shops prefer free alternatives | Medium | AI differentiation; 10x better than spreadsheets |
| Price sensitivity in Malaysia | Medium | Offer monthly; annual discount; starter plan at RM199 |
| OpenAI costs rise | Low | Implement rate limiting; cache results; swap models |
| 360dialog price increases | Medium | Build abstraction layer; can swap to other WhatsApp BSPs |

---

## Summary

- **POC is free for brother** (you invest ~RM1,500 + 6 months time)
- **Month 6**: Brother starts paying RM1,500/mo for 5 outlets
- **SaaS pricing**: RM199–799/month per shop, AI included
- **Year 1 target**: RM55,000–140,000 revenue from 20–50 shops
- **Gross margin target**: 60%+ at scale
- **Breakeven**: ~15 shops on Pro plan (RM12,000 MRR = ~RM12,300 costs)

The AI features are not just product differentiators — they justify the price premium over manual systems. A shop paying RM399/month saves 10+ staff-hours, which at Malaysian wages is worth RM300–500/month. The ROI is immediate.
