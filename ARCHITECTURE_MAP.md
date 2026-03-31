# ParentNinja — Full UI Architecture Map
**Generated:** 2026-03-30 | **Last updated:** 2026-03-31 | **Current prototype:** v1.5
**Based on:** v1.3 prototype, demo prototype, Family Profile UI Spec v1.0
**Stage:** Stage 1 → Integration-Ready roadmap

---

## 1. App-Level Structure

```
ParentNinja
│
├── LOCK SCREEN                  ← entry point (unauthenticated)
│
└── AUTHENTICATED APP
    ├── HOME                     ← tab 1 (default)
    ├── ATTENDANCE               ← tab 2
    ├── FAMILY ACCOUNT           ← tab 3
    ├── BILLING                  ← tab 4
    └── SHOP                     ← tab 5
```

**Navigation pattern:** Persistent bottom tab bar (5 items). All screens sit at the same depth — no deep nesting in v1.3. Modals and slide-in panels provide sub-interactions without full screen pushes.

---

## 2. Screen-by-Screen Inventory

---

### 2.1 LOCK SCREEN

**Purpose:** Family authentication gate before entering the app.

| Component | Description | Status |
|-----------|-------------|--------|
| Family name display | Shows family surname at top | ✅ Built |
| Student tiles | One per child — name, age, belt name, belt colour dot | ✅ Built |
| Passcode numpad | 4-digit entry with dot indicators | ✅ Built |
| Wrong passcode feedback | Dots flash red, clear after 700ms | ✅ Built |
| Passcode lockout | Lockout after N failed attempts | ✅ Built in v1.4 |
| Biometric auth option | Face ID / Touch ID shortcut | ❌ Missing |
| "Forgot passcode" flow | Recovery path | ❌ Missing |
| Stripe detail on tiles | Element badges on lock screen tiles | ❌ Missing (strips appear elsewhere only) |
| Session timeout / auto-lock | Re-show lock screen after inactivity | ❌ Missing |

**Entry paths:**
- Tap student tile → authenticated as that family
- Enter passcode → authenticated

**Gaps for integration:** No real auth handshake. Currently accepts any family passcode with no backend validation. Session management entirely absent.

---

### 2.2 HOME SCREEN

**Purpose:** Family overview — child progress at a glance, pending actions, notifications.

#### 2.2a — Dark Hero Section (top, `#1A1A2E`)

| Component | Description | Status |
|-----------|-------------|--------|
| Greeting eyebrow | "Good morning," + first name | ✅ Built |
| Family name header | Large display heading | ✅ Built |
| Current date | Formatted date string | ✅ Built |
| Term name | e.g. "Spring Term 2026" | ✅ Built |
| Notification bell | Tappable icon; gold tint when unread; red count badge | ✅ Built |
| Term strip | 3-stat bar (sessions attended, stripes earned, challenges set) — Cinzel numerals | ✅ Built |

#### 2.2b — Student Overview Cards (scrollable, `#F7F7FA`)

| Component | Description | Status |
|-----------|-------------|--------|
| Student card | Name, age, current belt, element stripe badges (28px SVG, Air·Earth·Water·Fire order) | ✅ Built |
| Belt label | Colour-coded belt name | ✅ Built |
| Stripe badges | Full / Half / Ghost states per element | ✅ Built |
| Tappable card → Student Profile | Deep-link to full student profile | ⚠️ Wired up but Student Profile screen not built |
| Multi-student support | Cards stack vertically per child | ✅ Built |
| "Inactive" student state | Visual indicator for paused students | ⚠️ Status field exists, distinct visual not confirmed |

#### 2.2c — Challenge Banner

