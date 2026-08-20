# 02 · Test Strategy & Risk Register
## My Demo App (Android) — `com.saucelabs.mydemoapp.android`

---

## 1. Quality risk register

**This table is the answer to "how did you prioritise?"** — the single most common interview
question about test design, and the one most candidates answer with "by importance", which is
not an answer.

Each risk is scored **Impact × Likelihood**, both on a 1–5 scale. The product sets the priority
band, and the band sets the priority of every test case that mitigates that risk. So when someone
asks why the cart has more P1 cases than the drawing screen, the answer is a number rather than an
opinion.

| Score | Band | Meaning |
|---|---|---|
| **15–25** | **P1** | Failure blocks a purchase, loses customer data, or displays a wrong price. Must pass before release. |
| **9–14** | **P2** | Failure degrades the experience but a workaround exists. |
| **1–8** | **P3** | Cosmetic, rare path, or low-traffic. |

Sorted highest risk first. Every one of the **164 test cases** maps to exactly one risk, and every risk has at least one case — verifiable in both directions in `traceability/MyDemoApp-RTM.xlsx`.

| ID | Risk | Impact | Likelihood | Score | Band | Cases | Test case IDs |
|---|---|---:|---:|---:|:---:|---:|---|
| **R-05** | App state (cart, session, selections) is lost on process death or backgrounding | 5 | 4 | **20** | P1 | 11 | `TC-CART-009`, `TC-CART-010`, `TC-CHKR-010`, `TC-E2E-004`, `TC-LOGIN-020`, `TC-LOGIN-021`, `TC-SORT-010`, `TC-XCUT-003`, `TC-XCUT-004`, `TC-XCUT-008`, `TC-XCUT-020` |
| **R-02** | Form validation is absent or misleading, so users cannot self-correct | 4 | 4 | **16** | P1 | 7 | `TC-LOGIN-005`, `TC-LOGIN-006`, `TC-LOGIN-007`, `TC-LOGIN-012`, `TC-LOGIN-013`, `TC-LOGIN-014`, `TC-LOGIN-016` |
| **R-10** | Navigation loses sort, scroll position or entered form data | 4 | 4 | **16** | P1 | 5 | `TC-CAT-008`, `TC-CHKP-012`, `TC-CHKS-011`, `TC-PDP-013`, `TC-SORT-007` |
| **R-12** | Rotation or a configuration change loses data or breaks the layout | 4 | 4 | **16** | P1 | 5 | `TC-CAT-010`, `TC-DRAW-003`, `TC-XCUT-005`, `TC-XCUT-006`, `TC-XCUT-007` |
| **R-14** | Loss of network produces an infinite spinner, a blank screen or a false success | 4 | 4 | **16** | P1 | 4 | `TC-CAT-013`, `TC-CHKR-009`, `TC-PDP-016`, `TC-WEB-003` |
| **R-23** | Shipping form validation blocks valid input or accepts undeliverable input | 4 | 4 | **16** | P1 | 5 | `TC-CHKS-003`, `TC-CHKS-004`, `TC-CHKS-006`, `TC-CHKS-007`, `TC-CHKS-009` |
| **R-01** | Users cannot authenticate, blocking checkout entirely | 5 | 3 | **15** | P1 | 6 | `TC-LOGIN-001`, `TC-LOGIN-002`, `TC-LOGIN-003`, `TC-LOGIN-004`, `TC-LOGIN-009`, `TC-LOGIN-017` |
| **R-03** | Credentials or payment data are exposed on screen, in screenshots or in logs | 5 | 3 | **15** | P1 | 10 | `TC-CHKP-008`, `TC-CHKR-002`, `TC-CHKS-010`, `TC-LOGIN-008`, `TC-LOGIN-010`, `TC-LOGIN-011`, `TC-LOGIN-015`, `TC-WEB-002`, `TC-WEB-004`, `TC-XCUT-015` |
| **R-07** | The same price is displayed inconsistently across screens | 5 | 3 | **15** | P1 | 3 | `TC-CAT-005`, `TC-PDP-002`, `TC-XCUT-014` |
| **R-11** | Cart badge or contents do not match what the user actually added | 5 | 3 | **15** | P1 | 5 | `TC-CART-002`, `TC-CAT-009`, `TC-PDP-007`, `TC-PDP-008`, `TC-PDP-017` |
| **R-13** | Users relying on a screen reader, large text or colour alternatives cannot complete a purchase | 5 | 3 | **15** | P1 | 12 | `TC-CART-014`, `TC-CART-015`, `TC-CAT-011`, `TC-CAT-012`, `TC-CHKR-013`, `TC-NAV-007`, `TC-PDP-014`, `TC-PDP-015`, `TC-SORT-011`, `TC-XCUT-009`, `TC-XCUT-010`, `TC-XCUT-011` |
| **R-15** | Rapid repeated input causes duplicate actions, including duplicate orders | 5 | 3 | **15** | P1 | 5 | `TC-CART-012`, `TC-CAT-014`, `TC-CHKR-008`, `TC-NAV-006`, `TC-SORT-009` |
| **R-17** | Quantity controls allow an invalid quantity or miscalculate the line total | 5 | 3 | **15** | P1 | 3 | `TC-PDP-004`, `TC-PDP-005`, `TC-PDP-006` |
| **R-21** | Order arithmetic is wrong — the customer is shown an incorrect total | 5 | 3 | **15** | P1 | 8 | `TC-CART-003`, `TC-CART-004`, `TC-CART-005`, `TC-CART-006`, `TC-CART-007`, `TC-CART-013`, `TC-CHKR-004`, `TC-E2E-003` |
| **R-22** | Checkout cannot be reached, or the cart is lost on the way into it | 5 | 3 | **15** | P1 | 4 | `TC-CHKS-001`, `TC-CHKS-002`, `TC-CHKS-005`, `TC-E2E-002` |
| **R-26** | Payment form does not collect or present payment details correctly | 5 | 3 | **15** | P1 | 4 | `TC-CHKP-001`, `TC-CHKP-003`, `TC-CHKP-010`, `TC-CHKP-011` |
| **R-27** | Payment validation accepts invalid card data, guaranteeing a later failure | 5 | 3 | **15** | P1 | 6 | `TC-CHKP-002`, `TC-CHKP-004`, `TC-CHKP-005`, `TC-CHKP-006`, `TC-CHKP-007`, `TC-CHKP-009` |
| **R-28** | The order placed does not match what the customer reviewed and agreed to | 5 | 3 | **15** | P1 | 7 | `TC-CHKR-001`, `TC-CHKR-003`, `TC-CHKR-005`, `TC-CHKR-006`, `TC-CHKR-007`, `TC-CHKR-011`, `TC-E2E-005` |
| **R-08** | Product detail shows the wrong product or incomplete information | 4 | 3 | **12** | P2 | 4 | `TC-CAT-006`, `TC-PDP-001`, `TC-PDP-003`, `TC-PDP-010` |
| **R-16** | Sorting produces an incorrect order, or adds, drops or duplicates products | 4 | 3 | **12** | P2 | 7 | `TC-SORT-001`, `TC-SORT-002`, `TC-SORT-003`, `TC-SORT-004`, `TC-SORT-005`, `TC-SORT-006`, `TC-SORT-008` |
| **R-24** | European names, characters and formats are mishandled | 4 | 3 | **12** | P2 | 2 | `TC-CHKS-008`, `TC-XCUT-013` |
| **R-25** | Friction in the checkout forms causes avoidable abandonment | 3 | 4 | **12** | P2 | 6 | `TC-CHKP-013`, `TC-CHKS-012`, `TC-CHKS-013`, `TC-XCUT-017`, `TC-XCUT-018`, `TC-XCUT-019` |
| **R-30** | Navigation is broken, trapping the user or exposing the wrong screen | 4 | 3 | **12** | P2 | 5 | `TC-NAV-001`, `TC-NAV-002`, `TC-NAV-003`, `TC-NAV-004`, `TC-NAV-005` |
| **R-04** | User-facing copy contains errors, placeholders or inconsistencies | 2 | 5 | **10** | P2 | 7 | `TC-CHKR-012`, `TC-CHKR-014`, `TC-LOGIN-018`, `TC-LOGIN-019`, `TC-LOGIN-022`, `TC-PDP-011`, `TC-XCUT-012` |
| **R-06** | Catalogue fails to render, blocking all downstream discovery | 5 | 2 | **10** | P2 | 4 | `TC-CAT-001`, `TC-CAT-002`, `TC-CAT-003`, `TC-CAT-004` |
| **R-29** | The end-to-end purchase journey cannot be completed at all | 5 | 2 | **10** | P2 | 1 | `TC-E2E-001` |
| **R-09** | App is slow to start, janky to scroll, or degrades over a session | 3 | 3 | **9** | P2 | 4 | `TC-CAT-007`, `TC-XCUT-001`, `TC-XCUT-002`, `TC-XCUT-016` |
| **R-20** | Empty states are dead ends with no way forward | 3 | 3 | **9** | P2 | 3 | `TC-CART-001`, `TC-CART-008`, `TC-CART-011` |
| **R-31** | Runtime permissions are requested or handled incorrectly | 3 | 3 | **9** | P2 | 5 | `TC-GEO-001`, `TC-GEO-002`, `TC-GEO-003`, `TC-QR-001`, `TC-QR-002` |
| **R-19** | External links or scanned URLs open the wrong destination | 3 | 2 | **6** | P3 | 3 | `TC-PDP-012`, `TC-QR-003`, `TC-WEB-001` |
| **R-18** | Product rating control behaves incorrectly | 1 | 3 | **3** | P3 | 1 | `TC-PDP-009` |
| **R-32** | Drawing screen loses work or renders incorrectly | 1 | 3 | **3** | P3 | 2 | `TC-DRAW-001`, `TC-DRAW-002` |

