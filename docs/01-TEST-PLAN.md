# 01 · Test Plan
## My Demo App (Android) — Full Manual Test Cycle

| Field | Value |
|---|---|
| Document ID | TP-MDA-001 |
| Version | 1.0 |
| Author | Akanksh Gurupadappa Akki |
| Application under test | My Demo App — Android, `com.saucelabs.mydemoapp.android` |
| App version | *(record from `adb shell dumpsys package … \| findstr versionName` before each cycle)* |
| Test level | System testing, black box, manual only |
| Test basis | Observable app behaviour, the app's published open-source string resources, standard e-commerce conventions, Android platform guidelines, WCAG 2.1 AA |

---

## 1. Purpose and objectives

### 1.1 Purpose
Define the scope, approach, environment, resources, schedule and exit criteria for a complete
manual system-test cycle of an Android e-commerce application, and define what "finished" means.

### 1.2 Objectives, in priority order
1. **Verify the purchase funnel can be completed** — a customer can find a product, evaluate it,
   add it to a cart and place an order.
2. **Verify every monetary calculation is correct.** Line totals, subtotals, delivery, order total.
3. **Verify state survives real-world conditions** — process death, backgrounding, rotation,
   network loss.
4. **Verify the application refuses invalid input safely and informatively.**
5. **Verify a customer with a disability can complete a purchase.**
6. **Establish which cases are worth automating**, against stated criteria, and document the
   reasoning for the ones that are not.

### 1.3 Why this application
It is an open-source demo application published specifically for QA practice. That matters for two
reasons, and both are worth stating explicitly:

- **Depth is possible.** Because there is no real payment backend and no real customer data, the
  cycle can cover checkout, payment and destructive testing at full depth. Doing the same against a
  commercial production app would be irresponsible and, for the destructive cases, likely unlawful.
- **The oracle is reliable.** The catalogue is fixed local data rather than changing stock, so
  "the product count must be identical across two runs" is a dependable assertion. On a live
  retailer, that same check would be permanently flaky.

---

## 2. Scope

### 2.1 In scope — 164 cases across 15 modules

| # | Module | Cases | Journey stage | Why it is in scope |
|---|---|---:|---|---|
| M-01 | Login & Authentication | 22 | Account | Gates checkout; handles credentials |
| M-02 | Catalog | 14 | Discovery | Entry point; blocks everything downstream if broken |
| M-03 | Sorting | 11 | Discovery | Primary discovery mechanism in this app |
| M-04 | Product Detail | 17 | Consideration | The decision moment; quantity and colour selection |
| M-05 | Cart | 15 | Conversion | Where the arithmetic lives |
| M-06 | Checkout — Shipping | 13 | Conversion | Seven-field form; highest validation complexity |
| M-07 | Checkout — Payment | 13 | Conversion | Card validation; privacy-sensitive |
| M-08 | Checkout — Review | 14 | Conversion | The total the customer agrees to pay |
| M-09 | End-to-End | 5 | Conversion | The seams between all of the above |
| M-10 | Menu & Navigation | 7 | Cross-cutting | Entry point to every module |
| M-11 | WebView | 4 | Cross-cutting | URL validation; embedded content |
| M-12 | Geo Location | 3 | Cross-cutting | Runtime permission handling |
| M-13 | Drawing | 3 | Cross-cutting | Configuration-change data loss |
| M-14 | QR Scanner | 3 | Cross-cutting | Camera permission; external URL handling |
| M-15 | Cross-cutting | 20 | Cross-cutting | Lifecycle, accessibility, performance, localisation |

**By testing type:** Smoke 14 · Functional 61 · Negative 17 · Boundary 9 · Input Validation 8 ·
Destructive 18 · End-to-End 5 · Usability 9 · Accessibility 12 · Compatibility 4 · Performance 3 ·
Localisation 4.

Plus **5 exploratory charters** (document 07), which are time-boxed sessions rather than scripted
cases, and a **regression selection strategy** (document 16) defined over the existing cases.

### 2.2 Out of scope, and why

