# 05 · Negative Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**17 test cases** in this document — 9 P1, 4 P2, 4 P3 — across 9 modules.

---

## 1. What this type of testing is

**Negative testing** checks that the application correctly *refuses* what it should refuse.

Functional testing proves the happy path works. Negative testing proves the unhappy path fails
**safely, predictably and informatively**. The three words matter:

- **Safely** — no crash, no data loss, no partial state.
- **Predictably** — the same invalid input always produces the same refusal.
- **Informatively** — the user is told what is wrong in terms they can act on.

A common misunderstanding is that negative testing means "trying to break the app". That is
destructive testing, which has its own file. Negative testing is narrower and more rigorous: it
tests the **defined invalid paths** — the ones a specification would describe if there were one.
Empty required field, wrong password, locked account, empty cart at checkout.

---

## 2. Why it matters for this application

Two reasons this matters more on mobile than on desktop, and more in checkout than
anywhere else.

**First, mobile input is error-prone.** Small keyboard, one hand, walking down the street. Users
submit incomplete forms constantly — not because they are careless but because the medium invites
it. Validation is not an edge case on mobile; it is a main path.

**Second, checkout is where refusals are most expensive.** A vague or misleading validation
message on a payment form does not just annoy the user — it ends the sale. And a validation gap
in the other direction is worse: accepting an expired card means the customer *believes* they
bought something, and finds out later that they did not.

The negative cases here concentrate on the login form and the two checkout forms for exactly
that reason.

---

## 3. Technique used

**Equivalence partitioning, invalid classes.** This is the core technique and it is worth
being able to explain precisely.

You divide every input's possible values into classes where all members should behave
identically. For a required text field, the classes are roughly:

| Class | Example | Expected |
|---|---|---|
| Valid | `bod@example.com` — the username exactly as printed on the Login screen; tap it to auto-populate | Accepted |
| Absent | `""` | Rejected: "required" |
| Whitespace only | `"   "` | Treated as absent |
| Wrong format | `notanemail` | Rejected: format message |
| Wrong value, right format | `nobody@example.com` | Rejected: credential message |

Then you test **one representative from each class** rather than dozens of examples from one.
Testing five wrong passwords is four wasted tests; testing one wrong password, one empty
password and one whitespace password is three useful ones.

**Error guessing** supplements this. It is experience-driven rather than systematic: you have
seen forms that flag only the first error, forms that clear themselves on validation failure,
and login screens that leak whether an account exists. So you go and look for those specifically.

---

## 4. How to execute these cases

- After every refusal, check **three** things, not one: the message shown, the field flagged,
  and whether previously entered valid data was preserved. A form that clears itself on a
  validation error is a defect that is easy to miss if you only read the message.
- Record error messages **verbatim**, including capitalisation and punctuation. Paraphrasing loses
  the evidence, and copy defects are real defects.
