# 16 · Regression Testing & Release Gate

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

This document defines **which existing cases are re-run and when**. It introduces no new cases.

The **fixed regression core is the 14-case smoke pack** listed at the end — it runs on every single build. The **full P1 set is 66 cases** and runs on every release. Everything between those two is chosen by change-impact analysis.

---

## 1. What this type of testing is

**Regression testing** re-runs existing tests against a changed build to confirm that what
worked before still works.

It is the least glamorous and most valuable testing activity, because software has a property that
makes it necessary: **fixing one thing breaks another.** A developer changes how the cart total is
calculated to fix a rounding bug, and the delivery charge stops being included. Nobody intended it,
nobody noticed, and the only thing standing between that and a customer is a regression suite.

Regression testing is **not a separate set of test cases.** It is a *selection* from the cases you
already have, re-run at a defined trigger. That is why this document contains no new cases — it
defines which existing cases run, when, and what happens when one fails.

---

## 2. Why it matters for this application

Regression suites fail in a predictable way, and understanding the failure mode is more
useful than knowing the definition.

A team starts with a small suite. It finds things, so they add to it. It grows to four hundred
cases and takes three days to run. So it gets run less often — only before major releases. Then
only when someone remembers. Then a release goes out untested because there was no time, nothing
bad happens, and the suite is quietly abandoned.

**The suite did not fail because it was wrong. It failed because it was too expensive to run at
the frequency that mattered.**

So regression scope is an economic decision, and it is governed by two principles:

**1. Risk-based selection.** Not everything is re-run every time. The **smoke pack** runs on every
build, always — it is the fixed core listed at the end of this document. The full **P1 set** runs
on every release. Everything in between is selected by what changed.

**2. Change-impact analysis.** Read the release notes, identify the modules touched, and re-run
that module's full case set plus its **neighbours**. Neighbours matter: a change to the cart
affects checkout, because checkout consumes the cart's output. The defects live in the
dependencies, and the traceability matrix is what makes those dependencies visible.

---

## 3. Technique used

**Regression selection by trigger:**

| Trigger | Scope | Duration |
|---|---|---|
| Any new build | Smoke pack (see document 03) | ~20 min |
| Bug-fix build | Smoke pack + the fixed module's full set + its neighbours | ~90 min |
| Feature release | Full P1 set + P2 cases in the changed modules + all 5 end-to-end journeys | ~5 h |
| Major release | Full suite + all exploratory charters + full device matrix | ~3 days |
| Any release touching money | Every arithmetic case, without exception | ~30 min |

That last row is a deliberate override. Arithmetic cases are never descoped for time, because a
wrong total is the one defect class with direct financial and legal consequences.

**Defect-fix verification** is a distinct activity that belongs here, and it has three parts —
most people only do the first:

1. **Verify the fix.** Re-run the exact reproduction steps from the original defect report on the
   new build.
2. **Test around the fix.** Exercise the neighbouring functionality the change could plausibly
   have affected. This is where fix-induced regressions are caught.
3. **Add a case if one was missing.** If the defect escaped, the suite had a gap. Close it, or the
   same class of defect returns.

Record the **build number** in which each defect was verified. "Fixed" without a build number is
not a closure — nobody can tell later which release contains it.

**Change-impact map for this app** — read across to find the neighbours to re-test:

| Changed module | Also re-test |
|---|---|
| Catalog | Sorting, Product Detail |
| Sorting | Catalog |
| Product Detail | Cart, Catalog |
| Cart | Checkout (all three stages), Product Detail |
| Checkout — Shipping | Checkout — Payment, Checkout — Review |
| Checkout — Payment | Checkout — Review |
| Checkout — Review | Cart, all end-to-end journeys |
| Login | Checkout — Shipping (the login gate), Menu |
| Menu & Navigation | Every module (it is the entry point to all of them) |

---

## 4. How to execute these cases

**Before you start:**
- Read the release notes and write down which modules changed. If there are no release notes,
  that is itself worth raising — regression scope cannot be chosen rationally without them.
- Record the **exact build or version number** under test. A regression result without a build
  number cannot be compared against anything.
- List the defects marked ready-for-test so you verify them in the same pass.

**During:**
- Run the **smoke pack first**. If it fails, stop — there is no point regression-testing a broken
  build.
- Re-run failed cases once before raising a defect, to rule out your own mis-step. Once, not
  repeatedly, and note that you did.

**After:**
- Update the test summary report with the pass rate for this build and the previous one. **The
  trend matters more than the number** — a pass rate falling across three builds is a signal about
  the codebase, not about this release.
- Give a clear recommendation: **Go**, **Go with conditions**, or **No-go** — with the reasoning
  and a named owner for the decision.

---

## 5. The fixed regression core — the 14-case smoke pack

These run on **every** build, in this order, before anything else. If any one fails, the build is rejected and no further regression testing happens.

| # | Case ID | Module | Title |
|---:|---|---|---|
| 1 | `TC-LOGIN-001` | Login & Authentication | Login screen is reachable from the menu and renders all its controls |
| 2 | `TC-LOGIN-003` | Login & Authentication | Valid credentials log the user in successfully |
| 3 | `TC-CAT-001` | Catalog | Catalog opens on launch and displays the product grid |
| 4 | `TC-CAT-002` | Catalog | Every product tile shows an image, a name and a price |
| 5 | `TC-CAT-006` | Catalog | Tapping a product tile opens the matching product detail page |
| 6 | `TC-SORT-001` | Sorting | Sort control opens and lists all four sort options |
| 7 | `TC-PDP-001` | Product Detail | Product detail page displays name, price, image, rating and description |
| 8 | `TC-CART-002` | Cart | Cart lists every added product with correct name, colour, price and quantity |
| 9 | `TC-CHKS-002` | Checkout — Shipping | Shipping address form renders all its fields with mandatory markers |
| 10 | `TC-CHKS-005` | Checkout — Shipping | Valid shipping data allows progression to the payment screen |
| 11 | `TC-CHKP-001` | Checkout — Payment | Payment form renders all fields and the billing-address checkbox |
| 12 | `TC-CHKP-003` | Checkout — Payment | Valid payment data allows progression to the order review screen |
| 13 | `TC-CHKR-006` | Checkout — Review | Place Order completes the purchase and shows a confirmation screen |
| 14 | `TC-NAV-001` | Menu & Navigation | Menu opens and lists every expected destination |

Full step-by-step detail for each case is in the document for its own testing type, and in `test-cases/MyDemoApp-MasterTestSuite.xlsx`.

## 6. What "done" looks like

- Smoke pack 100% pass.
- Selected regression scope executed per the trigger table.
- Every ready-for-test defect verified, with the build number recorded.
- Every fix tested *around* as well as *at* the fix.
- A case added for any defect that escaped the existing suite.
- Release recommendation communicated with reasoning.

---

## 7. Interview talking point

> "Regression scope is an economic decision, not a completeness one. The failure mode I've seen
> is a suite that grows to four hundred cases, takes three days, gets run less and less often, and
> is quietly abandoned — it didn't fail because it was wrong, it failed because it was too expensive
> to run at the frequency that mattered. So I keep a fixed 14-case smoke pack that runs on every
> build, the full P1 set on every release, and I select everything between them by change-impact analysis: the changed module plus its neighbours, because a cart
> change affects checkout even though nobody touched checkout. The one rule I don't negotiate is
> that every arithmetic case runs on any release that touches money."
