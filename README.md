# Manual Testing Portfolio — My Demo App (Android)
### A complete manual test cycle across 12 testing types · 164 test cases · full e-commerce funnel

[![Test cases](https://img.shields.io/badge/test%20cases-164-1f3864)](test-cases/MyDemoApp-MasterTestSuite.xlsx)
[![Testing types](https://img.shields.io/badge/testing%20types-12-1f3864)](#the-12-testing-types)
[![Modules](https://img.shields.io/badge/modules-15-1f3864)](#coverage-by-module)
[![Platform](https://img.shields.io/badge/platform-Android-3ddc84)](#the-application-under-test)
[![Type](https://img.shields.io/badge/testing-100%25%20manual-orange)](#why-this-is-100-manual)

**Author:** Akanksh Gurupadappa Akki · QA Engineer
**Application under test:** [My Demo App — Android](https://github.com/saucelabs/my-demo-app-android) (`com.saucelabs.mydemoapp.android`)

---

## What this repository is

A structured, end-to-end **manual** test cycle against a real Android e-commerce application,
organised so that **each type of testing is a separate, fully explained document**. Every document
states what that type of testing is, why it matters for this application, which formal technique it
uses, how to execute it, and then lists its own test cases in full step-by-step detail.

The application is an open-source demo app published by Sauce Labs and built specifically for QA
practice. It is a genuine e-commerce app with a complete funnel — catalogue, sorting, product
detail, cart, login, shipping, payment, order review and confirmation — plus supporting screens
for a WebView, geolocation, QR scanning, biometrics and drawing.

**Why a demo app and not a commercial one:** because destructive testing on someone else's
production system is neither responsible nor legal, and because a purpose-built demo app can be
tested at *full depth* — including checkout and payment — without touching real money or real
personal data. The trade-off is stated openly rather than hidden.

---

## Why this is 100% manual

This repository contains **no automation code, by design**. It exists to demonstrate manual testing
skill specifically:

- **test design** — deriving 164 cases from an application with no available requirements
  documentation, using named formal techniques rather than intuition
- **risk-based prioritisation** — a written risk register with impact × likelihood scoring that
  drives every case's priority, so prioritisation is defensible rather than a matter of opinion
- **exploratory testing** — structured session-based charters with live notes and debriefs
- **destructive testing** — five named attack patterns, with hit rates recorded rather than
  impressions
- **defect reporting** — tickets specific enough that an engineer can act without asking a
  follow-up question
- **release judgement** — knowing when to say no-go, and being able to defend it

A companion repository holds the Appium automation work. The two are deliberately separate,
because **the decision about what to automate is itself a manual-testing judgement** — and this
repository documents that judgement. Of the 164 cases here, **80 are flagged as automation
candidates** against four stated criteria; the other 84 stay manual on purpose, not by omission.

---

## The 12 testing types

Each links to its own document. Read them in this order — they build on each other.

| # | Document | Cases | What it proves |
|---|---|---:|---|
| 03 | [Smoke Testing](docs/03-SMOKE-TESTING.md) | 14 | Is this build worth testing at all? |
| 04 | [Functional Testing](docs/04-FUNCTIONAL-TESTING.md) | 61 | Does every feature do what it should? |
| 05 | [Negative Testing](docs/05-NEGATIVE-TESTING.md) | 17 | Does the app refuse invalid input safely and informatively? |
| 06 | [Boundary Value Testing](docs/06-BOUNDARY-VALUE-TESTING.md) | 9 | Are the edges of every input range correct? |
| 07 | [Exploratory Testing](docs/07-EXPLORATORY-TESTING.md) | 5 charters | Can I find the defects nobody designed a case for? |
| 08 | [Destructive Testing](docs/08-DESTRUCTIVE-TESTING.md) | 18 | How does the app fail when actively abused? |
| 09 | [End-to-End Testing](docs/09-END-TO-END-TESTING.md) | 5 | Can a customer actually complete a purchase? |
| 10 | [Usability Testing](docs/10-USABILITY-TESTING.md) | 9 | Is it pleasant and efficient, not merely functional? |
| 11 | [Accessibility Testing](docs/11-ACCESSIBILITY-TESTING.md) | 12 | Can a disabled customer complete a purchase? |
| 12 | [Compatibility Testing](docs/12-COMPATIBILITY-TESTING.md) | 4 | Does it work across devices, OS versions and configurations? |
| 13 | [Performance Testing](docs/13-PERFORMANCE-TESTING.md) | 3 | Is it fast enough that users don't leave? |
| 14 | [Localisation Testing](docs/14-LOCALISATION-TESTING.md) | 4 | Is it correct for a specific market, and adaptable to others? |
| 15 | [Input Validation Testing](docs/15-INPUT-VALIDATION-TESTING.md) | 8 | Is unexpected input handled as inert data? |
| 16 | [Regression Testing](docs/16-REGRESSION-TESTING.md) | selection | What is re-run, when, and what happens on failure? |

Types 03–15 total **164 cases**. Document 16 introduces no new cases — it defines the selection
strategy over the existing ones. Document 07 uses time-boxed charters instead of scripted cases,
because that is what exploratory testing is.

---

## Supporting documents

| Document | Purpose |
|---|---|
| [00 · Setup and How to Run](docs/00-SETUP-AND-HOW-TO-RUN.md) | **Start here.** Install everything on Windows, install the app, execute the suite. Written for a complete beginner. |
| [01 · Test Plan](docs/01-TEST-PLAN.md) | Scope, approach, environment, entry and exit criteria, schedule, deliverables |
| [02 · Test Strategy & Risk Register](docs/02-TEST-STRATEGY-AND-RISK-REGISTER.md) | The 32-risk register that drives every priority decision in the suite |
| [17 · Defect Reports](docs/17-DEFECT-REPORTS.md) | Full Jira-format tickets, plus **eight concrete leads** for defects that are genuinely likely to exist in this app |
| [18 · Test Summary Report](docs/18-TEST-SUMMARY-REPORT.md) | The release-meeting document: metrics, coverage, residual risk, go/no-go |
| [19 · Journey Scope Split](docs/19-JOURNEY-SCOPE-SPLIT.md) | Which cases are **Discovery & Consideration** and which are the wider funnel |

---

## Repository layout

```
├── README.md                                  ← you are here
├── docs/
│   ├── 00-SETUP-AND-HOW-TO-RUN.md
│   ├── 01-TEST-PLAN.md
│   ├── 02-TEST-STRATEGY-AND-RISK-REGISTER.md
│   ├── 03-SMOKE-TESTING.md
│   ├── 04-FUNCTIONAL-TESTING.md
│   ├── 05-NEGATIVE-TESTING.md
│   ├── 06-BOUNDARY-VALUE-TESTING.md
│   ├── 07-EXPLORATORY-TESTING.md
│   ├── 08-DESTRUCTIVE-TESTING.md
│   ├── 09-END-TO-END-TESTING.md
│   ├── 10-USABILITY-TESTING.md
│   ├── 11-ACCESSIBILITY-TESTING.md
│   ├── 12-COMPATIBILITY-TESTING.md
│   ├── 13-PERFORMANCE-TESTING.md
│   ├── 14-LOCALISATION-TESTING.md
│   ├── 15-INPUT-VALIDATION-TESTING.md
│   ├── 16-REGRESSION-TESTING.md
│   ├── 17-DEFECT-REPORTS.md
│   ├── 18-TEST-SUMMARY-REPORT.md
│   └── 19-JOURNEY-SCOPE-SPLIT.md
├── test-cases/
│   ├── MyDemoApp-MasterTestSuite.xlsx         all 164 cases + auto-calculating dashboard
│   └── MyDemoApp-MasterTestSuite.csv          same data, ready for TestRail import
├── defects/
│   └── MyDemoApp-Defect-Log.xlsx              defect log + triage summary + release gate
├── traceability/
│   └── MyDemoApp-RTM.xlsx                     32 risks mapped to all 164 cases
├── evidence/
│   └── README.md                              where screenshots and logs go, and how to capture them
└── .gitignore
```

---

## Coverage by module

The 15 modules follow the customer journey in order:

| Module | Cases | Journey stage |
|---|---:|---|
| Login & Authentication | 22 | Account |
| Catalog | 14 | Discovery |
| Sorting | 11 | Discovery |
| Product Detail | 17 | Consideration |
| Cart | 15 | Conversion |
| Checkout — Shipping | 13 | Conversion |
| Checkout — Payment | 13 | Conversion |
| Checkout — Review | 14 | Conversion |
| End-to-End | 5 | Conversion |
| Menu & Navigation | 7 | Cross-cutting |
| WebView | 4 | Cross-cutting |
| Geo Location | 3 | Cross-cutting |
| Drawing | 3 | Cross-cutting |
| QR Scanner | 3 | Cross-cutting |
| Cross-cutting (lifecycle, a11y, perf, l10n) | 20 | Cross-cutting |
| **Total** | **164** | |

**Priority split:** P1 **66** · P2 **69** · P3 **29**
**Journey stage:** Discovery 25 · Consideration 17 · Conversion 60 · Account 22 · Cross-cutting 40
**Automation candidates:** 80 of 164

---

## Two scopes in one suite

Every case is tagged with a journey stage, so the suite can be read two ways. This matters if you
are reviewing it against a specific team's remit — see [document 19](docs/19-JOURNEY-SCOPE-SPLIT.md)
for the full breakdown.

| Scope | Cases | Modules |
|---|---:|---|
| **Discovery & Consideration only** — how a customer finds and evaluates a product | **42** | Catalog, Sorting, Product Detail |
| **Full funnel** — everything above plus login, cart, checkout, order placement and cross-cutting concerns | **164** | All 15 |

Filter the `Journey Stage` column in the master workbook to `Discovery` and `Consideration` to see
the narrower scope on its own.

---

## Techniques applied

Every test case names the technique it was derived from. This is what separates designed test cases
from remembered ones.

| Technique | Where it is used |
|---|---|
| **Equivalence partitioning** | Credential fields, address fields, card fields — invalid classes tested one representative at a time |
| **Boundary value analysis** | Quantity steppers, card expiry dates, field lengths, cart line counts |
| **State transition testing** | Cart state machine, login session, sort persistence, process death |
| **Decision table testing** | Mandatory versus optional field combinations, billing-address checkbox |
| **Consistency oracles** | The same price across four screens — the most useful technique when you have no requirements |
| **Arithmetic invariants** | Line total = quantity × unit price; order total = subtotal + delivery |
| **Set-equality invariants** | Sorting must never add, drop or duplicate a product |
| **Session-based exploratory testing** | Five time-boxed charters with live notes and debriefs |
| **Destructive patterns** | Resource removal, lifecycle interruption, rapid repeated input, rapid state change, volume and endurance |
| **Heuristic evaluation** | Nielsen's usability heuristics, named per case |
| **WCAG 2.1 AA criteria** | Every accessibility case cites its specific success criterion |
| **Risk-based prioritisation** | 32-risk register, impact × likelihood, driving all 164 priorities |

---

## Highlights worth reading first

If you have five minutes, these four things show the most:

1. **[The risk register](docs/02-TEST-STRATEGY-AND-RISK-REGISTER.md#1-quality-risk-register)** — 32
   risks scored on impact × likelihood, and every one of the 164 cases traced back to one of them.
   This is the answer to "how did you prioritise?"

2. **[The arithmetic invariants](docs/04-FUNCTIONAL-TESTING.md#2-why-it-matters-for-this-application)** —
   every monetary total is verified by hand calculation before looking at what the app shows,
   because reading the app's number first means you will rationalise it.

3. **[The destructive checkout cases](docs/08-DESTRUCTIVE-TESTING.md)** — tapping *Place Order* ten
   times in two seconds; killing the process between payment and confirmation. In a real payment
   system the first one charges the customer twice.

4. **[The accessibility argument](docs/11-ACCESSIBILITY-TESTING.md#3-technique-used)** — the label a
   screen reader announces is the same attribute an automation framework locates. One change, three
   justifications: TalkBack support, cheap automation, and European Accessibility Act compliance.

---

## Disclaimer & ethics

- This is an **independent personal portfolio project**. It is not affiliated with, commissioned by,
  or endorsed by Sauce Labs.
- The application under test is **open source and published specifically for testing purposes**.
- All testing is **black-box**, from an ordinary end-user perspective.
- **No real personal or payment data** is used anywhere. Fictional names and obvious dummy card
  numbers only.
- **No credentials are committed** to this repository at any point.
- The input-validation document is explicitly **not** security or penetration testing — it is
  client-side robustness checking on a demo app, and the boundary is stated in that document.

---

## Getting started

New to this? Go to **[docs/00-SETUP-AND-HOW-TO-RUN.md](docs/00-SETUP-AND-HOW-TO-RUN.md)**. It
covers every install step on Windows, how to get the app onto a phone or emulator, and how to work
through the suite — written assuming no prior setup.
