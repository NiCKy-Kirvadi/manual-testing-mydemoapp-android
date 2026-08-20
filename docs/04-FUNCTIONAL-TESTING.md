# 04 · Functional Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**61 test cases** in this document — 28 P1, 22 P2, 11 P3 — across 14 modules.

---

## 1. What this type of testing is

**Functional testing** verifies that each feature does what it is supposed to do.
You take a requirement — stated or reasonably inferred — and confirm the software delivers it.

It is the largest category in almost every test suite, and it is the one most people mean when
they say "testing". It answers *"does the feature work?"* on the paths a real user actually takes.

Functional testing is **positive** by default: valid inputs, expected sequences, reasonable
conditions. The deliberately awkward stuff lives in the negative, boundary and destructive
documents, which is why they are separate files here — mixing them makes both harder to
prioritise and to report on.

---

## 2. Why it matters for this application

For an e-commerce app, functional correctness is not evenly important. A defect in the
product description is unfortunate; a defect in the cart total is a refund, a support ticket and
a compliance problem. So the functional cases below are weighted heavily toward three areas:

**1. Arithmetic.** Quantity × unit price = line total. Sum of lines + delivery = order total.
Every one of these is asserted by hand-calculation, because a total is the single number the
customer is legally agreeing to pay.

**2. Cross-screen consistency.** The same product's price must be identical on the catalogue,
the product page, the cart and the order review. Four screens, one number. Divergence between
them is one of the most damaging defect classes in retail software because it destroys trust in
the total.

**3. State that must survive.** Cart contents, login session, selected sort. These are asserted
across navigation, backgrounding and process death — because state that survives the UI but not
the process passes every casual manual check and fails on a real customer's phone.

---

## 3. Technique used

Three formal techniques are used, and each case names which one it comes from:

**Specification-based (use case) testing** — derive the case from the intended user journey.
"The user selects a colour, sets a quantity, and adds to cart" becomes a case that does exactly
that and verifies each observable outcome.

**State transition testing** — model the feature as states and transitions, then test the
transitions rather than the states. The cart is a state machine: empty → one item → many items →
empty again. The bugs live in the *arrows*, not the boxes: what happens on the transition
back to empty, or when the process dies mid-transition.

**Consistency oracles** — when there is no requirements document to compare against (as here),
you compare the app against *itself*. If the catalogue says $29.99 and the cart says $29.90, you
do not need a specification to know one of them is wrong. This is the most useful technique
available when testing software you did not build, and it is worth naming explicitly in an
interview.

---

## 4. How to execute these cases

- Work module by module in the order listed; the modules follow the customer journey.
- For every case involving money, **calculate the expected value by hand before you look at what
  the app shows**. Reading the app's number first and then deciding whether it "looks right" is
  how arithmetic defects get missed — you will unconsciously rationalise it.
- Record the **actual** result even when it passes. "Pass" alone is not evidence; "Pass — total
  $64.97 matched hand calculation of 2 × $29.49 + $5.99" is.
- **This app prices in US dollars** — its delivery charge is a hardcoded `$5.99`. Record whatever
  currency symbol is actually on your screen; do not assume euros.