| Component | Description | Status |
|-----------|-------------|--------|
| Challenge banner | Persistent banner — non-dismissible except by action | ✅ Built |
| "Set Challenge" button | Expands textarea inline | ✅ Built |
| Challenge textarea | Parent enters challenge description | ✅ Built |
| "Mark Complete" button | Sets challenge_confirmed = true | ✅ Built |
| Banner fade-out on confirm | Animation on completion | ✅ Built |
| Banner state: pending / set / completed | Conditional render | ⚠️ Partially — no confirmed empty state |
| Push notification on completion | Fires to parent and instructor | ❌ Not wired (backend dependency) |
| Banner when no challenge pending | Empty / hidden state | ❌ Not defined |

#### 2.2d — Notifications Panel (slide-in overlay)

| Component | Description | Status |
|-----------|-------------|--------|
| Full-height right-side panel | Slides in on bell tap | ✅ Built |
| Chronological notification feed | All notifications listed | ✅ Built |
| Filter chips | Filter by type (challenges, attendance, grading, etc.) | ✅ Built |
| Unread row styling | Gold background + gold dot | ✅ Built |
| Soft read flag (read_at timestamp) | Set on tap | ✅ Specified (backend dependency) |
| Inline action buttons | "Set Challenge" / "Mark Complete" within feed | ✅ Built |
| Never-delete policy | Soft read only, no deletion | ✅ Specified |
| Notification preferences | Which types can be toggled off | ❌ Not built (referenced in Account tab only) |
| Empty notifications state | "No notifications yet" view | ✅ Built in v1.4 |
| Real-time push delivery | Websocket / push integration | ❌ Missing |

---

### 2.3 ATTENDANCE SCREEN

**Purpose:** Parent reports attendance for scheduled classes; books make-up sessions; reviews history.

#### 2.3a — Dark Hero: Today's Classes

| Component | Description | Status |
|-----------|-------------|--------|
| "Today's Classes" section | Cards per student with scheduled class | ✅ Built |
| Class card | Student name, class name, instructor, time | ✅ Built |
| Attending button | Green, highlights on tap + confirmation message | ✅ Built |
| Absent button | Red, highlights on tap + confirmation message | ✅ Built |
| Late button | Amber, highlights on tap + confirmation message | ✅ Built |
| Instructor notified on tap | Backend event trigger | ❌ Not wired |
| Already-reported state | Button disabled once reported | ✅ Built in v1.5 |
| No classes today state | Empty / rest day message | ✅ Built in v1.4 |

#### 2.3b — Tab: Upcoming

| Component | Description | Status |
|-----------|-------------|--------|
| Upcoming classes list | Next scheduled sessions across students | ✅ Built |
| Class cards | Date, time, class name, student, instructor | ✅ Built |
| Calendar view option | Alternative to list view | ❌ Missing |

#### 2.3c — Tab: Make-Up Classes

| Component | Description | Status |
|-----------|-------------|--------|
| Make-up allowance pips | 4 per term; blue = healthy, amber = 1 remaining | ✅ Built |
| Age-appropriate class list | Filters to relevant classes only | ✅ Built |
| Space indicators | Green (comfortable) / Amber (1 space) | ✅ Built |
| Full class filtering | Classes at 30+ students hidden | ✅ Specified |
| Class selection | Tap to select | ✅ Built |
| Confirm button | Context-aware disabled until selection | ✅ Built |
| Zero allowance state | All pips gone — booking blocked | ⚠️ Visual specified, blocking enforcement not confirmed |
| Booking confirmation | Receipt / confirmation message | ✅ Built in v1.5 (includes date in confirmation) |
| Date picker for make-up | Choose which date to attend | ✅ Built in v1.5 |
| Backend booking record | absence_id, student_id, class_id, term_id, confirmed_at | ❌ Not wired |

#### 2.3d — Tab: History

| Component | Description | Status |
|-----------|-------------|--------|
| Chronological session list | All students, all sessions | ✅ Built |
| Month grouping | Sessions grouped by month header | ✅ Built |
| Status pills | Present (green) / Absent (grey) / Make-Up (amber) | ✅ Built |
| Export / download history | PDF or CSV of attendance | ❌ Missing |
| Filter by student | Show one child's history | ❌ Missing |

