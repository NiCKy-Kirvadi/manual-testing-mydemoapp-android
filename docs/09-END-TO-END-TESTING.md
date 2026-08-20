# 09 · End-to-End Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**5 test cases** in this document — 2 P1, 3 P2, 0 P3 — across 1 module.

---

## 1. What this type of testing is

**End-to-end testing** validates a complete user journey across every screen and feature it
touches, in one continuous flow, as a real customer would experience it.

The distinction from functional testing is scope, and it is not a pedantic one. Functional
testing verifies that the cart calculates correctly. End-to-end testing verifies that a person can
**start with an intention and finish with an outcome** — land on the catalogue, find something,
buy it, and receive confirmation — without hitting a wall.

The two find genuinely different defects. Every individual feature can pass its own tests while
the journey between them is broken, because **defects live in the seams**: state that does not
carry from the cart to the review screen, a login redirect that loses the basket, an address that
is silently truncated between the form and the confirmation.

---

## 2. Why it matters for this application

A funnel is only as strong as its weakest transition, and the transitions are where nobody
owns the code.

Consider the login-during-checkout handoff in this app. A logged-out user with a full cart taps
checkout, is sent to login, authenticates — and then what? If they land back on the catalogue with
their cart intact, that is a mild annoyance. If they land on the catalogue with an *empty* cart,
that is a lost sale. Neither the login feature nor the cart feature is individually broken. **The
seam between them is.** Only an end-to-end case walking that exact path finds it.

The five journeys in this file are chosen to cover the seams that matter:

1. **Happy path, logged out** — the full funnel including the login handoff.
2. **Happy path, already logged in** — the alternate entry state, which skips a screen.
3. **Mid-flow correction** — cart edited before checkout; does the edit propagate to the total?
4. **Interrupted journey** — app killed mid-checkout and resumed.
5. **Repeat purchase** — a second order in the same session; does state leak from the first?

Journey 5 is the one most people forget, and it finds a specific and common defect class: leftover
state from a previous transaction contaminating the next one.

---

## 3. Technique used

**Scenario-based testing**, sometimes called user-journey or soap-opera testing.

You write the scenario as a narrative first — "a customer who has not logged in wants to buy the
cheapest item in two units" — and only then decompose it into steps. Writing the narrative first
matters: it keeps the case anchored to something a person would actually do, rather than becoming
a checklist of features to touch.

Each journey is then constructed to satisfy three properties:

**Realism.** Every step is something a customer plausibly does, in an order they plausibly do it.
A journey that only makes sense to a tester finds defects nobody will ever hit.

**Coverage of seams.** The journey deliberately crosses feature boundaries — catalogue to product,
product to cart, cart to login, login to checkout, checkout to order. The seams are the point.

**A verifiable end state.** The journey ends with something you can check unambiguously:
confirmation shown, cart emptied, total correct. A journey with a vague ending cannot fail
cleanly, and a test that cannot fail cleanly is not a test.

Note that end-to-end cases are **long**, and that is a known trade-off: when one fails, you have to
work out *where* it failed. That is precisely why the functional, negative and boundary suites
exist as separate, granular files — they localise the defect that the end-to-end case merely
detects.

---

## 4. How to execute these cases

- **Reset before every journey.** Menu → *Reset App State*, then log out. An end-to-end case
  starting from contaminated state proves nothing.
- **Do not skip steps even when you are confident.** The value is in the continuity; skipping the
  step you assume works is how the seam defect survives.
- **Record the intermediate state at every transition**, not only the final outcome. When a
  journey fails at step 11, your notes from steps 1 to 10 are what turn it into a defect report
  instead of a shrug.
- **Verify the money at three points**: the cart total, the review total, and the arithmetic
  relationship between them and the delivery charge. Check them by hand.
