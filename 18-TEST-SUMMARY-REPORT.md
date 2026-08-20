# 18 · Test Summary Report
## My Demo App (Android) — Manual Test Cycle 1

| Field | Value |
|---|---|
| Report ID | TSR-MDA-001 |
| Test cycle | Cycle 1 |
| Period | *(start date)* – *(end date)* |
| App version | *(exact `versionName`)* |
| Primary device | *(model / Android version)* |
| Author | Akanksh Gurupadappa Akki |

> **Fill this in with your real numbers after execution.** The structure is what matters — this is
> the document a QA Manager actually presents at a release meeting, and being able to walk an
> interviewer through it is one of the strongest moments available to you.

---

## 1. Executive summary

*Three sentences maximum. A decision-maker should get the answer without scrolling.*

> Example of the right shape:
>
> "164 manual test cases were executed against version X of My Demo App on Android 14, covering
> twelve testing types across the full purchase funnel. 151 passed, 9 failed and 4 were blocked,
> producing 11 defects of which 1 is Critical and 3 are High. **Recommendation: No-go** — the
> Critical order-total discrepancy (BUG-001) must be fixed and verified before release; the three High
> defects can ship with documented workarounds."

Write yours here:

---

## 2. Execution results

### 2.1 By priority

| Priority | Planned | Executed | Passed | Failed | Blocked | Not run | Pass rate |
|---|---:|---:|---:|---:|---:|---:|---:|
| P1 | 66 | | | | | | |
| P2 | 69 | | | | | | |
| P3 | 29 | | | | | | |
| **Total** | **164** | | | | | | |

- **Execution rate:** ___ % *(Executed ÷ Planned)*
- **Pass rate:** ___ % *(Passed ÷ (Passed + Failed) — Blocked deliberately excluded)*

### 2.2 By testing type

| Type | Document | Planned | Passed | Failed | Blocked | Defects |
|---|---|---:|---:|---:|---:|---:|
| Smoke | [03](03-SMOKE-TESTING.md) | 14 | | | | |
| Functional | [04](04-FUNCTIONAL-TESTING.md) | 61 | | | | |
| Negative | [05](05-NEGATIVE-TESTING.md) | 17 | | | | |
| Boundary | [06](06-BOUNDARY-VALUE-TESTING.md) | 9 | | | | |
| Input Validation | [15](15-INPUT-VALIDATION-TESTING.md) | 8 | | | | |
| Destructive | [08](08-DESTRUCTIVE-TESTING.md) | 18 | | | | |
| End-to-End | [09](09-END-TO-END-TESTING.md) | 5 | | | | |
| Usability | [10](10-USABILITY-TESTING.md) | 9 | | | | |
| Accessibility | [11](11-ACCESSIBILITY-TESTING.md) | 12 | | | | |
| Compatibility | [12](12-COMPATIBILITY-TESTING.md) | 4 | | | | |
| Performance | [13](13-PERFORMANCE-TESTING.md) | 3 | | | | |
| Localisation | [14](14-LOCALISATION-TESTING.md) | 4 | | | | |
| **Total** | | **164** | | | | |

### 2.3 By module — with defect density

| Module | Cases | Passed | Failed | Blocked | Defects | Density |
|---|---:|---:|---:|---:|---:|---:|
| Login & Authentication | 22 | | | | | |
| Catalog | 14 | | | | | |
| Sorting | 11 | | | | | |
| Product Detail | 17 | | | | | |
| Cart | 15 | | | | | |
| Checkout — Shipping | 13 | | | | | |
| Checkout — Payment | 13 | | | | | |
| Checkout — Review | 14 | | | | | |
| End-to-End | 5 | | | | | |
| Menu & Navigation | 7 | | | | | |
| WebView | 4 | | | | | |
| Geo Location | 3 | | | | | |
| Drawing | 3 | | | | | |
| QR Scanner | 3 | | | | | |
| Cross-cutting | 20 | | | | | |
| **Total** | **164** | | | | | |

*Density = defects ÷ cases in module.*

**This is the most useful column in the report.** Pass rate tells you about this build; density tells
you which part of the codebase is structurally weak, and therefore where next cycle's test-design
effort should go. A report that gives a manager only a pass rate gives them nothing to act on.

Name the highest-density module explicitly and say what you would do about it.

### 2.4 By journey stage

| Stage | Cases | Passed | Failed | Defects | Funnel impact |
|---|---:|---:|---:|---:|---|
| Discovery | 25 | | | | |
| Consideration | 17 | | | | |
| Conversion | 60 | | | | |
| Account | 22 | | | | |
| Cross-cutting | 40 | | | | |