---

### 2.4 FAMILY ACCOUNT SCREEN

**Purpose:** Manage students, guardians, consent docs, and class transfers.

#### 2.4a — Tab: Students

| Component | Description | Status |
|-----------|-------------|--------|
| Student management cards | One per student — name, age, belt, class, membership type | ✅ Built |
| "View Profile" action | Navigates to Student Profile | ⚠️ Wired up but Student Profile not built |
| "Pause" action | Triggers confirmation dialog; soft pause, holds place 8 weeks | ✅ Built (UI); backend not wired |
| Pause confirmation dialog | Modal with confirm/cancel | ✅ Built |
| "Add New Student" CTA | Dashed border, expands inline form | ✅ Built |
| Add Student form | Full name, DOB, preferred class (age-gated dropdown) | ✅ Built |
| Consent notice | "Consent form sent to all guardians before record goes live" | ✅ Built |
| Enrolment request flow | Creates pending record, family + admin notified | ❌ Not wired |
| Edit existing student details | Change name, DOB, class preference | ❌ Not built |
| Student re-activate flow | Un-pause a paused student | ❌ Not built |
| Student removal / unenrol | Full exit flow | ❌ Not built |

#### 2.4b — Tab: Account

| Component | Description | Status |
|-----------|-------------|--------|
| Guardian chips | Initials avatar, name, role (Primary / Guardian) | ✅ Built |
| "Edit Details" action | Opens edit flow for guardian's name, email, phone, emergency contact, notification pref | ⚠️ Action visible, form not fully built |
| Edit guardian form | All contact fields, notification preference | ❌ Not fully built |
| "Change Passcode" action | Updates family passcode | ⚠️ Present in spec; implementation unclear |
| Consent documents section | Data Consent, Photo Permission, Medical Info — signed date + status pill | ✅ Built |
| Re-sign / update consent | Flow to re-sign outdated consent | ❌ Not built |
| Notification preferences | Toggle which notification types are received | ❌ Not built |
| Add second guardian | Invite a co-guardian to the family account | ❌ Not built |
| Remove guardian | Remove access for a guardian | ❌ Not built |
| Role-based access control | Differentiate what Primary vs Guardian can do | ❌ Not built |

#### 2.4c — Tab: Class Transfer

| Component | Description | Status |
|-----------|-------------|--------|
| Student selector dropdown | Lists active students | ✅ Built |
| Age-appropriate class list | Available, in-space classes auto-populate | ✅ Built |
| Reason text field | Optional free text | ✅ Built |
| "Request Transfer" button | Creates request + notifies instructor | ✅ Built (UI); backend not wired |
| Pending transfer state | Show transfer is awaiting approval | ❌ Not built |
| Transfer approved / rejected | Outcome notification | ❌ Not built |
| Transfer history | Log of past transfers | ❌ Not built |

---

### 2.5 BILLING SCREEN

**Purpose:** View membership costs, payment method, and payment history.

| Component | Description | Status |
|-----------|-------------|--------|
| Monthly total card | Dark ink card, total family cost | ✅ Built |
| Per-student breakdown | Membership tier per student | ✅ Built |
| "Upgrade" action | Tier comparison view | ⚠️ Button visible, comparison screen not built |
| "Pause" action | With confirmation; end of billing period | ⚠️ Button visible, flow not fully built |
| "Cancel" action | Cancels membership at end of period | ⚠️ Button visible, flow not fully built |
| Payment method display | Masked last-4 digits + expiry only (no raw card data) | ✅ Built |
| "Update card" action | Replace saved payment method | ❌ Not built |
| Payment history list | Chronological, amount + date | ✅ Built |
| Invoice download | PDF per payment | ❌ Not built |
| Refund / dispute | Billing query route | ❌ Missing (Phase 1 exclusion) |
| Stripe integration | All card handling via Stripe tokens; no direct mobile client access | ❌ Not wired |
| Upcoming payment preview | Next charge date + amount | ❌ Not built |
| Failed payment state | Alert + recovery flow | ❌ Not built |

