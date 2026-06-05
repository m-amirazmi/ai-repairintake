# Repair Intake System — Project Overview

## Vision

An **AI-native repair intake system** for smartphone repair shops. Built first as an
internal tool for a 5-outlet chain in Dungun/Kerteh, Malaysia, then extracted into a
SaaS platform for the broader Malaysian repair shop market.

## Core Philosophy

- **AI-First**: Device identification, damage detection, diagnosis, and voice transcription from Day 1
- **Staff-First**: Mobile app on staff smartphones, minimal typing, maximum speed
- **Graduated Intake**: Three speed tiers — Voice (<5s), Quick Repair (<10s), Full AI (30-90s). The system adapts to the complexity of the job, not the other way around.
- **Two-Stage Intake**: Front desk receives → Technician assesses (can be same person)
- **The System IS the POS**: Payment, receipt, and inventory tracking replace the old transactional POS for repairs. No parallel system.
- **WhatsApp-Native**: 100% customer communication via WhatsApp (Malaysia standard)
- **SaaS-Ready**: Single-tenant architecture that becomes multi-tenant with one config flag
- **Bahasa Melayu**: Primary interface language with English fallback

## The Problem

Customers come into repair shops, tell staff their device is broken, and want to know:

1. What's wrong?
2. How much will it cost?
3. How long will it take?

The challenge: front desk staff often lack technical expertise to diagnose issues.
Customers can't always articulate the problem clearly. Devices are sometimes completely
dead, making diagnosis impossible at reception. The device may change hands multiple
times before assessment.

## The Solution

A **graduated intake system** that adapts to the complexity of each repair:

### Tier 1: Voice Intake (<5 seconds)

One tap, speak into phone: _"iPhone 14 Pro skrin pecah, Ahmad, RM350."_
AI extracts device, issue, customer, and price. One tap to confirm. Ticket created.
No typing, no photo, no wizard. For any repair where staff already knows the answer.

### Tier 2: Quick Repair (<10 seconds)

Typeahead device (2-3 keystrokes) + quick-select issue from top-10 common fixes.
Customer phone optional — if skipped, generates QR code receipt. Price auto-filled
from parts catalog. Ticket auto-approved and moves straight to `in_progress`.
For technician-at-counter doing common fixes with a known price.

### Tier 3: Full AI Intake (30-90 seconds)

1. **Stage 1**: Staff snaps a photo → AI identifies model, detects pre-existing damage.
   Staff captures customer complaint (voice or text) and contact info. Ticket created.
   **No diagnosis or pricing at this stage.**
2. **Stage 2**: Technician opens ticket. AI suggests diagnoses ranked by confidence.
   Technician confirms, selects parts, system auto-calculates quote. **WhatsApp
   notification sent** with YES/NO approval flow.
   For unknown problems, dead devices, or when front-desk handles intake.

When a technician handles intake directly, the flow compresses into a single
session — receive and assess in one go. The app prompts: "Assess now or later?"
For known fixes, they jump straight to Quick Repair or Voice Intake.

## Scope

### MVP (Months 1–3)

- **Devices**: iPhones + Samsung Galaxy (50 models seeded)
- **Intake**: Three-tier graduated intake (Voice, Quick Repair, Full AI)
- **Flow**: Receive → Assess → Quote → Approve → Repair → Pay → Notify → Pickup
- **POS Replacement**: Payment capture, receipt generation, parts inventory decrement
- **Platforms**: Single Next.js PWA (mobile-first responsive) — serves staff app, admin dashboard, and public ticket view. No Expo / React Native. Staff use via mobile browser with "Add to Home Screen."
- **Communication**: WhatsApp Business API (centralized HQ number)
- **Language**: Bahasa Melayu (default) + English
- **Multi-Outlet**: 5 branches, shared pricing catalog
- **AI Features**: Device ID (Vision), Diagnosis Suggestions, Photo Quality Gate, Pre-Existing Damage Detection, Voice-to-Structured Notes, AI WhatsApp Drafting, Voice Intake Parse

### Phase 2 (Months 4–6)

- Inventory management
- Offline mode with background sync
- SaaS platform extraction (signup, billing, multi-tenant)
- Repair Outcome Learning Loop (AI calibration)
- Smart Quote Flagging

### Explicitly Deferred

- E-invoicing (LHDN MyInvois)
- Thermal printer integration
- Sales/POS for refurbished phones
- Multi-brand beyond iPhone/Samsung (for MVP)
- Android app (already works via Expo — not tested/polished for launch)

## Key Design Decisions

| Decision                                     | Rationale                                                                                                                   |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Graduated intake (3 tiers)                   | 80% of repairs are known fixes. Forcing full AI flow = staff skip the system. Quick paths for quick jobs.                   |
| No price at Stage 1                          | Avoids misquotes — price only after technician diagnosis (except Quick Repair, where tech knows the price)                  |
| AI Vision Day 1                              | Core differentiator, low cost (~$7.50/month for 50 tickets/day)                                                             |
| WhatsApp for everything                      | 100% of Malaysian communication, no SMS fallback needed                                                                     |
| QR code receipt for anonymous repairs        | Cash-and-go customers don't need to share phone number. QR links to public ticket page where they can optionally subscribe. |
| Static parts catalog                         | Each shop uses different suppliers — owner manages their own pricing                                                        |
| Single WhatsApp number                       | Centralized HQ communication for all 5 outlets                                                                              |
| One codebase for all roles                   | Role-based UI, not separate apps for front-desk vs technician                                                               |
| PWA over native app | Solo frontend web dev — PWA ships in weeks, not months. Staff use the app as a work tool (9am-6pm), not a consumer app. Add to Home Screen = indistinguishable from native for this use case. |
| Voice-to-structured notes                    | Faster than typing; extracts structured data from speech                                                                    |
| AI damage documentation                      | Protects shop from "you broke my phone" disputes                                                                            |
| AI WhatsApp drafting                         | Consistent professional tone across all 5 outlets                                                                           |

## Who Uses It

| Role             | Device              | Primary Action                                                                                                     |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Front Desk Staff | Phone (iOS/Android) | Full AI intake (photo + customer info), QR receipt handoff                                                         |
| Technician       | Phone (iOS/Android) | Quick Repair / Voice Intake for known fixes, Full AI assessment for unknown problems, record payment at completion |
| Manager/Owner    | Phone or Web        | View queue, reports, manage catalog, payment reconciliation                                                        |
| Platform Owner   | Web                 | Manage all tenants (SaaS phase)                                                                                    |
| Customer         | Web (QR link)       | View ticket status, subscribe to WhatsApp updates                                                                  |

## Success Metrics

- **Intake speed**: Under 10 seconds for Quick Repair, under 5 seconds for Voice Intake, under 30 seconds for Full AI
- **Adoption rate**: 90%+ of repairs go through the system (vs old POS), measured by ticket-to-customer ratio
- **AI accuracy**: 80%+ correct device identification from photo
- **Diagnosis speed**: Technician can assess in under 2 minutes with AI suggestions
- **Customer satisfaction**: 90%+ WhatsApp reply rate for approvals
- **Brother's adoption**: All 5 branches using it daily after 3 months
