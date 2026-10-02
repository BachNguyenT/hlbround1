# Assumption & Ambiguity Closure
**Product:** HLB Point-of-Purchase Credit Decisioning — **HLB CreditPulse** | **Skill:** 22 | **Status:** Done (2026-09-30). All decisions resolved; zero-assumption certified
**Depends on:** every file in `outputs/businessChallenge2026/` | **Feeds into:** product-prd.md (Skill 23 is blocked until this file certifies zero unresolved items)

---

## Step 1: Sweep (what was swept)

- **Files swept:** 27.
  - `_context.md`, `_requirements.md`
  - Skills 01–21, including the literature review and the research notes
  - The three story part files. They are superseded by the merged `17-user-stories.md`, which is canonical.
- **Items extracted:**
  - 129 open-item rows in Skills 01–18 (excluding stories)
  - 62 story-level open items (OQ-17A/B/C)
  - 8 in Skill 19 and 4 in Skill 20
  - 25 business decisions in Skill 20 (BD-20-xx)
  - 28 assumptions in the Skill 21 Assumption Log
  - About 28 named policy parameters without values
  - Cross-file contradictions found during the sweep (§2)

## Step 2: Triage summary

| Class | Count (approx.) | Handling |
|---|---|---|
| **Researchable, now resolved** | 11 | Resolved by research or correction and propagated (see Closure Log, AC-R rows) |
| **Accepted risk** (unknowable before launch: real consent rates, predictive lift in Vietnam, uplift, conversion, drift) | ~35 | Already in `21-risk-register.md` with validation methods and gates. **Not raised to the user**, per the Skill 22 rule |
| **Decision** (only the user can answer) | Grouped into **14 decisions** below | ⏸ Awaiting the user |

---

## 1. Assumption Closure Log

### 1a. Resolved by research or correction (propagated)

| ID | Item | Found In | Type | Resolution | Resolved By |
|---|---|---|---|---|---|
| AC-R01 | Dated CIC coverage (51% c.2018; ~60% 2022) | 01, 02, 03, lit | Researchable | CIC holds 55.2M records, covering >70% of adults (reported Jan 2026) | Research (Skill 07 [R70]) |
| AC-R02 | AI Law deadline stated as 1 Sep 2027 in 10+ places | 02, 08–11, 16, 17 | Researchable / contradiction | The grace period covers only systems operating before 15 Aug 2026. A new system must comply **before deployment** (≤ 1 Mar 2027). Corrected in all files | Research (Skill 14 T17; Skill 18 N6) |
| AC-R03 | `source:CIC` consent scope claimed as legally required | 19 | Researchable (wrong claim) | Circular 15/2023 Art. 12(6) consent applies to third-party users, not HLB's own enquiry. Removed | Research (Skill 19 P7) |
| AC-R04 | Circular 04/2025 retention periods and a "Circular 37/2026" amendment cited without a source | 19 | Researchable (unverified) | The circular's applicability to credit institutions is verified. The specific periods could not be read from a primary source, so they are converted to a legal-check item (see D-05) | Research (Skill 19 P5) |
| AC-R05 | VNBA collection code Decision 15/QĐ-HHNH "not verified" | 02 | Researchable | Verified: issued and effective 31 Mar 2026 | Research (Skill 20 E2) |
| AC-R06 | Consumer Protection Law Art. 38(3), whether it gives a cooling-off right | 20 | Researchable | Verified conditional: 30-day termination applies only if the trader gave inaccurate or incomplete information | Research (Skill 20 E3) |
| AC-R07 | Bank SMS with links | 17 (US-126), 13 | Researchable | Circular 50/2024 Art. 17(3) forbids links unless the customer requests them. The design is changed to no-link SMS | Research (Skill 20 E1) |
| AC-R08 | Tier-policy bug: A/B + CONSENTED-PARTNER fell through to DECLINE | 13, 17 | Contradiction | Fixed in Skill 13 §1.1; stories patched | Correction |
| AC-R09 | Thin-file applicants all declined for lack of income input | 13, 17 | Contradiction | F2_thin from a declared-income field. The design is fixed; **the default needs confirmation (D-06)** | Correction + user |
| AC-R10 | Retention flow missing its holdout (C4) | 13, 17 | Contradiction | Holdout added (Flow 5, US-240 AC 1b) | Correction |
| AC-R11 | Web checkout can't read the chip-CCCD over NFC | 13, 15, 16 | Researchable | Web-to-app hand-off added (U-01) | Research (Skill 14 T24) |