**Why this cut matters.** Defects in Discovery and Consideration are *silent* — the customer leaves
rather than complaining, so they never generate a support ticket. A defect count that is low in
Conversion and high in Discovery is not good news; it means the problems are in the part of the funnel
nobody will report.

---

## 3. Defect summary

### 3.1 By severity and priority

| Severity | Open | Fixed | Deferred | Rejected | Total | P1 | P2 | P3 | P4 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| Critical | | | | | | | | | |
| High | | | | | | | | | |
| Medium | | | | | | | | | |
| Low | | | | | | | | | |
| **Total** | | | | | | | | | |

### 3.2 Top defects

| ID | Summary | Severity | Priority | Status | Journey stage |
|---|---|---|---|---|---|
| BUG-001 | | | | | |
| BUG-002 | | | | | |
| BUG-003 | | | | | |
| BUG-004 | | | | | |
| BUG-005 | | | | | |

### 3.3 Discovery method

| Found by | Defects | Hours | Defects per hour |
|---|---:|---:|---:|
| Scripted execution (164 cases) | | ~14 | |
| Exploratory charters (5 sessions, in-session time) | | 2.5 | |
| **Total** | | **~16.5** | |

**State the conclusion explicitly.** If the exploratory rate is higher, write the sentence: *"the
five exploratory charters found N defects in 2.5 hours of session time against M in 14 hours of scripted execution,
which supports allocating protected exploratory time in every sprint."* That is a QA Manager's
argument, made with a number.

---

## 4. Exploratory testing results

| Charter | Duration | Defects | Highest severity | Questions raised | Key insight |
|---|---|---:|---|---:|---|
| C1 — Cart arithmetic & state sync | 30 min | | | | |
| C2 — Checkout form, backwards | 30 min | | | | |
| C3 — Lifecycle persistence | 30 min | | | | |
| C4 — Copy, content & consistency | 30 min | | | | |
| C5 — Unprescribed sequences | 30 min | | | | |

**Average task breakdown across sessions:** design & execution ___% · bug investigation ___% ·
setup ___%

*(Setup above 25% means the environment needs investment, not more testing.)*

### Questions raised for the Product Owner

These are **not defects** — they are behaviours where the intent is unknown. Listing them separately
rather than filing them as bugs is a deliberate choice.

| # | Question | Screen | Why it matters |
|---|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |

---

## 5. Coverage

### 5.1 Risk coverage

| | Count | % |
|---|---:|---:|
| Total risks in the register | 32 | 100% |
| Risks with at least one **executed** case | | |
| Risks with at least one **failed** case | | |
| Risks in the P1 band (score ≥ 15) | | |

See `traceability/MyDemoApp-RTM.xlsx` for the full mapping. Every one of the 164 cases traces to
exactly one of the 32 risks, verifiable in both directions.

### 5.2 Device coverage

| Device | Profile | Scope run | Pass rate | Device-specific findings |
|---|---|---|---:|---|
| D1 | *(your physical phone)* | Full suite + charters | | |
| D2 | Pixel 7 emulator / API 34 | Full suite | | |
| D3 | Pixel 4a emulator / API 31 | P1 + P2 | | |
| D4 | Low-RAM emulator, 2 GB | Smoke + destructive lifecycle | | |
| D5 | Tablet emulator | Smoke + layout | | |

### 5.3 Known coverage gaps

Stated deliberately — knowing the edges of your own coverage is part of the job.

- **iOS not covered.** The test environment is Windows, so no iOS toolchain is available. An iOS
  build of this app exists and is the natural extension.