- Expect **10 to 15 minutes** per journey done properly. Five journeys is roughly an hour.
- Use fictional names and obvious dummy card numbers only.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-E2E-001`](#tc-e2e-001) | P1 | End-to-End | Conversion | Complete happy-path purchase from launch to order confirmation |
| [`TC-E2E-002`](#tc-e2e-002) | P1 | End-to-End | Conversion | Purchase journey when the user is already logged in |
| [`TC-E2E-003`](#tc-e2e-003) | P2 | End-to-End | Conversion | Journey with a mid-flow correction: edit the cart before checkout |
| [`TC-E2E-004`](#tc-e2e-004) | P2 | End-to-End | Conversion | Abandoned journey resumed after an app restart |
| [`TC-E2E-005`](#tc-e2e-005) | P2 | End-to-End | Conversion | Second consecutive purchase in the same session |

---

## 6. Test cases — full detail

### TC-E2E-001
**Complete happy-path purchase from launch to order confirmation**

| | |
|---|---|
| Priority | **P1** |
| Module | End-to-End |
| Journey stage | Conversion |
| Technique | End-to-end scenario (happy path) |
| Risk covered | `R-29` — The end-to-end purchase journey cannot be completed at all |
| Automation candidate | Yes |

**Preconditions**

App installed. Reset App State performed. User logged out.

**Steps**

1. Launch the app and land on the Catalog
2. Sort by price ascending
3. Open the cheapest product
4. Select a colour and set quantity to 2
5. Add to cart and verify the badge shows 2
6. Open the cart and verify the line and the total
7. Proceed to checkout and log in with valid credentials
8. Enter valid shipping data and continue
9. Enter valid dummy payment data and continue
10. Verify the review screen matches the cart and the total is correct
11. Tap Place Order
12. Verify the confirmation screen and that the cart is now empty

**Expected result**

Every step succeeds with no crash, no data loss, no incorrect total, and no need to re-enter information. This single case exercises the whole funnel and is the first thing to run on any new build.

---

### TC-E2E-002
**Purchase journey when the user is already logged in**

| | |
|---|---|
| Priority | **P1** |
| Module | End-to-End |
| Journey stage | Conversion |
| Technique | End-to-end scenario (alternate entry state) |
| Risk covered | `R-22` — Checkout cannot be reached, or the cart is lost on the way into it |
| Automation candidate | Yes |

**Preconditions**

User is already logged in. Cart is empty.

**Steps**

1. Add two different products to the cart with different quantities
2. Open the cart and verify both lines and the total
3. Proceed to checkout
4. Verify no login prompt appears
5. Complete shipping, payment and review
6. Place the order and verify the confirmation

**Expected result**

Checkout proceeds straight to shipping with no login interruption. The rest of the journey behaves identically to the logged-out variant.

---

### TC-E2E-003
**Journey with a mid-flow correction: edit the cart before checkout**

| | |
|---|---|
| Priority | **P2** |
| Module | End-to-End |
| Journey stage | Conversion |
| Technique | End-to-end scenario (mid-flow modification) |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart is empty. User is logged in.

**Steps**

1. Add three different products to the cart
2. Open the cart and increase one line's quantity to 4
3. Remove a different line entirely
4. Verify the total against the remaining lines by hand
5. Complete checkout and verify the review screen matches the edited cart
6. Place the order

**Expected result**

The order placed reflects the edited cart exactly, not the original one. Edits made in the cart must propagate all the way to the review screen and the total.

---

### TC-E2E-004
**Abandoned journey resumed after an app restart**

| | |
|---|---|
| Priority | **P2** |
| Module | End-to-End |
| Journey stage | Conversion |
| Technique | End-to-end scenario (interrupted journey) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

Cart is empty. User is logged in.

**Steps**

1. Add two products to the cart
2. Proceed to checkout and fill in the shipping address
3. Force-stop the app: adb shell am force-stop com.saucelabs.mydemoapp.android
4. Relaunch the app
5. Determine where the app resumes and whether the cart is intact
6. Complete the purchase from wherever you land

**Expected result**

The cart survives. The user can complete the purchase without rebuilding it. Record whether the shipping data was retained — losing it is acceptable, losing the cart is not.

---

### TC-E2E-005
**Second consecutive purchase in the same session**

| | |
|---|---|
| Priority | **P2** |
| Module | End-to-End |
| Journey stage | Conversion |
| Technique | End-to-end scenario (repeat transaction) |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

User has just completed an order.

**Steps**

1. From the confirmation screen, tap continue shopping
2. Add a different product to the cart
3. Verify the badge shows only the new item
4. Complete a second full checkout
5. Verify the second review screen shows only the second order's contents

**Expected result**

The second order is completely independent of the first. No items, addresses or totals leak between orders. State left over from a previous order is a classic source of wrong-order defects.

---

## 7. What "done" looks like

- All five journeys executed and passing on the primary device.
- Journeys 1 and 2 additionally executed on a second device profile.
- Every total verified by hand calculation at both the cart and the review screen.
- Any journey that cannot be completed at all is a **release blocker**, regardless of how many
  individual feature tests pass.

---

## 8. Interview talking point

> "End-to-end cases find defects in the seams between features, which is where nobody owns the
> code. The example I use is the login handoff during checkout: a logged-out user with a full cart
> taps checkout, logs in, and lands back on the catalogue with an empty basket. Neither the login
> feature nor the cart feature is individually broken — the transition between them is, and only a
> journey that walks that exact path finds it. The one people forget is the second consecutive
> purchase in a session, which catches state leaking from the previous order."
