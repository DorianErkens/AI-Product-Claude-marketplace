# PRD — OCR for Paper Attendance Sheets

**Date**: 2026-02-26
**Author**: Dorian Erkens
**Mode**: pre-build (structured)

---

## 1. Idea — Description

Allow platform admins to scan or photograph paper attendance sheets and automatically import attendance data via OCR (Optical Character Recognition).

**Context — Why now?**
- Some organizations still use paper attendance sheets alongside digital (off-site sessions, rooms without connectivity, backup when QR/NFC fails, legacy habits) **(Assumption)**
- Manual data entry from paper sheets is a recurring pain: the admin must read each signature, identify the attendee, and manually check attendance in the corresponding session **(Assumption)**
- OCR/vision technologies are mature and accessible (Google Vision, Tesseract, Claude Vision, Mistral Vision) — cost and integration complexity have dropped significantly

**Problem statement**
> Platform admins who manage paper attendance sheets spend significant time manually entering attendance data. This problem occurs after every session that used a paper sheet, generating data entry errors and delays in updating attendance records.

---

## 2. Goal — Structured Objective

> Reduce paper attendance data entry time by 80% **(Assumption)**, for admins who still manage paper-based attendance, by automating reading and import via OCR.

- **Key metric**: Average time to enter a paper attendance sheet
  - Current value: estimated 5-15 min per sheet depending on number of attendees **(Assumption)**
  - Target: < 2 min (scan + quick validation)
- **Expected impact**: ↓ admin time, ↓ data entry errors

---

## 3. Strategic Alignment

**Goal 🎯**: Reduce Support Volume **(Assumption)**
<!-- Also candidate for "AI Feature Validation" if we want to test OCR/vision as a reusable AI building block -->

**OKR target**: ⏳ Benefits — Reduce admin time on attendance data entry tasks **(Assumption)**

**Platforms**: Web (Admin)
**Feature area**: Attendance Management
**Team**: Innovation **(Assumption)** — OCR/AI component

---

## 4. ICE Score + Confidence Meter

### Impact (I): 2/10 — XS

Incremental improvement for a subset of admins still managing paper. The majority of users are already digital (QR, NFC, manual signature). Impact is real but limited to the "paper-first" or "hybrid" segment.

### Confidence (C)

- [x] Opinion: self-conviction (+0.1) — Intuition says this is a real pain for some admins
- [x] Opinion: market trends (+0.1) — OCR/vision AI is a mature trend, other SaaS platforms have implemented it
- [ ] Assessment: anecdotal customer feedback (+0.4) — **No data. Need to validate: how many orgs still use paper?**
- [ ] Assessment: feasibility estimates (+0.4)
- [ ] Data: market research / surveys (+1.0)
- [ ] Data: customer preference data (+1.0)
- [ ] Test Results: validation interviews (+2.5)
- [ ] Test Results: user studies with prototype (+2.5–3.0)
- [ ] Test Results: MVP launch + behavioral metrics (+3.0+)
- **Total C**: 0.2

### Effort (E): 5

- OCR engine integration (Google Vision or Mistral Vision)
- Pipeline: upload image → preprocessing → OCR → structured extraction (names + signatures)
- Matching extracted names ↔ existing attendees (fuzzy matching)
- UI: upload/scan, OCR results preview, manual correction, validation
- Association to existing session (date + group)
- Error handling (illegible names, ambiguous signatures)
- ~2-3 weeks for a functional prototype, 1 person

### ICE = 2 × 0.2 / 5 = **0.08**

**Analysis**: Very low ICE. Impact is incremental (user subsegment), Confidence is minimal (no real customer feedback), and Effort is moderate. **Before prototyping, must validate demand**: how many orgs still use paper? Is the pain strong enough to justify the investment?

---

## 5. Steps — Validation Experiments (GIST)

> Sequential steps: only proceed to next if previous validates. First 3 are **Must validate** before any prototyping.

- **Step 1 — Validate paper usage** (analytics)
  - Hypothesis: > 20% of active organizations still use paper attendance sheets for at least some sessions **(Assumption)**
  - Experiment: Analytics query (orgs with no digital attendance) + mini-survey from CS/Sales
  - Validation criteria: > 15% of active orgs have regular paper usage
  - Criticality: **Must validate** — if nobody uses paper, no market
  - Estimated effort: 1 day