### 1b. Decisions resolved by the user (2026-09-30: "accept all defaults")

| ID | Item | Found In | Type | Resolution | Resolved By |
|---|---|---|---|---|---|
| AC-D01 | Parked pain points PL-03 to PL-09 | 08, 11, 16, 17 | Decision | PR-06 promoted; PR-05 promoted (narrowed to a weekly payout-aligned option for GIG/MSME); others stay parked. Stories US-231 to US-236 added | User |
| AC-D02 | Primary persona | 05, 08, 15 | Decision | GIG (Anh Hùng) for new-to-bank; SAL-N account-holder for existing customers | User |
| AC-D03 | MSME loan purpose | 03, 05, 08 | Decision | Purchase finance at partner merchants; Sổ Bán Hàng as income signal | User |
| AC-D04 | HLB-internal numbers and ~28 policy parameters | 01–21 | Decision | Named parameters set by HLB at launch; no invented values in the PRD | User |
| AC-D05 | ~15 unverified legal items | 14, 18, 19, 20 | Accepted risk | Logged non-blocking (RK-23); legal review sprint at G0; PRD marks "legal to confirm" | User (explicit acceptance) |
| AC-D06 | Design-fix defaults (F2_thin declared income, stacking outcome, CONSENTED-PARTNER, corridor) | 13, 17 | Decision | Accepted | User |
| AC-D07 | Skill 20 business decisions BD-20-01 to 24 | 20 | Decision | All recommended defaults accepted | User |
| AC-D08 | ML challenger (E-06) in v1 | 11, 16 | Decision | Kept; goes live per segment after corridor labels | User |
| AC-D09 | Hosting and offshore access | 14, 18 | Decision | In-country, portable; de-identified extracts to HLB Group | User |
| AC-D10 | Operational design defaults (A-18-*, A-20-*) | 18, 20 | Decision | Accepted as design defaults | User |
| AC-D11 | KR 1.2 target scope | 10, 18 | Decision / contradiction | End-to-end p50 ≤ 3 min; system budget per NFR-PERF-02 | User |
| AC-D12 | Pre-approval display without MK consent | 13 | Decision | Kept; legal to confirm (RK-23) | User |
| AC-D13 | Engine name | 12 | Decision | "HLB CreditPulse" | User |
| AC-D14 | Competition context (optional) | _context | Decision | Skipped by the user. PRD emphasis set by user instruction: the competition rubric, AI track, with research-backed strategic direction | User |

---

## 2. Cross-File Consistency Check

| # | Contradiction | Reconciliation |
|---|---|---|
| X-01 | AI Law deadline (1 Sep 2027 vs "before deployment") | Resolved (AC-R02) |
| X-02 | Primary persona: Skill 05 recommends GIG; Skills 08 and 15 use two lanes (GIG for new-to-bank, SAL-N account-holder for existing customers) | → **D-02** |
| X-03 | KR 1.2 definition: Skill 10 targets *submit → decision (incl. eKYC)*; Skill 18 NFR-PERF-01 targets *end-to-end checkout* at the same p50 ≤ 3 min | → **D-11** |
| X-04 | Tier-policy and income gaps between Skill 13 and Skill 17 | Resolved (AC-R08/R09) |
| X-05 | CIC coverage figures differ (51% / 60% / >70%) | Resolved (AC-R01). The latest figure is used in the PRD |
| X-06 | Product name: some headers say "(working name)", others "HLB CreditPulse" | → **D-13** (cosmetic) |
| X-07 | Skill 11 RICE dissents vs Skill 08 Tier 1 (E-06, RT-01, PR-02) | Mostly resolved by Skill 14: RT-01 and PR-02 now launch rules-first. E-06 → **D-08** |

---

## 3. Decisions Raised to the User (all resolved; see §1b)