**32 risks · 164 case links · 18 risks in the P1 band.**

### How the register was built without a requirements document

There is no specification for this application, so the risks were derived from four sources —
and being able to name them is the point:

1. **Observable behaviour.** Walk every screen and ask "what could go wrong here that a customer
   would notice?"
2. **The app's own published string resources.** This app is open source, so its user-facing text is
   readable. That surfaced several concrete leads for copy and consistency defects — see
   [document 17](17-DEFECT-REPORTS.md).
3. **Domain knowledge of e-commerce failure modes.** Wrong totals, lost carts, filters that lie,
   duplicate order submission. These recur across every retail application because they arise from
   the same architectural pressures.
4. **Platform knowledge of Android failure modes.** Process death, configuration change, runtime
   permissions, aggressive background limits. Android *causes* these; they are not user error.

Sources 3 and 4 are where experience shows. A tester who has only ever used a specification would
not think to kill the process mid-checkout, because no specification ever asks for it.

---

## 2. Where the effort goes, and why

Test cases are not distributed evenly, and the distribution is a deliberate argument.

| Module | Cases | Share | Why this weighting |
|---|---:|---:|---|
| Login & Authentication | 22 | 13% | Gates checkout and handles credentials. Heavy negative and input-validation coverage because it is the app's only authentication surface. |
| Catalog | 14 | 9% | Blocks every downstream module if it fails. Also where cross-screen price consistency starts. |
| Sorting | 11 | 7% | The primary active discovery mechanism — this app has no search — so it carries the Discovery stage together with the Catalog. Invariant-based cases (set equality, ordering) make it cheap to test thoroughly. |
| Product Detail | 17 | 10% | The decision moment, plus the quantity stepper — which is the first place arithmetic can go wrong. |
| Cart | 15 | 9% | Where the money is calculated. Every arithmetic invariant lives here. |
| Checkout — Shipping | 13 | 8% | A seven-field form is the highest validation-complexity surface in the app. |
| Checkout — Payment | 13 | 8% | Card validation plus privacy exposure. Accepting an expired card guarantees a later failure. |
| Checkout — Review | 14 | 9% | The total the customer legally agrees to pay, and the last chance to catch a divergence. |
| End-to-End | 5 | 3% | Deliberately few but long. These find defects in the seams between modules, which granular cases cannot. |
| Menu & Navigation | 7 | 4% | Entry point to everything, so a small set with wide blast radius. |
| WebView | 4 | 2% | URL scheme validation — a genuine input-validation surface. |
| Geo Location | 3 | 2% | Runtime permission handling, including the denial path. |
| Drawing | 3 | 2% | Low commercial value, but an excellent configuration-change data-loss test. |
| QR Scanner | 3 | 2% | Camera permission plus external URL handling. |
| Cross-cutting | 20 | 12% | Lifecycle, accessibility, performance and localisation apply to every screen, so they are grouped rather than duplicated 15 times. |
| **Total** | **164** | **100%** | |

