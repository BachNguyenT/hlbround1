# Error States & Edge Cases
**Product:** HLB Point-of-Purchase Credit Decisioning (working name) — **HLB CreditPulse** | **Skill:** 20 | **Status:** Done (2026-09-30)
**Depends on:** data-requirements.md | **Feeds into:** product-prd.md (Section 4 — Behaviour Under Failure)

> **How to read this file**
> - **Scope.** Every v1 flow in Skill 13 §2 (Flows 1–6, Lanes A and B, including the 2026-09-30 fixes: web-to-app hand-off for chip NFC, parallel CIC with `policy.cic_timeout_s`, stacking-fail outcome, F2_thin declared income, randomised corridor), every screen in the Skill 13 §3 IA, and every design gap in the Skill 17 open items that is an error, edge or degraded-mode case. `[Should]` items (AD-02, AD-03, RT-03) are covered briefly. `[Later]` and `[Parked — pending user]` features (PL-03 to PL-09, e.g. the assisted eKYC fallback PL-04 and flexible repayment PL-06) are **not** designed here. Where v1 needs a minimum behaviour in their place, this file specifies only that minimum.
> - **Zero silent assumptions.** Where the right handling is a **business policy choice**, this file gives a *recommended default* and marks it `[DECISION REQUIRED — Skill 22]`, with a `BD-20-#` number (list in §8). Design defaults that this skill introduces are logged as `A-20-#` (§7).
> - **Microcopy status.** The Vietnamese copy is a **draft for HLB's copywriters and compliance to review**. It is not final wording. It follows the vocabulary that users are evidenced to use (e.g. "sinh trắc học", "căn cước", "quét NFC" [Skill03 S44]). The formal register (whether to address customers as "bạn" or "Quý khách") is an HLB brand-voice choice that research could not settle (OQ-20-01). The draft uses "bạn", the register seen in the in-market eKYC message "Không nhận ra khuôn mặt. Bạn hãy xác thực lại nhé" [Skill03 S44].
> - **Citations.** `[Skill13 §x]`, `[Skill14 B-0x]`, `[NFR-xxx]` / `[NFR §5.2]` (Skill 18), `[US-xxx]` (Skill 17), `[Skill03 Sxx]`, `[E#]` = new sources for this skill (Source Index at the end). Rubric tags are written `[Rubric R#]`.

---

## 0. Summary for engineers

| Deliverable | Count | Where |
|---|---|---|
| Error states (ERR-xxx) | **~120** across 13 flow groups | §1 |
| Empty states (EMP-xx) | **15** | §2 |
| Edge cases (EC-xx) | **38** | §3 |
| Degraded-mode fallbacks (DEP-xx) | **17** dependencies | §4 |
| Network & offline rules (NET-xx) | **14** rules, plus a per-step table | §5 |
| Business decisions raised for Skill 22 (BD-20-xx) | **25** (incl. BD-20-22b) | §8 |
| Upstream corrections (UC-20-xx) | **5** | §6 |