- Use only fictional names and obvious dummy card numbers. Never enter real personal or payment
  data, even into a demo app.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-002`](#tc-login-002) | P1 | Login & Authentication | Account | Tapping a listed username auto-populates both the username and password fields |
| [`TC-LOGIN-004`](#tc-login-004) | P1 | Login & Authentication | Account | The locked-out user is refused with a clear, specific message |
| [`TC-LOGIN-010`](#tc-login-010) | P1 | Login & Authentication | Account | Password input is masked and not readable on screen |
| [`TC-LOGIN-017`](#tc-login-017) | P1 | Login & Authentication | Account | Log Out returns the user to a logged-out state with a confirmation |
| [`TC-LOGIN-020`](#tc-login-020) | P2 | Login & Authentication | Account | Login state survives the app being killed and relaunched |
| [`TC-LOGIN-021`](#tc-login-021) | P2 | Login & Authentication | Account | Reset App State clears the session and the cart |
| [`TC-CAT-003`](#tc-cat-003) | P2 | Catalog | Discovery | The catalogue contains a stable, repeatable number of products |
| [`TC-CAT-004`](#tc-cat-004) | P2 | Catalog | Discovery | Every product tile displays a star rating |
| [`TC-CAT-005`](#tc-cat-005) | P1 | Catalog | Discovery | Prices are displayed in a consistent currency format across the whole catalogue |
| [`TC-CAT-008`](#tc-cat-008) | P2 | Catalog | Discovery | Scroll position is preserved when returning from a product detail page |
| [`TC-CAT-009`](#tc-cat-009) | P1 | Catalog | Discovery | Cart badge on the Catalog header reflects the true cart contents |
| [`TC-SORT-002`](#tc-sort-002) | P1 | Sorting | Discovery | Sort by name ascending orders the catalogue A to Z |
| [`TC-SORT-003`](#tc-sort-003) | P2 | Sorting | Discovery | Sort by name descending orders the catalogue Z to A |
| [`TC-SORT-004`](#tc-sort-004) | P1 | Sorting | Discovery | Sort by price ascending never steps down in price |
| [`TC-SORT-005`](#tc-sort-005) | P2 | Sorting | Discovery | Sort by price descending never steps up in price |
| [`TC-SORT-006`](#tc-sort-006) | P1 | Sorting | Discovery | Sorting changes only the order, never the set of products |
| [`TC-SORT-007`](#tc-sort-007) | P2 | Sorting | Discovery | The selected sort option persists when returning from a product detail page |
| [`TC-SORT-010`](#tc-sort-010) | P3 | Sorting | Discovery | Sort persists across an app restart |
| [`TC-PDP-002`](#tc-pdp-002) | P1 | Product Detail | Consideration | The price on the detail page matches the price on the catalogue tile |
| [`TC-PDP-003`](#tc-pdp-003) | P1 | Product Detail | Consideration | Colour variant selection updates the displayed product image |
| [`TC-PDP-004`](#tc-pdp-004) | P1 | Product Detail | Consideration | Quantity can be increased and decreased with the stepper controls |
| [`TC-PDP-007`](#tc-pdp-007) | P1 | Product Detail | Consideration | Add to cart increments the cart badge by exactly the selected quantity |
| [`TC-PDP-008`](#tc-pdp-008) | P2 | Product Detail | Consideration | Adding the same product twice consolidates rather than duplicating |
| [`TC-PDP-009`](#tc-pdp-009) | P3 | Product Detail | Consideration | Star rating control responds to taps and reflects the selected rating |
| [`TC-PDP-010`](#tc-pdp-010) | P2 | Product Detail | Consideration | Product image gallery can be swiped through in both directions |
| [`TC-PDP-011`](#tc-pdp-011) | P2 | Product Detail | Consideration | Product description and highlights are complete and free of encoding errors |
| [`TC-PDP-012`](#tc-pdp-012) | P3 | Product Detail | Consideration | Social media links on the product page open the correct destinations |
| [`TC-PDP-013`](#tc-pdp-013) | P1 | Product Detail | Consideration | Back navigation from the product detail page returns to the Catalog |
| [`TC-CART-001`](#tc-cart-001) | P1 | Cart | Conversion | Empty cart shows a helpful empty state with a way forward |
| [`TC-CART-003`](#tc-cart-003) | P1 | Cart | Conversion | Cart total equals the sum of unit price times quantity for every line |
| [`TC-CART-004`](#tc-cart-004) | P1 | Cart | Conversion | Increasing quantity inside the cart updates the line and the total correctly |
| [`TC-CART-005`](#tc-cart-005) | P1 | Cart | Conversion | Decreasing quantity inside the cart updates the line and the total correctly |
| [`TC-CART-007`](#tc-cart-007) | P1 | Cart | Conversion | Remove item deletes only the intended line |
| [`TC-CART-008`](#tc-cart-008) | P2 | Cart | Conversion | Removing every item returns the cart to its empty state |
| [`TC-CART-009`](#tc-cart-009) | P1 | Cart | Conversion | Cart contents survive an app restart |
| [`TC-CART-010`](#tc-cart-010) | P2 | Cart | Conversion | Cart survives the app being backgrounded for an extended period |
| [`TC-CHKS-001`](#tc-chks-001) | P1 | Checkout — Shipping | Conversion | Checkout requires login when the user is not authenticated |
| [`TC-CHKS-006`](#tc-chks-006) | P2 | Checkout — Shipping | Conversion | Optional fields are genuinely optional |
| [`TC-CHKS-011`](#tc-chks-011) | P2 | Checkout — Shipping | Conversion | Back navigation from shipping preserves the cart and the entered data |
| [`TC-CHKP-008`](#tc-chkp-008) | P2 | Checkout — Payment | Conversion | Security code input is masked or otherwise protected on screen |
| [`TC-CHKP-010`](#tc-chkp-010) | P2 | Checkout — Payment | Conversion | Unticking the billing-address checkbox reveals a billing address form |
| [`TC-CHKP-011`](#tc-chkp-011) | P3 | Checkout — Payment | Conversion | Payment card type indicator responds to the card number entered |
| [`TC-CHKP-012`](#tc-chkp-012) | P2 | Checkout — Payment | Conversion | Back navigation from payment returns to shipping with data intact |
| [`TC-CHKR-001`](#tc-chkr-001) | P1 | Checkout — Review | Conversion | Review screen displays the delivery address exactly as entered |
| [`TC-CHKR-002`](#tc-chkr-002) | P1 | Checkout — Review | Conversion | Review screen displays a masked payment method, not the full card number |
| [`TC-CHKR-003`](#tc-chkr-003) | P1 | Checkout — Review | Conversion | Review screen lists every ordered product with correct quantity and price |
| [`TC-CHKR-004`](#tc-chkr-004) | P1 | Checkout — Review | Conversion | Order total equals the item subtotal plus the stated delivery charge |
| [`TC-CHKR-005`](#tc-chkr-005) | P1 | Checkout — Review | Conversion | Delivery charge is stated explicitly before the order is placed |
| [`TC-CHKR-007`](#tc-chkr-007) | P1 | Checkout — Review | Conversion | Cart is emptied after a successful order |
| [`TC-NAV-002`](#tc-nav-002) | P1 | Menu & Navigation | Cross-cutting | Every menu entry opens its own distinct screen |
| [`TC-NAV-003`](#tc-nav-003) | P2 | Menu & Navigation | Cross-cutting | Menu can be dismissed without selecting anything |
| [`TC-NAV-004`](#tc-nav-004) | P2 | Menu & Navigation | Cross-cutting | Back button from the first screen exits rather than looping |
| [`TC-NAV-005`](#tc-nav-005) | P2 | Menu & Navigation | Cross-cutting | Deep back-stack navigation unwinds one screen at a time |
| [`TC-WEB-001`](#tc-web-001) | P2 | WebView | Cross-cutting | WebView screen accepts a valid HTTPS URL and renders the page |
| [`TC-GEO-001`](#tc-geo-001) | P3 | Geo Location | Cross-cutting | Geo location screen requests permission and then reports coordinates |
| [`TC-GEO-003`](#tc-geo-003) | P3 | Geo Location | Cross-cutting | Start and stop observing controls behave as labelled |
| [`TC-DRAW-001`](#tc-draw-001) | P3 | Drawing | Cross-cutting | Drawing pad records strokes and the clear control erases them |
| [`TC-DRAW-002`](#tc-draw-002) | P3 | Drawing | Cross-cutting | Save control on the drawing pad completes without error |
| [`TC-QR-001`](#tc-qr-001) | P3 | QR Scanner | Cross-cutting | QR scanner requests camera permission before opening the camera |
| [`TC-QR-003`](#tc-qr-003) | P3 | QR Scanner | Cross-cutting | Scanning a QR code containing a URL opens that URL |
| [`TC-XCUT-020`](#tc-xcut-020) | P3 | Cross-cutting | Cross-cutting | Uninstalling and reinstalling the app produces a genuinely clean state |

---

## 6. Test cases — full detail

### TC-LOGIN-002
**Tapping a listed username auto-populates both the username and password fields**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Specification-based (use case) |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Read the helper text on screen — it states that tapping a username auto-populates the credentials
2. Tap the first listed username
3. Observe the Username field
4. Observe the Password field

**Expected result**

Both fields are populated. The Username field contains exactly the username that was tapped. The Password field contains a masked value. No field is left empty.

---

### TC-LOGIN-004
**The locked-out user is refused with a clear, specific message**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Specification-based (error guessing) |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen. The list marks one username as locked out.

**Steps**

1. Tap the username labelled as locked out to auto-populate credentials
2. Tap Login
3. Read the error message exactly as displayed
4. Confirm whether the user is still on the Login screen

**Expected result**

Login is refused. An error states the user has been locked out — it does not say 'wrong password', because that would be misleading. The user remains on the Login screen with the entered username retained.

---

### TC-LOGIN-010
**Password input is masked and not readable on screen**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Security-adjacent / specification-based |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the Login screen.

**Steps**

1. Tap the Password field
2. Type a password slowly, watching the field
3. Take a screenshot of the screen
4. If a show/hide toggle exists, use it and observe

**Expected result**

Characters are replaced with dots or asterisks. At most the last typed character is briefly visible, which is standard Android behaviour. The screenshot contains no readable password. If a reveal toggle exists it defaults to hidden.

---

### TC-LOGIN-017
**Log Out returns the user to a logged-out state with a confirmation**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | State transition testing |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

User is logged in.

**Steps**

1. Open the menu
2. Tap 'Log Out'
3. Read any confirmation message
4. Reopen the menu and check the Log In / Log Out entry

**Expected result**

A confirmation message states the user has been logged out. The menu now shows 'Log In'. Any screen that requires authentication redirects to Login rather than showing stale data.

---

### TC-LOGIN-020
**Login state survives the app being killed and relaunched**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | State transition testing (process death) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | Yes |

**Preconditions**

User is logged in.

**Steps**

1. Confirm the menu shows 'Log Out'
2. Force-stop the app: adb shell am force-stop com.saucelabs.mydemoapp.android
3. Relaunch the app from the icon
4. Open the menu

**Expected result**

Nothing in the app states whether the login session survives a restart, so either result is defensible. Record the actual behaviour precisely. If the session is lost, raise it as a question for the Product Owner rather than a defect, because the intended behaviour is unknown.

---

### TC-LOGIN-021
**Reset App State clears the session and the cart**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | State transition testing |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | Yes |

**Preconditions**

User is logged in with at least one item in the cart.

**Steps**

1. Note the cart badge count and that the menu shows 'Log Out'
2. Open the menu and tap 'Reset App State'
3. Observe the cart badge
4. Reopen the menu and check the login entry

**Expected result**

The cart badge returns to empty and the menu shows 'Log In'. Reset App State genuinely clears all app state, not just part of it. A reset that clears the cart but leaves the session is an inconsistency worth raising.

---

### TC-CAT-003
**The catalogue contains a stable, repeatable number of products**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Specification-based (fixed-data oracle) |
| Risk covered | `R-06` — Catalogue fails to render, blocking all downstream discovery |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Scroll from the top to the very bottom, counting products
2. Record the total
3. Reset App State from the menu, return to Catalog and count again

**Expected result**

Both counts are identical. This app has a fixed local catalogue, so the count is a reliable oracle: any variation between runs indicates a rendering or data-loading defect rather than changing stock.

---

### TC-CAT-004
**Every product tile displays a star rating**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Specification-based / boundary awareness |
| Risk covered | `R-06` — Catalogue fails to render, blocking all downstream discovery |
| Automation candidate | No |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Observe the rating indicator on each visible tile
2. Confirm the number of filled stars is between 0 and 5
3. Scroll through the whole catalogue repeating the check

**Expected result**

Every tile shows a rating control. No rating shows more than five stars or a negative value. The rating is visually distinguishable from the price and the name.

---

### TC-CAT-005
**Prices are displayed in a consistent currency format across the whole catalogue**

| | |
|---|---|
| Priority | **P1** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Static consistency oracle |
| Risk covered | `R-07` — The same price is displayed inconsistently across screens |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Record the exact price string of every product, including the currency symbol and its position
2. Compare the formats against each other

**Expected result**

Every price uses the same currency symbol in the same position with the same number of decimal places. A mix of formats (for example one price with two decimals and another with none) is an inconsistency defect.

---

### TC-CAT-008
**Scroll position is preserved when returning from a product detail page**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | State transition testing |
| Risk covered | `R-10` — Navigation loses sort, scroll position or entered form data |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Scroll down to the last product in the catalogue
2. Tap it to open the detail page
3. Press the system back button
4. Observe the scroll position

**Expected result**

The catalogue is restored at the same scroll position with the same product visible. Being thrown back to the top of the list forces the user to redo their browsing and is a real usability defect.

---

### TC-CAT-009
**Cart badge on the Catalog header reflects the true cart contents**

| | |
|---|---|
| Priority | **P1** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | State transition testing |
| Risk covered | `R-11` — Cart badge or contents do not match what the user actually added |
| Automation candidate | Yes |

**Preconditions**

Cart is empty. User is on the Catalog screen.

**Steps**

1. Note the cart badge with an empty cart
2. Open a product, add one item to the cart, press back
3. Observe the badge
4. Add a second, different product and observe the badge again

**Expected result**

The badge starts empty or at zero, shows 1 after the first add, and 2 after the second. The number always equals the quantity actually in the cart.

---

### TC-SORT-002
**Sort by name ascending orders the catalogue A to Z**

| | |
|---|---|
| Priority | **P1** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Ordering invariant |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Select the name-ascending sort option
2. Record every product name from top to bottom in display order
3. Compare each consecutive pair alphabetically

**Expected result**

No product name sorts alphabetically before the name above it. The comparison is case-insensitive: mixed-case names must not cluster all uppercase entries before all lowercase ones, which would indicate a raw byte-order sort rather than a locale-aware one.

---

### TC-SORT-003
**Sort by name descending orders the catalogue Z to A**

| | |
|---|---|
| Priority | **P2** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Ordering invariant (symmetry oracle) |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Select the name-descending sort option
2. Record every product name in display order
3. Compare each consecutive pair

**Expected result**

The sequence is the exact reverse of the name-ascending sequence. If the two lists are not mirror images of each other, one of the two sorts is wrong.

---

### TC-SORT-004
**Sort by price ascending never steps down in price**

| | |
|---|---|
| Priority | **P1** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Ordering invariant |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Select the price-ascending sort option
2. Record every product price in display order
3. Check every consecutive pair for a decrease

**Expected result**

No price is lower than the price above it. Equal adjacent prices are acceptable. A single downward step anywhere in the list, including at a scroll boundary, is a failure.

---

### TC-SORT-005
**Sort by price descending never steps up in price**

| | |
|---|---|
| Priority | **P2** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Ordering invariant (symmetry oracle) |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Select the price-descending sort option
2. Record every product price in display order
3. Check every consecutive pair for an increase

**Expected result**

No price is higher than the price above it. The sequence is the exact reverse of the price-ascending sequence.

---

### TC-SORT-006
**Sorting changes only the order, never the set of products**

| | |
|---|---|
| Priority | **P1** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Set-equality invariant |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. With the default sort, record the complete set of product names and the total count
2. Apply each of the four sort options in turn
3. After each, record the complete set of names and the count

**Expected result**

All four sorted lists contain exactly the same products and the same total count as the unsorted list. Sorting must never add, drop or duplicate a product. This is the single most valuable sort assertion because it catches data-loss defects that an ordering check alone would miss.

---

### TC-SORT-007
**The selected sort option persists when returning from a product detail page**

| | |
|---|---|
| Priority | **P2** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | State transition testing |
| Risk covered | `R-10` — Navigation loses sort, scroll position or entered form data |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen with price ascending applied.

**Steps**

1. Apply the price-ascending sort and confirm the order
2. Open any product detail page
3. Press the system back button
4. Observe the sort indicator and the product order

**Expected result**

The sort indicator still shows price ascending and the products are still in ascending price order. Silently reverting to the default sort makes the user redo their work.

---

### TC-SORT-010
**Sort persists across an app restart**

| | |
|---|---|
| Priority | **P3** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | State transition testing (process death) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Apply the price-descending sort
2. Force-stop the app: adb shell am force-stop com.saucelabs.mydemoapp.android
3. Relaunch and observe the Catalog

**Expected result**

Nothing in the app states whether the sort persists across a restart, so either result is a valid finding. Record the actual behaviour and raise it as a question for the Product Owner rather than a defect.

---

### TC-PDP-002
**The price on the detail page matches the price on the catalogue tile**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Consistency oracle across screens |
| Risk covered | `R-07` — The same price is displayed inconsistently across screens |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Record the exact price shown on five different product tiles
2. Open each of those five products in turn
3. Compare the detail-page price against the recorded tile price

**Expected result**

The price is identical on both screens for all five products, including the currency symbol and decimals. A mismatch is a trust defect: the customer believes they were shown one price and may be charged another.

---

### TC-PDP-003
**Colour variant selection updates the displayed product image**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | State transition testing |
| Risk covered | `R-08` — Product detail shows the wrong product or incomplete information |
| Automation candidate | Yes |

**Preconditions**

User is on a product detail page for a product with multiple colour options.

**Steps**

1. Note the currently selected colour and the main image
2. Select a different colour
3. Observe the main image and the colour indicator
4. Select a third colour and observe again

**Expected result**

The main image changes to match the newly selected colour each time. The selected colour is clearly indicated. The image of the previously selected colour does not remain on screen.

---

### TC-PDP-004
**Quantity can be increased and decreased with the stepper controls**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Specification-based (use case) |
| Risk covered | `R-17` — Quantity controls allow an invalid quantity or miscalculate the line total |
| Automation candidate | Yes |

**Preconditions**

User is on a product detail page. Quantity shows its default value.

**Steps**

1. Note the default quantity
2. Tap the increase control three times, observing the value after each tap
3. Tap the decrease control twice, observing the value after each tap

**Expected result**

The quantity increments by exactly one per tap and decrements by exactly one per tap. The displayed value is always correct and never skips or lags behind the taps.

---

### TC-PDP-007
**Add to cart increments the cart badge by exactly the selected quantity**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | State transition testing |
| Risk covered | `R-11` — Cart badge or contents do not match what the user actually added |
| Automation candidate | Yes |

**Preconditions**

Cart is empty. User is on a product detail page.

**Steps**

1. Confirm the cart badge shows an empty cart
2. Set the quantity to 3
3. Tap Add to cart
4. Observe the cart badge

**Expected result**

The badge shows exactly 3. It does not show 1, ignoring the quantity, and it does not show 4, double-counting. Getting this wrong directly misprices the order.

---

### TC-PDP-008
**Adding the same product twice consolidates rather than duplicating**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | State transition testing |
| Risk covered | `R-11` — Cart badge or contents do not match what the user actually added |
| Automation candidate | Yes |

**Preconditions**

Cart is empty.

**Steps**

1. Open a product, set quantity 2, add to cart
2. Press back to the Catalog
3. Open the same product again, set quantity 3, add to cart
4. Open the cart and inspect the entries

**Expected result**

Record the actual behaviour. Either the cart holds one line for that product with quantity 5, or two separate lines of 2 and 3. Whichever it is, the badge count and the cart total must agree with what is displayed — an inconsistency between them is the defect.

---

### TC-PDP-009
**Star rating control responds to taps and reflects the selected rating**

| | |
|---|---|
| Priority | **P3** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Boundary awareness / state transition |
| Risk covered | `R-18` — Product rating control behaves incorrectly |
| Automation candidate | No |

**Preconditions**

User is on a product detail page.

**Steps**

1. Note the current rating
2. Tap the fourth star
3. Observe the number of filled stars
4. Tap the first star and observe again

**Expected result**

Tapping the fourth star fills exactly four stars; tapping the first fills exactly one. The control never shows more than five or fewer than zero filled stars. If a thank-you confirmation appears it is dismissible and does not block the page.

---

### TC-PDP-010
**Product image gallery can be swiped through in both directions**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Specification-based (use case) |
| Risk covered | `R-08` — Product detail shows the wrong product or incomplete information |
| Automation candidate | No |

**Preconditions**

User is on a product detail page with more than one image.

**Steps**

1. Note the image position indicator
2. Swipe left through every image to the last one
3. Swipe right back to the first
4. Watch for blank frames or a stuck position

**Expected result**

Every image loads and matches the product. The position indicator tracks correctly. Swiping works in both directions and stops cleanly at each end rather than looping unexpectedly or freezing.

---

### TC-PDP-011
**Product description and highlights are complete and free of encoding errors**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Static review of user-facing copy |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

User is on a product detail page.

**Steps**

1. Read the full description word by word
2. Read the product highlights section
3. Look for truncated sentences, raw HTML tags, doubled spaces, or replacement characters

**Expected result**

The text is complete, ends in a full stop, and contains no raw markup, no character-encoding artefacts and no obvious grammatical errors. Product copy is customer-facing content and defects in it are reportable.

---

### TC-PDP-012
**Social media links on the product page open the correct destinations**

| | |
|---|---|
| Priority | **P3** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Specification-based (use case) |
| Risk covered | `R-19` — External links or scanned URLs open the wrong destination |
| Automation candidate | No |

**Preconditions**

User is on a product detail page. Device has a browser and network access.

**Steps**

1. Scroll to the social links section
2. Tap the first social link and note where it lands
3. Return to the app and repeat for each remaining social link

**Expected result**

Each link opens the platform it depicts, and the app is returned to intact when you come back. A link that opens the wrong platform, or one that leaves the app in a broken state on return, is a defect.

---

### TC-PDP-013
**Back navigation from the product detail page returns to the Catalog**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | State transition testing |
| Risk covered | `R-10` — Navigation loses sort, scroll position or entered form data |
| Automation candidate | Yes |

**Preconditions**

User has opened a product from a sorted Catalog.

**Steps**

1. Apply the price-ascending sort on the Catalog
2. Scroll down and open a product
3. Press the system back button
4. Observe the screen, the sort state and the scroll position

**Expected result**

The user returns to the Catalog with the sort still applied and the scroll position preserved. Landing on a different screen, or on an unsorted list scrolled to the top, is a defect.

---

### TC-CART-001
**Empty cart shows a helpful empty state with a way forward**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Specification-based (empty-state path) |
| Risk covered | `R-20` — Empty states are dead ends with no way forward |
| Automation candidate | Yes |

**Preconditions**

Cart is empty (use Reset App State from the menu).

**Steps**

1. Open the menu and tap Reset App State
2. Tap the cart icon in the header
3. Observe the screen

**Expected result**

A clear empty-cart message is shown together with an action that takes the user back to shopping. The screen is not blank and shows no error. A dead-end empty state is a conversion defect: the customer has nowhere to go.

---

### TC-CART-003
**Cart total equals the sum of unit price times quantity for every line**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Arithmetic invariant |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart contains at least three items across two different products.

**Steps**

1. For each cart line, record the unit price and the quantity
2. Calculate the expected subtotal by hand
3. Compare against the total the app displays
4. Note whether delivery is included in the displayed total

**Expected result**

The displayed total matches the hand-calculated figure exactly, to the cent. State clearly whether delivery is included. An arithmetic error here is the highest-severity defect class in the whole application.

---

### TC-CART-004
**Increasing quantity inside the cart updates the line and the total correctly**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Arithmetic invariant / state transition |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart contains one product with quantity 1.

**Steps**

1. Record the unit price and the current total
2. Increase the quantity to 3 using the cart's stepper
3. Observe the line quantity, the badge and the total

**Expected result**

The quantity shows 3, the badge shows 3, and the total equals three times the unit price. All three update together — a total that lags behind the quantity by one interaction is a real defect.

---

### TC-CART-005
**Decreasing quantity inside the cart updates the line and the total correctly**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Arithmetic invariant / state transition |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart contains one product with quantity 3.

**Steps**

1. Record the total
2. Decrease the quantity to 1
3. Observe the line quantity, the badge and the total

**Expected result**

The quantity shows 1, the badge shows 1, and the total equals one unit price. The total decreases by exactly the unit price per decrement.

---

### TC-CART-007
**Remove item deletes only the intended line**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | State transition testing |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart contains three different products.

**Steps**

1. Record all three product names and the total
2. Remove the middle line
3. Observe the remaining lines, the badge and the total

**Expected result**

Exactly the middle product is gone. The other two remain unchanged. The badge and total both reduce by precisely the removed line's contribution. Removing the wrong line is a classic list-index defect.

---

### TC-CART-008
**Removing every item returns the cart to its empty state**

| | |
|---|---|
| Priority | **P2** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | State transition testing |
| Risk covered | `R-20` — Empty states are dead ends with no way forward |
| Automation candidate | Yes |

**Preconditions**

Cart contains two products.

**Steps**

1. Remove the first line
2. Remove the second line
3. Observe the screen and the badge

**Expected result**

The empty-cart message and the shopping action reappear, and the badge shows an empty cart. The screen must not be left blank or show a total of zero with no explanation.

---

### TC-CART-009
**Cart contents survive an app restart**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | State transition testing (process death) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | Yes |

**Preconditions**

Cart contains two products with known quantities.

**Steps**

1. Record the exact cart contents, quantities and total
2. Force-stop the app: adb shell am force-stop com.saucelabs.mydemoapp.android
3. Relaunch the app and open the cart
4. Compare against what you recorded

**Expected result**

Record the actual behaviour precisely. If the cart is identical — same products, colours, quantities and total — that is the desirable outcome. If it is lost, that is a High-severity finding regardless of intent: silent loss of a cart is one of the most damaging defects in e-commerce, because the customer usually does not rebuild it.

---

### TC-CART-010
**Cart survives the app being backgrounded for an extended period**

| | |
|---|---|
| Priority | **P2** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | State transition testing (lifecycle) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

Cart contains two products.

**Steps**

1. Record the cart contents and total
2. Background the app with the home gesture
3. Wait ten minutes with the screen locked
4. Return to the app from recents and open the cart

**Expected result**

The cart is unchanged and the app resumes without a crash or a restart to the first screen. Losing the cart to ordinary backgrounding would affect a large share of real sessions.

---

### TC-CHKS-001
**Checkout requires login when the user is not authenticated**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Specification-based (guard condition) |
| Risk covered | `R-22` — Checkout cannot be reached, or the cart is lost on the way into it |
| Automation candidate | Yes |

**Preconditions**

User is logged out. Cart contains at least one item.

**Steps**

1. Log out from the menu
2. Add a product to the cart
3. Open the cart and proceed to checkout
4. Observe the screen presented

**Expected result**

The user is taken to the Login screen rather than straight into checkout. After a successful login the user continues to checkout with the cart intact — being dropped back at the Catalog with an untouched cart is a conversion defect.

---

### TC-CHKS-006
**Optional fields are genuinely optional**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Decision table (mandatory versus optional) |
| Risk covered | `R-23` — Shipping form validation blocks valid input or accepts undeliverable input |
| Automation candidate | Yes |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Fill only the mandatory fields, leaving every field not marked mandatory empty
2. Tap continue to payment

**Expected result**

Progression succeeds. A field marked optional that in fact blocks submission is a defect in either the validation or the label — and either way the user cannot tell which.

---

### TC-CHKS-011
**Back navigation from shipping preserves the cart and the entered data**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | State transition testing |
| Risk covered | `R-10` — Navigation loses sort, scroll position or entered form data |
| Automation candidate | No |

**Preconditions**

User has partially filled the shipping form.

**Steps**

1. Fill the full name and city fields
2. Press the system back button
3. Note where you land and whether the cart is intact
4. Return to the shipping screen and check whether the two fields are still populated

**Expected result**

The cart is unchanged. Record whether the partially entered address is retained. Losing a half-completed address form on an accidental back press is a well-known abandonment cause.

---

### TC-CHKP-008
**Security code input is masked or otherwise protected on screen**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Security-adjacent observation |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the payment screen.

**Steps**

1. Type a security code into the field
2. Observe whether the digits are visible
3. Take a screenshot and inspect it
4. Check the app switcher preview

**Expected result**

Record the behaviour precisely. A security code rendered in plain text and captured in screenshots is a privacy weakness worth reporting even in a demo app, because it demonstrates the pattern that would ship in a real one.

---

### TC-CHKP-010
**Unticking the billing-address checkbox reveals a billing address form**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Decision table (conditional visibility) |
| Risk covered | `R-26` — Payment form does not collect or present payment details correctly |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen with the billing checkbox ticked.

**Steps**

1. Note the checkbox state and the fields visible
2. Untick the checkbox
3. Observe whether billing address fields appear
4. Re-tick it and confirm they disappear

**Expected result**

Unticking reveals a billing address form; re-ticking hides it. If unticking reveals nothing, the checkbox is decorative and the user's billing address can never differ from their shipping address — a functional gap worth reporting.

---

### TC-CHKP-011
**Payment card type indicator responds to the card number entered**

| | |
|---|---|
| Priority | **P3** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Specification-based observation |
| Risk covered | `R-26` — Payment form does not collect or present payment details correctly |
| Automation candidate | No |

**Preconditions**

User is on the payment screen. The screen shows card-brand imagery.

**Steps**

1. Note which card brand images are displayed
2. Enter a card number beginning with 4 and observe whether any brand is highlighted
3. Enter one beginning with 5 and observe again

**Expected result**

Record the actual behaviour. If the brand images are purely decorative and never respond to input, that is worth noting as a missed usability affordance rather than a functional failure.

---

### TC-CHKP-012
**Back navigation from payment returns to shipping with data intact**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | State transition testing |
| Risk covered | `R-10` — Navigation loses sort, scroll position or entered form data |
| Automation candidate | Yes |

**Preconditions**

User has completed shipping and is on the payment screen.

**Steps**

1. Note the shipping data you entered
2. Press the system back button
3. Observe the shipping screen and whether every field is still populated
4. Continue forward again to payment and check whether the payment fields are retained

**Expected result**

Shipping data is fully retained. Making the customer retype an entire address to correct one payment digit is a well-documented abandonment cause.

---

### TC-CHKR-001
**Review screen displays the delivery address exactly as entered**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Consistency oracle across screens |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

User has completed shipping and payment and reached the review screen.

**Steps**

1. Record the shipping address exactly as you entered it
2. Reach the review screen
3. Compare the displayed delivery address field by field

**Expected result**

Every element of the address matches what was entered, including capitalisation and any accented characters. A silently altered or truncated address produces an undeliverable order.

---

### TC-CHKR-002
**Review screen displays a masked payment method, not the full card number**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Security-adjacent observation |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the review screen.

**Steps**

1. Observe the payment method section
2. Note exactly how much of the card number is displayed
3. Screenshot the screen and inspect it

**Expected result**

Only a masked representation should be shown, typically the last four digits. A full card number rendered on the review screen and captured in screenshots is a high-severity privacy defect and would breach card-industry expectations in a production app.

---

### TC-CHKR-003
**Review screen lists every ordered product with correct quantity and price**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Consistency oracle across screens |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

Cart contains three items across two products. User is on the review screen.

**Steps**

1. Record the exact cart contents before checkout
2. Reach the review screen
3. Compare every product, colour, quantity and line price against the cart

**Expected result**

The review screen shows exactly what was in the cart. Nothing is added, dropped, or has its quantity altered between the cart and the review screen.

---

### TC-CHKR-004
**Order total equals the item subtotal plus the stated delivery charge**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Arithmetic invariant |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

User is on the review screen with a known cart.

**Steps**

1. Record every line price and quantity on the review screen
2. Record the delivery charge exactly as displayed
3. Calculate subtotal plus delivery by hand
4. Compare against the displayed total

**Expected result**

The displayed total equals the hand-calculated figure to the cent. This is the single most important assertion in the application: the total is the number the customer is asked to agree to pay.

---

### TC-CHKR-005
**Delivery charge is stated explicitly before the order is placed**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Specification-based / regulatory awareness |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | No |

**Preconditions**

User is on the review screen.

**Steps**

1. Look for a delivery or shipping charge line
2. Note its exact value and label
3. Confirm it is visible without scrolling, or that scrolling is clearly indicated

**Expected result**

The delivery charge is shown as its own labelled line with a value, before the total. An unexplained gap between the item subtotal and the total is a transparency defect and, for a European retailer, a consumer-protection concern.

---

### TC-CHKR-007
**Cart is emptied after a successful order**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | State transition testing |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

User has just completed an order.

**Steps**

1. Complete an order and reach the confirmation screen
2. Observe the cart badge
3. Tap continue shopping and open the cart

**Expected result**

The badge shows an empty cart and the cart screen shows its empty state. A cart still holding the items that were just purchased invites an accidental duplicate order.

---

### TC-NAV-002
**Every menu entry opens its own distinct screen**

| | |
|---|---|
| Priority | **P1** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | Specification-based (exhaustive traversal) |
| Risk covered | `R-30` — Navigation is broken, trapping the user or exposing the wrong screen |
| Automation candidate | Yes |

**Preconditions**

Menu is open.

**Steps**

1. Tap each menu entry in turn
2. After each, confirm which screen opened and that it is the one named
3. Return to the menu each time

**Expected result**

Each entry opens the screen its label promises. No entry opens a blank screen, the wrong destination, or crashes the app.

---

### TC-NAV-003
**Menu can be dismissed without selecting anything**

| | |
|---|---|
| Priority | **P2** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | State transition testing |
| Risk covered | `R-30` — Navigation is broken, trapping the user or exposing the wrong screen |
| Automation candidate | No |

**Preconditions**

Menu is open on top of the Catalog.

**Steps**

1. Tap outside the menu panel and observe
2. Reopen the menu and press the system back button
3. Reopen the menu and swipe it closed if the gesture is supported

**Expected result**

Each method closes the menu and returns to the underlying screen unchanged. A menu that can only be dismissed by choosing a destination traps the user.

---

### TC-NAV-004
**Back button from the first screen exits rather than looping**

| | |
|---|---|
| Priority | **P2** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | State transition testing |
| Risk covered | `R-30` — Navigation is broken, trapping the user or exposing the wrong screen |
| Automation candidate | No |

**Preconditions**

App is on the Catalog, reached directly after launch.

**Steps**

1. Press the system back button once
2. Observe the behaviour

**Expected result**

The app exits to the device home screen, or shows a confirmation before exiting. It must not loop between screens or leave a blank screen visible.

---

### TC-NAV-005
**Deep back-stack navigation unwinds one screen at a time**

| | |
|---|---|
| Priority | **P2** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | State transition testing |
| Risk covered | `R-30` — Navigation is broken, trapping the user or exposing the wrong screen |
| Automation candidate | Yes |

**Preconditions**

User is four screens deep: Catalog, product detail, cart, checkout shipping.

**Steps**

1. Navigate Catalog to product detail to cart to checkout shipping
2. Press back and note the screen
3. Repeat until you reach the Catalog, noting each screen

**Expected result**

Each back press moves exactly one screen up the stack in reverse order. The user is never dropped straight to the Catalog from a deep screen, and never enters a loop.

---

### TC-WEB-001
**WebView screen accepts a valid HTTPS URL and renders the page**

| | |
|---|---|
| Priority | **P2** |
| Module | WebView |
| Journey stage | Cross-cutting |
| Technique | Specification-based (use case) |
| Risk covered | `R-19` — External links or scanned URLs open the wrong destination |
| Automation candidate | No |

**Preconditions**

Device has network access. User is on the WebView screen.

**Steps**

1. Read the field hint and the instruction text
2. Enter https://www.saucelabs.com
3. Tap the go-to-site control
4. Observe the rendered page

**Expected result**

The page loads inside the app and is scrollable. The app does not hand off to an external browser if the design is to render in place.

---

### TC-GEO-001
**Geo location screen requests permission and then reports coordinates**

| | |
|---|---|
| Priority | **P3** |
| Module | Geo Location |
| Journey stage | Cross-cutting |
| Technique | Specification-based (permission flow) |
| Risk covered | `R-31` — Runtime permissions are requested or handled incorrectly |
| Automation candidate | No |

**Preconditions**

Location permission not yet granted. Device location services enabled.

**Steps**

1. Open the geo location screen from the menu
2. Observe any permission prompt and grant it
3. Read the explanatory text
4. Wait for latitude and longitude to populate

**Expected result**

A permission prompt appears before any location access. After granting, latitude and longitude populate with plausible values. The screen explains that determining position may take a while, so the wait is not mistaken for a hang.

---

### TC-GEO-003
**Start and stop observing controls behave as labelled**

| | |
|---|---|
| Priority | **P3** |
| Module | Geo Location |
| Journey stage | Cross-cutting |
| Technique | Specification-based (state transition) |
| Risk covered | `R-31` — Runtime permissions are requested or handled incorrectly |
| Automation candidate | No |

**Preconditions**

Location permission granted. User is on the geo location screen.

**Steps**

1. Tap stop observing and note whether the coordinates freeze
2. Move the device or wait, and confirm the values do not change
3. Tap start observing and confirm updates resume
4. Leave the screen and return

**Expected result**

Stop genuinely halts updates and start resumes them. The screen stops observing automatically when left, as its own explanatory text states — continuing to poll location in the background would be a battery and privacy concern.

---

### TC-DRAW-001
**Drawing pad records strokes and the clear control erases them**

| | |
|---|---|
| Priority | **P3** |
| Module | Drawing |
| Journey stage | Cross-cutting |
| Technique | Specification-based (use case) |
| Risk covered | `R-32` — Drawing screen loses work or renders incorrectly |
| Automation candidate | No |

**Preconditions**

User is on the drawing screen.

**Steps**

1. Draw several strokes across the pad
2. Confirm the strokes appear where you drew them
3. Tap clear
4. Observe the pad

**Expected result**

Strokes follow the finger accurately with no offset. Clear removes everything and leaves a blank pad. A visible offset between finger and stroke is a real defect on a drawing surface.

---

### TC-DRAW-002
**Save control on the drawing pad completes without error**

| | |
|---|---|
| Priority | **P3** |
| Module | Drawing |
| Journey stage | Cross-cutting |
| Technique | Specification-based (use case) |
| Risk covered | `R-32` — Drawing screen loses work or renders incorrectly |
| Automation candidate | No |

**Preconditions**

User has drawn something on the pad.

**Steps**

1. Draw a recognisable shape
2. Tap save
3. Observe any confirmation or permission prompt
4. Leave the screen and return

**Expected result**

Save completes with clear feedback about what happened and where the drawing went. Record whether the drawing persists on return — silence after a save leaves the user unsure whether it worked.

---

### TC-QR-001
**QR scanner requests camera permission before opening the camera**

| | |
|---|---|
| Priority | **P3** |
| Module | QR Scanner |
| Journey stage | Cross-cutting |
| Technique | Specification-based (permission flow) |
| Risk covered | `R-31` — Runtime permissions are requested or handled incorrectly |
| Automation candidate | No |

**Preconditions**

Camera permission not yet granted.

**Steps**

1. Open the QR code scanner from the menu
2. Observe whether a permission prompt appears before the camera preview
3. Grant the permission

**Expected result**

A permission prompt appears before any camera preview is shown. After granting, a live camera preview appears. Opening the camera without asking would be a privacy defect.

---

### TC-QR-003
**Scanning a QR code containing a URL opens that URL**

| | |
|---|---|
| Priority | **P3** |
| Module | QR Scanner |
| Journey stage | Cross-cutting |
| Technique | Specification-based (use case) |
| Risk covered | `R-19` — External links or scanned URLs open the wrong destination |
| Automation candidate | No |

**Preconditions**

Camera permission granted. A QR code containing an HTTPS URL is available on screen or paper.

**Steps**

1. Point the camera at a QR code containing a known HTTPS URL
2. Observe what happens when it is recognised
3. Compare the opened destination against the encoded URL

**Expected result**

The code is recognised and the encoded URL is opened. The destination matches the code exactly — opening a different URL than the one encoded would be a serious defect.

---

### TC-XCUT-020
**Uninstalling and reinstalling the app produces a genuinely clean state**

| | |
|---|---|
| Priority | **P3** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | State transition testing (clean install) |
| Risk covered | `R-05` — App state (cart, session, selections) is lost on process death or backgrounding |
| Automation candidate | No |

**Preconditions**

App installed with a populated cart and an active session.

**Steps**

1. Note the cart contents and login state
2. Uninstall: adb uninstall com.saucelabs.mydemoapp.android
3. Reinstall the APK: adb install <path-to-apk>
4. Launch the app and inspect the cart and login state

**Expected result**

The app starts completely fresh: empty cart, logged out, default sort. Data surviving an uninstall would indicate storage outside the app sandbox, which is both a defect and a privacy concern.

---

## 7. What "done" looks like

- Every P1 functional case executed and passing.
- At least 90% of P2 cases executed.
- Every failure has a defect raised with evidence attached.
- Every arithmetic case has the hand-calculated figure recorded in the Actual Result column, not
  just the word "Pass".

---

## 8. Interview talking point

> "The functional cases I care most about in an e-commerce app are the arithmetic ones and the
> cross-screen consistency ones. I hand-calculate every total before I look at what the app shows,
> because if you read the app's number first you'll rationalise it. And I check the same product's
> price on the catalogue, the product page, the cart and the review screen — four screens, one
> number. When you're testing an app without access to its requirements, the app's own internal
> consistency is your most reliable oracle."
