# PRD — Chatbot KB Scalability: Mass Upload + Source Connectors

> **Prototype scope**: minimal functional logic, testable locally by internal/power users.
> Generated via GIST/ICE framework + Confidence Meter (Itamar Gilad).

---

## 1. Idea — Description

- **Idea title**: Knowledge Base Scalability — Mass Upload & Connectors

- **Context — Why now?**
  - The customer-facing chatbot is live, powered by a RAG system manually fed by admins.
  - To be useful, a KB needs 500+ files — unreachable with the current UI (single file upload, 100% manual).
  - The Q1 goal is 50% activation of target accounts; the main blocker is KB onboarding friction.
  - Admin sources are heterogeneous: flat files, company websites, potentially Google Drive / Notion.

- **Problem statement**
  > Platform admins face the following problem: populating the chatbot knowledge base is a prohibitive manual effort (file-by-file upload) that makes scaling to 500+ documents impossible.
  > This problem manifests during initial onboarding and every KB update, blocking activation and downstream adoption.

---

## 2. Goal — Structured Objective

> **Goal**
> Increase chatbot activation rate (AI feature switch ON) from current X% to **50% of target accounts by end of Q1**, for the admin segment, by reducing initial KB population effort from 100% manual to a semi-automated process.

- **Key metrics**
  | Metric | Current | Target Q1 |
  |--------|---------|-----------|
  | Chatbot activation (AI switch ON) | — | 50% target accounts |
  | Admins using an AI feature ≥1x/week | — | 50% of admins |
  | End-users using chatbot ≥1x/week | — | 30% of users |
  | Support ticket reduction | baseline | -20% |

- **Expected impact**
  - Direction: ↑ activation, ↑ usage, ↓ onboarding friction
  - Magnitude: **high** — without a populated KB, the chatbot can't answer, blocking all downstream adoption.

---

## 3. ICE Score + Confidence Meter

- **Impact (I)**: **8/10**
  > Empty KB = useless chatbot = zero adoption. Unblocking large-scale population has a direct and immediate impact on the activation metric.

- **Ease (E)**: **5/10**
  > Mass upload (files): moderate effort (multi-select UI + batch ingestion backend).
  > Web crawl: medium effort (parsing, recursion, error handling).
  > Drive/Notion connectors: high effort (OAuth, sync, permissions).
  > → Weighted average prioritizing mass upload first.

- **Confidence (C)**:

  #### Evidence collected

  - [x] Self-conviction / internal opinion (+0.1)
  - [x] Team member opinions / stakeholders (+0.1)
  - [x] Anecdotal customer feedback — "manual updates" identified as blocker (+0.4)
  - [x] Feasibility estimates / plans (+0.4)
  - [ ] Quantitative survey / market research
  - [ ] Analytics / logs / funnel data
  - [ ] User validation interviews
  - [ ] User study with prototype
  - [ ] MVP / live feature + behavioral metrics

  **Confidence Score (C) = 1.0** → Level **Low**

  > ⚠️ No formal discovery done. Priority is based on logical deduction (empty KB = useless chatbot), not direct user validation. Admin interviews strongly recommended before delivery.

- **ICE score**
  > ICE = (I × C × E) / 10 = (8 × 1.0 × 5) / 10 = **4.0**
  - Priority: **medium-high** by product logic, but **low confidence** — fast discovery needed before delivery.

---

## 4. Hypotheses to Test (Discovery)

- **H1 — Volume is the real blocker**
  - Hypothesis:
    > "We believe the inability to upload in bulk is the main reason admins don't activate or abandon chatbot configuration."
  - Test: Interview 5 activated + 5 non-activated admins. Ask: "What blocked you during setup?"
  - Validation: ≥ 60% spontaneously mention volume or upload effort.
  - Criticality: **Must validate**

- **H2 — Sources are mainly flat files + website**
  - Hypothesis:
    > "We believe most admins have their KB content as PDFs/Word/PPT and/or on their company website, not in collaboration tools (Drive, Notion)."
  - Test: Direct question in interviews: "Where are the documents you'd want in the chatbot today?"
  - Validation: ≥ 70% cite flat files or website as primary source.
  - Criticality: **Must validate** — determines mass upload vs connector priority.

---

## 5. Prototype Scope — Functional & Interface

> Scoped to two priority vectors: **Mass Upload** (P0) and **Web Crawler** (P1).
> Drive/Notion connectors = **out of scope** for prototype.

### 5.1 Critical User Flows

- **UF1 — Mass File Upload**
  - Trigger: Admin is on the "Knowledge Base" page
  - Steps:
    1. Admin clicks "Bulk import files" (new button)
    2. A modal / drop zone opens with multi-file drag-and-drop
    3. Admin selects or drops N files (PDF, DOCX, PPTX, TXT)
    4. A preview table lists files with name, size, status (Pending)
    5. Admin clicks "Start import"
    6. A progress bar shows file-by-file progress
    7. Files move to status: Processing → Indexed / Error
    8. A final summary shows: X files indexed, Y errors (with details)
  - Expected outcome: N files added to KB, available for RAG

- **UF2 — Import from URL (web crawler)**
  - Trigger: Admin is on the "Knowledge Base" page
  - Steps:
    1. Admin clicks "Import from website"
    2. Enters root URL (e.g. `https://example.com`)
    3. Configures crawl depth (1 = page only, 2 = page + direct links, 3 = recursive — **[Assumption]**: default 2)
    4. Preview of detected pages (paginated list with title + URL)
    5. Admin can uncheck pages to exclude
    6. Clicks "Start import"
    7. Async progress: pages are crawled, cleaned, indexed
    8. Final summary: X pages indexed, Y errors
  - Expected outcome: Structured web content added to KB

### 5.2 Expected Functional Logic

- If a file exceeds max size → status "Error: file too large" + splitting suggestion. **[Assumption]**: limit TBD with backend (e.g. 50 MB/file).
- If format is not supported → status "Error: unsupported format" with list of accepted formats.
- If crawler detects an authenticated site → explicit error "Protected site, import not possible".
- During processing, admin can leave the page — processing continues in background, notification on completion.
- If admin re-uploads an existing file → replacement prompt (no silent duplicates).
- Existing KB is not overwritten by bulk import — new files are added.

---

## 6. Success Criteria

- **Qualitative**
  - Testers understand the feature without detailed explanation.
  - Main flow completed without major blockers.
  - Overall feedback mostly positive on ease of use.

- **Quantitative**
  - ≥ 4/5 internal testers complete UF1 (mass upload) without blockers.
  - ≥ 3/5 internal testers complete UF2 (web crawler) without help.
  - Average time to import 50 files ≤ 3 minutes (excluding backend processing).

- **Post-test decision**
  - If criteria met → move to delivery, prioritize mass upload P0, web crawler P1.
  - If UF2 blocks → descope crawler from V1, ship mass upload only.
  - If criteria not met → iterate on identified friction points.