- For the locked-out account, check that the message says the account is locked and not that the
  password is wrong. Misattributed errors send users into a password-reset loop that cannot help
  them.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-005`](#tc-login-005) | P1 | Login & Authentication | Account | Submitting with an empty username shows a username-specific validation error |
| [`TC-LOGIN-006`](#tc-login-006) | P1 | Login & Authentication | Account | Submitting with an empty password shows a password-specific validation error |
| [`TC-LOGIN-007`](#tc-login-007) | P2 | Login & Authentication | Account | Submitting with both fields empty flags both fields, not just the first |
| [`TC-LOGIN-008`](#tc-login-008) | P2 | Login & Authentication | Account | An unrecognised username is rejected without revealing whether the account exists |
| [`TC-LOGIN-009`](#tc-login-009) | P1 | Login & Authentication | Account | A valid username with a wrong password is rejected |
| [`TC-LOGIN-011`](#tc-login-011) | P2 | Login & Authentication | Account | Password is not recoverable from the app switcher preview |
| [`TC-LOGIN-022`](#tc-login-022) | P3 | Login & Authentication | Account | Biometric login toggle behaves correctly on a device without enrolled biometrics |
| [`TC-SORT-008`](#tc-sort-008) | P3 | Sorting | Discovery | Applying the same sort option twice does not reverse or corrupt the order |
| [`TC-CART-011`](#tc-cart-011) | P1 | Cart | Conversion | Proceed to checkout from an empty cart is prevented |
| [`TC-CHKS-003`](#tc-chks-003) | P1 | Checkout — Shipping | Conversion | Submitting the shipping form with every field empty flags every mandatory field |
| [`TC-CHKS-004`](#tc-chks-004) | P1 | Checkout — Shipping | Conversion | Omitting a single mandatory field blocks progress and flags only that field |
| [`TC-CHKP-002`](#tc-chkp-002) | P1 | Checkout — Payment | Conversion | Submitting the payment form empty flags every mandatory field |
| [`TC-CHKP-005`](#tc-chkp-005) | P1 | Checkout — Payment | Conversion | Expiry date in the past is rejected |
| [`TC-CHKR-011`](#tc-chkr-011) | P1 | Checkout — Review | Conversion | Back navigation from the review screen returns to payment without placing the order |
| [`TC-WEB-002`](#tc-web-002) | P2 | WebView | Cross-cutting | WebView rejects a non-HTTPS URL with a clear message |
| [`TC-GEO-002`](#tc-geo-002) | P3 | Geo Location | Cross-cutting | Denying location permission is handled gracefully |
| [`TC-QR-002`](#tc-qr-002) | P3 | QR Scanner | Cross-cutting | Denying camera permission is handled gracefully |

---

## 6. Test cases — full detail

### TC-LOGIN-005
**Submitting with an empty username shows a username-specific validation error**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning (invalid class: absent) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen. Both fields empty.

**Steps**

1. Leave the Username field empty
2. Type any value into the Password field
3. Tap Login
4. Observe which field is flagged and read the message

**Expected result**

A validation error appears on the Username field stating that a username is required. The Password field is not flagged. No login attempt is made and the screen does not change.

---

### TC-LOGIN-006
**Submitting with an empty password shows a password-specific validation error**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning (invalid class: absent) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Populate the Username field with a valid username
2. Leave the Password field empty
3. Tap Login
4. Observe which field is flagged and read the message

**Expected result**

A validation error appears on the Password field stating that a password is required. The Username field is not flagged. No login attempt is made.

---

### TC-LOGIN-007
**Submitting with both fields empty flags both fields, not just the first**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen with both fields empty.

**Steps**

1. Tap Login without typing anything
2. Observe both fields

**Expected result**

Both fields show their own validation error simultaneously. The form does not stop at the first error, which would force the user through two round trips to discover both problems.

---

### TC-LOGIN-008
**An unrecognised username is rejected without revealing whether the account exists**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Error guessing / security-adjacent |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the Login screen.

**Steps**

1. Type 'nosuchuser@example.com' into Username
2. Type any password
3. Tap Login
4. Read the error message

**Expected result**

Login is refused with a generic credential error. The message does not state 'this user does not exist', because that discloses which email addresses are registered — an account-enumeration weakness.

---

### TC-LOGIN-009
**A valid username with a wrong password is rejected**

| | |
|---|---|
| Priority | **P1** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning (invalid class: wrong value) |
| Risk covered | `R-01` — Users cannot authenticate, blocking checkout entirely |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Tap a listed username to populate the fields
2. Clear the Password field and type 'wrongpassword'
3. Tap Login
4. Read the error and confirm the screen did not change

**Expected result**

Login is refused with a credential error. The user remains on the Login screen. No partial access is granted — for example, the menu must still show 'Log In'.

---

### TC-LOGIN-011
**Password is not recoverable from the app switcher preview**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Error guessing / security-adjacent |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the Login screen with a password typed in.

**Steps**

1. Type a password into the field
2. Open the Android app switcher (recents)
3. Look at the app's thumbnail preview
4. Return to the app

**Expected result**

The recents thumbnail either hides the screen contents or shows the password masked. A plaintext password visible in the app switcher would be a real privacy defect.

---

### TC-LOGIN-022
**Biometric login toggle behaves correctly on a device without enrolled biometrics**

| | |
|---|---|
| Priority | **P3** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Error guessing / negative path |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

Device has no fingerprint enrolled. User can reach the Biometrics screen from the menu.

**Steps**

1. Open the menu and tap 'Biometrics'
2. Read the explanatory text on the screen
3. Attempt to enable the fingerprint login toggle
4. Read any resulting message

**Expected result**

The app explains that biometrics are unavailable or not enabled on this device, and does not enable the toggle. The message must be a well-formed sentence — a broken or ungrammatical error message here is a real, reportable copy defect.

---

### TC-SORT-008
**Applying the same sort option twice does not reverse or corrupt the order**

| | |
|---|---|
| Priority | **P3** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Idempotence check |
| Risk covered | `R-16` — Sorting produces an incorrect order, or adds, drops or duplicates products |
| Automation candidate | Yes |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Apply price ascending and record the order
2. Open the sort control and select price ascending again
3. Record the order and compare

**Expected result**

The order is unchanged. Selecting an already-active sort is idempotent and must not toggle the direction, since nothing in the UI tells the user it would.

---

### TC-CART-011
**Proceed to checkout from an empty cart is prevented**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Negative path / guard condition |
| Risk covered | `R-20` — Empty states are dead ends with no way forward |
| Automation candidate | Yes |

**Preconditions**

Cart is empty.

**Steps**

1. Reset App State so the cart is empty
2. Open the cart
3. Attempt to proceed to checkout by any route, including the header and any visible button

**Expected result**

There is no way to reach checkout with an empty cart. Either no checkout control is offered, or it is disabled. Reaching a payment screen with nothing to buy is a functional defect.

---

### TC-CHKS-003
**Submitting the shipping form with every field empty flags every mandatory field**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (all-absent class) |
| Risk covered | `R-23` — Shipping form validation blocks valid input or accepts undeliverable input |
| Automation candidate | Yes |

**Preconditions**

User is on the shipping address screen with all fields empty.

**Steps**

1. Tap the continue-to-payment control without entering anything
2. Observe which fields are flagged
3. Confirm no navigation occurred

**Expected result**

Every mandatory field shows its own validation error at once. Optional fields are not flagged. The user stays on the shipping screen. Revealing errors one at a time turns a single correction into several round trips.

---

### TC-CHKS-004
**Omitting a single mandatory field blocks progress and flags only that field**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (single-absent class) |
| Risk covered | `R-23` — Shipping form validation blocks valid input or accepts undeliverable input |
| Automation candidate | Yes |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Fill every mandatory field with valid data except the zip code
2. Tap continue to payment
3. Observe which field is flagged
4. Repeat, omitting the city instead

**Expected result**

Only the omitted field is flagged each time and navigation is blocked. Correctly filled fields retain their values — clearing the whole form on a validation error is a serious usability defect.

---

### TC-CHKP-002
**Submitting the payment form empty flags every mandatory field**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (all-absent class) |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen with all fields empty.

**Steps**

1. Tap the review-order control without entering anything
2. Observe which fields are flagged
3. Confirm no navigation occurred

**Expected result**

Every mandatory payment field shows its own validation error simultaneously and navigation is blocked. Reaching an order review screen with no payment details would be a severe functional defect.

---

### TC-CHKP-005
**Expiry date in the past is rejected**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Boundary value analysis (date boundary) |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen with other fields valid.

**Steps**

1. Enter an expiry date clearly in the past, such as 01/20
2. Submit
3. Enter last month's date and submit
4. Record the behaviour for each

**Expected result**

An expired card is rejected with a clear message. Accepting a past expiry date is a genuine functional defect: it guarantees a failed payment later in the flow, after the customer believes the order succeeded.

---

### TC-CHKR-011
**Back navigation from the review screen returns to payment without placing the order**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | State transition testing / negative path |
| Risk covered | `R-28` — The order placed does not match what the customer reviewed and agreed to |
| Automation candidate | Yes |

**Preconditions**

User is on the review screen.

**Steps**

1. Press the system back button
2. Observe where you land
3. Check the cart badge and confirm no order was placed

**Expected result**

The user returns to the payment screen with data intact and no order is placed. An order created by a back press would be a severe defect.

---

### TC-WEB-002
**WebView rejects a non-HTTPS URL with a clear message**

| | |
|---|---|
| Priority | **P2** |
| Module | WebView |
| Journey stage | Cross-cutting |
| Technique | Equivalence partitioning (scheme classes) |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | Yes |

**Preconditions**

User is on the WebView screen.

**Steps**

1. Enter http://example.com and submit
2. Read the message shown
3. Enter ftp://example.com and submit
4. Enter 'not a url' and submit

**Expected result**

Each non-HTTPS or malformed input is refused with a message stating an HTTPS URL is required. Loading plain HTTP content inside an app is an insecure-transport weakness, so this validation existing is the correct behaviour.

---

### TC-GEO-002
**Denying location permission is handled gracefully**

| | |
|---|---|
| Priority | **P3** |
| Module | Geo Location |
| Journey stage | Cross-cutting |
| Technique | Negative path (permission denied) |
| Risk covered | `R-31` — Runtime permissions are requested or handled incorrectly |
| Automation candidate | No |

**Preconditions**

Location permission not granted.

**Steps**

1. Open the geo location screen
2. Deny the permission prompt
3. Observe the screen and any message
4. Attempt to start observing

**Expected result**

The app explains that location is unavailable without permission and does not crash. Silently showing empty coordinates with no explanation leaves the user unable to diagnose the problem.

---

### TC-QR-002
**Denying camera permission is handled gracefully**

| | |
|---|---|
| Priority | **P3** |
| Module | QR Scanner |
| Journey stage | Cross-cutting |
| Technique | Negative path (permission denied) |
| Risk covered | `R-31` — Runtime permissions are requested or handled incorrectly |
| Automation candidate | No |

**Preconditions**

Camera permission not granted.

**Steps**

1. Open the QR code scanner
2. Deny the permission prompt
3. Observe the screen

**Expected result**

The app explains that the camera is required and does not crash. A black screen with no message leaves the user unable to understand what went wrong.

---

## 7. What "done" looks like

- Every negative case executed.
- Every validation message recorded verbatim.
- Any case where valid data was lost on a validation error is raised as a defect regardless of
  whether the validation itself worked.

---

## 8. Interview talking point

> "Negative testing is about how the app refuses, not just that it refuses. I check three
> things on every rejection: is the message specific enough to act on, is the right field flagged,
> and did the form keep the data the user already typed correctly. The third one is the one people
> miss — a payment form that clears itself when you get one digit wrong is a checkout-abandonment
> defect, even though the validation technically worked."
