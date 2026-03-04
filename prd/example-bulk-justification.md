# PRD — Bulk Justification for Attendance Records

> Prototype scope: minimal functional logic, testable locally by internal/power users.

---

## 1. Idea — Description

- **Idea title**
  Bulk justification of attendance records by common reason

- **Context — Why now?**
  - Admin teams spend up to **2h/day** processing ~2,000 absences, one by one
  - Two frequent and equally common patterns generate this volume:
    1. **Multi-attendee / common reason**: 30 attendees absent on the same day (closure, field trip, event) → as many records to justify with the same reason
    2. **Single attendee / multi-session**: 1 attendee absent all week → 5 records to justify with the same medical certificate
  - Teams generally have the **justification file in hand** at validation time (attendees send their attachment beforehand)
  - The current flow forces unit processing: one justification per record, one at a time
  - Priority entry point: `Attendees > Absences` (global list with multi-row selection)
  - Segment: high-volume administrative teams (large organizations, training centers)

- **Problem statement**
  > The admin team faces massive friction during absence validation: each unjustified attendance record must be processed individually, even when multiple records share the same reason. This problem manifests daily during backlog processing, generating up to 2h of repetitive work for ~2,000 rows.

---

## 2. Goal — Structured Objective

> Reduce absence processing time by 60% for high-volume admin teams, by allowing them to justify N records in a single action.

- **Key metric**
  - Name: Average daily absence backlog processing time (admin)
  - Current value: ~2h/day for ~2,000 absences
  - Target: < 45 min/day (≥ 60% reduction)

- **Expected impact**
  - Direction: ↓ processing time
  - Magnitude: **high** — the feature eliminates the main repetitive task

---

## 3. ICE Score + Confidence Meter

- **Impact (I)**: **8** — Directly eliminates the most time-consuming task for high-volume admin teams

- **Ease (E)**: **6** — Infrastructure exists (bulk absence API, mass accept, bulk drawer). New batch endpoint server-side + bulk justification modal client-side. No data model changes.

- **Confidence (C)** — Evidence collected:

  - [x] Self-conviction / internal opinion (+0.1)
  - [x] Anecdotal customer feedback: direct signal from a user ("up to 2k absences / 2h per day") (+0.4)
  - [x] Feasibility estimates: bulk absence mechanism already functional, batch endpoint estimated at < 2d back + 1d front (+0.4)

  **Confidence Score (C) = 0.9**

  | Score | Level | Recommended action |
  |-------|-------|--------------------|
  | 0.9 | **Low–Medium** | Light discovery (1–2 admin interviews) then internal prototype |

- **ICE score**
  > ICE = (I × C × E) / 10 = (8 × 0.9 × 6) / 10 = **4.3**
  - Priority: **medium-high** — test with prototype before moving to delivery

---

## 4. Hypotheses to Test (Discovery)

- **Hypothesis H1 — Both patterns (multi-attendee and single-attendee/multi-session) are common enough to justify a single UI**
  - Hypothesis:
    > "We believe a multi-row selection with a common justification form covers both frequent cases: N attendees / same reason AND 1 attendee / N sessions."
  - Test: Show prototype to both segments (collective case + individual case) during admin interviews
  - Validation: Same UF1 flow is deemed relevant by both profiles without UI adaptation
  - Criticality: **Must validate**

- **Hypothesis H2 — Multi-row selection + modal is intuitive**
  - Hypothesis:
    > "We believe the checkbox + action drawer + justification modal pattern is familiar enough (already present for mass accept) to be used without training."
  - Test: Internal test on prototype with 2 people outside product team
  - Validation: Task completed without help by ≥ 2/2 testers
  - Criticality: Nice to have (pattern already used in the product)

- **Hypothesis H3 — A single justification file for N absences is acceptable**
  - Hypothesis:
    > "We believe admin teams want to upload the received file (certificate, letter) once and link it to all concerned absences, without re-uploading for each record."
  - Test: Confirm during H1 interviews (case already validated by user signal — file systematically available)
  - Validation: Pattern confirmed (attendees send their file before validation → **already validated by source user**)
  - Criticality: Must implement (prototype must allow single shared upload)

---

## 5. Prototype Scope — Functional & Interface

> Scoped: critical flows + business logic + mock data. No pixel-perfect UI.

### 5.1 Critical User Flows

- **UF1 — Bulk justification from absence list**
  - Trigger: Admin is on `Attendees > Absences`, filtered view on unjustified absences
  - Steps:
    1. User checks N unjustified absences (belonging to different attendees or the same)
    2. Bottom action drawer shows new button **"Justify selection (N)"**
    3. User clicks → modal opens: absence type selector, optional comment, optional file upload (shared)
    4. User validates → system creates one justified absence per concerned attendee (grouping same-attendee rows by min/max date range)
    5. Justified rows disappear from "unjustified" filtered view; success toast shows "N absences justified"
  - Expected outcome: N rows move from unjustified to justified state in a single validation

