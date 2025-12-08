# Driver Portal Design

**Last Updated:** December 8, 2025
**Status:** Design & Planning Phase
**Target Phase:** Phase 4 (Q2-Q3 2026)

---

## Purpose

This document captures the collaborative design for the Durdle Driver Portal - the system that will enable drivers to receive jobs, update status, manage their compliance documents, and integrate with dispatch operations.

---

## Current State (Phase 1-2)

**How drivers currently work:**
- Job assignment via phone/WhatsApp from dispatch
- Navigation via Google Maps (personal devices)
- Status updates manually communicated to dispatch and passengers
- Document submission handled offline/via email

**Backend infrastructure already built:**
- `uploads-presigned` Lambda - S3 document upload capability
- `document-comments` Lambda - Comments on documents/quotes
- `vehicle-manager` Lambda - Fleet management
- `feedback-manager` Lambda - Customer ratings/feedback

---

## Design Questions for Client

### 1. Driver Access Model

**Question:** How should drivers log in?

Options:
- [ ] **Option A: Email/Password** - Traditional login, driver creates account
- [ ] **Option B: Phone/SMS Code** - Mobile-first, SMS verification
- [ ] **Option C: Magic Link** - Email sent with login link (no password)
- [ ] **Option D: Dispatcher Creates Account** - Centralized control, dispatcher issues credentials

**Recommendation:** Option D initially (dispatcher control), with Option B for future self-service.

---

### 2. Device Requirements

**Question:** What devices will drivers use?

- [ ] Personal smartphones (iOS/Android)
- [ ] Company-provided tablets
- [ ] Mix of both

**Question:** Do we need offline support?
- Jobs can be synced before going to areas with poor signal
- Status updates queued when offline

---

### 3. Job Lifecycle

**Question:** What job statuses do drivers need to report?

Proposed Status Flow:
```
ASSIGNED → ACCEPTED → EN_ROUTE → ARRIVED → PASSENGER_ONBOARD → COMPLETED
                ↓
             DECLINED (with reason)
```

**Question:** Can drivers decline jobs? What happens if they do?

- [ ] Jobs return to unassigned pool
- [ ] Dispatcher notified immediately
- [ ] Decline count tracked (affects performance score)

---

### 4. Driver Information Display

**Question:** What information does the driver see for each job?

Essential:
- [ ] Pickup location + address
- [ ] Pickup time
- [ ] Passenger name
- [ ] Passenger phone number
- [ ] Drop-off location
- [ ] Special instructions
- [ ] Number of passengers
- [ ] Luggage requirements

Optional:
- [ ] Passenger rating (if repeat customer)
- [ ] Flight/train details (for airport/station pickups)
- [ ] Waypoints (multi-stop journeys)
- [ ] Price paid (or hide from driver?)

---

### 5. Navigation Integration

**Question:** How should navigation work?

Options:
- [ ] **Option A: Open in Google Maps** - Driver taps address, opens Maps app
- [ ] **Option B: Open in Waze** - Same, but Waze
- [ ] **Option C: Built-in Maps** - Embedded navigation (more expensive to build)
- [ ] **Option D: Driver Choice** - Let driver pick preferred app

**Recommendation:** Option D - provide navigation buttons for Google Maps and Waze.

---

### 6. Passenger Communication

**Question:** How do drivers contact passengers?

Options:
- [ ] **Option A: Direct Phone** - Tap to call passenger directly
- [ ] **Option B: In-App Messaging** - Messages routed through platform (privacy)
- [ ] **Option C: WhatsApp Direct** - Open WhatsApp with passenger number
- [ ] **Option D: All Options** - Give driver choice

**Privacy Consideration:** Option B masks phone numbers but requires more development.

---

### 7. Document Management

**Question:** Which documents should drivers manage through the portal?

Required Documents:
- [ ] DBS Certificate
- [ ] Private Hire Vehicle (PHV) License
- [ ] Vehicle Insurance
- [ ] MOT Certificate
- [ ] Tax Documentation
- [ ] Profile Photo
- [ ] Vehicle Photos