| Area | Why excluded |
|---|---|
| **Automated test execution** | Deliberate. This repository exists to demonstrate manual testing skill. Automation is a separate companion project, and the decision about *what* to automate is documented here as a manual-testing judgement. |
| **Server-side load, stress and soak testing** | There is no backend under my control to load. Load-testing infrastructure you do not own is neither permitted nor meaningful. Client-side performance *is* in scope — see document 13. |
| **Security and penetration testing** | Not authorised, and out of my remit. Document 15 covers *client-side input robustness* only, and states that boundary explicitly. |
| **API and integration testing** | No access to non-production environments or API documentation. |
| **iOS** | The test environment is Windows, so no iOS toolchain is available. An iOS build of this app exists and would be the natural extension. |
| **Real payment processing** | The app has no payment backend. No real card data is used under any circumstances. |
| **Biometric authentication happy path** | Requires enrolled fingerprint hardware. The *negative* path — behaviour with no biometrics enrolled — is in scope (TC-LOGIN-022). |
| **Code review, static analysis, unit tests** | White-box activities; no build environment is set up and the deliverable is a black-box cycle. |

**Interview note:** being able to state clearly what you did *not* test, and give a reason for each,
is a senior signal. Candidates who say "I tested everything" have either not thought about scope or
are not being accurate.

---

## 3. Test approach

### 3.1 One document per testing type
Each testing type is a separate document that explains the type, justifies its relevance to this
application, names the technique, and lists its own cases. The reasoning behind that structure:

- **It forces the technique to be named.** A case that cannot be traced to a technique was probably
  remembered rather than designed.
- **It makes the suite reviewable in pieces.** A reader interested in destructive testing can read
  eighteen cases in context rather than filtering a spreadsheet.
- **It maps to how execution actually happens.** You test a whole type in one sitting, because the
  mindset for functional testing is different from the mindset for destructive testing and switching
  between them constantly is inefficient.

### 3.2 Techniques applied

| Technique | Applied to |
|---|---|
| Equivalence partitioning | Credential fields, address fields, card fields, character-set classes |
| Boundary value analysis | Quantity steppers, card expiry, field lengths, cart line counts |
| Decision table testing | Mandatory vs optional field combinations, billing-address checkbox |
| State transition testing | Cart state machine, login session, sort persistence, process death |
| Consistency oracles | The same price across four screens; sort symmetry |
| Arithmetic invariants | Line total, subtotal, delivery, order total |
| Set-equality invariants | Sorting must not add, drop or duplicate products |
| Session-based exploratory testing | 5 time-boxed charters |
| Destructive patterns | Resource removal, lifecycle interruption, rapid input, rapid state change, volume/endurance |
| Heuristic evaluation | Nielsen's heuristics, named per usability case |
| WCAG 2.1 AA success criteria | Every accessibility case cites its criterion |
| Static review of user-facing copy | Placeholders, typos, inconsistent footers, hardcoded years |

### 3.3 Prioritisation
Risk-based, driven by the 32-risk register in [document 02](02-TEST-STRATEGY-AND-RISK-REGISTER.md).
Each risk is scored **impact × likelihood** (1–5 each), and the score sets the priority band:

| Score | Band | Meaning | Cases |
|---|---|---|---:|
| 15–25 | **P1** | Failure blocks a purchase, loses data, or shows a wrong price. Must pass before release. | 66 |
| 9–14 | **P2** | Failure degrades the experience; a workaround exists. Target ≥ 90% execution. | 69 |
| 1–8 | **P3** | Cosmetic, rare path, or low-traffic. | 29 |

The **14 Smoke cases** are a subset of P1 and form the fast gate: they run first, on every build,
and a failure rejects the build outright.

### 3.4 Test data

