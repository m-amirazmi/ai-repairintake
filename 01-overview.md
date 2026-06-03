# Repair Intake System — Project Overview

## Vision

An **AI-native repair intake system** for smartphone repair shops. Built first as an
internal tool for a 5-outlet chain in Dungun/Kerteh, Malaysia, then extracted into a
SaaS platform for the broader Malaysian repair shop market.

## Core Philosophy

- **AI-First**: Device identification, damage detection, diagnosis, and voice transcription from Day 1
- **Staff-First**: Mobile app on personal iPhones, minimal typing, maximum speed
- **Two-Stage Intake**: Front desk receives → Technician assesses (can be same person)
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

A two-stage intake system:
1. **Stage 1 (Front Desk)**: Staff snaps a photo of the device → AI identifies the model.
   Staff captures customer's rough description (voice or text) and contact info (for
   WhatsApp). The system creates a ticket. **No diagnosis or pricing at this stage.**
2. **Stage 2 (Technician)**: Staff opens the ticket in their queue. AI suggests likely
   diagnoses based on the device model and symptoms. Technician confirms or overrides,
   selects parts, and the system auto-calculates the quote. **WhatsApp notification sent**
   to customer with estimate and YES/NO approval flow.

When a technician handles the intake directly (no handoff), the flow compresses into a
single session — receive and assess in one go. The app prompts: "Assess now or later?"

## Scope

### MVP (Months 1–3)
- **Devices**: iPhones + Samsung Galaxy (50 models seeded)
- **Flow**: Receive → Assess → Quote → Approve → Repair → Notify → Pickup
- **Platforms**: iOS mobile app (Expo) + Web admin dashboard (Next.js)
- **Communication**: WhatsApp Business API (centralized HQ number)
- **Language**: Bahasa Melayu (default) + English
- **Multi-Outlet**: 5 branches, shared pricing catalog
- **AI Features**: Device ID (Vision), Diagnosis Suggestions, Photo Quality Gate, Pre-Existing Damage Detection, Voice-to-Structured Notes, AI WhatsApp Drafting

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
- Android mobile app (iOS staff devices only)

## Key Design Decisions

| Decision | Rationale |
|---|---|
| No price at Stage 1 | Avoids misquotes — price only after technician diagnosis |
| AI Vision Day 1 | Core differentiator, low cost (~$7.50/month for 50 tickets/day) |
| WhatsApp for everything | 100% of Malaysian communication, no SMS fallback needed |
| Static parts catalog | Each shop uses different suppliers — owner manages their own pricing |
| Single WhatsApp number | Centralized HQ communication for all 5 outlets |
| One codebase for all roles | Role-based UI, not separate apps for front-desk vs technician |
| Mobile-first (iOS only) | All staff use iPhones, no Android devices at the shops |
| Voice-to-structured notes | Faster than typing; extracts structured damage data from speech |
| AI damage documentation | Protects shop from "you broke my phone" disputes |
| AI WhatsApp drafting | Consistent professional tone across all 5 outlets |

## Who Uses It

| Role | Device | Primary Action |
|---|---|---|
| Front Desk Staff | iPhone | Receive device, capture photo + customer info |
| Technician | iPhone | Assess device, diagnose, quote, repair |
| Manager/Owner | iPhone or Web | View queue, reports, manage catalog |
| Platform Owner | Web | Manage all tenants (SaaS phase) |

## Success Metrics

- **Intake speed**: Under 30 seconds to create a ticket (photo + customer info)
- **AI accuracy**: 80%+ correct device identification from photo
- **Diagnosis speed**: Technician can assess in under 2 minutes with AI suggestions
- **Customer satisfaction**: 90%+ WhatsApp reply rate for approvals
- **Brother's adoption**: All 5 branches using it daily after 3 months