**Question:** Should drivers upload documents themselves or go through dispatch?

---

### 8. Compliance Alerts

**Question:** How should expiring documents be handled?

Options:
- [ ] 30-day warning → 14-day warning → 7-day warning → Blocked
- [ ] Single 30-day warning → Blocked on expiry
- [ ] Admin sets custom warning periods per document type

**Question:** Should expired documents automatically block job assignment?

---

### 9. Performance Metrics (Visible to Driver)

**Question:** Should drivers see their own performance stats?

Proposed Metrics:
- [ ] Average customer rating
- [ ] Number of completed trips (this week/month/all-time)
- [ ] Acceptance rate (% of jobs accepted)
- [ ] Cancellation rate
- [ ] On-time performance

**Question:** Should drivers see how they rank vs other drivers?

---

### 10. Earnings & Payments

**Question:** How are drivers paid?

Options:
- [ ] **Option A: Percentage of fare** - Driver gets X% of booking value
- [ ] **Option B: Fixed rate per job** - Set amount regardless of fare
- [ ] **Option C: Hourly rate** - Track hours worked
- [ ] **Option D: Driver is self-employed** - Passenger pays driver directly, company takes commission

**Question:** Should earnings tracking be in the portal?

---

## Compliance Automation Roadmap

**Full Analysis:** See [DRIVER_COMPLIANCE_ANALYSIS.md](../../.documentation/COO/DRIVER_COMPLIANCE_ANALYSIS.md)

### Why This Matters

**Legal Risk:** Dispatching an unlicensed/uninsured driver is a criminal offense. Fine up to £5,000, operator license revoked, vehicle seized.

**The Challenge:** 12 document types per driver/vehicle with staggered expiry dates. Manual tracking doesn't scale.

---

### Phase 1: Manual Tracking (5-10 drivers)
**Status:** Ready to build (existing `uploads-presigned` infrastructure)
**Cost:** Development only, no API fees

**Features:**
- Admin uploads driver documents
- DynamoDB tracks expiry dates
- Daily cron sends email alerts (30 days before expiry)
- Admin dashboard with color-coded compliance status (green/amber/red)
- Manual verification workflow

---

### Phase 2: Hybrid Automation (10+ drivers)
**Status:** Planned (requires API integrations)
**Cost:** £10-15K development + £50-100/month API fees

**Automated Verifications:**
| API | Purpose | Cost |
|-----|---------|------|
| **Onfido** | Driving license verification + identity | £2-5 per driver (one-time) |
| **DVSA MOT API** | Vehicle MOT status | Free (rate-limited) |
| **AskMID** | Insurance verification | £1-3 per check (annual) |

**Key Feature: Dispatch Blocker**
- Before assigning driver to booking, system checks compliance status
- If ANY document expired: Block assignment + alert admin
- UI shows: "Cannot assign - Driver license expired on [date]"

**Driver Self-Service:**
- Driver uploads documents via portal
- Onfido auto-verifies driving license
- System auto-checks MOT status daily
- SMS alerts to driver 7 days before expiry

---

### Phase 3: Full Compliance Platform (50+ drivers)
**Status:** Future consideration
**Cost:** £5-15 per driver per month

**Options:**
1. **Passenger** (used by Uber, Bolt, Addison Lee)
2. **iCabbi Compliance**

**What You Get:**
- Full automation (DVLA, DVSA, AskMID, Home Office APIs)
- DBS application + tracking
- Mobile app for drivers
- Audit-ready reporting for licensing authorities
- Zero admin burden

**Verdict:** Not needed for MVP with 5-10 drivers. Evaluate when approaching 50+ drivers.

---

### Document Types to Track