| Data | Values used | Rationale |
|---|---|---|
| Valid credentials | Taken from the app's own on-screen credential list | The app publishes them; nothing is guessed |
| Locked-out account | The account the app labels as locked out | Tests a documented negative path |
| Invalid credentials | `nosuchuser@example.com`, `wrongpassword` | Equivalence classes: unknown user, wrong password |
| Fictional names | `Anne-Marie O'Brien`, `Björn Müller-Schäfer`, `Ñuñez Ferrão` | Real names contain apostrophes, hyphens and accents — naive validation rejects them |
| Boundary lengths | 1, 200, 256, 500 characters | Lower bound, layout stress, upper bound, beyond |
| Character sets | Latin, accented Latin, Cyrillic, CJK, emoji, whitespace-only | Encoding and rendering classes |
| Markup-like input | `<b>Test</b>`, `<script>alert(1)</script>` | Output-encoding check at every display point |
| SQL-like input | `' OR '1'='1` | Client-side robustness only — see 2.2 |
| Dummy card | `4111111111111111` | Universally recognised test value; unmistakably not a real card |
| Expiry dates | Past month, current month, next month, +20 years | Boundary values derived at execution time, not hardcoded |
| Security codes | 2, 3, 10 digits | Below, at, and above the hinted length |

> **Data protection.** No real personal data, no real payment data, and no credentials are recorded
> anywhere in this repository. All destructive input strings are used strictly as **client-side
> input-handling checks on a demo application**, with assertions limited to "the app does not crash,
> does not render input as markup, and does not surface an internal error".

---

## 4. Test environment

### 4.1 Devices

| # | Device | Type | Purpose |
|---|---|---|---|
| D1 | *(your physical phone — record model, Android version, screen size, RAM)* | Physical | Primary execution. All 164 cases plus all charters. Required for usability, accessibility and performance. |
| D2 | Pixel 7 emulator, Android 14 (API 34) | Emulator | Reference AOSP behaviour on the current platform |
| D3 | Pixel 4a emulator, Android 12 (API 31) | Emulator | Older API, smaller screen — catches layout truncation and platform differences |
| D4 | Low-RAM emulator, 2 GB, Android 13 | Emulator | Process death and memory pressure for the destructive lifecycle cases |
| D5 | Tablet emulator, Android 14 | Emulator | Layout at large widths |

**Suite allocation** — not everything runs everywhere:

| Device | Scope |
|---|---|
| D1 | Full 164-case suite + all 5 exploratory charters |
| D2 | Full 164-case suite |
| D3 | All P1 and P2 cases (135) |
| D4 | Smoke pack + every destructive lifecycle case |
| D5 | Smoke pack + layout inspection |

### 4.2 Tooling

| Tool | Used for |
|---|---|
| ADB | Screenshots, screen recording, logcat, force-stop, memory and frame profiling |
| Accessibility Scanner (Android app) | Automated contrast and touch-target checks |
| Android Developer Options | *Don't keep activities*, animation scales, layout bounds |
| Excel / LibreOffice | Test-case execution, dashboard, defect log, RTM |
| Git + GitHub | Version control and publication |
| Windows 11 host | Runs ADB and holds the documentation |

### 4.3 Configurations exercised
Portrait and landscape · default and maximum font size · default and maximum display size ·
English and German device language · online, airplane mode, and mid-request network loss ·
*Don't keep activities* on and off · TalkBack on and off · grayscale colour correction.

---

## 5. Entry and exit criteria

### 5.1 Entry criteria
- APK installed on D1 and the exact `versionName` recorded
- `adb devices` confirms the connection
- Application launches and the catalogue renders
- Test-case workbook available and the previous cycle's results archived
- Any defects marked ready-for-test listed for verification in this cycle

### 5.2 Exit criteria
- **100%** of P1 cases (66) executed
- **≥ 90%** of P2 cases (69) executed
- **Zero** open Critical defects
- **Zero** open High defects on the P1 path, or each explicitly accepted by the Product Owner with
  the reasoning recorded
- All **5** exploratory charters completed and debriefed
- **Every arithmetic case executed** — this is never descoped for time
- RTM shows all **32** risks covered by at least one executed case
- Test summary report completed with a stated release recommendation

### 5.3 Suspension and resumption
**Suspend** if: the app crashes on launch; the smoke pack fails; or more than 30% of executed cases
are blocked by a single defect.

**Resume** when: a new build is available, or the blocking defect is verified fixed. On resumption,
re-run the smoke pack first — always.

---

## 6. Deliverables