**The cart and the three checkout modules together hold 55 of 164 cases — a third of the whole
suite for four screens.** That is the single clearest expression of the risk register in the design: the funnel's
final stage is where a defect costs real money, so it gets a third of the effort. Being able to
defend a weighting like that with a number, rather than "it felt important", is what makes test
design a discipline.

---

## 3. Coverage by testing type

| Type | Cases | Document | What it exists to catch |
|---|---:|---|---|
| Smoke | 14 | [`03-SMOKE-TESTING.md`](03-SMOKE-TESTING.md) | A build not worth testing |
| Functional | 61 | [`04-FUNCTIONAL-TESTING.md`](04-FUNCTIONAL-TESTING.md) | Features that do not do what they should |
| Negative | 17 | [`05-NEGATIVE-TESTING.md`](05-NEGATIVE-TESTING.md) | Invalid input accepted, or refused unhelpfully |
| Boundary | 9 | [`06-BOUNDARY-VALUE-TESTING.md`](06-BOUNDARY-VALUE-TESTING.md) | Off-by-one errors at the edge of a range |
| Input Validation | 8 | [`15-INPUT-VALIDATION-TESTING.md`](15-INPUT-VALIDATION-TESTING.md) | Unexpected input crashing the app or being rendered as markup |
| Destructive | 18 | [`08-DESTRUCTIVE-TESTING.md`](08-DESTRUCTIVE-TESTING.md) | Failure modes under abuse — the ones nobody designed for |
| End-to-End | 5 | [`09-END-TO-END-TESTING.md`](09-END-TO-END-TESTING.md) | Defects in the seams between features |
| Usability | 9 | [`10-USABILITY-TESTING.md`](10-USABILITY-TESTING.md) | Friction that causes abandonment without generating a support ticket |
| Accessibility | 12 | [`11-ACCESSIBILITY-TESTING.md`](11-ACCESSIBILITY-TESTING.md) | A disabled customer unable to complete a purchase |
| Compatibility | 4 | [`12-COMPATIBILITY-TESTING.md`](12-COMPATIBILITY-TESTING.md) | Defects that only appear on certain devices or configurations |
| Performance | 3 | [`13-PERFORMANCE-TESTING.md`](13-PERFORMANCE-TESTING.md) | Slowness that makes users leave before reporting it |
| Localisation | 4 | [`14-LOCALISATION-TESTING.md`](14-LOCALISATION-TESTING.md) | Hardcoded strings, wrong number formats, mishandled characters |
| **Total** | **164** | | |