- **UF2 — Bulk accept with common justification from requests**
  - Trigger: Admin is on `Attendees > Justification Requests`, "Pending" view
  - Steps:
    1. User checks N pending requests
    2. Drawer shows **"Accept with common reason (N)"** alongside existing "Accept" button
    3. User clicks → modal: absence type (overrides each request's type), optional comment, optional shared file
    4. User validates → system converts each request to justified absence with common parameters
    5. N requests disappear from list; success toast
  - Expected outcome: N requests accepted with same reason in a single action

### 5.2 Expected Functional Logic

- **Multi-row selection**:
  - Only unjustified absences are selectable for UF1
  - Only pending requests (STATUS=0) are selectable for UF2
  - "Justify selection" button only appears in drawer when ≥ 1 unjustified absence is checked

- **Grouping by attendee (UF1)**:
  - If multiple rows of same attendee are selected, system creates **one** justified absence covering range [min(START), max(END)]
  - If rows from multiple attendees are selected, system creates **one justified absence per attendee**

- **Shared file management (single upload, required)**:
  - Modal shows a file upload field (PDF/PNG/JPG, max 4MB) — **displayed by default and required**
  - File is uploaded **once** before creating absences
  - Same file URL is referenced in each created absence record
  - Form validation blocks if no file is provided

- **Overlap handling**:
  - For each attendee, system checks overlaps before creation
  - If overlap detected for 1 attendee: that row is skipped, others are created
  - Final toast distinguishes successes and skipped rows: "8/10 absences justified — 2 skipped (overlap)"

- **Error state**:
  - If 0 absences created (all overlap or locked) → error toast "No absences could be justified"
  - If type not selected → blocking validation on modal

### 5.3 Mock Data

- **Data types**: Unjustified absences from multiple attendees in the same week
- **Key attributes**: ATTENDEE_ID, ATTENDEE_NAME, SESSION_START, SESSION_END, ORG_ID
- **Concrete examples**:
  ```
  Attendee A — Monday 10am session, Tuesday 2pm, Wednesday 9am
  Attendee B — Monday 10am session, Tuesday 2pm
  Attendee C — Thursday 8am (locked)
  → Select 6 rows → 2 absences created (A and B), 1 skipped (C locked)
  → Toast: "5/6 absences justified — 1 skipped (locked session)"
  ```

---

## 6. Success Criteria

- **Qualitative**
  - Testers understand without explanation that multi-row selection + "Justify selection" applies a common reason
  - UF1 flow (unjustified absences → bulk justification) completed without blockers
  - Exclusion cases (overlap, locked) understood via feedback messages

- **Quantitative**
  - ≥ 3/4 internal testers complete UF1 without help
  - Processing time for 10 grouped absences < 60s (vs ~5-10min currently)

- **Post-test decision**
  - If criteria met → move to delivery with: fine-grained overlap handling, grouped-by-attendee modal UI, full i18n
  - If criteria not met → rework entry point (maybe from attendee profile rather than global list)

---

## 7. Notes for Implementation

- **Fidelity level**
  - Minimal functional prototype, testable locally
  - Prioritize: UF1 (unjustified absences), per-attendee grouping logic, toast feedback
  - UF2 (requests) as secondary if time allows

- **Technical entry points**

  | Layer | File / Method | Role in prototype |
  |-------|---------------|-------------------|
  | Backend core | `core/absences.ts` → `addMultipleAbsences()` | Reuse as-is for each attendee |
  | Backend route | `routes/absences.routes.ts` | Add `POST /absences/batch-justify` |
  | Backend class | `classes/absences.ts` | Add `batchJustifyAbsences()` |
  | Frontend service | `services/absences.service.ts` | Add `batchJustifyAbsences()` |
  | Frontend component | `absence-list.component.ts` | Add action to bulk drawer |
  | Frontend modal | New `batch-justify-absence` modal or reuse existing in multi mode | Common reason input |

- **Constraints**
  - Do not modify existing API endpoints (External API frozen)
  - Respect permission rules: action visible only if `user.PERMISSIONS > 0`
  - Respect overlap rules: check overlaps per attendee before creation
  - Shared FILE_URL is valid (multiple absences can share the same URL)

- **Specific instructions**
  - Mark ambiguous or missing information as **Assumption**
  - Start with backend (`batchJustifyAbsences`) then frontend (drawer → modal → service)
  - Individual email notifications per attendee are **out of scope** for prototype
  - Sufficient mock data for 2-3 attendees with 5-6 absences total