- **No automated execution.** By design — see [document 02, section 6](02-TEST-STRATEGY-AND-RISK-REGISTER.md#6-what-this-manual-cycle-says-about-automation)
  for which 80 cases are automation candidates and why the other 84 stay manual.
- **No API or backend testing.** No access to non-production environments or API documentation.
- **No server-side load testing.** There is no backend under my control to load.
- **Biometric happy path not covered.** Requires enrolled fingerprint hardware. The negative path is
  covered by `TC-LOGIN-022`.
- **Single physical device.** Mitigated by four emulator profiles spanning API level, screen size and
  RAM tier, but real-device fragmentation is inherently under-sampled.
- **No German translation available**, so localisation testing assessed i18n *symptoms* rather than
  translation quality.

---

## 6. Exit criteria assessment

| Criterion | Target | Actual | Met? |
|---|---|---|:---:|
| P1 cases executed | 100% (66) | | ☐ |
| P2 cases executed | ≥ 90% (63 of 69) | | ☐ |
| Open Critical defects | 0 | | ☐ |
| Open High defects on the P1 path | 0, or PO-accepted | | ☐ |
| Exploratory charters completed | 5 | | ☐ |
| Every arithmetic case executed | 100% | | ☐ |
| Risks covered by ≥ 1 executed case | 32 of 32 | | ☐ |
| Smoke pack passing | 100% (14) | | ☐ |

**Any unmet criterion must be either met or explicitly waived by a named person.** A test summary
report with an unmet exit criterion and no waiver is an unfinished report.

---

## 7. Quality assessment

### 7.1 Client-side performance measurements

| Metric | Target | Run 1 | Run 2 | Run 3 | Run 4 | Run 5 | Median | Worst |
|---|---|---|---|---|---|---|---|---|
| Cold start to interactive (s) | < 3 | | | | | | | |
| Warm start to interactive (s) | < 1 | | | | | | | |

Report the **median and worst case**, never the best — the worst case is what a frustrated user
experiences.

**Scroll:** frames over 16 ms from `dumpsys gfxinfo`: ______
**Memory at 0 / 10 / 30 minutes:** ______ / ______ / ______ MB

### 7.2 Accessibility assessment

| Check | Result |
|---|---|
| Full purchase journey completable with TalkBack alone | ☐ Yes ☐ No |
| Accessibility Scanner issues on Catalog | |
| Accessibility Scanner issues on Product Detail | |
| Accessibility Scanner issues on Cart | |
| Accessibility Scanner issues on Checkout | |
| All monetary values readable at 200% font | ☐ Yes ☐ No |
| Colour selection perceivable in grayscale | ☐ Yes ☐ No |

**If the purchase journey cannot be completed with TalkBack, say so in the executive summary.** That
is a functional failure for that user group and, for an EU e-commerce service, a compliance concern
under the European Accessibility Act.

---

## 8. Residual risk at release

Everything the business is choosing to accept by shipping.

| Risk | Defect | Severity | Who is affected | Workaround | Accepted by |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |

**An empty table here is a red flag, not a clean bill of health.** Every release carries residual
risk; a report that shows none has not looked.

---

## 9. Recommendations

Ordered by value, with the reasoning stated. These are the recommendations this project's findings
support — adjust to match what you actually found.

1. **Fix every Critical defect before release.** Non-negotiable, particularly any arithmetic defect:
   a wrong total is a direct financial error and an EU consumer-protection exposure.

2. **Add stable accessibility identifiers to every interactive element.** One change with three
   justifications — it makes the app usable with TalkBack, it makes automation cheap to maintain, and
   accessibility for e-commerce is a European Accessibility Act requirement effective since June 2025.
   This is the highest-leverage engineering ask in the report.

3. **Automate the 80 flagged candidate cases, starting with the smoke pack.** The 14-case smoke pack
   currently takes ~20 minutes per build by hand. See document 02, section 6 for the four criteria and
   which cases qualify.

4. **Protect exploratory time in every sprint**, at the rate justified by section 3.3. If the
   detection rate favours exploration, that is the business case.

5. **Instrument client-side performance in production.** Manual stopwatch measurement catches
   order-of-magnitude problems only. Real-user monitoring of cold start and crash-free session rate
   would catch what this cycle cannot.

6. **Widen the device matrix with at least one genuine low-end physical device.** The 2 GB emulator
   approximates memory pressure but not real thermal throttling or real OEM skin behaviour.

7. **Track defect density per module across cycles, not just pass rate.** The trend tells you where
   the architecture is weak; the pass rate only tells you about this build.

8. **Establish release notes as a prerequisite for regression scoping.** Regression scope cannot be
   selected rationally without knowing what changed — see document 16.

---

## 10. Release recommendation

**☐ Go  ☐ Go with conditions  ☐ No-go**

**Conditions and reasoning:**

**Decision owner:** *(name the person who owns this call — QA recommends, the business decides)*

---

## 11. Post-release monitoring

Quality does not end when the button is pressed. What to watch for the first 48 hours:

- [ ] Crash-free session rate versus the previous version
- [ ] ANR rate
- [ ] Store review sentiment, filtered to the new version only
- [ ] Funnel metrics: catalogue-to-product rate, product-to-cart rate, cart-to-order completion rate
- [ ] Any spike in support contacts mentioning totals, prices or the cart
- [ ] Rollout staged (e.g. 10% → 50% → 100%) with a **pre-agreed rollback trigger**

**Interview talking point:** *"A release checklist that stops at 'shipped' isn't finished. I'd want a
defined post-release watch window with rollback triggers agreed in advance — a crash-free rate below
a threshold, or a drop in cart-to-order completion, which is the metric that would tell us a
Conversion defect got through. Agreeing the trigger before release is the point; agreeing it during
an incident is too late."*

---

**Signed:** Akanksh Gurupadappa Akki, QA · *(date)*
