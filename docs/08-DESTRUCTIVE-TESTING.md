# 08 · Destructive Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**18 test cases** in this document — 6 P1, 8 P2, 4 P3 — across 9 modules.

---

## 1. What this type of testing is

**Destructive testing** deliberately abuses the application to find out how it fails.

Where negative testing walks the *defined* invalid paths, destructive testing walks the paths
nobody defined at all. You remove resources the app depends on, interrupt it at the worst possible
moment, and drive it faster than a human plausibly would — then look at the wreckage.

The mindset shift is important: you are not trying to confirm the app works. You are trying to
find the conditions under which it does not, and then judge whether those conditions are plausible
enough to matter. **"A user would never do that" is not a defence** if a passing bus, a low
battery or a flaky lift can do it for them.

Destructive testing is named explicitly in most mobile QA job specifications, and it is the type
most portfolios omit entirely. This file is the answer.

---

## 2. Why it matters for this application

Mobile is a hostile environment, and that is the whole argument.

A desktop web app runs on mains power, a stable network and a machine that will not kill it to
free memory. A mobile app runs on a device that loses signal in a lift, gets killed by the OS
while backgrounded, receives phone calls mid-form, rotates without warning, and runs out of
battery. **These are not edge cases on mobile. They are Tuesday.**

Three destructive scenarios in this file matter more than the rest, and all three sit in checkout:

**Duplicate order submission.** Tapping *Place Order* ten times rapidly. If the handler is
unguarded, a real system charges the customer more than once. This is the highest-severity
defect class in the application.

**Process death mid-checkout.** Killing the app between payment and confirmation. The state
afterwards must be unambiguous: either the order was placed and the cart is empty, or it was not
and the cart is intact. The failure mode to hunt for is the third outcome — empty cart, no order —
where the customer has silently lost their basket.

**Network loss at submission.** The app must not show a confirmation for an order that did not
happen. **A false success is worse than a visible failure**, because the customer stops watching.

---

## 3. Technique used

Destructive testing is exploratory in spirit but systematic in structure. Five attack
patterns are used here, and naming them is what separates this from "clicking around":

**1. Resource removal.** Take away something the app needs, mid-operation. Network, permission,
storage. The test is not "does it work offline" but "does it *tell the truth* about being offline".

**2. Lifecycle interruption.** Force the Android lifecycle transitions the OS would perform
under memory pressure: background, process death, activity recreation. The **"Don't keep
activities"** developer option is the single most effective tool here — it forces the recreation
that would otherwise only happen on a cheap phone with fifteen apps open, and it turns an
intermittent field crash into a reproducible one.

**3. Rapid repeated input.** Tap the same control faster than the app can respond. Finds
unguarded click handlers, duplicate submissions and stacked screens.

**4. Rapid state change.** Switch between states faster than transitions can complete. Finds race
conditions where the UI ends up showing one state while the data is in another.

**5. Volume and endurance.** Push quantities, item counts and session length well past typical.
Finds leaks, accumulating rounding errors and layouts that only break when full.

---

## 4. How to execute these cases

**Capture evidence as you go — destructive defects are often intermittent, and an
unreproduced crash is close to worthless to an engineer.**

Start a screen recording before each destructive case:

```
adb shell screenrecord /sdcard/destructive.mp4
```
Press `Ctrl+C` to stop, then:
```
adb pull /sdcard/destructive.mp4 ./evidence/
```

Clear the log first, reproduce, then capture — so the log contains only the failure:
```
adb logcat -c
```
…reproduce the failure…
```
adb logcat -d > evidence/BUG-NNN-logcat.txt
```

**Always report a hit rate, never a feeling.** "Intermittent — reproduced 3 of 5 attempts" lets an
engineer plan. "Sometimes crashes" does not. Attempt every destructive case **five times** and
record the count.

Useful commands:
```
:: kill the process, to simulate Android reclaiming memory
adb shell am force-stop com.saucelabs.mydemoapp.android

:: scripted rapid taps at a screen coordinate
adb shell input tap 500 1200

:: memory during a soak test
adb shell dumpsys meminfo com.saucelabs.mydemoapp.android
```