---

### 2.6 SHOP SCREEN

**Purpose:** Browse and purchase merchandise, camps, and learning materials.

| Component | Description | Status |
|-----------|-------------|--------|
| Category filter chips | All / Merchandise / Camps / Residencies / Learning materials | ✅ Built |
| 2-column product grid | Emoji thumbnail, name, description, price, CTA | ✅ Built |
| CTA variants | "Add to basket" / "Book Now" / "Register Interest" / "Subscribe" | ✅ Built |
| Basket / cart | Collect items before checkout | ❌ Missing |
| Checkout flow | Payment, confirmation, receipt | ❌ Missing (Phase 1 illustrative only) |
| Product detail view | Expanded product page | ❌ Missing |
| Order history | Past purchases | ❌ Missing |
| "Register Interest" flow | Capture email for waitlisted items | ❌ Not built |
| Camp booking flow | Date selection, student assignment, consent | ❌ Missing |
| Stock / availability | Live inventory indicators | ❌ Missing |

---

### 2.7 STUDENT PROFILE

**Purpose:** Full view of an individual student's progress, belt history, session log, and instructor notes. Referenced throughout the app.

| Component | Description | Status |
|-----------|-------------|--------|
| Belt progression timeline | Visual history of belt levels earned | ✅ Built in v1.5 |
| Current belt + stripe detail | Full element badge breakdown | ✅ Built in v1.5 |
| Session log | Per-class attendance + performance notes | ✅ Built in v1.5 |
| Instructor notes (read-only) | What the instructor has recorded | ✅ Built in v1.5 |
| Challenge history | All past challenges set and completed | ✅ Built in v1.5 |
| Aura stage indicator | Current aura stage progress (referenced in ARCHITECTURE.md) | ✅ Built in v1.5 |
| Next milestone | What's needed for next belt/stripe | ✅ Built in v1.5 |
| Photo / avatar | Student photo display | ⚠️ Initial letter placeholder — full photo support is gap P4 |

---

### 2.8 SETTINGS SCREEN (MISSING SCREEN)

**Purpose:** App-level settings. Absent from v1.3 but needed for a finished app.

| Component | Description | Status |
|-----------|-------------|--------|
| Notification preferences | Toggle per notification type | ❌ Not built |
| Passcode change | From settings as well as Account tab | ❌ Not built |
| Biometric toggle | Enable/disable Face ID / Touch ID | ❌ Not built |
| App version / legal | About, T&Cs, Privacy Policy, GDPR data request | ❌ Not built |
| Language / region | Localisation (future) | ❌ Not built |
| Help / FAQ | In-app support content | ❌ Not built |
| Log out / switch family | Session management | ❌ Not built |

---

## 3. Navigation & Interaction Flows

```
LOCK SCREEN
    │
    ├─ [correct passcode] ──────────────────► HOME
    │                                          │
    │                                      [tap bell] ──► NOTIFICATIONS PANEL (overlay)
    │                                          │
    │                                      [tap student card] ──► STUDENT PROFILE ⚠️ (not built)
    │                                          │
    │                                      [challenge banner] ──► inline actions
    │
    └─ [tap student tile] ─────────────────► HOME
```

```
BOTTOM TAB BAR  (persistent across all authenticated screens)
    HOME  ·  ATTENDANCE  ·  FAMILY ACCOUNT  ·  BILLING  ·  SHOP
```

```
FAMILY ACCOUNT
    ├─ Students tab
    │   ├─ [tap student card] → STUDENT PROFILE ⚠️
    │   ├─ [pause] → Confirmation Modal → soft pause
    │   └─ [add new student] → inline form → enrolment request
    │
    ├─ Account tab
    │   └─ [edit details] → Edit Guardian form ⚠️ (partial)
    │
    └─ Class Transfer tab
        └─ [request transfer] → instructor notified → pending state ⚠️
```