| # | Decision | Recommended default | Why it matters |
|---|---|---|---|
| D-01 | Which **parked pain-point features** (PL-03 to PL-09) move into v1? | **Promote PR-06** (collection-conduct standards) and **PR-05** (a flexible weekly repayment option for GIG and MSME only). Keep AP-08, AP-09, PR-07, RT-04 and MX-02 parked. | PR-06 is Kano Basic and ranks 7th on RICE. PR-05 has the strongest evidence (an RCT showed lower default) |
| D-02 | **Primary persona** for the PRD narrative | Two lanes: **Anh Hùng (gig worker)** for new-to-bank; **existing HLB account-holder without credit history (SAL-N)** for existing customers | Decides whose story leads the PRD and the submission |
| D-03 | **MSME loan purpose** | Purchase finance at partner merchants only. Sổ Bán Hàng sales data is used as an income signal, not as a separate business loan | Changes the product, legal treatment and data |
| D-04 | **HLB-internal numbers** (volumes, approval and loss rates, pricing, cost of funds, and ~28 policy parameters such as starter limit, DSR cap, thresholds, holdout %, N, K, corridor size, reward type) | The PRD keeps them as **named, configurable parameters set by HLB at launch**, with no invented values. Illustrative values for the competition one-pager can be prepared later, labelled as assumptions | The zero-assumption rule: without this, the PRD would need numbers nobody has |
| D-05 | **~15 legal items** that couldn't be verified from primary sources: Circular 04/2025 appendix retention periods; Decree 356 article-level duties; Circular 39/29 disclosure fields; Decision 2345 supersession; AI Law conformity pathway; Art. 38(3) applicability to credit; pre-approval display vs marketing consent | Accept them as **logged, non-blocking risks** (RK-23), closed by a legal review sprint at gate G0. The PRD marks them "legal to confirm" | These need a lawyer, not more web research |
| D-06 | **Design-fix defaults**: (a) Lane B asks for **one self-declared monthly income** figure, with a haircut and cap (F2_thin); (b) a failed stacking check → REFER if grade A/B, else DECLINE; (c) CONSENTED-PARTNER treated like BANK-OBSERVED; (d) a randomised approval corridor to learn thin-file risk | Accept all four | (a) is what makes the thin-file "aha" moment possible without documents |
| D-07 | **Skill 20 business decisions** (BD-20-01 to 24) | Accept all recommended defaults. Key ones: full return cancels the loan with no interest or fees; instant-approval validity lasts until the session ends, officer approvals 24 h; CIC fallback = REFER; no approve-and-guarantee by default (hold ≤ 30 min then cancel); fee waiver when an HLB outage causes a missed due date; referral reward paid as a fee waiver or voucher, never cash | Behaviour under failure (PRD Section 4) needs these |
| D-08 | Keep the **ML challenger (E-06)** in v1, even though RICE would defer it? | **Keep.** It ships in v1 but only goes live per segment once randomised-corridor outcome labels exist; the scorecard champion runs from day one | Your "comprehensive AI" direction (SD-3) vs Skill 11 RICE |
| D-09 | **Hosting / offshore access** (unknown HLB facts) | Assume in-country hosting on portable components; HLB Group (Malaysia) receives only de-identified extracts | Architecture and PDPL transfer obligations |
| D-10 | **Operational design defaults**: 99.9% decision-path availability, CIC 15 s timeout, retry and timer values (A-18-*, A-20-*), refer queue in HLB business hours | Accept as design defaults, to be revised after pilot | NFR and error sections need concrete values |
| D-11 | Which latency the **"≤ 3 min" target** applies to | **End-to-end checkout** (tap "Pay over time" → order confirmed), matching how competitors advertise it (Techcombank "approval and disbursement within 3 minutes"). The system decision gets its own budget (p99 ≤ 20 s) | Removes the Skill 10 vs Skill 18 contradiction |
| D-12 | Pre-approved limit shown on a **self-initiated app visit** without marketing consent (DD-11) | Keep as designed, marked "legal to confirm" (covered by D-05) | Awareness reach |
| D-13 | **Engine name** | Keep "HLB CreditPulse" | Cosmetic; appears across the PRD |
| D-14 | **Competition context** (optional): deadline, team size, any angle to emphasise (the brief p.2 hints at sustainability/inclusion) | Not required for the PRD; skip unless you want the PRD tailored | Only affects emphasis |

---

## 4. Zero-Assumption Certification

```
Files swept: 27
Decisions raised to user: 14 (grouped)
Decisions resolved: 14 (user, 2026-09-30: accept all defaults)
Items resolved by research instead of assumption: 11
Items explicitly accepted as open risk (not blocking): ≈15 legal items (RK-23, explicitly accepted by the user) + ~35 unknowable-before-launch items with validation methods in risk-register.md
Remaining unresolved items: 0  ✅ → Skill 23 (PRD) unblocked
```

## Source Index
This skill adds no external sources. Resolutions cite the propagated sources in each affected file. User decisions are cited as "User decision, Skill 22, 2026-09-30" in: 05, 08, 10, 11, 13, 15, 16, 17, 18, 19, 20, 21 and `_context.md`.
