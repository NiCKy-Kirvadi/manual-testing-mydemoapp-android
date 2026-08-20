# 03 · Smoke Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**14 test cases** in this document — 14 P1, 0 P2, 0 P3 — across 9 modules.

---

## 1. What this type of testing is

**Smoke testing** answers one question: *is this build worth testing at all?*

It is a shallow, wide pass over the application's most critical paths. You touch every major
area once and check only that it works at the most basic level. You are not looking for edge
cases — you are looking for a reason to stop.

The name comes from electronics: you power up a new circuit board and see whether it releases
smoke. If it does, you don't bother testing the rest.

A smoke test is **binary and non-negotiable**. Either every case passes and testing proceeds,
or one fails and the build is rejected back to the engineers. There is no "mostly passed".

---

## 2. Why it matters for this application

This app has a purchase funnel, and a funnel has a property that makes smoke testing
especially valuable: **a break anywhere upstream blocks everything downstream**. If the catalogue
does not render, then sorting, product detail, cart, checkout and order placement are all
untestable — not failing, *untestable*. Reporting forty failures caused by one broken screen
wastes your time and buries the real defect.

So the smoke pack walks the funnel in order and stops at the first break:

    Catalog → Sort → Product Detail → Cart → Login → Shipping → Payment → Review → Order Placed

Running this first turns a potentially wasted day into a fifteen-minute answer.

---

## 3. Technique used

**Risk-based path selection.** There is no formal technique for choosing smoke cases;
there is a judgement call, and the judgement is:

1. Include it if a failure would block a large share of the remaining test suite.
2. Include it if a failure would make the app commercially useless (nobody can buy anything).
3. Exclude anything that only affects a minority path or a cosmetic outcome.
4. Keep the whole pack short enough to run before you have finished your first coffee.

Every case here also carries **P1** priority. Note the relationship carefully, because it is a
common point of confusion: the smoke pack is a **subset** of P1, not the whole of it. P1 means
"must pass before release"; Smoke means "must pass before testing even continues". The smoke pack
is the fast gate at the front; the full P1 set is the slower gate at the end.

---

## 4. How to execute these cases

Run these **in the listed order**, because they build on each other.

- Stop at the first failure. Do not continue and do not log the downstream cases as failures —
  log them as **Blocked**, and name the blocking defect. That distinction matters: "Blocked" tells
  a manager the coverage gap is not your fault, "Failed" implies you tested it and it broke.