Note the shape: **Functional is the largest single type at 61 cases, but the other eleven types
together account for 103 — nearly two thirds of the suite.** A suite that is 90% functional cases is
testing only the happy path, and the happy path is the part most likely to already work, because it
is what the developer tried while building it.

---

## 4. Defect management

### 4.1 Severity — technical impact, set by QA

| Severity | Definition | Example in this app |
|---|---|---|
| **Critical** | App unusable, data lost, or money calculated wrongly. No workaround. | Order total does not equal the sum of its lines plus delivery |
| **High** | Major function broken; workaround is painful. | Cart is lost when the app is killed and relaunched |
| **Medium** | Function impaired; a reasonable workaround exists. | Scroll position lost on back navigation from a product |
| **Low** | Cosmetic, or a copy and content error. | A misspelled username in the login credential list |

### 4.2 Priority — business urgency, agreed with the Product Owner

| Priority | Meaning |
|---|---|
| **P1 Blocker** | Fix before release |
| **P2 High** | Fix in the current sprint |
| **P3 Medium** | Fix in an upcoming sprint |
| **P4 Low** | Backlog |

### 4.3 Severity is not Priority

Have this example ready, because it is asked in almost every QA interview:

> A misspelled brand name on the login screen is **Low severity** — nothing is broken, everything
> works. It can still be **P1 priority**, because it is the first thing every customer sees and it
> makes the company look careless.
>
> Conversely, a crash in a feature 0.1% of users touch is **Critical severity** and might be
> **P3 priority**, because almost nobody hits it.