**Per Driver (8 documents):**
1. PHV Driver License (1-3 years)
2. UK Driving License (10 years)
3. DBS Certificate (3 years) - **Cannot store, view once only**
4. Right to Work (varies)
5. Proof of Address (3 months)
6. National Insurance Number (lifetime)
7. Professional Qualifications (varies)
8. Medical Certificate (5 years)

**Per Vehicle (4 documents):**
1. PHV Vehicle License (annual)
2. MOT Certificate (annual)
3. Vehicle Insurance (annual)
4. V5C Logbook (lifetime)

---

### GDPR Considerations

| Data Type | Can Store? | Retention |
|-----------|------------|-----------|
| License numbers | Yes (encrypted) | 7 years after employment |
| Document images | Yes (S3 encrypted) | 2 years after employment |
| DBS certificate | **NO** - record "DBS passed on [date]" only | Employment duration |
| Passport/visa | View & record "verified on [date]" only | Employment + 2 years |
| Medical certificate | Yes (encrypted, admin-only) | Employment + 2 years |

---

## Technical Design (CTO Section)

### Architecture Options

**Option 1: Mobile Web App (PWA)**
- Pros: Single codebase, works on all devices, no app store approval
- Cons: Limited offline capability, no push notifications on iOS
- Build time: 4-6 weeks

**Option 2: Native Mobile Apps (React Native)**
- Pros: Full device features, push notifications, better offline
- Cons: Two stores to manage, longer build time
- Build time: 8-12 weeks

**Option 3: Hybrid - Web Portal + WhatsApp Integration**
- Pros: Drivers use familiar WhatsApp, simple to start
- Cons: Limited features, manual processes remain
- Build time: 2-3 weeks

**Recommendation:** Start with Option 1 (PWA) for Phase 4 launch, evaluate native apps based on driver feedback.

### Backend Requirements

New Lambda functions needed:
- `driver-auth` - Driver authentication (separate from admin-auth)
- `driver-jobs` - Job listing, acceptance, status updates
- `driver-profile` - Driver personal info, documents, earnings

New DynamoDB tables:
- `durdle-drivers-dev` - Driver profiles
- `durdle-job-assignments-dev` - Job-to-driver mapping
- `durdle-driver-documents-dev` - Document metadata and expiry tracking

### Real-Time Features

For live status updates:
- Option A: Polling (simple, every 30s)
- Option B: WebSockets (instant updates, more complex)
- Option C: AWS AppSync (managed WebSockets)

---

## Open Questions for Discussion

1. What's the driver onboarding process? In-person training? Video? Self-service?
2. How many drivers are expected at launch? 5? 10? 50?
3. Should drivers see each other's availability?
4. What happens to a job if a driver's car breaks down mid-journey?
5. Do drivers need to take photos (pickup location, vehicle condition)?
6. Is there a driver support hotline?

---

## Next Steps

1. [ ] Client to answer design questions above
2. [ ] Finalize MVP feature set for Phase 4
3. [ ] Create wireframes/mockups
4. [ ] Technical specification
5. [ ] Development sprint planning

---

## Appendix: Driver Journey Map

```
DRIVER ONBOARDING
─────────────────
1. Dispatcher creates driver account
2. Driver receives credentials (email/SMS)
3. Driver logs in, completes profile
4. Driver uploads required documents
5. Admin reviews/approves documents
6. Driver marked as "Available"

DAILY WORKFLOW
──────────────
1. Driver logs in, sets availability
2. Driver sees assigned jobs
3. Driver accepts/declines each job
4. For accepted jobs:
   a. Driver navigates to pickup
   b. Driver marks "Arrived"
   c. Driver contacts passenger if needed
   d. Driver marks "Passenger Onboard"
   e. Driver navigates to destination
   f. Driver marks "Completed"
5. Customer rates driver (optional)
6. Driver sees updated stats

END OF DAY
──────────
1. Driver sets availability to "Off"
2. Driver reviews earnings summary
3. Driver checks any messages from dispatch
```

---

**Document Status:** Draft - Awaiting Client Input
**Last Updated By:** CTO