- **Step 2 — Validate data entry pain** (interviews)
  - Hypothesis: Admins managing paper spend > 5 min per sheet and consider it a major pain
  - Experiment: 3-5 interviews with "paper-heavy" admins
  - Validation criteria: At least 3/5 admins spontaneously cite paper data entry as a top-3 pain
  - Criticality: **Must validate**
  - Estimated effort: 3-5 days (recruitment + interviews)

- **Step 3 — Validate OCR feasibility** (technical test)
  - Hypothesis: OCR can correctly extract > 80% of names/signatures on a standard attendance sheet
  - Experiment: Test Google Vision / Mistral Vision on 10 real sheet photos
  - Validation criteria: > 80% names correctly extracted without manual intervention
  - Criticality: **Must validate** — if OCR is too unreliable, the feature doesn't save time
  - Estimated effort: 2 days

- **Step 4 — Functional prototype** (build)
  - Hypothesis: The scan → preview → correction → validation flow is understandable and fast (< 3 min)
  - Experiment: Internally testable prototype (see section 6)
  - Validation criteria: ≥ 3/5 testers complete the flow without blockers, time < 2 min
  - Criticality: Must validate before Beta
  - Estimated effort: 2-3 weeks

---

## 6. Prototype Scope — Functional & Interface

### 6.1 Critical User Flows

**UF1 — Scan and import a sheet**
1. Admin opens a session page on the platform
2. Admin clicks "Import paper attendance" (new button)
3. Admin uploads a photo/scan of the sheet (drag & drop or mobile camera)
4. System sends image to OCR and extracts data (names, detected signatures)
5. System displays a preview: list of detected names, matched with session attendees
6. Admin corrects errors (unrecognized names, ambiguous signatures)
7. Admin validates → attendance is updated in the session
8. Confirmation: "X attendances imported, Y to review"

**UF2 — Correction and validation**
1. For each extracted name, system proposes a match (high/medium/low confidence)
2. High confidence (> 90%): pre-validated, shown in green
3. Medium confidence (60-90%): suggestion with alternatives, shown in orange
4. Low confidence (< 60%) or unrecognized: manual input, shown in red
5. Admin reviews orange/red items, corrects, and validates in batch

### 6.2 Functional Logic