- Total expected duration: **15 to 20 minutes** on a real device.
- Run it on a freshly reset app: open the menu and tap **Reset App State** first, so you are not
  testing on top of state left over from a previous session.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-001`](#tc-login-001) | P1 | Login & Authentication | Account | Login screen is reachable from the menu and renders all its controls |
| [`TC-LOGIN-003`](#tc-login-003) | P1 | Login & Authentication | Account | Valid credentials log the user in successfully |
| [`TC-CAT-001`](#tc-cat-001) | P1 | Catalog | Discovery | Catalog opens on launch and displays the product grid |
| [`TC-CAT-002`](#tc-cat-002) | P1 | Catalog | Discovery | Every product tile shows an image, a name and a price |
| [`TC-CAT-006`](#tc-cat-006) | P1 | Catalog | Discovery | Tapping a product tile opens the matching product detail page |
| [`TC-SORT-001`](#tc-sort-001) | P1 | Sorting | Discovery | Sort control opens and lists all four sort options |
| [`TC-PDP-001`](#tc-pdp-001) | P1 | Product Detail | Consideration | Product detail page displays name, price, image, rating and description |
| [`TC-CART-002`](#tc-cart-002) | P1 | Cart | Conversion | Cart lists every added product with correct name, colour, price and quantity |
| [`TC-CHKS-002`](#tc-chks-002) | P1 | Checkout — Shipping | Conversion | Shipping address form renders all its fields with mandatory markers |
| [`TC-CHKS-005`](#tc-chks-005) | P1 | Checkout — Shipping | Conversion | Valid shipping data allows progression to the payment screen |
| [`TC-CHKP-001`](#tc-chkp-001) | P1 | Checkout — Payment | Conversion | Payment form renders all fields and the billing-address checkbox |
| [`TC-CHKP-003`](#tc-chkp-003) | P1 | Checkout — Payment | Conversion | Valid payment data allows progression to the order review screen |
| [`TC-CHKR-006`](#tc-chkr-006) | P1 | Checkout — Review | Conversion | Place Order completes the purchase and shows a confirmation screen |
| [`TC-NAV-001`](#tc-nav-001) | P1 | Menu & Navigation | Cross-cutting | Menu opens and lists every expected destination |

---

## 6. Test cases — full detail

### TC-LOGIN-001
**Login screen is reachable from the menu and renders all its controls**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Specification-based (use case) |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

App installed and launched. User is logged out.

**Steps**

1. Tap the hamburger menu icon (top left)
2. Tap 'Log In'
3. Observe the screen

**Expected result**

The Login screen opens with: a 'Login' heading, the helper text about selecting a username, a Usernames list, a Username field, a Password field and a Login button. Nothing is cut off or overlapping.

---

### TC-LOGIN-003
**Valid credentials log the user in successfully**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Specification-based (use case) |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen and logged out.

**Steps**

1. Tap the standard (non-locked-out) username in the list to auto-populate credentials
2. Tap Login
3. Observe the resulting screen
4. Open the menu and check the Log In / Log Out entry

**Expected result**

Login succeeds. The user is taken to the Catalog (or back to the screen that required login). The menu now shows 'Log Out' instead of 'Log In'. No error message is displayed.

---

### TC-CAT-001
**Catalog opens on launch and displays the product grid**

| | |
|---|---|
| Priority | **P1** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Specification-based (use case) |
| Risk covered | `R-06` — Catalogue fails to render, blocking all downstream discovery |
| Automation candidate | Yes |

**Preconditions**

App freshly launched.

**Steps**

1. Force-stop the app, then launch it from the icon
2. Observe the first screen
3. Confirm a product grid is visible

**Expected result**

The app opens on the Catalog (Products) screen without a crash. A grid of products is rendered. The header shows the app logo, the menu icon and the cart icon.

---

### TC-CAT-002
**Every product tile shows an image, a name and a price**

| | |
|---|---|
| Priority | **P1** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Specification-based / exhaustive inspection |
| Risk covered | `R-06` — Catalogue fails to render, blocking all downstream discovery |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Observe every product tile visible without scrolling
2. Scroll to the end of the catalogue
3. For each tile, check for an image, a product name and a price

**Expected result**

No tile has a missing or broken image, an empty name, or a missing price. No tile shows placeholder text such as 'null', 'undefined' or a raw resource identifier.

---

### TC-CAT-006
**Tapping a product tile opens the matching product detail page**

| | |
|---|---|
| Priority | **P1** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Specification-based (use case) |
| Risk covered | `R-08` — Product detail shows the wrong product or incomplete information |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Note the exact name, price and image of the third product tile
2. Tap that tile
3. Compare the product detail page against what you noted

**Expected result**

The detail page shows the same product: identical name, identical price, and an image matching the tile. Opening the third tile must not open the first product — an index-off-by-one is a classic list defect.

---

### TC-SORT-001
**Sort control opens and lists all four sort options**

| | |
|---|---|
| Priority | **P1** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Specification-based (use case) |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Tap the sort control in the header
2. Observe the options presented
3. Note which option is currently indicated as selected

**Expected result**

Four options are listed: name ascending, name descending, price ascending and price descending. The currently active option is visually indicated so the user knows the present state.

---

### TC-PDP-001
**Product detail page displays name, price, image, rating and description**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Specification-based (use case) |
| Risk covered | `R-08` — Product detail shows the wrong product or incomplete information |
| Automation candidate | Yes |

**Preconditions**

User has opened a product from the Catalog.

**Steps**

1. Observe the product detail page from top to bottom
2. Scroll to the end of the page
3. Confirm the presence of: product name, price, at least one image, a star rating, a description and the product highlights section

**Expected result**

All six elements are present and populated. No section shows placeholder or truncated content. The description is readable prose with no raw markup or encoding errors.

---

### TC-CART-002
**Cart lists every added product with correct name, colour, price and quantity**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Specification-based (use case) |
| Risk covered | `R-11` — Cart badge or contents do not match what the user actually added |
| Automation candidate | Yes |

**Preconditions**

Cart is empty.

**Steps**

1. Add product A in a specific colour, quantity 2
2. Add product B in a different colour, quantity 1
3. Open the cart
4. Compare every field against what you selected

**Expected result**

Both products are listed with the exact name, colour, unit price and quantity that were chosen. Nothing is missing, duplicated, or attributed to the wrong product.

---

### TC-CHKS-002
**Shipping address form renders all its fields with mandatory markers**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Specification-based (use case) |
| Risk covered | `R-22` — Checkout cannot be reached, or the cart is lost on the way into it |
| Automation candidate | Yes |

**Preconditions**

User is logged in with items in the cart and has proceeded to checkout.

**Steps**

1. Reach the shipping address screen
2. List every field on screen
3. Note which fields are marked mandatory and which are optional

**Expected result**

The form shows full name, two address lines, city, state or region, zip code and country. Mandatory fields are marked consistently (typically with an asterisk) and optional fields are not. Inconsistent marking misleads the user into avoidable errors.

---

### TC-CHKS-005
**Valid shipping data allows progression to the payment screen**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Specification-based (happy path) |
| Risk covered | `R-22` — Checkout cannot be reached, or the cart is lost on the way into it |
| Automation candidate | Yes |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Fill every mandatory field with plausible test data — use a fictional name and address only, never real personal data
2. Leave the optional address line 2 empty
3. Tap continue to payment
4. Observe the resulting screen

**Expected result**

The payment screen opens. The optional field being empty does not block progress. Entered data is retained if the user navigates back.

---

### TC-CHKP-001
**Payment form renders all fields and the billing-address checkbox**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Specification-based (use case) |
| Risk covered | `R-26` — Payment form does not collect or present payment details correctly |
| Automation candidate | Yes |

**Preconditions**

User has completed the shipping step.

**Steps**

1. Reach the payment screen
2. List every field and control on screen
3. Read the explanatory text about when the card will be charged

**Expected result**

The form shows card number, expiration date and security code, all marked mandatory, plus a checkbox stating that the billing address matches the shipping address. Text reassuring the user they will not be charged until review is present and legible.

---

### TC-CHKP-003
**Valid payment data allows progression to the order review screen**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Specification-based (happy path) |
| Risk covered | `R-26` — Payment form does not collect or present payment details correctly |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen.

**Steps**

1. Enter a well-formed test card number — use only obvious dummy values such as 4111111111111111, never a real card
2. Enter a future expiry date and a three-digit security code
3. Leave the billing-address checkbox ticked
4. Tap review order

**Expected result**

The review screen opens showing the order. No real payment is processed — this is a demo application with no payment backend.

---

### TC-CHKR-006
**Place Order completes the purchase and shows a confirmation screen**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Specification-based (happy path) |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

User is on the review screen with a valid order.

**Steps**

1. Tap Place Order
2. Observe the resulting screen
3. Record every element of the confirmation message

**Expected result**

A confirmation screen appears thanking the user and stating the order is on its way, with an option to continue shopping. The user is never left on the review screen with no feedback about whether the order went through.

---

### TC-NAV-001
**Menu opens and lists every expected destination**

| | |
|---|---|
| Priority | **P1** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | Specification-based (use case) |
| Risk covered | `R-30` — Navigation is broken, trapping the user or exposing the wrong screen |
| Automation candidate | Yes |

**Preconditions**

App launched.

**Steps**

1. Tap the hamburger menu icon
2. List every entry shown, scrolling if necessary
3. Note the login state entry

**Expected result**

The menu opens and lists the catalogue, the webview, the QR scanner, geo location, drawing, about, reset app state, biometrics, virtual USB and the login or logout entry. Record the full list you actually see. No entry is blank or duplicated.

---

## 7. What "done" looks like

- 100% of these cases pass → the build is accepted for full testing.
- Any case fails → **the build is rejected**. Raise the defect, notify the team, and stop testing.
  Do not spend the day working around a broken build; that is how a team learns that "red" is
  survivable.

---

## 8. Interview talking point

> "The first thing I run on any build is a smoke pack that walks the purchase funnel in
> order — catalogue, product, cart, login, checkout, order placed. It's about fifteen minutes,
> and it exists to fail fast. The important detail is what I do with the downstream cases when it
> breaks: I mark them Blocked, not Failed, and I name the blocking defect. That keeps the coverage
> report honest — a manager reading forty 'Failed' rows thinks the build is a disaster, when
> actually one screen is broken and thirty-nine cases were never reachable."