---

## 4. Data Model Overview

### Entities surfaced in parent-facing UI

| Entity | Key Fields | Source |
|--------|-----------|--------|
| Family | family_id, family_name, passcode (hashed) | Family Account |
| Guardian | guardian_id, name, role, email, phone, emergency_contact, notification_pref | Account tab |
| Student | student_id, full_name, dob, age (calc), belt, element_stripes, status, class_schedule, membership_type | Student cards |
| Belt | belt_id, belt_name, belt_colour | Student card, Lock screen |
| Element Stripe | stripe_id, element (Air/Earth/Water/Fire), state (Full/Half/Ghost) | Student card |
| Class | class_id, class_name, instructor, schedule, capacity (30 max), age_range | Attendance, Transfer |
| Attendance Record | attendance_id, student_id, class_id, status (Present/Absent/Late/Make-Up), reported_at | Attendance screen |
| Make-Up Booking | makeup_id, absence_id, student_id, class_id, term_id, confirmed_at | Make-Up tab |
| Challenge | challenge_id, student_id, description, confirmed (bool), confirmed_at | Home / Challenge banner |
| Notification | notification_id, guardian_id, type, body, read_at (nullable), created_at | Notifications panel |
| Term | term_id, term_name, start_date, end_date | Home hero |
| Membership | membership_id, student_id, type (Monthly Rolling/Termly), monthly_cost, status | Billing, Student card |
| Payment | payment_id, family_id, amount, date, stripe_token | Billing screen |
| Product | product_id, name, description, category, price, cta_type | Shop screen |
| Consent Document | consent_id, student_id/guardian_id, type, signed_date, status | Account tab |
| Transfer Request | transfer_id, student_id, from_class_id, to_class_id, reason, status, created_at | Class Transfer tab |

---

## 5. Quality-of-Life Improvements: Current → Integration-Ready

The following table maps every identified gap to its priority and the screen it affects. This is the working list for bringing the app from Stage 1 prototype to integration-ready.

### 🔴 Critical — App cannot ship without these

| # | Gap | Screen | Notes |
|---|-----|--------|-------|
| C1 | Real authentication backend | Lock Screen | Passcode must be validated server-side; session tokens needed |
| C2 | Session management (timeout, auto-lock) | Global | Re-lock after inactivity; token expiry |
| C3 | Student Profile screen | Home, Family Account | Referenced everywhere; not built at all — ✅ Built in v1.5 (2026-03-31) |
| C4 | Empty states for all screens | All | No classes, no notifications, new account — all need defined views — ✅ Built in v1.4 (2026-03-31) |
| C5 | Error states for all forms and actions | All | Network errors, validation failures, server errors — ✅ Built in v1.4 (2026-03-31) |
| C6 | Loading / skeleton states | All | Data fetching indicators needed throughout — ✅ Built in v1.4 (2026-03-31) |
| C7 | Attendance "already reported" guard | Attendance | Prevent duplicate attendance submissions — ✅ Built in v1.5 (2026-03-31) |
| C8 | Make-up date selection | Attendance | Can't book a make-up without choosing a date — ✅ Built in v1.5 (2026-03-31) |
| C9 | Instructor notification wiring | Attendance, Home, Account | All backend event triggers need to be live |
| C10 | Passcode lockout after N failed attempts | Lock Screen | Phase 2 flag in spec — needed before launch — ✅ Built in v1.4 (2026-03-31) |

### 🟠 High — Needed for a complete parent experience