- If image is too blurry or illegible → error message suggesting rescan
- If extracted name doesn't match any session attendee → propose group attendees with fuzzy search
- If signature detected but name illegible → mark as "present, unidentified"
- If session already has digital attendance → merge (don't overwrite existing records)
- Paper import should be flagged as such (source: "paper_ocr") for traceability

### 6.3 Mock Data (for prototype)

- 3 attendance sheet images (variety: clean, average, difficult)
- 1 session with 20 registered attendees
- Simulated OCR results with different confidence levels
- Types: printed sheet signed, free-format sheet, handwritten table

---

## 7. Success Criteria

- **Qualitative**
  - Testers understand the scan → preview → correction → validation flow without explanation
  - Flow completed in < 3 min for a 20-attendee sheet
  - Testers find it "significantly faster" than manual entry

- **Quantitative**
  - ≥ 80% names correctly auto-matched (high confidence)
  - ≥ 3/5 internal testers complete the flow without blockers
  - Average time < 2 min (vs 5-15 min manual entry)

- **Post-test decision**
  - If H1+H2 validated AND prototype OK → move to Beta with 5-10 "paper-heavy" orgs
  - If H1 or H2 not validated → kill (no market)
  - If H3 not validated (OCR < 80%) → evaluate if assisted manual correction justifies the feature

---

## 8. Technical Architecture — App Marketplace

> OCR Attendance is a **standalone marketplace app** — zero modification to the core platform. It uses the existing App Actions and Block rendering system.

### Technical Flow

```
Admin UI (view_session) → App Action block "Scan attendance"
  → Upload image via form block (proxy /integrations/apps/:id/form)
    → OCR App backend receives image + context {sessionId, orgId}
      → GET /v1/sessions/:sessionId (registered attendees via API key)
      → Mistral Vision API → extract names + signatures
      → Fuzzy match OCR names ↔ session attendees
      → Return Block[] (matching table green/orange/red + correction form)
    → Admin corrects orange/red, validates
      → OCR App → batch update attendance via platform API
```

### Marketplace App Config

- **Action location**: `view_session` (injects block on session page)
- **Platform**: Admin
- **API permissions**: `ACL_SESSIONS` (read), `ACL_ATTENDEES` (read), `ACL_SESSIONS` (write — for attendance update)
- **Parameters**: OCR provider (Mistral Vision / Google Vision), default language
- **Webhooks**: standard install/uninstall

### Stack

| Component | Choice | Justification |
|-----------|--------|---------------|
| Backend | Node.js/Express standalone | Lightweight, same stack as platform |
| Hosting | Cloudflare Worker or Railway | Quick deploy for prototype |
| OCR/Vision | **Mistral Vision API (Pixtral)** | EU-hosted, sovereign AI, cost-effective (~$0.03/scan), end-to-end in 1 API call |
| Matching | Levenshtein fuzzy match | Simple, sufficient for names |
| UI | Block schema | Native marketplace, no custom frontend code |
| Auth | Marketplace API key | Managed by existing install system |

### App Endpoints

| Method | Path | Description |
|--------|------|-------------|
| POST | `/action/view_session` | Receives context {sessionId}, returns "Scan attendance" block |
| POST | `/form/upload` | Receives uploaded image, runs OCR, returns matching blocks |
| POST | `/form/validate` | Receives corrections, calls platform API to update attendance |
| POST | `/webhook/install` | Receives CLIENT_ID + CLIENT_SECRET on install |
| POST | `/webhook/uninstall` | Cleanup on uninstall |

### Out of Scope (Prototype)

- Native mobile app with integrated camera
- Multi-page support (1 image = 1 sheet for prototype)
- Import history
- Automatic session detection (admin selects session manually)

---

# Experiment Register

**Experiment ID**: EXP-2026-02-26-ocr-paper-attendance
**Created**: 2026-02-26
**Owner**: Dorian Erkens
**Status**: 🔵 Prototyping

## Lifecycle

| Stage | Status | Entry date | Exit criteria | Metrics to track | Exit date |
|-------|--------|------------|---------------|------------------|-----------|
| 🟡 Hypothesis | ✅ done | 2026-02-26 | Steps 1-2 validated (paper usage + pain confirmed) | % orgs with paper usage, # interviews | 2026-02-27 |
| 🔵 Prototyping | **CURRENT** | 2026-02-27 | Step 3 (OCR reliable >80%) + Step 4 (functional marketplace prototype) | OCR accuracy (Pixtral), build time, blockers | — |
| 🟠 Marketplace Integration | pending | — | Marketplace app deployed with block rendering + platform design system | App Actions integration, Block schema, install flow | — |
| 🟣 Measuring | pending | — | App installed by 5+ orgs, 2 weeks of data | See metrics below | — |
| 🔴 Decision | pending | — | Kill / Iterate / Scale decided | Confidence delta, OKR alignment | — |

### Metrics per Stage

**Hypothesis → Prototyping** (criteria to start building):
- [ ] Step 1 validated: >15% active orgs have regular paper usage (analytics)
- [ ] Step 2 validated: ≥3/5 interviewed admins cite paper entry as top-3 pain
- [x] Step 3 validated: OCR >80% names correctly extracted — **100% achieved** (28/28 PDF, 9/9 paper)

**Prototyping → Measuring** (criteria to start measuring):
- [ ] Marketplace app deployed and installable
- [ ] Prototype accessible to minimum 5 "paper-heavy" orgs
- [ ] Usage tracking integrated (number of scans, matching rate, time per sheet)

**Measuring → Decision** (criteria to decide):
- [ ] **Adoption**: ≥3/5 equipped orgs use the app at least 1x/week
- [ ] **Time savings**: Average time <2 min per sheet (vs 5-15 min manual)
- [ ] **OCR accuracy**: >80% automatic matching in real conditions
- [ ] **Satisfaction**: Marketplace rating ≥4/5
- [ ] Minimum 5 orgs / 14 days of data
- [ ] Confidence score updated with new evidence

## Tier

- [ ] 🏖️ **Sandbox** — hypothesis stage, no code written, discovery in progress
- [x] 🧪 **Beta** — marketplace prototype in progress, Pixtral end-to-end architecture validated
- [ ] 🚀 **GA** — public marketplace publication, visible to all orgs

## Kill Criteria

> If after Step 1+2 (max 2 weeks), no signal of paper demand, KILL.

- **No market**: <15% orgs use paper → immediate kill (Step 1)
- **No pain**: <3/5 admins consider paper entry a real problem → kill (Step 2)
- **OCR unreliable**: <60% correct matching → kill or pivot to assisted entry without OCR (Step 3)
- **No Beta adoption**: <2/5 orgs use the app after 2 weeks → reevaluate format
- **Inactivity**: No activity on experiment for 30 days → auto-archive

## Confidence Progression

| Date | Event | Evidence type | Delta C | Total C | Notes |
|------|-------|---------------|---------|---------|-------|
| 2026-02-26 | PRD created | Opinion: self-conviction | +0.1 | 0.1 | Intuition that the pain exists |
| 2026-02-26 | OCR trend mature | Opinion: market trends | +0.1 | 0.2 | Google Vision, Mistral Vision — tech available |
| 2026-02-27 | OCR test — PDF export | Assessment: feasibility | +0.4 | 0.6 | 28/28 names, 25/28 signatures (3 absent = correct) = 100% accuracy on 4-page PDF export |
| 2026-02-27 | OCR test — handwritten paper | Test Results: user studies prototype | +0.3 | 0.9 | 9/9 names, 6 present + 3 absent, 100% accuracy on paper photo |
| 2026-02-27 | Fuzzy matching validated | Assessment: feasibility | +0.2 | 1.1 | 9/9 auto-match (>90%), handles typos, name inversions, missing accents |
| 2026-02-28 | Standalone E2E prototype functional | Test Results: user studies prototype | +0.5 | 1.6 | Full flow: upload → OCR → session select → match review → apply absences via API. Bug fix: only set-absent (set-present interferes with signature flow) |
| 2026-02-28 | Architecture decision: Mistral OCR 3 | Assessment: feasibility | +0.1 | 1.7 | `mistral-ocr-latest` via `/v1/ocr` with `document_annotation_format` (JSON schema). Cost ~$2/1000 pages. Time 3-13s/doc |

## Decision Log

| Date | Decision | Rationale | Next action | By |
|------|----------|-----------|-------------|-----|
| 2026-02-26 | Start discovery (Steps 1-2) | ICE=0.08, need validation before any build | Analytics query + identify paper-heavy admins | Dorian Erkens |
| 2026-02-27 | Architecture: Pixtral (Mistral Vision) end-to-end | Goal: cost-effective + EU-sovereign AI. 3 options evaluated: (A) DocTR local ~$0/scan, (B) DocTR + Mistral Small ~$0.003/scan, (C) Pixtral end-to-end ~$0.03/scan. Option C chosen: simplest architecture (1 API call), high sovereignty (Mistral = EU-hosted), acceptable cost for prototype. | Update architecture section + test Pixtral on 3 sheet photos | Dorian Erkens |
| 2026-02-27 | Move to Prototyping | Steps 1-2 skipped — decided to go directly to marketplace prototype to validate technical feasibility (Pixtral + App Marketplace). Market validation (paper usage, admin pain) to be done in parallel or via prototype itself. | Build marketplace OCR app prototype with Pixtral | Dorian Erkens |
| 2026-02-28 | Step 3 PASS — OCR feasibility validated | 100% accuracy on 3 sheet types (PDF export, handwritten paper, paper with absences). Mistral OCR 3 via `/v1/ocr` with JSON schema annotation. Fuzzy matching 9/9 auto. Standalone E2E prototype functional. | Migrate to marketplace app (Block rendering, design system, install flow) | Dorian Erkens |
| 2026-02-28 | Start marketplace integration | Standalone prototype validated (C=1.7). Next: convert to marketplace app with Block rendering, App Actions, native design system. Injection point: `view_attendancelist` or `view_session`. | Restructure app for Block schema + webhook endpoints | Dorian Erkens |

## Link to Roadmap

- **Goal 🎯**: Reduce Support Volume
- **OKR target**: ⏳ Benefits — Reduce admin time on attendance data entry tasks
- **Project management task**: [to fill]