| Deliverable | Location |
|---|---|
| Setup and execution guide | `docs/00-SETUP-AND-HOW-TO-RUN.md` |
| This test plan | `docs/01-TEST-PLAN.md` |
| Test strategy and 32-risk register | `docs/02-TEST-STRATEGY-AND-RISK-REGISTER.md` |
| 14 per-type testing documents | `docs/03-…` through `docs/16-…` |
| Master test suite, 164 cases + dashboard | `test-cases/MyDemoApp-MasterTestSuite.xlsx` |
| TestRail-importable CSV | `test-cases/MyDemoApp-MasterTestSuite.csv` |
| Defect reports | `docs/17-DEFECT-REPORTS.md` + `defects/MyDemoApp-Defect-Log.xlsx` |
| Requirements traceability matrix | `traceability/MyDemoApp-RTM.xlsx` |
| Test summary report | `docs/18-TEST-SUMMARY-REPORT.md` |
| Journey scope split | `docs/19-JOURNEY-SCOPE-SPLIT.md` |
| Evidence | `evidence/` |

---

## 7. Schedule and effort

| Phase | Effort | Output |
|---|---:|---|
| Environment setup | 1.5 h | Device connected, app installed, version recorded |
| Familiarisation | 0.5 h | Unstructured walkthrough of every screen |
| Smoke pack | 0.3 h | Build accepted or rejected |
| Functional execution | 5 h | 61 cases executed with recorded actual results |
| Negative + boundary + input validation | 2 h | 34 cases; all messages captured verbatim |
| End-to-end journeys | 1 h | 5 journeys with totals hand-verified |
| Destructive | 2 h | 18 cases, each attempted 5 times with hit rates |
| Exploratory | 3.5 h | 5 charters (30 min each) plus a 10-min debrief each |
| Usability + accessibility | 2 h | 21 cases on a physical device |
| Compatibility + performance + localisation | 2 h | 11 cases across device profiles |
| Defect write-up | 1.5 h | Full tickets with evidence attached |
| Reporting | 1 h | RTM and test summary report |
| **Total** | **~22 h** | |

A realistic plan is two weeks of evenings, or three focused days.

*(Execution and write-up alone is ~20 h; the remainder is environment setup and familiarisation.)*

---

## 8. Roles

Single-tester project. Mapped to a team context, the responsibilities divide as follows — worth
knowing because these responsibilities split differently once more than one person is involved:

| Role | Owns |
|---|---|
| **QA Manager** | Test plan, scope decisions, risk register, exit criteria, release recommendation, and the argument for protected exploratory time |
| **QA Engineer** | Case design and execution, defect reporting, RTM maintenance, evidence capture |
| **App Engineer** | Defect resolution; adding stable accessibility identifiers to support both TalkBack and automation |
| **Product Owner** | Accepting residual risk on deferred defects; prioritising defects against business value |

---

## 9. Risks to the test effort itself

Distinct from product risks — these are the things that could undermine the *cycle*.

| Risk | Impact | Mitigation |
|---|---|---|
| No access to requirements or acceptance criteria | Expected results are inferred, not authoritative | Base expectations on observable behaviour, the app's own published strings, platform guidelines and internal consistency. Anything genuinely ambiguous is logged as a **Query for the PO**, not a defect. |
| Single physical device | Device-specific defects missed | Supplement with four emulator profiles spanning API level, screen size and RAM tier |
| Tester bias toward the happy path | Negative and destructive coverage thins out | Negative, boundary and destructive cases are separate documents with their own exit criteria, so they cannot be quietly skipped |
| Fatigue on a 164-case suite | Later cases executed less carefully | Execute by type in separate sittings; never more than 2 hours continuously; the highest-risk modules are scheduled first |
| Confirmation bias on arithmetic | Wrong totals rationalised as correct | Hand-calculate the expected value **before** looking at the app's figure. This is a stated rule in document 04. |
| App updated mid-cycle | Results become inconsistent | Record `versionName` per execution; do not update the APK during a cycle |
| Intermittent defects dismissed | Real defects lost | Every destructive case is attempted 5 times and a hit rate recorded, never an impression |

---

## 10. Approval

| Role | Name | Date |
|---|---|---|
| Author | Akanksh Gurupadappa Akki | *(date)* |
| Reviewer | — (independent portfolio project) | — |