| # | Gap | Screen | Notes |
|---|-----|--------|-------|
| H1 | Edit guardian details form | Family Account → Account | Form fields partially spec'd; not built |
| H2 | Change passcode flow | Family Account → Account | Referenced but implementation unclear |
| H3 | Edit existing student details | Family Account → Students | Currently no way to correct student info |
| H4 | Student re-activate (un-pause) flow | Family Account → Students | Pause exists; un-pause does not |
| H5 | Transfer pending / approved / rejected states | Family Account → Class Transfer | Parent left hanging after submitting request |
| H6 | Notification preferences | Family Account → Account, Settings | Which notification types can be toggled |
| H7 | Push notification permission flow | Global | iOS/Android permission request on first launch |
| H8 | Real-time notification delivery | Notifications Panel | Currently no websocket/polling — notifications are static |
| H9 | Upcoming payment preview | Billing | Next charge date + amount before it hits |
| H10 | Failed payment alert + recovery flow | Billing | Critical for subscription model |
| H11 | Stripe integration (live) | Billing | Payment method display only; no live calls |
| H12 | Passcode lockout | Lock Screen | Same as C10 — elevated here due to family data risk |
| H13 | Settings screen | Global | Notification prefs, biometrics, legal, logout |
| H14 | "Forgot passcode" / recovery | Lock Screen | No path out of a forgotten passcode |

### 🟡 Medium — Quality of life; expected by parents

| # | Gap | Screen | Notes |
|---|-----|--------|-------|
| M1 | Filter attendance history by student | Attendance → History | Multi-student families need this badly |
| M2 | Challenge history view | Home / Student Profile | Previous challenges: what was set, when completed |
| M3 | Belt progression timeline | Student Profile | Key motivational feature for parents |
| M4 | Aura stage progress | Student Profile | Referenced in ARCHITECTURE.md but not visible to parent |
| M5 | "Next milestone" indicator | Student Profile | What does my child need for their next belt/stripe? |
| M6 | Tier comparison view (Upgrade) | Billing | Button present; destination screen not built |
| M7 | Invoice download per payment | Billing | Parents often need receipts for expenses |
| M8 | Consent document re-sign flow | Family Account → Account | Consents expire or change; no refresh flow |
| M9 | Add / remove second guardian | Family Account → Account | Family circumstances change |
| M10 | Role-based actions (Primary vs Guardian) | Family Account | Secondary guardians may need limited permissions |
| M11 | Shop basket + checkout | Shop | Currently all CTAs lead nowhere |
| M12 | Camp booking flow | Shop | Requires date picker, student assignment, consent |
| M13 | "Register Interest" capture | Shop | Form + confirmation for waitlisted items |
| M14 | Biometric auth (Face ID / Touch ID) | Lock Screen | Standard expectation for any family app |
| M15 | Calendar view for upcoming classes | Attendance → Upcoming | List is functional; calendar is more intuitive |

### 🟢 Polish — Makes it feel finished and professional

| # | Gap | Screen | Notes |
|---|-----|--------|-------|
| P1 | Passcode lockout visual (countdown / locked state) | Lock Screen | Matches spec's Phase 2 flag |
| P2 | Inactive student visual distinction | Home, Family Account | Paused students should look clearly different |
| P3 | Stripe detail on Lock Screen student tiles | Lock Screen | Currently only shown post-login |
| P4 | Student photo / avatar support | Student Profile, cards | Nice-to-have visual personalisation |
| P5 | Attendance export (PDF/CSV) | Attendance → History | Parents may want records for school absences |
| P6 | Order history in Shop | Shop | Post-purchase receipts and re-order |
| P7 | Transfer history log | Family Account → Class Transfer | Audit trail for past class moves |
| P8 | In-app messaging (parent ↔ instructor) | Global (new screen) | Listed as backlog in WORKFLOW.md |
| P9 | Onboarding / first-run flow | Global | No tutorial or welcome experience |
| P10 | Help / FAQ section | Settings | In-app support to reduce admin load |
| P11 | GDPR data request + deletion | Settings / Account | Legal requirement before launch in UK |
| P12 | App version, T&Cs, Privacy Policy | Settings | Legal baseline |
| P13 | Product detail / expanded view | Shop | Tapping a product just highlights; no drill-down |
| P14 | Dark mode support | Global | Single theme only; increasingly expected |
| P15 | Accessibility (WCAG 2.1 AA) | Global | No colour contrast ratios, alt text, or focus states defined |