Enable **Developer options → Don't keep activities** before the lifecycle cases, and remember to
turn it off afterwards — leaving it on makes every subsequent test unrepresentative.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-CAT-013`](#tc-cat-013) | P2 | Catalog | Discovery | Catalogue loads without a network connection |
| [`TC-CAT-014`](#tc-cat-014) | P2 | Catalog | Discovery | Rapid repeated taps on a product tile open exactly one detail page |
| [`TC-SORT-009`](#tc-sort-009) | P2 | Sorting | Discovery | Rapidly switching between all four sort options leaves a correct final state |
| [`TC-PDP-016`](#tc-pdp-016) | P2 | Product Detail | Consideration | Adding to cart while offline behaves predictably |
| [`TC-PDP-017`](#tc-pdp-017) | P1 | Product Detail | Consideration | Rapid repeated taps on Add to cart add a defined, correct number of items |
| [`TC-CART-012`](#tc-cart-012) | P2 | Cart | Conversion | Rapid repeated taps on the remove control do not remove extra items |
| [`TC-CART-013`](#tc-cart-013) | P3 | Cart | Conversion | Cart layout holds up with a large number of items |
| [`TC-CHKR-008`](#tc-chkr-008) | P1 | Checkout — Review | Conversion | Rapid repeated taps on Place Order create exactly one order |
| [`TC-CHKR-009`](#tc-chkr-009) | P1 | Checkout — Review | Conversion | Placing an order with no network connection is handled honestly |
| [`TC-CHKR-010`](#tc-chkr-010) | P1 | Checkout — Review | Conversion | Killing the app mid-checkout leaves the cart and order state consistent |
| [`TC-NAV-006`](#tc-nav-006) | P2 | Menu & Navigation | Cross-cutting | Rapid switching between menu destinations leaves a correct final screen |
| [`TC-WEB-003`](#tc-web-003) | P3 | WebView | Cross-cutting | WebView handles an unreachable URL without hanging |
| [`TC-DRAW-003`](#tc-draw-003) | P3 | Drawing | Cross-cutting | Rotating the device while drawing does not crash the app |
| [`TC-XCUT-003`](#tc-xcut-003) | P1 | Cross-cutting | Cross-cutting | App survives repeated backgrounding and foregrounding |
| [`TC-XCUT-004`](#tc-xcut-004) | P1 | Cross-cutting | Cross-cutting | App recovers correctly from process death with Don't Keep Activities enabled |
| [`TC-XCUT-008`](#tc-xcut-008) | P2 | Cross-cutting | Cross-cutting | App handles an interruption from an incoming call or notification |
| [`TC-XCUT-015`](#tc-xcut-015) | P2 | Cross-cutting | Cross-cutting | App does not leak sensitive data into device logs |
| [`TC-XCUT-016`](#tc-xcut-016) | P3 | Cross-cutting | Cross-cutting | App remains stable under an extended continuous session |

---

## 6. Test cases — full detail

### TC-CAT-013
**Catalogue loads without a network connection**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Destructive (resource removal) |
| Risk covered | `R-14` — Loss of network produces an infinite spinner, a blank screen or a false success |
| Automation candidate | No |

**Preconditions**

Airplane mode can be toggled on the device.

**Steps**

1. Enable airplane mode
2. Force-stop the app and relaunch it
3. Observe the Catalog screen for up to 30 seconds
4. Disable airplane mode and pull to refresh if available

**Expected result**

Record the actual behaviour precisely. If the catalogue is bundled locally it should render fully offline. If it comes from the network, an error with a retry option must appear — never a permanent spinner or a blank grid with no explanation.

---

### TC-CAT-014
**Rapid repeated taps on a product tile open exactly one detail page**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Destructive (rapid repeated input) |
| Risk covered | `R-15` — Rapid repeated input causes duplicate actions, including duplicate orders |
| Automation candidate | No |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Tap a single product tile ten times in under two seconds
2. Observe the resulting screen
3. Press the system back button once and observe where you land

**Expected result**

Exactly one detail page opens. A single back press returns to the Catalog. If several stacked copies of the detail page opened, back would need to be pressed repeatedly — that is a navigation defect caused by an unguarded click handler.

---

### TC-SORT-009
**Rapidly switching between all four sort options leaves a correct final state**

| | |
|---|---|
| Priority | **P2** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Destructive (rapid state change) |
| Risk covered | `R-15` — Rapid repeated input causes duplicate actions, including duplicate orders |
| Automation candidate | No |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Switch between all four sort options as fast as the UI allows, at least fifteen changes
2. Stop on price ascending
3. Verify the sort indicator and the actual product order

**Expected result**

The indicator matches the last option selected, and the products are genuinely in that order. No crash, no duplicated products, no empty grid, no mismatch between the indicator and the real order.

---

### TC-PDP-016
**Adding to cart while offline behaves predictably**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Destructive (resource removal) |
| Risk covered | `R-14` — Loss of network produces an infinite spinner, a blank screen or a false success |
| Automation candidate | No |

**Preconditions**

User is on a product detail page.

**Steps**

1. Enable airplane mode
2. Tap Add to cart
3. Observe the immediate feedback and the cart badge
4. Disable airplane mode, open the cart and verify the contents

**Expected result**

Either the item is added locally and the badge updates, or a clear error explains the failure. What must not happen is a success indication for an action that did not persist — the badge and the actual cart contents must always agree.

---

### TC-PDP-017
**Rapid repeated taps on Add to cart add a defined, correct number of items**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Destructive (rapid repeated input) |
| Risk covered | `R-11` — Cart badge or contents do not match what the user actually added |
| Automation candidate | Yes |

**Preconditions**

Cart is empty. User is on a product detail page with quantity 1.

**Steps**

1. Reset App State so the cart is empty
2. Tap Add to cart ten times as fast as possible
3. Note the cart badge
4. Open the cart and compare the actual quantity and total against the badge

**Expected result**

The badge, the cart quantity and the cart total all agree with each other and with the number of taps that registered. A badge saying 10 while the cart holds 3 items is a high-severity defect because it misprices the order.

---

### TC-CART-012
**Rapid repeated taps on the remove control do not remove extra items**

| | |
|---|---|
| Priority | **P2** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Destructive (rapid repeated input) |
| Risk covered | `R-15` — Rapid repeated input causes duplicate actions, including duplicate orders |
| Automation candidate | No |

**Preconditions**

Cart contains three different products.

**Steps**

1. Record all three lines
2. Tap the remove control on the first line ten times in under two seconds
3. Observe the remaining lines, the badge and the total

**Expected result**

Exactly one line is removed. The remaining two are intact and the total matches them. Removing two or three lines from one intended action is an unguarded-handler defect.

---

### TC-CART-013
**Cart layout holds up with a large number of items**

| | |
|---|---|
| Priority | **P3** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Destructive (volume / stress) |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | No |

**Preconditions**

Cart is empty.

**Steps**

1. Add every product in the catalogue to the cart, several of them at a quantity above five
2. Open the cart and scroll through the whole list
3. Verify the total by hand against every line
4. Watch for layout breakage, overlapping text and scroll stutter

**Expected result**

All lines render correctly and are scrollable. The total is arithmetically correct across all lines. Neither the layout nor the arithmetic degrades as the list grows.

---

### TC-CHKR-008
**Rapid repeated taps on Place Order create exactly one order**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Destructive (rapid repeated input) |
| Risk covered | `R-15` — Rapid repeated input causes duplicate actions, including duplicate orders |
| Automation candidate | Yes |

**Preconditions**

User is on the review screen with a valid order.

**Steps**

1. Tap Place Order ten times in under two seconds
2. Observe how many confirmation screens appear
3. Press back and check the cart badge and contents

**Expected result**

Exactly one confirmation appears and the cart is emptied once. Duplicate submission is the highest-severity defect class at this point in the funnel: in a real system it would charge the customer more than once.

---

### TC-CHKR-009
**Placing an order with no network connection is handled honestly**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Destructive (resource removal) |
| Risk covered | `R-14` — Loss of network produces an infinite spinner, a blank screen or a false success |
| Automation candidate | No |

**Preconditions**

User is on the review screen.

**Steps**

1. Enable airplane mode
2. Tap Place Order
3. Observe the screen for up to 30 seconds
4. Disable airplane mode and check the cart and any order state

**Expected result**

Either a clear error appears with a retry option, or the order completes locally. What must never happen is a confirmation screen for an order that was not actually placed — a false success is worse than a visible failure.

---

### TC-CHKR-010
**Killing the app mid-checkout leaves the cart and order state consistent**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Destructive (process death at a critical step) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

User is on the review screen with a known cart.

**Steps**

1. Record the exact cart contents
2. Force-stop the app: adb shell am force-stop com.saucelabs.mydemoapp.android
3. Relaunch the app and open the cart
4. Determine whether the order was placed, and whether the cart is intact

**Expected result**

The state must be unambiguous: either the order was not placed and the cart is intact, or the order was placed and the cart is empty. A cart that is empty with no order placed means the customer silently lost their basket.

---

### TC-NAV-006
**Rapid switching between menu destinations leaves a correct final screen**

| | |
|---|---|
| Priority | **P2** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | Destructive (rapid state change) |
| Risk covered | `R-15` — Rapid repeated input causes duplicate actions, including duplicate orders |
| Automation candidate | No |

**Preconditions**

App launched.

**Steps**

1. Open the menu and tap a destination, then immediately open the menu again and tap a different one
2. Repeat rapidly across all destinations, at least fifteen switches
3. Settle on the Catalog and verify it is fully functional

**Expected result**

No crash, no frozen screen, no content from one screen bleeding into another. The final screen is fully rendered and interactive.

---

### TC-WEB-003
**WebView handles an unreachable URL without hanging**

| | |
|---|---|
| Priority | **P3** |
| Module | WebView |
| Journey stage | Cross-cutting |
| Technique | Destructive (unreachable resource) |
| Risk covered | `R-14` — Loss of network produces an infinite spinner, a blank screen or a false success |
| Automation candidate | No |

**Preconditions**

User is on the WebView screen.

**Steps**

1. Enter https://this-domain-does-not-exist-99887766.com and submit
2. Observe the screen for up to 30 seconds
3. Enable airplane mode, enter a valid URL and submit

**Expected result**

An error is shown within a reasonable timeout in both cases. There is no indefinite spinner and no blank screen with no explanation, and the app remains responsive.

---

### TC-DRAW-003
**Rotating the device while drawing does not crash the app**

| | |
|---|---|
| Priority | **P3** |
| Module | Drawing |
| Journey stage | Cross-cutting |
| Technique | Destructive (configuration change) |
| Risk covered | `R-12` — Rotation or a configuration change loses data or breaks the layout |
| Automation candidate | No |

**Preconditions**

User is on the drawing screen with strokes drawn.

**Steps**

1. Draw several strokes
2. Rotate the device to landscape
3. Observe whether the drawing survives and whether the layout is correct
4. Rotate back

**Expected result**

The app does not crash. Record whether the drawing is preserved across rotation — configuration-change data loss is a very common Android defect and worth reporting either way.

---

### TC-XCUT-003
**App survives repeated backgrounding and foregrounding**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Destructive (lifecycle stress) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

User is on a product detail page with items in the cart.

**Steps**

1. Background and foreground the app twenty times in quick succession
2. After the final return, verify the screen renders correctly
3. Verify the cart badge and contents are unchanged

**Expected result**

No crash and no ANR. The screen renders correctly and the cart is intact. Repeated lifecycle transitions are a reliable way to surface state-management defects.

---

### TC-XCUT-004
**App recovers correctly from process death with Don't Keep Activities enabled**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Destructive (forced activity recreation) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

Developer options: Don't keep activities is enabled.

**Steps**

1. Enable Don't keep activities in Developer options
2. Navigate Catalog to product detail to cart
3. Background the app, wait ten seconds, and return
4. Press back twice and observe each screen

**Expected result**

The app returns to the cart and unwinds correctly through the product detail page to the Catalog with no crash or blank screen. This setting simulates aggressive memory reclamation on a low-end device and is the single most effective way to find state-restoration bugs.

---

### TC-XCUT-008
**App handles an interruption from an incoming call or notification**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Destructive (external interruption) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

Ability to trigger an incoming call or a full-screen notification.

**Steps**

1. Begin filling in the checkout payment form
2. Trigger an incoming call or full-screen notification
3. Dismiss it and return to the app
4. Verify the form data and the cart

**Expected result**

The app returns to the payment form with entered data intact. Losing checkout data to a phone call is a very common real-world abandonment cause and a legitimate high-severity finding.

---

### TC-XCUT-015
**App does not leak sensitive data into device logs**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Security-adjacent inspection |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

ADB connected to the device.

**Steps**

1. Clear the log: adb logcat -c
2. Perform a full login and checkout in the app
3. Capture the log: adb logcat -d > logcat-check.txt
4. Search the file for the password, the card number and the security code you used

**Expected result**

No password, card number or security code appears in the log. Credentials written to logcat are readable by other tooling on the device and are a genuine privacy defect. Redact anything sensitive before attaching the log to a report.

---

### TC-XCUT-016
**App remains stable under an extended continuous session**

| | |
|---|---|
| Priority | **P3** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Destructive (soak / endurance) |
| Risk covered | `R-09` — App is slow to start, janky to scroll, or degrades over a session |
| Automation candidate | No |

**Preconditions**

App installed. Around thirty minutes available.

**Steps**

1. Use the app continuously for thirty minutes: browse, sort, open products, add and remove cart items, enter and leave checkout
2. Watch for progressive slowdown, growing memory use and rendering glitches
3. At the end, verify the cart total is still arithmetically correct

**Expected result**

Performance at the end of the session is comparable to the start. No crash, no progressive slowdown, and the cart arithmetic is still correct. Degradation over time usually indicates a leak.

---

## 7. What "done" looks like

- Every destructive case attempted five times with the hit rate recorded.
- Screen recording and logcat captured for every failure.
- Every crash has a logcat attached — a crash report without a stack trace usually cannot be fixed.
- "Don't keep activities" turned back off.

---

## 8. Interview talking point

> "My favourite destructive case in a checkout flow is tapping Place Order ten times in two
> seconds. If the handler isn't guarded, a real payment system charges the customer more than
> once — that's the highest-severity thing you can find in an e-commerce app. The second one is
> killing the process between payment and confirmation, because the state afterwards has to be
> unambiguous. Either the order went through and the cart is empty, or it didn't and the cart is
> intact. The failure I'm hunting for is the third outcome: empty cart, no order, and a customer
> who silently lost their basket and won't rebuild it."