**Five findings that change upstream design** `[Rubric R3]`:
1. **Bank SMS may not contain links.** SBV Circular 50/2024/TT-NHNN Art. 17(3): *"Ngân hàng không gửi tin nhắn SMS, thư điện tử cho khách hàng có nội dung chứa đường dẫn liên kết (Hyperlink) truy cập các trang tin điện tử, trừ trường hợp theo yêu cầu của khách hàng"* ("Banks do not send customers SMS or email containing hyperlinks to websites, except at the customer's request") [E1]. US-126 AC 2 ("open the notification link") and the proposed answer to OQ-17B-24 (an SMS link to the hosted page) both breach this unless the customer explicitly asks for the link. This file designs an **explicit "send me a link" request**, captured in-session, as the recommended default. Whether that satisfies the exception is a legal question (BD-20-06, UC-20-01).
2. **The QR "web fallback" cannot finish Lane B.** US-143 AC 4 sends a shopper without HLB Connect to the hosted page, but browsers cannot read the CCCD chip [Skill14 B-06, T24]. The hosted page can show packages and cost, but chip-NFC eKYC still needs the native app, an HLB SDK or VNeID authentication (SP-3). See BD-20-07 and UC-20-02.
3. **There is no general cooling-off right to cite, but a conditional 30-day termination right exists.** The 2023 Consumer Protection Law Art. 38(3) lets a consumer terminate a *distance contract* within 30 days **only when** the trader failed to give the required information [E3]. This search did not verify whether that article applies to credit contracts, which are governed by banking law (OQ-20-04). The refund and return design (BD-20-01) therefore does not assume that any cancellation right exists.
4. **Collection conduct now has an industry code.** VNBA Decision 15/QĐ-HHNH (issued and effective 31 Mar 2026) forbids threats, harassment, contact at inappropriate frequency or hours, and sharing debt information with unauthorised third parties [E2]. Every repayment message in §1.8 is written to that code. This matters because collection harassment is a documented harm here [Skill03 Pain #8, S11, S12].
5. **Network failure must never count as an eKYC failure.** US-120 counts three failed attempts and then refers the case. A dropped upload is not a failed attempt (NET-06). Otherwise poor connectivity in a shop pushes customers into the refer queue.

### 0.1 Cross-cutting rules (apply to every entry below)

**Copy rules (C-1 to C-9)** `[Rubric R3]`
| # | Rule | Basis |
|---|---|---|
| C-1 | Say what happened, why (in plain words), and what to do next, with one primary action. Never show a bare error code or a dead-end spinner. | NN/g: human-readable, precise, constructive advice [E4]; US-116 AC 2 |
| C-2 | Never blame the customer. Avoid "không hợp lệ" (invalid), "sai" (wrong) and "lỗi của bạn" (your error). Prefer "chưa đọc được" (couldn't read yet) and "chưa khớp" (doesn't match yet). The in-market message "Thông tin giấy tờ không hợp lệ" [Skill03 S44] is the anti-pattern. | NN/g: no blaming wording such as "invalid, illegal, incorrect" [E4] |
| C-3 | Never threaten. No mention of relatives, references, employers or third parties. No penalties beyond the late-payment consequences disclosed in CO-02. | VNBA code [E2]; US-223 AC 2; DD-08 |
| C-4 | Never disclose which **integrity or fraud signal** fired, the F1 letter grade, the PD band, or other lenders' CIC details. | US-122 AC 4; US-247 AC 4 |
| C-5 | Merchants never see reason codes, CCP fields, CIC data, income, or consent details. Merchant-facing states are limited to APPROVED_BOOKED / REFERRED_PENDING / DECLINED / CANCELLED / EXPIRED. | US-140 AC 4; US-144 AC 5 |
| C-6 | **No hyperlinks in SMS** unless the customer requested the link in this interaction. SMS says "Mở ứng dụng HLB Connect…" ("Open HLB Connect…") or tells the customer where to go. | Circular 50/2024 Art. 17(3) [E1] |
| C-7 | A technical failure must not read as a credit decision. System failures on the decision path show the **REFER** wording ("we need a little longer"), never "declined" and never an error screen. | NFR-PERF-14; NFR-REL-08 |
| C-8 | Amounts are in VND with dot separators ("1.250.000 đ"). Dates are dd/mm/yyyy. Time limits are stated as a clock time ("trước 10:00 thứ Hai", "before 10:00 Monday"), not only as a duration. | Plain-language disclosure NFR-A11Y-09 |
| C-9 | Every customer-facing message is reachable by screen reader and is not conveyed by colour alone. | Skill 18 §3 accessibility |

**Logging rules (L-1 to L-5): no PII in operational logs.**
| # | Rule |
|---|---|
| L-1 | **Standard error envelope** on every `*_error` / `*_failed` event: `event`, `error_code` (= ERR-ID), `flow`, `channel` (widget / hosted / QR / app / internal), `lane`, `app_version`, `correlation_id`, pseudonymous `session_id` / `application_id` / `decision_id` / `customer_token`, `merchant_id`, `attempt_n`, `dependency`, `latency_ms`, server `timestamp`. |
| L-2 | **Never in logs:** name, phone, CCCD number, DOB, address, face image or template, NFC chip data, OTP value, declared income, CIC payload, CCP field values, reason codes tied to a person, device contacts. Customer-level decision data lives only in the governed decision log (US-033), not in operational logs. (NFR-SEC-09; PDPL purpose limitation) |
| L-3 | Device data is logged only as an OS / model **class** (e.g. "Android 13, NFC-capable"), as in US-120 AC 4. |
| L-4 | Consent denials log the purpose and the reason (`CONSENT_NOT_GRANTED`, `CONSENT_CHECK_UNAVAILABLE`), not the customer's consent history (US-008). |
| L-5 | Operational log retention follows the Skill 19 register. Decision-log retention stays open (OQ-14-10). |

**Default retry policy (R-1 to R-4)**
| # | Rule |
|---|---|
| R-1 | Reads (GET): automatic retry ×2 with jittered back-off (≈ 0.5 s, 1.5 s), then show the error. |
| R-2 | Writes (application submit, consent change, OTP sign, payment, booking, create session) are retried **only with an idempotency key**. The same key returns the original result; the same key with a different payload returns an error [US-B1][US-B2]. |
| R-3 | Customer-initiated retry is always available unless an attempt cap applies (eKYC ×3 [A-13-8], OTP per HLB policy, eligibility checks ≤ 3 per 24 h [A-18-2]). |
| R-4 | Server-to-server (webhooks, CIC reporting, booking) uses exponential back-off with the same event ID. Schedules are given per entry. |

---

## 1. Error State Catalogue `[Rubric R3]`

Column key: **Type** = User / System / Network / Permission / Validation. **Message** gives the Vietnamese draft first, then *(English)*. "—" means no customer-facing message (internal only). **Log** lists the event name plus fields beyond the L-1 envelope.

### 1.1 Cross-cutting (all customer channels)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-001 | Unexpected server error **outside** the decision path (hub screens, plans, consent screens) | System | "Đã có trục trặc từ phía chúng tôi. Vui lòng thử lại sau ít phút." *(Something went wrong on our side. Please try again in a few minutes.)* | Return a typed error; the page keeps its last-good content read-only | "Thử lại" (Try again) button | R-1, then manual | `ui_error` + `endpoint`, `http_status` | NFR-PERF-14 |
| ERR-002 | Unexpected internal failure **on the decision path** (orchestration exception, policy service 5xx) | System | REFER wording (C-7): "Chúng tôi cần thêm chút thời gian để xem xét hồ sơ. Chúng tôi sẽ báo kết quả cho bạn trước {deadline}." *(We need a little more time to review your application. We'll tell you the result before {deadline}.)* | Tier = REFER, reason `SYSTEM_REVIEW`; case created with the evidence captured so far; merchant gets REFERRED_PENDING | Wait for the outcome, or leave and use another payment method at the merchant | No auto re-decision; the officer decides | `decision_internal_failure` + `failed_component` | NFR-REL-08; US-125 |
| ERR-003 | HLB Connect login session expires mid-flow (the app's session-timeout policy is OQ-18-17) | Permission | "Phiên đăng nhập đã hết hạn để bảo vệ tài khoản của bạn. Đăng nhập lại để tiếp tục — lựa chọn của bạn vẫn được giữ." *(Your login session has ended to protect your account. Log in again to continue — your choices are kept.)* | Server keeps the application and package hold (30 min, A-13-6) against the session ID | Re-login returns to the same step | — | `auth_session_expired` + `step` | US-104 |
| ERR-004 | App version too old to show the "Trả sau" (Pay Later) hub or the deep-link target | Validation | "Hãy cập nhật HLB Connect lên phiên bản mới nhất để dùng tính năng Trả sau." *(Please update HLB Connect to the latest version to use Pay Later.)* | Deep link falls back to the store page; the checkout session stays alive | Update, then reopen the QR or link | — | `app_version_unsupported` + `min_version` | US-143 |
| ERR-005 | Instant-eligibility checks exceed `integrity.eligibility_checks_per_24h` (proposed 3 per identity or device [A-18-2]) | Validation (anti-probing) | "Bạn đã kiểm tra nhiều lần trong hôm nay. Vui lòng thử lại sau {time}." *(You've checked several times today. Please try again after {time}.)* No mention of fraud (C-4) | Block new checks; no CIC pull; F5 velocity signal updated | Try again after the window; the merchant's other payment methods remain | None until the window resets | `eligibility_rate_limited` + `window_end` | NFR-SEC-13; US-121 |
| ERR-006 | Decision-log write not acknowledged | System | Same as ERR-002 (REFER wording) | **Fail closed**: no decision released; the case moves to the REFER queue; the log write retries | As ERR-002 | Log write retried every 30 s for 10 min, then Sev-2 alert (A-20-3) | `decision_log_write_failed` | NFR-REL-08; US-033 |
| ERR-007 | Reason-code generator returns no code | System | Same as ERR-002 | Decision not released; REFER with reason "explanation unavailable"; alert | As ERR-002 | — | `reason_code_missing` | US-113 AC 3; US-024 AC 4 |
| ERR-008 | Feature switched off by a pause switch (e.g. `pause.checkout`, `pause.preapproval`) | System | Widget hidden. In-app: "Tính năng Trả sau đang tạm dừng. Các khoản trả sau hiện có của bạn không bị ảnh hưởng." *(Pay Later is temporarily paused. Your existing plans are not affected.)* | Honour the `pause.*` flag at render and at decision | Other payment methods; existing plans remain payable | — | `feature_paused_view` + `switch` | US-034 |

### 1.2 Flow 1 — Awareness (Lane A, AW-01/02/04)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-101 | Nightly CCP refresh not complete, or failed, at list-build time | System | — (campaign workspace banner: "Danh sách chưa được tạo vì hồ sơ tín dụng chưa cập nhật xong" / *List not built because profiles have not finished refreshing*) | Do not build the list from stale or partial CCPs; do not re-send the previous list; alert | Ops re-runs the list after the refresh completes | Scheduler re-checks every 30 min until the campaign window closes (A-20-4) | `target_list_blocked` + `refresh_status` | US-200 AC 5; NFR-PERF-11 |
| ERR-102 | MK consent withdrawn between list build and delivery | Permission | — (nothing is sent) | Delivery-time re-check suppresses the offer (KR 4.1) | — | — | `offer_suppressed` + `reason=CONSENT_WITHDRAWN` | US-204 |
| ERR-103 | Consent ledger unavailable at send time | System | — | **Fail closed**: no send; the batch is retried within the campaign window; alert | Automatic | Retry every 15 min inside the window; unsent customers roll to the next cycle (A-20-4) | `campaign_send_blocked` + `reason=CONSENT_CHECK_UNAVAILABLE` | US-204 AC 4 |
| ERR-104 | Early-warning flag or anti-stacking result unreadable for a candidate | System | — | Exclude with `SUITABILITY_UNKNOWN` (fail closed) | — | Next nightly run | `suitability_unknown` | US-202 AC 4 |
| ERR-105 | Adverse CCP change before the customer opens the offer (e.g. a new CIC obligation) | System (data change) | "Ưu đãi của bạn đã được cập nhật: hạn mức hiện tại là {L} đ." / no-offer variant: "Hiện chúng tôi chưa có ưu đãi trả sau phù hợp cho bạn. Chúng tôi sẽ thông báo khi có." *(Your offer has been updated: your current limit is {L} VND. / We don't have a suitable pay-later offer for you right now. We'll let you know when we do.)* | Re-validate on open against the latest CCP version; never show the stale limit | Continue with the new limit, or close | — | `offer_revalidated` + `outcome` | US-207; US-018 AC 5 |
| ERR-106 | CCP service unavailable when the offer card is opened | System | "Chúng tôi chưa thể hiển thị ưu đãi lúc này. Vui lòng thử lại sau." *(We can't show your offer right now. Please try again later.)* | Do **not** show a cached limit | Try again | R-1 | `offer_open_failed` | US-207 AC 4 |
| ERR-107 | Pre-approved limit paused because an early-warning flag is active | System | On "My limit": "Hạn mức Trả sau của bạn đang tạm dừng. Các kỳ thanh toán hiện tại vẫn diễn ra như bình thường. Nếu cần hỗ trợ, hãy trao đổi với chúng tôi." *(Your Pay Later limit is on hold. Your current instalments continue as normal. If you need help, talk to us.)* CTA "Trao đổi với chúng tôi" (Talk to us). No reason is given, and no mention of "risk" (C-4) | Limit paused; suppressions propagate within 15 min (NFR-PERF-12) | "Talk to us" creates a servicing case | — | `limit_paused_view` | US-224; US-247 |
| ERR-108 | Customer opts out or complains about marketing | User | "Bạn sẽ không nhận ưu đãi Trả sau qua thông báo nữa. Hạn mức và các khoản hiện có không bị ảnh hưởng." *(You won't receive Pay Later offers by notification any more. Your limit and existing plans are unaffected.)* | Set MK off in the ledger; start the cooling period (value `[DECISION REQUIRED]`, OQ-13-02); count toward G-6 | Can re-enable in Privacy & consent | — | `mk_opt_out` + `source` | US-209 |

### 1.3 Flow 2 — Consideration (packages CO-01, cost CO-02)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-201 | Basket above the limit or above F2 at every tenor | Validation | "Đơn hàng này vượt hạn mức Trả sau hiện có của bạn. Bạn có thể trả trước nhiều hơn hoặc chọn kỳ hạn dài hơn." *(This order is above your current Pay Later limit. You can pay more upfront or choose a longer term.)* | Offer larger down-payments or longer tenors that fit F2; never show a package above F2 (G-4) | Adjust the down-payment or tenor; otherwise pay another way | — | `basket_over_limit` | US-101 |
| ERR-202 | Financed amount cannot be brought to `policy.max_financed_vnd` or below (90,100,000 VND pilot range, OQ-17B-05) | Validation | "Đơn hàng vượt số tiền tối đa HLB có thể hỗ trợ trả sau (90.100.000 đ)." *(This order is above the maximum HLB can finance with Pay Later (90,100,000 VND).)* | No package list | Other payment method, or reduce the basket | — | `basket_above_product_max` | US-101 AC 4 |
| ERR-203 | Basket below `policy.min_financed_vnd` (1,600,000 VND) | Validation | Widget: "Trả sau áp dụng cho đơn hàng từ 1.600.000 đ." *(Pay Later is available for orders from 1,600,000 VND.)* Merchant API: validation error naming the range | No session created | — | — | `session_rejected_range` | US-140 AC 2 |
| ERR-204 | **No** package fits F2 (Lane A: F2 < the cheapest package instalment) | Validation | "Hiện chưa có gói trả góp phù hợp với khả năng chi trả của bạn cho đơn hàng này. Một đơn hàng nhỏ hơn hoặc khoản trả trước lớn hơn có thể phù hợp." *(There's no instalment plan that fits your budget for this order right now. A smaller order or a larger down-payment may fit.)* | Treated as the affordability path (§1.1 rule 2); reason code `AFFORDABILITY` | Change the basket or down-payment | — | `no_affordable_package` | US-100; US-101 |
| ERR-205 | Rate grid or merchant 0% subsidy data unavailable | System | "Ưu đãi khuyến mãi hiện chưa khả dụng. Các gói dưới đây áp dụng lãi suất tiêu chuẩn." *(The promotional offer isn't available right now. The plans below use standard rates.)* | Fall back to the last published standard grid **without** 0% offers | Continue, or come back later | R-1 on subsidy data | `promo_data_unavailable` | US-103; Skill13 Flow 2 |
| ERR-206 | **No published grid version** is readable at all | System | Widget hidden or "Trả sau tạm thời chưa khả dụng" *(Pay Later is temporarily unavailable)*; never blocks other payment methods | Cannot price, so no package is offered | Other payment methods | Probe every 1 min | `rate_grid_unavailable` | US-016; US-141 AC 4 |
| ERR-207 | All-in cost block fails to load | System | "Chúng tôi chưa thể hiển thị đầy đủ chi phí của gói này. Vui lòng thử lại." *(We can't show the full cost of this plan yet. Please try again.)* "Tiếp tục" (Continue) is disabled | No acceptance without disclosure (FM-4) | Try again | R-1 | `cost_disclosure_failed` | US-107 AC 5 |
| ERR-208 | Early-settlement terms not configured for a product | System | — | That package is not built | — | — | `package_suppressed` + `reason=NO_EARLY_SETTLEMENT_TERMS` | US-108 AC 3 |
| ERR-209 | Saved package expired (> 30 min, A-13-6) | System | "Gói bạn chọn đã hết thời gian giữ (30 phút). Chúng tôi đã cập nhật lại các gói phù hợp." *(The plan you chose is no longer held (30 minutes). We've refreshed the plans that fit.)* | Discard the hold and rebuild from current data | Choose again | — | `package_hold_expired` | US-104 AC 3 |
| ERR-210 | Saved package no longer affordable (CCP changed within the hold) | System | "Gói bạn chọn trước đó không còn phù hợp với hạn mức hiện tại. Đây là các gói phù hợp." *(The plan you chose earlier no longer fits your current limit. Here are plans that fit.)* | Rebuild the affordable set (US-101 rules) | Choose again | — | `package_hold_invalidated` | US-104 AC 4 |
| ERR-211 | Lane B: the indicative tenor picked before the decision is not affordable after it | System | "Gói bạn chọn đã được điều chỉnh theo kết quả xét duyệt. Vui lòng chọn một trong các gói dưới đây." *(Your plan has been adjusted to match your approval. Please choose one of the plans below.)* | Show only the approved packages that fit | Choose again | — | `indicative_package_replaced` | US-102 AC 3 |
| ERR-212 | No active SC consent when packages need the CCP | Permission | "Để đưa ra gói trả góp phù hợp, chúng tôi cần bạn đồng ý cho HLB đánh giá hồ sơ. Nếu không đồng ý, bạn vẫn có thể thanh toán bằng cách khác." *(To offer plans that fit you, we need your permission for HLB to assess your application. If you'd rather not, you can still pay another way.)* | Purpose check refuses the read; no CCP field is used | Give SC, or return to the merchant's payment options | — | `consent_required` + `purpose=SC` | US-100 AC 5; US-111 AC 3; US-021 AC 3 |

### 1.4 Flow 3 — Application & Instant Decision: identity (eKYC, web-to-app hand-off)

The web-to-app hand-off (Skill13 Flow 3 Lane B step 2; U-01) applies because browsers cannot read the CCCD chip [Skill14 B-06, T24]. The method is still open: app hand-off, merchant-app SDK or VNeID authentication (SP-3, OQ-14-05). The entries below cover the **app hand-off**, the method this file assumes. If SP-3 chooses VNeID authentication, ERR-311 to ERR-314 are replaced by the VNeID equivalents.

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-301 | Lane A face match fails (biometric confirm against the CCCD on file) | User | "Chưa nhận diện được khuôn mặt. Hãy đứng nơi đủ sáng, bỏ khẩu trang hoặc kính, và giữ điện thoại ngang tầm mắt rồi thử lại." *(We couldn't recognise your face yet. Stand somewhere well lit, remove a mask or glasses, hold the phone at eye level and try again.)* Shows "Lần thử {n}/3" (Attempt {n}/3) | Attempt counter +1 (shared with US-120) | "Thử lại" (Try again) | Customer-initiated, up to 3 (A-13-8); then ERR-318 | `ekyc_attempt` + `step=face_match`, `failure_type`, device class | US-119 AC 4; US-120 |
| ERR-302 | Lane A customer has no chip-CCCD biometric reference on file | Validation | "Để xác nhận an toàn, bạn cần quét căn cước gắn chip một lần. Việc này mất khoảng 1–2 phút." *(To confirm securely, you need to scan your chip ID card once. It takes about 1–2 minutes.)* | Route to the NFC + liveness flow before the decision | Continue with the scan | — | `ekyc_reference_missing` | US-119 AC 3 |
| ERR-310 | Lane B on a web widget or hosted page reaches the eKYC step (browser cannot read the chip) | Validation (channel) | "Bước tiếp theo cần quét căn cước gắn chip bằng ứng dụng HLB Connect trên điện thoại có NFC. Quét mã QR này bằng điện thoại để tiếp tục. Gói bạn chọn được giữ đến {hh:mm}." *(The next step needs your chip ID scanned with the HLB Connect app on an NFC phone. Scan this QR code with your phone to continue. Your plan is held until {hh:mm}.)* On mobile web: a "Mở HLB Connect" (Open HLB Connect) deep link | Hand-off token bound to session ID + package hold; the web page polls the server for status | Scan the QR, or tap the deep link | — | `ekyc_handoff_shown` + `method` | US-141 AC 5; US-118; OQ-17B-21 |
| ERR-311 | Shopper does not have HLB Connect at hand-off | User | "Bạn chưa cài HLB Connect? Tải ứng dụng miễn phí, sau đó quay lại quét mã này. Gói của bạn vẫn được giữ đến {hh:mm}." *(Don't have HLB Connect yet? Download the free app, then come back and scan this code. Your plan is still held until {hh:mm}.)* | Store link, then the deferred deep link carries the session ID | Install and resume within the hold | — | `ekyc_handoff_install` | BD-20-07; US-143 AC 4 |
| ERR-312 | Hand-off link or QR opened after the hold or checkout session expired, or already used | Validation | "Mã này đã hết hạn hoặc đã được sử dụng. Vui lòng quay lại trang thanh toán của cửa hàng để bắt đầu lại." *(This code has expired or has already been used. Please go back to the store's checkout to start again.)* | Token single-use; no new application starts | Restart from the merchant | — | `handoff_token_invalid` + `reason` | US-143 AC 3 |
| ERR-313 | eKYC completed on the phone but the web page lost its status connection | Network | Web: "Đang chờ kết quả từ điện thoại của bạn…" *(Waiting for the result from your phone…)*; after 60 s a "Kiểm tra kết quả" (Check result) button | Server is the source of truth; the page resumes by session ID | Tap "Check result", or continue on the phone | Poll every 3 s; back-off to 10 s after 60 s (A-20-5) | `handoff_status_poll_timeout` | NET-08 |
| ERR-314 | Phone has no NFC, or NFC is switched off | User / Validation | NFC off: "Hãy bật NFC trong Cài đặt, sau đó đặt căn cước sát mặt sau điện thoại." *(Turn on NFC in Settings, then hold your ID card against the back of the phone.)* No NFC: "Điện thoại này không hỗ trợ NFC nên chưa đọc được căn cước gắn chip. Bạn có thể dùng một điện thoại khác có NFC." *(This phone doesn't support NFC, so it can't read the chip ID. You can use another phone that has NFC.)* | NFC off: not counted as an attempt. No NFC: counted as a failed attempt per US-120 (AC 4 of US-118). **Recommend** instead: offer a switch of device via QR without counting it (EC-14) | Enable NFC, or switch phone | — | `ekyc_attempt` + `failure_type=NFC_UNAVAILABLE` | US-118 AC 4 |
| ERR-315 | NFC chip read fails (card moved, phone case, weak contact) | User | "Chưa đọc được chip trên căn cước. Hãy tháo ốp điện thoại, đặt căn cước sát mặt sau máy và giữ yên đến khi có thông báo." *(We couldn't read the chip yet. Remove the phone case, hold the card flat against the back of the phone and keep it still until you see a confirmation.)* Never "không hợp lệ" (C-2) | Attempt +1 | Try again | Up to 3 | `ekyc_attempt` + `failure_type=NFC_READ` | US-118; US-120 AC 1 |
| ERR-316 | Liveness check fails | User | "Chưa xác nhận được bạn đang trực tiếp thực hiện. Hãy nhìn thẳng vào camera và làm theo hướng dẫn trên màn hình." *(We couldn't confirm it's you in person yet. Look straight at the camera and follow the on-screen steps.)* | Attempt +1 | Try again | Up to 3 | `ekyc_attempt` + `failure_type=LIVENESS` | US-118; US-120 |
| ERR-317 | Selfie does not match the chip photo | User | "Khuôn mặt chưa khớp với ảnh trên căn cước. Hãy thử lại nơi đủ sáng, không đeo khẩu trang hoặc kính." *(Your face doesn't match the photo on your ID yet. Try again in good light without a mask or glasses.)* | Attempt +1; the mismatch also feeds F5 | Try again | Up to 3 | `ekyc_attempt` + `failure_type=FACE_MISMATCH` | US-118; US-120 |
| ERR-318 | Third eKYC attempt fails | System | "Chúng tôi sẽ kiểm tra danh tính của bạn thủ công. Việc này không ảnh hưởng đến đánh giá tín dụng của bạn. Kết quả sẽ có trước {deadline}." *(We'll check your identity manually. This doesn't affect your credit assessment. You'll have the result before {deadline}.)* | REFER, reason `IDENTITY_REVIEW`; **no F1 penalty**; no counter-offer (identity refers go straight to review) | Wait for review | No more attempts in this application | `ekyc_exhausted` | US-120 AC 2–3; US-124 AC 4 |
| ERR-319 | National identity verification (C06 / VNeID) or eKYC vendor unavailable | System | "Dịch vụ xác thực căn cước đang tạm gián đoạn. Gói của bạn được giữ đến {hh:mm} — bạn có thể thử lại sau ít phút, hoặc gửi hồ sơ để chúng tôi xem xét." *(The ID verification service is temporarily unavailable. Your plan is held until {hh:mm} — try again in a few minutes, or send your application for review.)* | Not counted as an attempt; offer save-and-resume (30 min) or REFER (identity review) | Retry later, or send for review; the merchant can switch payment method | Health probe; the customer may retry freely | `ekyc_provider_unavailable` + `provider` | NFR §5.2; DEP-02 |
| ERR-320 | Chip read shows an **expired CCCD**, or an applicant below the eligible age | Validation | Expired: "Căn cước của bạn đã hết hạn nên chưa thể dùng để đăng ký. Vui lòng dùng căn cước còn hiệu lực." *(Your ID card has expired, so it can't be used to apply. Please use a valid ID card.)* Age: "Rất tiếc, bạn chưa đủ điều kiện độ tuổi để sử dụng Trả sau." *(Sorry, you don't meet the age requirement for Pay Later.)* | Stop before any CIC pull; no decision record is created (not a DECLINE) | Other payment method | — | `ekyc_ineligible_document` + `reason` | EC-15; BD-20-17 |
| ERR-321 | Lane B chip read matches an **existing HLB customer** | System | "Bạn đã là khách hàng HLB. Chúng tôi sẽ dùng thông tin sẵn có để xét duyệt nhanh hơn." *(You're already an HLB customer. We'll use your existing details to decide faster.)* | Switch to Lane A handling (existing CCP under SC; face match against the CCCD on file) | Continue | — | `lane_switch_b_to_a` | US-112 AC 4; OQ-17B-16 |

### 1.5 Flow 3 — Application form, consent and decision

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-330 | Declared monthly income left blank or zero (Lane B, F2_thin) | Validation | Inline: "Vui lòng nhập thu nhập trung bình hằng tháng (ước tính). Bạn không cần nộp giấy tờ chứng minh." *(Please enter your average monthly income (an estimate). You don't need to provide any documents.)* | **Validate at the form** so that US-009 AC 4b (F2 = 0 → DECLINE) is never reached by accident | Enter a figure | — | `form_validation` + `field=declared_income` (never the value, L-2) | US-111; US-009 AC 4b |
| ERR-331 | Phone number format invalid, or the OTP phone differs from the chip-verified identity's registered number (Lane A) | Validation | "Số điện thoại chưa đúng định dạng (10 số, bắt đầu bằng 0)." *(The phone number isn't in the right format (10 digits starting with 0).)* Lane A: the OTP always goes to the registered number, shown masked "09xx xxx 123" | — | Correct the number | — | `form_validation` + `field=phone` | US-111 |
| ERR-332 | SC consent refused at the Lane B form | Permission | "Chúng tôi cần bạn cho phép đánh giá hồ sơ để xét duyệt Trả sau. Nếu không, bạn vẫn có thể thanh toán bằng cách khác." *(We need your permission to assess your application for Pay Later. Otherwise, you can still pay another way.)* MK and RF refusal has **no effect** on the decision (US-021 AC 4) | No CCP, no CIC pull | "Quay lại cửa hàng" (Back to store) | — | `consent_refused` + `purpose=SC` | US-111 AC 3; US-021 AC 3 |
| ERR-333 | CIC call exceeds `policy.cic_timeout_s` (proposed 15 s hard timeout, A-18-8), or errors | System | **Lane A** (cached pull < 30 days, BUREAU+BANK): no visible change. **Otherwise**: REFER wording "Chúng tôi cần thêm chút thời gian để hoàn tất đánh giá. Kết quả sẽ có trước {deadline}." *(We need a little more time to complete the assessment. You'll have the result before {deadline}.)*, **or** a normal capped approval if `policy.cic_fallback_mode` = capped starter | AP-07: `cic_status=UNAVAILABLE` (never "no file"); F2 `CIC_OBLIGATIONS_UNKNOWN`; stacking is never reported as passed; `cic_fallback=true`; re-check within 24 h of recovery | Wait for review, or continue with the capped approval | The CIC client does **not** retry inside the session (the budget is used up); the circuit breaker applies | `cic_unavailable` + `mode` (cached / refer / capped) | US-130; US-131; US-007 AC 5; BD-20-09 |
| ERR-334 | Decision not complete within `decision.in_session_budget_seconds` (proposed 20 s from submit, excluding eKYC = NFR-PERF-02 p99; A-20-1) | System | REFER wording (as ERR-333) | Route to the refer path; customer and merchant notified; no silent drop | Wait for review | — | `decision_budget_exceeded` + `slow_component` | US-116 AC 4; OQ-17B-08 |
| ERR-335 | While waiting for a decision (> 3 s) | — (progress state) | Step labels: "Đang xác minh danh tính…", "Đang kiểm tra lịch sử tín dụng…", "Đang tính gói phù hợp…" *(Verifying your identity… / Checking your credit history… / Working out plans that fit…)* | No dead-end spinner | — | — | `decision_progress_shown` | US-116 AC 2 |
| ERR-336 | F2 re-validated at checkout is below the selected package instalment (Lane A) | System | "Gói của bạn đã được cập nhật theo hạn mức hiện tại. Vui lòng chọn lại." *(Your plan has been updated to match your current limit. Please choose again.)* | Selected package not approved; affordable set rebuilt | Choose again | — | `package_revalidation_failed` | US-110 AC 3 |
| ERR-337 | CIC stacking check fails at checkout | System | F1 ∈ {A,B}: REFER wording. Otherwise DECLINE (see ERR-340) with reason `EXISTING_OBLIGATIONS` in plain words: "Các khoản vay hiện có của bạn đang ở mức cao so với khả năng chi trả." *(Your existing loans are high compared with what you can comfortably repay.)* | `policy.stacking_fail_outcome` (default REFER if A/B, else DECLINE; confirm in Skill 22, OQ-17B-15) | Review or path message | — | `stacking_fail` + `outcome` | US-110 AC 4; US-015 AC 3 |
| ERR-338 | F5 integrity below `policy.integrity_floor` | System | "Chúng tôi cần kiểm tra thêm một số thông tin trước khi hoàn tất. Kết quả sẽ có trước {deadline}." *(We need to check a few more details before finishing. You'll have the result before {deadline}.)* No signal disclosed (C-4); no counter-offer | REFER `IDENTITY_REVIEW` | Wait | — | `integrity_refer` (flags go to the decision log only) | US-122; US-124 AC 4 |
| ERR-339 | Confirmed identity mismatch (definition open, OQ-17B-22) | System | "Chúng tôi chưa thể xác minh danh tính cho hồ sơ này nên chưa thể cung cấp Trả sau. Nếu bạn cho rằng có nhầm lẫn, vui lòng liên hệ HLB qua tổng đài {hotline}." *(We couldn't verify the identity for this application, so we can't offer Pay Later. If you think this is a mistake, please contact HLB on {hotline}.)* No accusation of fraud | DECLINE; fraud-team event (G-2) | Contact HLB | — | `identity_mismatch_decline` | US-122; US-029 |
| ERR-340 | DECLINE (affordability or policy) | System | "Rất tiếc, lần này chúng tôi chưa thể duyệt Trả sau cho đơn hàng này. Lý do chính: {reason_1}; {reason_2}. Một đơn hàng nhỏ hơn hoặc khoản trả trước lớn hơn có thể được duyệt. Xem chi tiết trong mục 'Vì sao có kết quả này'." *(Sorry, we can't approve Pay Later for this order this time. Main reasons: … A smaller order or a larger down-payment may be approved. See details under 'Why this decision'.)* | Top reason codes (count `reason_codes.display_count`); merchant receives DECLINED only | Other payment method; path message | Re-applying is subject to ERR-005 | `decision_returned` + `tier=DECLINE` | US-115; US-025 |
| ERR-341 | Integrity model unavailable | System | REFER wording (ERR-338) | No automated approval without F5; alert | Wait | — | `integrity_service_unavailable` | US-121 AC 4 |
| ERR-342 | Scoring API down (champion and challenger), or the online feature store is down for a Lane B applicant | System | REFER wording (ERR-002) | See DEP-05 and DEP-04 | Wait | — | `scoring_unavailable` / `feature_store_unavailable` | NFR §5.2 |
| ERR-343 | Customer already has an application in flight or an unsigned approval (second merchant, second device) | Validation | "Bạn đang có một hồ sơ Trả sau chưa hoàn tất tại {merchant}. Hãy hoàn tất hoặc huỷ hồ sơ đó trước khi bắt đầu hồ sơ mới." *(You have an unfinished Pay Later application at {merchant}. Please finish or cancel it before starting a new one.)* CTA "Huỷ hồ sơ trước" (Cancel the earlier one) | One in-flight application per customer; exposure reserved (EC-20) | Finish or cancel the earlier application | — | `concurrent_application_blocked` | EC-20; BD-20-19 |

### 1.6 Flow 3 — Refer band (AP-04 / OP-01) and CASA-first (AP-06)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-350 | REFER with an affordable counter-offer | System | "Chúng tôi cần thêm thời gian để xem xét số tiền bạn yêu cầu. Ngay bây giờ, bạn có thể nhận gói {amount} đ, {n} kỳ, {instalment} đ/kỳ." *(We need more time to review the amount you asked for. Right now, you can take a plan of … VND over n months at … VND/month.)* Buttons: "Nhận gói này" (Take this plan) / "Gửi hồ sơ xem xét" (Send for review) | Counter-offer is within F2 and the limit; the response is recorded | Accept, or send for review | — | `counter_offer_shown` / `_accepted` | US-124 |
| ERR-351 | Merchant does **not** hold goods during review (`merchant.supports_refer_hold` = false) | System (policy) | Shown **before** sending for review: "Cửa hàng này không giữ hàng trong lúc chúng tôi xem xét. Bạn có thể nhận gói hiện có, thanh toán bằng cách khác, hoặc vẫn gửi hồ sơ — nếu được duyệt, bạn có {validity} để hoàn tất mua hàng." *(This store can't hold your item while we review. You can take the plan available now, pay another way, or still send your application — if approved, you'll have {validity} to complete the purchase.)* | Recommended default in BD-20-05 | Choose one of the three | — | `refer_no_hold_notice` + `choice` | US-125 AC 4; OQ-17B-23 |
| ERR-352 | Case created outside business hours | System | "Hồ sơ của bạn sẽ được xem xét khi chúng tôi làm việc trở lại. Kết quả sẽ có trước {10:00 thứ Hai, dd/mm}." *(Your application will be reviewed when we reopen. You'll have the result before {10:00 Monday, dd/mm}.)* An actual clock time, not "2 business hours" (C-8) | SLA clock starts at the next business-hour opening (`ops.business_calendar`, BD-20-13) | Wait, or pay another way | — | `refer_created_off_hours` | US-026 AC 2; OQ-17B-10 |
| ERR-353 | Refer SLA breached (no officer decision by the deadline) | System | SMS or in-app: "Hồ sơ Trả sau của bạn vẫn đang được xem xét. Chúng tôi xin lỗi vì sự chậm trễ và sẽ báo kết quả sớm nhất." *(Your Pay Later application is still being reviewed. We're sorry for the delay and will tell you the result as soon as possible.)* Merchant webhook: still REFERRED_PENDING with a new ETA | Case marked `SLA_BREACHED`, top of queue, KR 1.4 miss | Wait, or cancel from the status screen | — | `refer_sla_breached` | US-026 AC 3; US-127 |
| ERR-354 | Officer decision cannot be delivered (SMS undelivered; the customer has no HLB Connect) | Network / System | SMS contains **no link** (C-6): "HLB: Hồ sơ Trả sau {mã 6 ký tự} đã có kết quả. Vui lòng mở HLB Connect hoặc quay lại cửa hàng {merchant} để xem." *(HLB: Pay Later application {6-character code} has a result. Please open HLB Connect or return to {merchant} to view it.)* A link is sent **only** if the customer ticked "Gửi đường link cho tôi qua SMS" (Send me a link by SMS) in-session (BD-20-06) | Retry SMS via the secondary gateway; merchant webhook carries the outcome | Customer checks at the merchant or in the app, or the merchant creates a completion session (BD-20-06) | SMS retried ×3 over 10 min, then the secondary gateway (A-20-6) | `refer_notify_failed` + `channel` | US-126 AC 1; OQ-17B-24; E1 |
| ERR-355 | Officer approval opened after `refer.approval_validity` elapsed | Validation | "Kết quả duyệt này đã hết hiệu lực vào {hh:mm dd/mm}. Bạn có thể bắt đầu hồ sơ mới tại cửa hàng." *(This approval expired at {hh:mm dd/mm}. You can start a new application at the store.)* | Approval void; the merchant gets EXPIRED | New application | — | `approval_expired_opened` | US-126 AC 3; BD-20-04 |
| ERR-356 | Officer approves, but the merchant checkout session has already expired | System | "Hồ sơ của bạn đã được duyệt. Hãy quay lại {merchant} và đưa mã {code} cho nhân viên thu ngân (hoặc nhập tại bước thanh toán) để hoàn tất." *(Your application has been approved. Go back to {merchant} and give code {code} to the cashier (or enter it at checkout) to complete.)* | A new merchant session is linked to the decision ID via a completion code (recommended in BD-20-06) | Complete at the merchant within validity | — | `approval_completion_new_session` | EC-09 |
| ERR-357 | CASA opening fails at approval (core-banking account-opening error) | System | "Chưa mở được tài khoản HLB lúc này. Khoản Trả sau của bạn vẫn được duyệt; bạn có thể mở tài khoản sau trong HLB Connect." *(We couldn't open your HLB account right now. Your Pay Later plan is still approved; you can open the account later in HLB Connect.)* | Loan not blocked; the repayment method falls back per BD-20-08 | Open the account later | Account opening retried once in-session; then deferred | `casa_open_failed` | US-129 AC 3; OQ-17B-25 |
| ERR-358 | Customer declines the CASA offer | User | "Bạn có thể thanh toán các kỳ bằng chuyển khoản hoặc VietQR đến số tài khoản khoản vay {mã}. Lịch thanh toán được gửi trong tin nhắn và hợp đồng." *(You can pay each instalment by bank transfer or VietQR to loan account {code}. Your schedule is in the message and the contract.)* (recommended default, BD-20-08) | Record the repayment method | — | — | `casa_declined` | US-129 AC 3 |

### 1.7 Flow 3 — e-contract, OTP and booking

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-401 | OTP entered does not match | User | "Mã OTP chưa khớp. Vui lòng kiểm tra lại tin nhắn mới nhất và nhập lại." *(The OTP doesn't match. Please check the latest message and enter it again.)* | Attempt count per HLB's existing OTP policy; loan not booked | Re-enter | Per HLB policy (values not known, OQ-20-06) | `otp_failed` + `reason=MISMATCH` (never the OTP value) | US-114 AC 4 |
| ERR-402 | OTP expired | Validation | "Mã OTP đã hết hạn. Nhấn 'Gửi mã mới' để nhận mã khác." *(The OTP has expired. Tap 'Send a new code' to get another.)* | Old code invalidated | Request a new code | Resend cool-down per HLB policy | `otp_failed` + `reason=EXPIRED` | US-114 AC 4 |
| ERR-403 | OTP attempts exhausted | Permission (security) | "Để bảo vệ bạn, việc ký hợp đồng tạm khoá đến {hh:mm}. Kết quả duyệt vẫn còn hiệu lực đến {validity}." *(To protect you, signing is locked until {hh:mm}. Your approval is still valid until {validity}.)* | Lock per HLB policy; approval validity unchanged; F5 velocity signal | Wait, then sign | — | `otp_locked` | US-114 |
| ERR-404 | OTP SMS not delivered within 30 s (A-18-6 p90) | Network / System | After 30 s: "Chưa nhận được mã? Bạn có thể gửi lại mã hoặc xác nhận bằng Smart OTP trong HLB Connect." *(Didn't get the code? You can resend it or confirm with Smart OTP in HLB Connect.)* | Fail over to the secondary SMS gateway, or in-app Smart OTP / biometric signing (existing HLB channels [Skill14 §3]) | Resend, or switch method | Resend available after 30 s | `otp_delivery_delayed` + `gateway` | NFR §5.2; DEP-08 |
| ERR-405 | Final affordability gate fails at booking (instalment > F2 at that moment) | System | "Hạn mức của bạn vừa thay đổi nên gói này không còn phù hợp. Vui lòng chọn một gói khác." *(Your limit has just changed, so this plan no longer fits. Please choose another plan.)* | Booking blocked (G-4 hard rule); return to the affordable set | Choose again | — | `booking_affordability_block` | US-114 AC 3 |
| ERR-406 | Contract terms differ from the disclosed-cost hash (grid, fee or tenor changed after disclosure) | System | "Điều khoản của gói đã được cập nhật. Vui lòng xem lại chi phí trước khi ký." *(The plan terms have been updated. Please review the cost before signing.)* | Signing blocked until the new disclosure is shown and acknowledged. Recommended: honour the disclosed terms within validity (EC-24, BD-20-18) | Review and sign | — | `terms_hash_mismatch` | US-107; US-114 AC 1 |
| ERR-407 | Duplicate "Ký" (Sign) tap or network retry | Network | The same confirmation is shown | Idempotent on decision ID + package ID; exactly one loan | — | Safe to retry | `booking_duplicate_suppressed` | US-114 AC 5 |
| ERR-408 | Core-banking booking API down or timing out | System | "Hồ sơ đã được duyệt — chúng tôi đang hoàn tất khoản vay. Vui lòng chờ trong giây lát; bạn sẽ nhận tin nhắn khi xong." *(Approved — we're finalising your loan. Please wait a moment; you'll get a message when it's done.)* After 30 min without booking: "Rất tiếc, chúng tôi chưa thể hoàn tất khoản vay do sự cố hệ thống. Bạn không bị tính phí. Vui lòng thanh toán bằng cách khác hoặc thử lại sau." *(Sorry, we couldn't complete your loan because of a system problem. You haven't been charged. Please pay another way or try again later.)* | Approval held; the merchant is **not** confirmed until booked; retry ≤ 30 min, then cancel and notify (NFR §5.2). Alternative: approve-and-guarantee (BD-20-10) | Wait, or pay another way | Booking retry at 10 s, 30 s, 1 min, then every 2 min to 30 min (A-20-7) | `booking_pending` / `booking_cancelled_timeout` | NFR §5.2; Skill14 B-07; OQ-14-11 |
| ERR-409 | Booked, but the merchant confirmation or redirect fails | Network | Customer: "Đơn hàng đã được xác nhận. Mã giao dịch: {decision_id_short}." *(Your order is confirmed. Transaction code: …)* Merchant till: shows "Đang chờ xác nhận từ HLB" (Waiting for HLB confirmation) until the webhook or poll succeeds | Merchant confirms from server status, not the redirect; webhook retried (ERR-811) | Cashier checks status with the code | Webhook schedule (ERR-811) | `merchant_confirm_pending` | US-142 AC 5; US-144 AC 4 |
| ERR-410 | Customer abandons at the e-contract | User | On return within validity: "Bạn còn một hợp đồng chưa ký. Hết hạn lúc {hh:mm}." *(You have an unsigned contract. It expires at {hh:mm}.)* | Approval kept until validity; the session then goes to EXPIRED | Sign within validity | — | `contract_abandoned` | US-126; BD-20-04 |

### 1.8 Flow 4 — Purchase & Repayment, graduation (PR-01 to PR-04)

Every message in this group is written to the VNBA debt-collection code [E2] and US-223 AC 2: no threats, no third parties, and only the late-payment consequences already disclosed in CO-02. Sending frequency is capped by `ew.max_supportive_messages_per_cycle`. Sending hours are `[DECISION REQUIRED]` (BD-20-24; the VNBA code prohibits "unsuitable hours" without defining them [E2]).

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-501 | Early-warning flag set (supportive outreach) | System | "Nhắc nhẹ: kỳ thanh toán {amount} đ đến hạn ngày {dd/mm}. Nếu bạn đang gặp khó khăn, hãy cho chúng tôi biết — chúng tôi sẵn sàng hỗ trợ." *(A gentle reminder: your … VND instalment is due on … If you're having difficulty, let us know — we're here to help.)* In-app: "Trao đổi với chúng tôi" (Talk to us). SMS: no link (C-6) | No marketing, graduation or recommendation content; suppressions set | "Talk to us" opens a servicing case | Capped per cycle | `ew_outreach_sent` + `template_version` | US-223; US-224 |
| ERR-502 | Instalment not received by the due date | System | "Kỳ thanh toán {amount} đ đến hạn ngày {dd/mm} chưa được ghi nhận. Nếu bạn đã thanh toán, vui lòng bỏ qua tin này. Nếu cần hỗ trợ, hãy trao đổi với chúng tôi." *(We haven't recorded your … VND instalment due on … If you've already paid, please ignore this message. If you need help, talk to us.)* | Standard collections process; F1 downgraded at the next re-score; PL-07 conduct standards are parked, but C-3 applies regardless | Pay now in "My plans", or contact HLB | Per collections policy (not designed, PL-07) | `payment_missed` | US-221; Skill13 Flow 4 |
| ERR-503 | Customer paid, but the ledger has not updated (repayment events on daily batch [Skill14 B-07]) | System | On "My plans": "Đang ghi nhận khoản thanh toán {amount} đ lúc {hh:mm}. Có thể mất đến 1 ngày làm việc." *(Recording your … VND payment made at … This may take up to 1 business day.)* | Record a pending-payment marker from the payment channel; **suppress reminders and missed-payment messages** while it is pending | — | — | `payment_pending_ledger` | EC-33 |
| ERR-504 | "Pay now" fails (network or processing error) | Network / System | If not debited: "Thanh toán chưa hoàn tất và tài khoản của bạn chưa bị trừ tiền. Vui lòng thử lại." *(The payment didn't go through and your account hasn't been charged. Please try again.)* If the status is unknown: "Chúng tôi đang kiểm tra trạng thái thanh toán. Vui lòng không thanh toán lại cho đến khi có kết quả." *(We're checking the payment status. Please don't pay again until you see the result.)* | Idempotent payment key; status resolved from the server | Retry only once the status is known | R-2 | `repayment_failed` + `status_known` | NET-10 |
| ERR-505 | Due date is not payable because of an **HLB-side outage** (app down, payment rail down) | System | "Hệ thống thanh toán đang gián đoạn. Nếu bạn không thể thanh toán hôm nay vì sự cố này, bạn sẽ không bị tính phí trễ hạn." *(Payments are currently disrupted. If you can't pay today because of this problem, you won't be charged a late fee.)* (recommended, BD-20-14) | Log outage windows; auto-waive late fees and suppress negative CIC reporting for affected due dates (recommended) | Pay after recovery | — | `outage_grace_applied` | BD-20-14; DEP-09 |
| ERR-506 | Graduation offer changed between offer and acceptance | System | "Ưu đãi của bạn đã thay đổi. Đây là ưu đãi hiện tại." / "Hiện ưu đãi này không còn khả dụng." *(Your offer has changed. Here is the current offer. / This offer is no longer available.)* | The original offer is not applied | Accept the re-sized offer, or close | — | `graduation_offer_changed` | US-227 AC 4 |
| ERR-507 | Graduation offer expired or declined | User | "Ưu đãi đã hết hạn. Hạn mức và điều khoản hiện tại của bạn không thay đổi." *(The offer has expired. Your current limit and terms are unchanged.)* | No penalty; still eligible for future evaluations | — | — | `graduation_offer_expired` | US-227 AC 3 |
| ERR-508 | Graduation paused (model drift, EW flag, or a guardrail pause switch) while an offer is open | System | "Ưu đãi nâng hạn mức tạm thời không khả dụng. Hạn mức hiện tại của bạn không thay đổi." *(The limit-increase offer is temporarily unavailable. Your current limit is unchanged.)* | Hide or suspend the offer; re-activation per BD-20-15 | — | — | `graduation_offer_suspended` + `cause` | US-228; OQ-17C-06 |
| ERR-509 | Re-score event out of order, or re-score job fails | System | — | Recompute in event-time order; one corrected version; failed refresh leaves the previous version marked `refresh_failed` | — | Next event, or the nightly batch | `rescore_failed` | US-220 AC 4; US-019 AC 4 |
| ERR-510 | CIC reporting submission rejected or CIC unavailable | System | — | Queue and never drop; per-record rejection list; escalate at the regulatory deadline | Ops corrects and resubmits | Per reporting runbook | `cic_report_failed` + `count` | US-230 |
| ERR-511 | Early-settlement quote requested but the quote service is unavailable, or the quote expired before payment | System | "Chưa tính được số tiền tất toán lúc này. Vui lòng thử lại sau." / "Số tiền tất toán đã được cập nhật theo ngày thanh toán." *(We can't calculate your payoff amount right now. Please try again later. / Your payoff amount has been updated for today's date.)* | Quote valid only until end of day (A-20-8); terms as disclosed in CO-02 | Retry, then pay the refreshed quote | R-1 | `settlement_quote_failed` | US-108 |

### 1.9 Flow 5 — Retention (RT-01, RT-02, RT-03)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-601 | Partner catalogue feed missing or stale | System | Category-level card: "Gợi ý cho bạn: {category} tại các cửa hàng đối tác — trả góp từ {instalment} đ/tháng." *(Suggested for you: {category} at partner stores — from … VND/month.)* | Category-level recommendations without specific products; fallback logged | — | Next weekly build | `catalogue_fallback` | US-240 AC 6 |
| ERR-602 | Stacking gate cannot get a CIC snapshot within the maximum age | System | — (no recommendation shown) | **SUPPRESS** `CIC_UNAVAILABLE` (fail closed) | — | Next build | `stacking_gate_suppress` | US-244 AC 3 |
| ERR-603 | Recommended product is no longer sold, or its price changed, at the merchant | System | "Sản phẩm này hiện không còn tại {merchant}. Xem các sản phẩm tương tự." *(This product is no longer available at {merchant}. See similar products.)* | Link to the category; the price is always re-quoted in Consideration | Browse the category | — | `recommendation_stale_item` | US-241 |
| ERR-604 | Ready limit cannot be computed (CCP service down) | System | "Hạn mức tạm thời chưa hiển thị được. Vui lòng thử lại sau." *(Your limit can't be shown right now. Please try again later.)* No cached figure | — | Try again | R-1 | `ready_limit_unavailable` | US-247 AC 5 |

### 1.10 Flow 6 — Advocacy (AD-01 v1; AD-02, AD-03 Should)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-701 | Inviter not eligible yet | Validation | "Tính năng mời bạn bè sẽ mở sau {N} kỳ thanh toán đúng hạn." or "Bật 'Chia sẻ lời mời' trong mục Quyền riêng tư & đồng ý." *(Invites unlock after N on-time payments. / Turn on 'Referral sharing' in Privacy & consent.)* Grade and flags are never shown | Button disabled | Meet the condition, or enable RF | — | `invite_ineligible_view` + `condition` | US-260 AC 2 |
| ERR-702 | Invite link expired, deactivated or malformed | Validation | "Lời mời này đã hết hạn, nhưng bạn vẫn có thể đăng ký Trả sau như bình thường." *(This invite has expired, but you can still apply for Pay Later as usual.)* | Normal Lane B flow; no attribution | Apply normally | — | `invite_link_invalid` | US-261 AC 4 |
| ERR-703 | Invitee is already an HLB customer or PoP borrower | System | "Bạn đã là khách hàng HLB — hãy mở mục Trả sau trong HLB Connect." *(You're already an HLB customer — open Pay Later in HLB Connect.)* | Route to the hub; attribution per OQ-17C-11 (BD-20-16) | — | — | `invitee_existing_customer` | US-261 AC 6 |
| ERR-704 | Referral abuse flagged (self-referral, shared device or identity) | System | Inviter status: "Lời mời này không đủ điều kiện nhận thưởng." *(This invite isn't eligible for a reward.)* No accusation (C-4) | Reward blocked; the F5 flag goes to the decision log | Contact HLB if they disagree | — | `referral_abuse_blocked` | US-264; US-013 AC 3 |
| ERR-705 | Invitee declined, or never applied | System | "Lời mời của bạn chưa được sử dụng." *(Your invite hasn't been used.)* The same text in both cases (privacy) | No decline detail shared | — | — | `invite_status_view` | US-263 AC 2 |
| ERR-706 | Invites paused (referred cohort MOB3 30+ above non-referred, KR 3.5) | System | "Tính năng mời bạn bè đang tạm dừng. Phần thưởng đang chờ của bạn không bị ảnh hưởng." *(Invites are temporarily paused. Your pending rewards are not affected.)* | Pause new links; pending rewards follow the ledger rules | — | — | `invites_paused_view` | US-265 |
| ERR-707 | Reward release blocked because the rule for inviter status (EW flag or RF withdrawn) is unconfirmed | System | "Phần thưởng của bạn đang được xử lý." *(Your reward is being processed.)* | Hold, do not pay (US-262 AC 4) until BD-20-16 is decided | — | Re-evaluated at each ledger run | `reward_held_pending_rule` | US-262 AC 4 |
| ERR-708 | "Suggest a shop" (Should): shop already a partner, or already suggested | Validation | "Cửa hàng này đã là đối tác của HLB (hoặc đã được giới thiệu). Cảm ơn bạn!" *(This shop is already an HLB partner (or has already been suggested). Thank you!)* | No duplicate lead | — | — | `merchant_lead_duplicate` | US-266 |
| ERR-709 | Passport (Should) presented at a merchant but offline or expired | Network / Validation | "Không xác minh được Hộ chiếu tín dụng lúc này. Bạn vẫn có thể thanh toán Trả sau như bình thường." *(We can't verify your credit passport right now. You can still use Pay Later as usual.)* | Checkout falls back to the CCP-driven decision | — | R-1 | `passport_verify_failed` | US-270 |

### 1.11 Merchant checkout (MX-01: API, widget, hosted page, QR)

API errors use a stable machine code plus a human-readable `message` for the merchant developer. Shopper-facing text is shown only where noted.

| ID | Trigger | Type | Message (merchant API code / shopper copy VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-801 | Invalid API key, bad signature or missing client certificate | Permission | API `401 AUTH_FAILED` | Reject; no session data returned | Merchant fixes credentials | None | `mx_auth_failed` + `reason` (no key material) | US-140 AC 5 |
| ERR-802 | Basket out of the financed range | Validation | API `422 BASKET_OUT_OF_RANGE` (min/max in body) | No session | Merchant hides the widget for the basket | — | `mx_validation` | US-140 AC 2 |
| ERR-803 | Same idempotency key sent with a different payload | Validation | API `409 IDEMPOTENCY_CONFLICT` | Original session unchanged | New key | — | `mx_idempotency_conflict` | US-140 AC 3 |
| ERR-804 | HLB widget script or API unreachable | Network | Widget hides itself, or "Trả sau với HLB tạm thời chưa khả dụng" *(Pay over time with HLB is temporarily unavailable)*; never blocks other methods | — | Other payment methods | Script load timeout 3 s (A-20-9) | RUM `widget_load_failed` | US-141 AC 4 |
| ERR-805 | Return URL not on the allow-list | Validation / Security | API `400 RETURN_URL_NOT_ALLOWED` | Reject session or redirect | Merchant registers the URL | — | `mx_redirect_rejected` | US-142 AC 3 |
| ERR-806 | Shopper closes the tab or abandons before a decision | User | — (merchant webhook EXPIRED, or CANCELLED on "Quay lại cửa hàng") | Status set; merchant notified | Shopper restarts | — | `session_abandoned` + `last_step` | US-142 AC 4 |
| ERR-807 | Merchant checkout session expired (`checkout.session_expiry`, BD-20-12) | Validation | Shopper: "Phiên thanh toán đã hết hạn. Vui lòng quay lại cửa hàng để thanh toán lại." *(The checkout session has expired. Please go back to the store to pay again.)* | EXPIRED webhook; the package hold ends with the session (EC-19) | Restart | — | `session_expired` | US-140; OQ-17B-27 |
| ERR-808 | QR expired, or its session already produced a booked loan | Validation | "Mã thanh toán này không còn hiệu lực — vui lòng nhờ thu ngân tạo mã mới." *(This payment code is no longer valid — please ask the cashier for a new one.)* | No new application | New QR | — | `qr_invalid` | US-143 AC 3 |
| ERR-809 | QR scanned by a phone without HLB Connect | Validation (channel) | Hosted page opens (packages + cost), then ERR-310 / ERR-311 at eKYC | See BD-20-07 | Install the app, or pay another way | — | `qr_no_app` | US-143 AC 4; OQ-17B-28 |
| ERR-810 | Cashier changes the basket after the QR was shown | User | Shopper (on the old QR): "Đơn hàng đã thay đổi. Vui lòng quét mã mới trên máy thu ngân." *(The order has changed. Please scan the new code at the till.)* | New session; the old one CANCELLED; any hold released | Scan the new QR | — | `session_replaced` | US-143 AC 5 |
| ERR-811 | Merchant webhook not acknowledged | Network | — | Retry with the same event ID; the status endpoint is always current | Merchant polls "get session status" | Back-off 10 s, 30 s, 2 min, 10 min, 30 min, then hourly to 24 h (A-20-10) | `webhook_delivery_failed` + `attempt` | US-144 AC 4 |
| ERR-812 | Webhook success rate below `mx.webhook_success_alert` | System | — | Alert the merchant-integration team | Merchant fixes the endpoint | — | `webhook_success_below_floor` | US-145 |
| ERR-813 | API rate limit exceeded | Validation | API `429 RATE_LIMITED` + `Retry-After` | Throttle | Merchant backs off | Per `Retry-After` | `mx_rate_limited` | NFR-SCAL |
| ERR-814 | Merchant suspended or not configured for PoP | Permission | API `403 MERCHANT_NOT_ACTIVE`; widget hidden | — | Merchant contacts HLB | — | `mx_inactive` | US-140 |
| ERR-815 | **Merchant settlement / disbursement fails** after booking (OQ-17B-18) | System | Customer: no message; the loan and schedule are unaffected. Merchant: settlement report shows "Đang xử lý" (Processing) | Retry settlement; ops alert; the customer's loan start date is not moved (recommended, BD-20-03) | HLB ops | Per settlement runbook (not designed) | `settlement_failed` + `merchant_id`, `amount_bucket` | OQ-17B-18 |
| ERR-816 | Merchant-initiated refund or cancellation call rejected (amount > outstanding, loan already settled, unknown decision ID) | Validation | API `422 REFUND_INVALID` + reason | No change to the loan | Merchant corrects the request | — | `mx_refund_rejected` | BD-20-01; OQ-17B-30 |

### 1.12 Officer workbench (OP-01) and internal tools

| ID | Trigger | Type | Message (staff-facing) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-901 | Officer submits an override without justification | Validation | "Vui lòng nhập lý do khi thay đổi kết quả đề xuất." *(Please enter a reason when changing the recommended outcome.)* | Submit blocked | Enter justification | — | `override_blocked_no_reason` | US-027 |
| ERR-902 | Two officers open the same case | Validation | "Hồ sơ đang được {officer} xử lý." *(This case is being handled by {officer}.)* | Case lock; read-only for the second officer | Pick another case | — | `case_lock_conflict` | US-026 |
| ERR-903 | Case evidence snapshot missing or corrupted | System | "Không tải được bằng chứng hồ sơ. Không đưa ra quyết định cho đến khi tải lại được." *(Couldn't load the case evidence. Don't decide until it loads.)* | Decide button disabled; alert | Reload; escalate | R-1 | `case_evidence_unavailable` | US-026 |
| ERR-904 | Officer tries to decide after the approval-validity window has passed | Validation | "Hồ sơ đã quá thời hạn hiệu lực; khách hàng cần bắt đầu hồ sơ mới." *(The case is past its validity window; the customer must start a new application.)* | Case closed EXPIRED | — | — | `case_expired_on_decide` | BD-20-04 |
| ERR-905 | Policy-parameter change submitted without second approver (four-eyes) | Permission | "Thay đổi tham số cần người phê duyệt thứ hai." *(Parameter changes need a second approver.)* | Change held as draft | Request approval | — | `policy_change_pending_approval` | US-017 |
| ERR-906 | Nightly or weekly job (AW-01, RT-01, drift, fairness) fails or overruns (A-18-10: done by 06:00 ICT) | System | Workspace banner with job name and status | No partial publish; alert; previous published output stays live | Ops re-run | Scheduler retry ×2 | `job_failed` + `job` | NFR-PERF-11 |
| ERR-907 | Drift alert PSI > 0.25 on the **champion** itself (no fallback model available) | System | Risk console: "Mô hình chuẩn có dấu hiệu lệch dữ liệu — cần xem xét của Quản trị rủi ro mô hình." *(The champion model shows data drift — Model Risk review required.)* | Recommended default: tighten to `policy.drift_safe_mode` (raise the refer band; pause graduation and pre-approval) until Model Risk signs off (BD-20-20) | Model Risk review | — | `champion_drift_alert` | OQ-17A (champion drift) |

### 1.13 Consent centre (Privacy & consent screen, E-01)

| ID | Trigger | Type | User-facing message (VI → EN) | System action | Recovery | Retry | Log | Stories |
|---|---|---|---|---|---|---|---|---|
| ERR-951 | Consent change fails to save | System | "Chưa lưu được thay đổi. Lựa chọn trước đó của bạn vẫn được giữ nguyên." *(Your change wasn't saved. Your previous choice still applies.)* | No partial state; idempotent write | Try again | R-2 | `consent_write_failed` + `purpose` | US-021 |
| ERR-952 | Customer withdraws SC while a loan is active | User | "Bạn có thể rút lại đồng ý đánh giá hồ sơ. Các khoản Trả sau đang có vẫn tiếp tục theo hợp đồng; chúng tôi chỉ xử lý dữ liệu cần thiết cho hợp đồng và nghĩa vụ pháp lý." *(You can withdraw your permission for profile assessment. Your existing Pay Later plans continue under the contract; we'll only process data needed for the contract and legal obligations.)* | Stop new profiling, marketing and graduation. Keep contract and legal-obligation processing (`privacy.sc_withdrawal_policy`, BD-20-22) | — | — | `consent_withdrawn` + `purpose=SC` | US-022; OQ-17A-04 |
| ERR-953 | Consent service unavailable when the screen opens | System | "Chưa tải được cài đặt quyền riêng tư. Vui lòng thử lại." *(Couldn't load your privacy settings. Please try again.)* | Fail closed for all consent-gated reads (DEP-06) | Try again | R-1 | `consent_screen_unavailable` | NFR-REL-02 |

---

## 2. Empty States

| ID | Screen | Empty condition | Headline (VI → EN) | Body | CTA | Illustration |
|---|---|---|---|---|---|---|
| EMP-01 | Pay Later hub → My limit | Not pre-approved, never applied | "Trả sau tại cửa hàng đối tác" *(Pay later at partner stores)* | "Khi mua sắm tại cửa hàng đối tác, chọn 'Trả sau với HLB' để xem gói phù hợp với bạn." *(When shopping at a partner store, choose 'Pay later with HLB' to see plans that fit you.)* | "Xem cửa hàng đối tác" (See partner stores) | Shopping bag with calendar |
| EMP-02 | My limit | Limit paused (early warning) | See ERR-107 | — | "Trao đổi với chúng tôi" | Neutral pause icon (no red) |
| EMP-03 | Offers for you | No MK consent | "Nhận gợi ý phù hợp với bạn" *(Get suggestions that fit you)* | "Bật 'Nhận ưu đãi' để nhận gợi ý Trả sau phù hợp với khả năng chi trả của bạn. Bạn có thể tắt bất cứ lúc nào." *(Turn on 'Receive offers' to get Pay Later suggestions that fit your budget. You can turn it off any time.)* | "Bật nhận ưu đãi" | Bell |
| EMP-04 | Offers for you | MK consent but no current suggestions | "Hiện chưa có gợi ý mới" *(No new suggestions right now)* | "Chúng tôi sẽ thông báo khi có gợi ý phù hợp." *(We'll let you know when there's something that fits.)* | — | Empty tray |
| EMP-05 | My plans | No PoP loans yet | "Bạn chưa có khoản Trả sau nào" *(You don't have any Pay Later plans yet)* | "Các khoản trả góp tại cửa hàng đối tác sẽ hiển thị ở đây cùng lịch thanh toán." *(Instalment plans from partner stores will appear here with their schedules.)* | "Xem cửa hàng đối tác" | Receipt |
| EMP-06 | My plans | All plans completed | "Bạn đã hoàn tất tất cả khoản Trả sau 🎉" *(You've completed all your Pay Later plans)* | "Cảm ơn bạn đã thanh toán đúng hạn." *(Thank you for paying on time.)* | "Xem hạn mức hiện tại" | Check mark |
| EMP-07 | Invite & earn | Not eligible yet | See ERR-701 | — | — | Two people |
| EMP-08 | Invite & earn | Eligible, no invites sent | "Mời người bạn tin tưởng" *(Invite someone you trust)* | "Phần thưởng chỉ được trao sau khi người được mời thanh toán đúng hạn {K} kỳ. Chúng tôi không bao giờ liên hệ bạn về khoản vay của người khác." *(The reward is only given after the person you invite pays on time for K instalments. We never contact you about someone else's loan.)* | "Tạo lời mời" | Handshake |
| EMP-09 | Why this decision | No decision yet | "Chưa có kết quả xét duyệt" *(No decision yet)* | "Khi bạn đăng ký Trả sau, lý do chính của kết quả sẽ hiển thị ở đây." *(When you apply for Pay Later, the main reasons for the result will show here.)* | — | Magnifier |
| EMP-10 | Privacy & consent → data sources | No connected sources | "Chưa kết nối nguồn dữ liệu nào" *(No data sources connected)* | "Bạn có thể kết nối thêm nguồn (ví dụ Sổ Bán Hàng) để có gói phù hợp hơn. Việc kết nối là tự nguyện." *(You can connect sources (e.g. Sổ Bán Hàng) for better-fitting plans. Connecting is voluntary.)* | "Tìm hiểu thêm" | Plug |
| EMP-11 | Good-payer passport (Should) | Not yet earned | "Hộ chiếu người thanh toán tốt" *(Good-payer passport)* | "Hoàn tất một khoản Trả sau đúng hạn để nhận." *(Complete one Pay Later plan on time to earn it.)* | — | Badge outline |
| EMP-12 | Officer refer queue | No open cases | "Không có hồ sơ chờ xử lý" *(No cases waiting)* | "Hồ sơ mới sẽ xuất hiện tại đây kèm thời hạn SLA." *(New cases will appear here with their SLA deadline.)* | — | — |
| EMP-13 | Campaign workspace | No eligible customers after filters | "Không có khách hàng đủ điều kiện" *(No eligible customers)* | Shows the counts excluded at each filter (consent, suitability, stacking) so the analyst can see why | "Xem chi tiết loại trừ" (See exclusions) | — |
| EMP-14 | Risk console → fairness | Too few observations to report | "Chưa đủ dữ liệu để báo cáo" *(Not enough data to report yet)* | Minimum-cell-size rule; no ratio shown below it (avoids misleading alerts) | — | — |
| EMP-15 | Merchant session status (API) | Session has no decision yet | API status `PENDING` | — | Poll or wait for the webhook | — |

---

## 3. Edge Case Registry

| ID | Edge case | Flow | Trigger condition | Handling | Priority |
|---|---|---|---|---|---|
| EC-01 | **Merchandise returned / order cancelled after booking** | Purchase & Repayment | Merchant refund call after the loan is booked | Recommended: a full return cancels the loan with no interest or fees charged. A partial return reduces principal and re-amortises over the remaining tenor. The merchant refunds HLB, not the customer. `[DECISION REQUIRED — Skill 22, BD-20-01]` | H |
| EC-02 | Partial return | Purchase & Repayment | Refund < outstanding | Recommended: reduce principal; keep instalment dates; lower instalments. `[DECISION REQUIRED — BD-20-02]` | M |
| EC-03 | Merchant settlement fails after booking | Merchant | ERR-815 | Loan start date unchanged; ops retry. `[BD-20-03]` | M |
| EC-04 | Approval never used (customer leaves) | Application | Approved but not signed | Approval valid for `refer.approval_validity` / `approval.validity_minutes` (recommended: until the end of the checkout session for instant approvals; 24 h for officer approvals). `[BD-20-04]` | H |
| EC-05 | Merchant cannot hold goods during refer | Refer | `merchant.supports_refer_hold=false` | ERR-351 choices. `[BD-20-05]` | H |
| EC-06 | Refer outcome delivery without HLB Connect | Refer | Lane B, no app | No-link SMS + completion code at the merchant; link only on explicit request. `[BD-20-06]` | H |
| EC-07 | QR shopper without HLB Connect | Merchant / Application | ERR-809 | Recommended: install-and-resume within the hold; otherwise another payment method. `[BD-20-07]` | H |
| EC-08 | Customer declines CASA-first | Application | ERR-358 | Repay by transfer/VietQR to the loan account. `[BD-20-08]` | M |
| EC-09 | Officer approves after the merchant session expired | Refer | ERR-356 | Completion code links a new merchant session to the decision ID | M |
| EC-10 | Loans booked while CIC was down (capped fallback) | Application | `cic_fallback=true` | CIC re-check within 24 h of recovery. Recommended: if stacking or delinquency is found, no clawback of the booked loan; pause further draws and graduation; flag for review. `[BD-20-21]` | H |
| EC-11 | CIC down and no cache (Lane B) | Application | ERR-333 | `policy.cic_fallback_mode`: REFER (recommended default) or capped starter. `[BD-20-09]` | H |
| EC-12 | Core banking cannot book in real time | Application | ERR-408 | Hold approval ≤ 30 min, then cancel; or approve-and-guarantee. `[BD-20-10]` | H |
| EC-13 | eKYC attempt interrupted by network | Application | Upload dropped | Not counted as an attempt (NET-06) | H |
| EC-14 | Phone without NFC | Application | ERR-314 | Recommended: device switch via QR, **not** counted as an attempt (UC-20-03). `[BD-20-11]` | M |
| EC-15 | Expired CCCD or applicant below the eligible age | Application | ERR-320 | Stop before CIC; not a DECLINE record. Age range `[BD-20-17]` | M |
| EC-16 | Lane B applicant is already an HLB customer | Application | ERR-321 | Switch to Lane A | M |
| EC-17 | Declared income implausibly high | Application | Declared income > `policy.declared_income_plausibility_cap` | Haircut and cap in F2_thin already bound exposure; F5 soft flag; no accusation | M |
| EC-18 | Basket exactly at the min/max range boundary | Consideration | 1,600,000 or 90,100,000 VND | Inclusive range [Brief p.9] | L |
| EC-19 | Package hold outlives the merchant session | Consideration | Merchant session expires first | The hold ends with the session (ERR-807) | M |
| EC-20 | Concurrent applications (two merchants or devices) | Application | ERR-343 | One in-flight application per customer; exposure reserved. `[BD-20-19]` | H |
| EC-21 | Customer cancels a referred application | Refer | "Huỷ hồ sơ" (Cancel application) on the status screen | Case closed CANCELLED; merchant notified; no decision recorded as decline. `[BD-20-20 covers the staff side]` | L |
| EC-22 | Grade upgrade arrives mid-checkout | Consideration | CCP refresh during the hold | Honour the displayed packages until the hold ends; the better terms apply to the next purchase | L |
| EC-23 | Grade downgrade arrives mid-checkout | Consideration | CCP refresh during the hold | Re-validate at submit (ERR-336) | M |
| EC-24 | Terms change between disclosure and signing | e-contract | ERR-406 | Recommended: honour the disclosed terms within validity. `[BD-20-18]` | H |
| EC-25 | Duplicate payment by the customer | Repayment | Two payments for one instalment | Excess applied to the next instalment or refunded on request (per HLB practice) | M |
| EC-26 | Early settlement on the booking day | Repayment | Settle within the first days | As disclosed in CO-02 (US-108) | L |
| EC-27 | Customer dies or becomes incapacitated | Repayment | Notified by family | Out of the digital flow; branch/ops process (HLB policy) | L |
| EC-28 | Early-warning flag clearing | Repayment | Customer catches up | Recommended: clear after `ew.clear_after_on_time_cycles` consecutive on-time cycles (proposed 2). `[BD-20-23]` | M |
| EC-29 | Graduation paused, then conditions clear | Repayment | ERR-508 cause removed | Recommended: re-offer at the next evaluation, never retroactively. `[BD-20-15]` | L |
| EC-30 | Inviter gets an early-warning flag while a reward is pending | Advocacy | ERR-707 | Recommended: the reward is still released when the invitee's condition is met (it depends on the invitee's repayment, not the inviter's), but paid into a fee waiver, not cash. `[BD-20-16]` | M |
| EC-31 | Inviter withdraws RF consent with a reward pending | Advocacy | ERR-707 | Recommended: release the earned reward; erase referral data except the accounting entry (R-02). `[BD-20-16]` | M |
| EC-32 | Invitee is an existing customer | Advocacy | ERR-703 | Recommended: no attribution. `[BD-20-16]` | L |
| EC-33 | Paid, but the ledger has not updated | Repayment | ERR-503 | Pending-payment marker suppresses reminders | H |
| EC-34 | HLB-side outage on a due date | Repayment | ERR-505 | Recommended: automatic late-fee waiver and no negative CIC report for the affected due date. `[BD-20-14]` | H |
| EC-35 | Refer case created outside business hours | Refer | ERR-352 | SLA starts at next opening per `ops.business_calendar`. `[BD-20-13]` | M |
| EC-36 | Supportive outreach timing | Repayment | ERR-501/502 | Only within `ew.contact_hours` (VNBA code forbids "unsuitable hours" without defining them [E2]). `[BD-20-24]` | H |
| EC-37 | Checkout session expiry length | Merchant | ERR-807 | `checkout.session_expiry` recommended 30 min (matches the package hold A-13-6). `[BD-20-12]` | M |
| EC-38 | Passport (Should) unlock scope | Advocacy | AD-03 | Recommended: the passport unlocks only the graduation tier the customer already qualifies for; it never bypasses affordability. `[BD-20-22b]` | L |

---

## 4. Degraded Experience Fallbacks

| ID | Dependency | Failure scenario | Fallback behaviour (what the user sees) | Recovery | User communication |
|---|---|---|---|---|---|
| DEP-01 | CIC | Timeout/error | Lane A with cached pull < 30 days: normal decision. Otherwise REFER or capped starter (EC-11) | Circuit breaker closes after health probes; loans booked in fallback re-checked within 24 h (EC-10) | REFER wording (C-7) |
| DEP-02 | C06/VNeID / eKYC provider | Unavailable | ERR-319: hold package 30 min; retry or send for review; not counted as an attempt | Health probe | ERR-319 |
| DEP-03 | Core banking (booking) | Down/timeout | ERR-408: approval held ≤ 30 min, merchant not confirmed; or approve-and-guarantee (BD-20-10) | Retry schedule A-20-7 | ERR-408 |
| DEP-04 | Online feature store | Down | Lane A: last CCP version if < `ccp.max_staleness` and no adverse event since; Lane B: REFER | Recompute on recovery | REFER wording |
| DEP-05 | Model serving | Challenger down | Champion only (no customer impact). Champion down → REFER (ERR-342) | Rollback ≤ 5 min (Skill 19 §3) | None / REFER wording |
| DEP-06 | Consent service | Down | **Fail closed**: no consent-gated read, no marketing send, no new profiling. Existing loans unaffected | Queued changes applied on recovery | ERR-953 / ERR-103 |
| DEP-07 | Merchant callback / webhook | Unacknowledged | Status endpoint always current; retries (A-20-10) | Merchant polls | Cashier status via code |
| DEP-08 | SMS/OTP gateway | Slow or down | Secondary gateway; Smart OTP / biometric signing in HLB Connect | Failover back | ERR-404 |
| DEP-09 | HLB Connect / payment rail | Outage | Existing plans unaffected; due-date grace (EC-34) | Outage window logged | ERR-505 |
| DEP-10 | Sổ Bán Hàng feed | Stale > 48 h | F3 downgrades from CONSENTED-PARTNER; MSME may fall to THIN handling | Refresh on feed recovery | None at checkout (decision reflects available evidence) |
| DEP-11 | Open API banks (Phase 2) | Unavailable | Asynchronous only; never on the checkout path; decision without S16 | Next pull | None |
| DEP-12 | CCP store / API | Down | Checkout: REFER; hub: ERR-106/604, no cached limits shown | Restore | ERR-106 |
| DEP-13 | Decision-log store | Write not acknowledged | Fail closed (ERR-006): no decision released | Retry; Sev-2 | REFER wording |
| DEP-14 | Policy service | Down | REFER (ERR-002) | Restore | REFER wording |
| DEP-15 | Analytics pipeline | Down | No customer impact; events buffered | Replay buffer | None |
| DEP-16 | Case management / workbench | Down | New refers queue; SLA clock keeps running; ops alert | Restore; breach handling ERR-353 | ERR-353 if breached |
| DEP-17 | Push-notification service | Down | Awareness/retention sends deferred inside the campaign window; no SMS fallback for marketing | Next window | None |

---

## 5. Network & Offline Handling

| ID | Rule |
|---|---|
| NET-01 | The **server is the source of truth** for application, decision, booking and payment state. The app never infers success from a local timeout. |
| NET-02 | Every write carries an **idempotency key** (application submit, consent, OTP sign, booking, payment, session creation). |
| NET-03 | The package hold (30 min, A-13-6) and application state survive app restarts; on reopen the app resumes at the last confirmed step. |
| NET-04 | Offline at open: the hub shows the last-loaded **non-credit** content (plan schedules) marked "cập nhật lúc {hh:mm}" (updated at …). Limits and offers are never shown from cache. |
| NET-05 | Uploads (NFC data, selfie) are chunk-retried; a failed upload shows "Kết nối chưa ổn định — đang thử lại…" (Unstable connection — retrying…). |
| NET-06 | A network failure during eKYC is **never counted** toward the 3-attempt cap (US-120). |
| NET-07 | Decision wait > 20 s (A-20-1) routes to REFER rather than leaving the customer on a spinner. |
| NET-08 | Web-to-app hand-off: the web page polls the server (3 s → 10 s) and resumes by session ID (ERR-313). |
| NET-09 | OTP: resend after 30 s; Smart OTP fallback in-app. |
| NET-10 | Payments: if the status is unknown, block a second payment until the status resolves (ERR-504). |
| NET-11 | In-store QR on poor connectivity: the cashier's status screen shows the server status by code; the shopper can finish on the cashier's confirmation. |
| NET-12 | Webhooks carry a stable event ID; merchants must de-duplicate. |
| NET-13 | Background refreshes (hub) use exponential back-off and stop after 3 failures until the next foreground. |
| NET-14 | No PII is cached on device beyond what HLB Connect already caches under its own security policy (Skill 18 §2). |

**Per-step behaviour on connection loss**

| Step | On loss | On return |
|---|---|---|
| Package selection | Hold kept server-side | Resume at the package list |
| eKYC | Not counted as attempt | Resume the same eKYC step |
| Waiting for decision | Decision continues server-side | Show the decision (or REFER status) |
| e-contract / OTP | Unsigned | Resume signing within validity |
| Payment | Status check before any retry | Show the resolved status |

---

## 6. Upstream Corrections (not edited here)

| # | File | Correction | Evidence |
|---|---|---|---|
| UC-20-01 | Skill 17 US-126 AC 2; Skill 13 Flow 3 refer path | Notification SMS must not contain a link unless the customer requested it in this interaction | Circular 50/2024 Art. 17(3) [E1] |
| UC-20-02 | Skill 17 US-143 AC 4 | The QR hosted-page fallback cannot finish Lane B (no chip NFC in browsers) | [Skill14 B-06] |
| UC-20-03 | Skill 17 US-118 AC 4 | A phone without NFC should trigger a device switch, not count as a failed attempt | NET-06 reasoning; BD-20-11 |
| UC-20-04 | Skill 13 Flow 4 | Collection and outreach messages must follow the VNBA Code of Conduct (Decision 15/QĐ-HHNH, effective 31 Mar 2026) | [E2] |
| UC-20-05 | Skill 02 RF-3 | Decision 15/QĐ-HHNH is now verified (was "not verified") | [E2] |

---

## 7. Assumptions Introduced by This Skill (carried to Skill 22)

| # | Assumption | Basis |
|---|---|---|
| A-20-1 | Decision budget 20 s from submit, excluding eKYC | Matches NFR-PERF-02 p99 |
| A-20-2 | Hand-off token single-use, valid for the package hold | Security default |
| A-20-3 | Decision-log write retried every 30 s for 10 min, then Sev-2 | Design default |
| A-20-4 | Campaign scheduler re-checks every 15–30 min inside the window | Design default |
| A-20-5 | Hand-off poll 3 s, then 10 s after 60 s | Design default |
| A-20-6 | SMS retried ×3 over 10 min, then secondary gateway | Design default |
| A-20-7 | Booking retry 10 s, 30 s, 1 min, then every 2 min to 30 min | Design default |
| A-20-8 | Early-settlement quote valid to end of day | Design default |
| A-20-9 | Widget script load timeout 3 s | Design default |
| A-20-10 | Webhook back-off 10 s → hourly to 24 h | Design default |

---

## 8. Business Decisions Raised for Skill 22 (BD-20-xx)

| # | Decision | Recommended default |
|---|---|---|
| BD-20-01 | Loan treatment when goods are fully returned or the order is cancelled after booking | Cancel the loan; no interest or fees; merchant refunds HLB |
| BD-20-02 | Partial return | Reduce principal; keep dates; lower instalments |
| BD-20-03 | Merchant settlement failure | Loan start date unchanged; ops retry |
| BD-20-04 | Approval validity window | Instant approvals: until the session ends; officer approvals: 24 h |
| BD-20-05 | Merchant won't hold goods during review | Show the three choices (take the counter-offer / pay another way / send anyway) |
| BD-20-06 | Refer-outcome delivery for customers without HLB Connect | No-link SMS + completion code at the merchant; link only on explicit in-session request (legal to confirm under Circular 50 Art. 17(3)) |
| BD-20-07 | QR shopper without HLB Connect | Install-and-resume within the hold |
| BD-20-08 | Repayment method if CASA-first is declined | Transfer/VietQR to the loan account |
| BD-20-09 | CIC fallback mode for Lane B with no cache | REFER |
| BD-20-10 | Approve-and-guarantee if booking isn't real-time | Not adopted by default; hold ≤ 30 min then cancel (Skill 14 OQ-14-11) |
| BD-20-11 | No-NFC phone counting | Device switch, not an attempt |
| BD-20-12 | Checkout session expiry | 30 min |
| BD-20-13 | Refer-queue business hours / calendar | HLB business hours; SLA starts at next opening |
| BD-20-14 | Due-date grace for HLB-side outages | Automatic late-fee waiver + no negative CIC report |
| BD-20-15 | Graduation re-activation after a pause | Re-offer at next evaluation; not retroactive |
| BD-20-16 | Referral reward edge cases (inviter EW flag, RF withdrawal, existing-customer invitee) | Release earned reward as fee waiver; keep accounting entry only; no attribution for existing customers |
| BD-20-17 | Eligible age range | HLB policy (not researched) |
| BD-20-18 | Terms changed between disclosure and signing | Honour disclosed terms within validity |
| BD-20-19 | One in-flight application per customer | Yes |
| BD-20-20 | Champion model drift with no fallback | `policy.drift_safe_mode`: raise refer band; pause graduation and pre-approval |
| BD-20-21 | Action after post-booking CIC re-check finds stacking | No clawback; pause further draws and graduation; review |
| BD-20-22 | Processing after SC withdrawal with an active loan | Contract + legal-obligation processing only |
| BD-20-22b | What the good-payer passport unlocks (Should) | Only an already-qualified graduation tier |
| BD-20-23 | Early-warning flag clearing | After 2 consecutive on-time cycles |
| BD-20-24 | Contact hours for outreach and collections | HLB to set within the VNBA code |

---

## 9. Open Items Raised by This Skill

| # | Item | Carry to |
|---|---|---|
| OQ-20-01 | Brand voice: "bạn" vs "Quý khách" | Skill 22 (HLB brand) |
| OQ-20-04 | Whether Consumer Protection Law 2023 Art. 38(3) (conditional 30-day termination when the trader gave inaccurate/incomplete information [E3]) applies to PoP credit contracts made at a distance | Skill 22 (legal) |
| OQ-20-06 | HLB's existing OTP attempt and lock policy values | Skill 22 (HLB) |
| OQ-20-07 | The 24 business decisions in §8 | Skill 22 |

---

## Source Index

| # | Source | URL | Used for |
|---|---|---|---|
| E1 | Thư Viện Pháp Luật, "Ngân hàng không gửi tin nhắn SMS, thư điện tử có chứa link cho khách hàng từ 01/01/2025" (Circular 50/2024/TT-NHNN Art. 17(3)); Kenh14 coverage | https://thuvienphapluat.vn/phap-luat-doanh-nghiep/bai-viet/ngan-hang-khong-gui-tin-nhan-sms-thu-dien-tu-co-chua-link-cho-khach-hang-tu-01-01-2025-9845.html ; https://kenh14.vn/tu-ngay-1-1-2025-cam-ngan-hang-tu-y-gui-tin-nhan-chua-duong-link-toi-khach-hang-215241126115839087.chn | No hyperlinks in bank SMS/email unless the customer requests; effective 1 Jan 2025 |
| E2 | LuatVietnam, "Đã có Bộ Quy tắc ứng xử trong hoạt động thu hồi nợ trong hệ thống ngân hàng"; Thư Viện Pháp Luật full text of Decision 15/QĐ-HHNH | https://luatvietnam.vn/tin-van-ban-moi/da-co-bo-quy-tac-ung-xu-trong-hoat-dong-thu-hoi-no-trong-he-thong-ngan-hang-186-108663-article.html ; https://thuvienphapluat.vn/phap-luat/ho-tro-phap-luat/toan-van-quyet-dinh-15qdhhnh-ve-bo-quy-tac-ung-xu-trong-hoat-dong-thu-hoi-no-moi-nhat-2026-ra-sao-265931.html | VNBA debt-collection code of conduct, issued and effective 31 Mar 2026; forbids threats, harassment, unsuitable contact, third-party disclosure |
| E3 | Law on Protection of Consumer Rights 2023, Chapter III (Wikisource full text) | https://vi.wikisource.org/wiki/Lu%E1%BA%ADt_B%E1%BA%A3o_v%E1%BB%87_quy%E1%BB%81n_l%E1%BB%A3i_ng%C6%B0%E1%BB%9Di_ti%C3%AAu_d%C3%B9ng_n%C6%B0%E1%BB%9Bc_C%E1%BB%99ng_h%C3%B2a_x%C3%A3_h%E1%BB%99i_ch%E1%BB%A7_ngh%C4%A9a_Vi%E1%BB%87t_Nam_2023/Ch%C6%B0%C6%A1ng_III | Art. 38(3): 30-day termination only when the trader gave inaccurate/incomplete Art. 37(1) information |
| E4 | Nielsen Norman Group, "Error-Message Guidelines" | https://www.nngroup.com/articles/error-message-guidelines/ | Human-readable, precise, constructive, non-blaming messages |
| — | Skill 13, 14, 17, 18, 19; `03-user-research.md` [Skill03 S44] | local | Flows, blockers, stories, NFRs, data |

## Skill 22 Resolutions (User decision, Skill 22, 2026-09-30)
- **D-07:** all business decisions BD-20-01 to BD-20-24 (including BD-20-22b) are **accepted at their recommended defaults** (§8). Referral reward type is a fee waiver or merchant voucher, never cash.
- OQ-20-01 (brand voice "bạn" vs "Quý khách") and OQ-20-06 (OTP policy values) are HLB brand/ops parameters (D-04). OQ-20-04 (Art. 38(3) applicability) is a legal item (D-05 → RK-23).