Keeping them as separate fields is what makes the prioritisation conversation with a Product Owner
possible. Collapsing them into one field means QA is silently making business decisions.

### 4.4 Defect lifecycle

    New → Triaged → In Progress → Ready for Test → Verified → Closed

Side paths: `Rejected` · `Duplicate` · `Cannot Reproduce` · `Deferred`

**Every closure records the build number it was verified in.** "Fixed" with no build number is not a
closure — nobody can tell later which release contains the fix.

### 4.5 What every ticket must contain

Title readable in a backlog without opening it · device, OS version, **app version**, network ·
preconditions · numbered steps a stranger could follow · expected result **and why** it is expected ·
actual result, facts only · severity · priority · **frequency as a hit rate** ("3 of 5"), never a
feeling · evidence file · business impact.

The two most commonly omitted and most valuable: **app version** and **hit rate**. Without the
version, an engineer cannot tell whether the defect still exists. Without the hit rate, they cannot
plan how to reproduce it.

---

## 5. Metrics reported

| Metric | Formula | What it tells you |
|---|---|---|
| Execution rate | Executed ÷ Planned | Whether the cycle finished |
| Pass rate | Passed ÷ (Passed + Failed) | Build quality — note that Blocked is deliberately excluded |
| Defect density by module | Defects ÷ Cases in module | **Where to invest test design next cycle** |
| Defect severity distribution | Count by severity | Whether the problems are serious or cosmetic |
| Defect detection rate | Defects ÷ Test hours | Scripted vs exploratory productivity |
| Requirements coverage | Risks with ≥ 1 executed case ÷ Total risks | Coverage gaps |
| Escaped defects | Found after release ÷ Total | Suite effectiveness over time |

**Defect density by module is the one that changes behaviour.** Pass rate tells you about this
build; density tells you which part of the codebase is structurally weak, and therefore where the
next cycle's effort should go. Reporting only pass rate gives a manager nothing to act on.

And a caution worth voicing: **pass rate is trivially gameable.** Add fifty easy cases and it rises
without any change in quality. Which is why the register above ties every case to a risk — so the
suite cannot be padded without someone noticing.

---

## 6. What this manual cycle says about automation

Manual and automated testing answer different questions, and deciding which is which is itself
a manual-testing judgement. **80 of the 164 cases** in
this suite are flagged as automation candidates. The remaining
84 stay manual **by decision, not by omission** —
and the reasoning is recorded so it can be challenged.

### The four criteria

A case is flagged for automation only if **all four** hold:

1. **Deterministic** — the same input always produces the same expected result.
2. **Repeated** — it runs every release, so the maintenance cost amortises.
3. **Expensive by hand** — long, tedious, or error-prone for a human. Checking 30 prices are in
   ascending order qualifies; checking a button exists does not.
4. **Stably locatable** — the elements have identifiers that survive a release.

Fail any one and it stays manual.

### What deliberately stays manual

| Stays manual | Why |
|---|---|
| All 9 usability cases | Requires human judgement about friction. An automated check cannot tell you a form is unpleasant. |
| Most accessibility cases | A scanner catches a missing label; only a person catches a label that says "button1". |
| All 5 exploratory charters | The entire value is a human noticing something unexpected. Automating exploration is a contradiction. |
| Copy and content review | Requires reading for meaning, not matching strings. |
| Permission-prompt flows | OS-level dialogs, device-dependent, low repeat value. |
| Performance measurement | Manual stopwatch and ADB profiling is adequate; instrumented profiling belongs to the app team. |

### The prerequisite nobody budgets for

None of the automation candidates are actually cheap to automate unless the app exposes stable
identifiers on its interactive elements. That is an ask to the app engineers, and it is worth making
because it is **one change with three justifications**:

1. It makes automation possible **and cheap to maintain**.
2. It is the same attribute a screen reader announces, so it is an **accessibility fix**.
3. Accessibility for e-commerce is a **European Accessibility Act** requirement, effective since
   June 2025 — so it is **compliance**, not goodwill.

Bringing that argument to an engineering team is a QA Manager's contribution. Asking for it as a
favour is a tester's.