---

## 6. Screens Summary: What Exists vs What's Needed

| Screen | Built | Gaps (Critical) | Gaps (Total) |
|--------|-------|----------------|-------------|
| Lock Screen | ✅ Core UI | Auth backend, lockout, biometrics | 5 |
| Home | ✅ Core UI | Student Profile drill-down, empty states | 8 |
| Attendance | ✅ Core UI | Backend wiring | 8 |
| Family Account | ✅ Core UI | Edit forms, transfer states, guardian management | 12 |
| Billing | ✅ Core UI | Stripe live, failed payment flow, invoice download | 8 |
| Shop | ✅ Core UI | Basket, checkout, product detail | 7 |
| **Student Profile** | ✅ Built (v1.5) | Photo support | 1 |
| **Settings** | ❌ Not built | Entire screen to be created | — |
| **In-App Messaging** | ❌ Not built | Backlog item | — |

---

## 7. Integration Dependencies

These items cannot be resolved within the parent app alone — they require coordination with other sections of the master project.

| Dependency | Parent Touch Point | Linked To |
|------------|-------------------|-----------|
| Instructor grades student | Notification in panel, challenge banner trigger | `_SHARED/integration/` instructor grading flow |
| Student profile data (belt, stripes, aura) | Student cards, Student Profile screen | `_SHARED/` Student data spec |
| Class capacity data | Make-up class list, Transfer tab | Instructor / admin section |
| Notification schema | Notifications panel feed | `_SHARED/integration/` notification events |
| Consent form delivery + signature | Add student, Account tab | Admin / legal section |
| Stripe token management | Billing screen | Backend / payment service |
| Push notification delivery | All event triggers | Device + backend push service |
| Transfer approval workflow | Class Transfer tab | Instructor section |

---

## 8. Recommended Build Order

Based on priority and dependency chain, the suggested sequence for moving to integration-ready:

**Phase A — Foundation (before any integration)**
1. Real auth + session management (C1, C2)
2. Empty states across all screens (C4)
3. Error + loading states across all screens (C5, C6)
4. Passcode lockout (C10)

**Phase B — Core completeness**
5. Student Profile screen (C3)
6. Edit guardian details + change passcode (H1, H2)
7. Edit student details + re-activate (H3, H4)
8. Attendance: date picker for make-up + duplicate guard (C7, C8)
9. Transfer pending/approved/rejected states (H5)
10. Settings screen (H13)

**Phase C — Live integration**
11. Backend auth wiring
12. Notification real-time delivery + preferences (H6, H7, H8)
13. Attendance instructor event triggers (C9)
14. Stripe live billing (H11, H9, H10)

**Phase D — Polish + launch-ready**
15. Student Profile rich content (belt timeline, aura, next milestone — M3, M4, M5)
16. Shop basket + checkout (M11, M12, M13)
17. Biometrics, GDPR, legal baseline (M14, P11, P12)
18. Accessibility audit (P15)
19. In-app messaging (P8)

---

## 9. Build Log

| Date | Version | Gaps Closed | Notes |
|------|---------|-------------|-------|
| 2026-03-31 | v1.4 | C4, C5, C6, C10 | Phase A foundation: empty states, error states, loading/skeleton states, passcode lockout. Tester approved with notes. Minor fixes applied (font stack, error banner pre-render, HTML close tag, transfer form inline error). C7 (attendance duplicate guard) and C8 (make-up date picker) confirmed as Phase B items. |
| 2026-03-31 | v1.5 | C3, C7, C8 | Phase B (partial): Student Profile screen (all 8 components), make-up date picker, attendance duplicate guard with undo. Tester: clean APPROVE, no issues. |

---

*This document is the working source of truth for the Parent App UI architecture. Update as screens are built and gaps are closed.*
