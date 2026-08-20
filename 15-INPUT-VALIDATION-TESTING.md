# 15 · Input Validation Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**8 test cases** in this document — 1 P1, 6 P2, 1 P3 — across 4 modules.

---

## 1. What this type of testing is

**Input validation testing** verifies that every field handles unexpected, malformed and
adversarial-looking input safely — treating it as inert data rather than acting on it.

**An important scoping statement, and one to say out loud in an interview.** This is *not*
penetration testing and *not* a security assessment. There is no attempt to compromise a system,
bypass a control, or reach a backend. Every case here is a **client-side robustness check on a
demo application built for testing**, and the assertions are all of the form "the app does not
crash, does not render the input as markup, and does not leak an internal error".

That boundary is not pedantry. Running actual security testing against systems you are not
authorised to test is both unethical and unlawful in most jurisdictions, and being clear about
where the line is demonstrates professional judgement rather than caution.

---

## 2. Why it matters for this application

Three distinct risks, and they are worth separating because they are fixed in different places.

**Robustness.** Unexpected input should never crash the app or produce an unhandled error. A crash
on an emoji in a name field is a defect regardless of any security consideration — and real names
do contain apostrophes, accents and hyphens, all of which naive validation rejects.

**Output encoding.** This is the subtler and more interesting one. When a value is entered on one
screen and displayed on another, it must be **escaped on the way out**. The single most instructive
case in this file: type `<b>Test</b>` into the full name field, then look at the order review
screen. If it displays literally, with the angle brackets visible, the app escapes its output
correctly. If it displays as **bold text**, then user input is being interpreted as markup — and
that is a genuine finding worth reporting, because the same weakness in a web-rendered context is
how cross-site scripting happens.

Note *where* that defect surfaces: two screens away from where you typed it. Which is why every
case in this file follows the value through to its display point rather than stopping at the field.

**Third, honestly: the fields here are the same ones that matter commercially.** A card number
field with no length validation, or a postal code field that accepts `!!!`, produces failed
payments and undeliverable orders. Input validation is not only a robustness concern — it is order
accuracy.

---

## 3. Technique used

**1. Equivalence partitioning across character-set classes.** Rather than inventing
hundreds of odd strings, partition the input space and take one representative from each class:

| Class | Representative | Expected |
|---|---|---|
| Plain valid | `Bob Smith` | Accepted |
| Accented / European | `Björn Müller-Schäfer` | Accepted, renders correctly |
| Apostrophe / hyphen | `Anne-Marie O'Brien` | Accepted — real names contain these |
| Non-Latin script | `Владимир`, `北京` | Accepted or cleanly rejected, renders correctly |
| Emoji | `😀👗` | No crash, renders or is rejected cleanly |
| Whitespace only | `"   "` | Treated as empty |
| Markup-like | `<b>Test</b>` | Displayed **literally** at every display point |
| Script-like | `<script>alert(1)</script>` | Inert; no dialog, no rendering |
| SQL-like | `' OR '1'='1` | Inert; no internal error surfaced |
| Path-like | `../../etc/passwd` | Inert; treated as ordinary text |
| Very long | 500 characters | Truncated or rejected; no layout break |

**2. Follow the value to every display point.** The field is the entry point, not the test. Enter
on the shipping form, then check the review screen — that is where encoding defects appear.

**3. Watch for information disclosure in error messages.** An unhandled exception, a stack trace,
or a database error surfaced to the user tells an attacker about the internals. The assertion is
that failures produce *user-facing* messages, not *developer-facing* ones.

**4. Check the device log, not just the screen.** Credentials and card numbers written to logcat
are readable by other tooling on the device. This is a real privacy defect and it is invisible
from the UI:
```
adb logcat -c
```
…perform a login and checkout…
```
adb logcat -d > evidence/logcat-check.txt
```
Then search that file for the password and card number you used.

---

## 4. How to execute these cases

- **Only ever use this demo application.** Do not run these inputs against any system you do
  not own or have written authorisation to test.
- Enter the input, submit, and then **check both the immediate screen and every later screen that
  displays the value**. Stopping at the field misses the defect.
- Record the app's behaviour factually: accepted, rejected with message X, crashed, or rendered as
  markup. Do not speculate in the report about what the underlying cause might be — describe what
  you observed and let the engineers diagnose.
- **Redact credentials and card numbers from any log file before committing it.** Read the file
  before you attach it; this is exactly the mistake the test is designed to find.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-014`](#tc-login-014) | P2 | Login & Authentication | Account | Leading and trailing whitespace in the username is trimmed or clearly rejected |
| [`TC-LOGIN-015`](#tc-login-015) | P2 | Login & Authentication | Account | Script-like and SQL-like input in the credential fields is treated as plain text |
| [`TC-LOGIN-016`](#tc-login-016) | P3 | Login & Authentication | Account | Emoji and non-Latin characters in the credential fields do not break the app |
| [`TC-CHKS-007`](#tc-chks-007) | P2 | Checkout — Shipping | Conversion | Zip code field rejects clearly invalid formats or accepts them by documented design |
| [`TC-CHKS-008`](#tc-chks-008) | P2 | Checkout — Shipping | Conversion | Full name field accepts names with accents, hyphens and apostrophes |
| [`TC-CHKS-010`](#tc-chks-010) | P2 | Checkout — Shipping | Conversion | Script-like input in address fields is treated as plain text throughout checkout |
| [`TC-CHKP-004`](#tc-chkp-004) | P1 | Checkout — Payment | Conversion | Card number field rejects alphabetic and clearly malformed input |
| [`TC-WEB-004`](#tc-web-004) | P2 | WebView | Cross-cutting | URL field does not execute injected script content |

---

## 6. Test cases — full detail

### TC-LOGIN-014
**Leading and trailing whitespace in the username is trimmed or clearly rejected**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning (whitespace class) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Type a valid username with three spaces before and after it
2. Populate the correct password
3. Tap Login
4. Observe the result

**Expected result**

Either the whitespace is trimmed and login succeeds, or login is refused with a message. What must not happen is a confusing failure where the user cannot see why their apparently correct credentials were rejected.

---

### TC-LOGIN-015
**Script-like and SQL-like input in the credential fields is treated as plain text**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Error guessing / input validation |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Type <script>alert(1)</script> into Username, tap Login, observe
2. Type ' OR '1'='1 into Username, tap Login, observe
3. Type ../../etc/passwd into Username, tap Login, observe

**Expected result**

Each input is treated as an ordinary string. Login is refused. No dialog is executed, no raw markup is rendered, no unhandled exception or stack trace is shown, and the app does not crash. This is a client-side input-handling check on a demo app, not an attack on any backend.

---

### TC-LOGIN-016
**Emoji and non-Latin characters in the credential fields do not break the app**

| | |
|---|---|
| Priority | **P3** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Equivalence partitioning (character-set classes) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | No |

**Preconditions**

User is on the Login screen.

**Steps**

1. Type an emoji sequence into Username, tap Login
2. Type Cyrillic text (например) into Username, tap Login
3. Type Chinese characters into Username, tap Login

**Expected result**

Each input renders correctly in the field with no replacement boxes or mojibake. Login is refused cleanly. The app does not crash on any of the three.

---

### TC-CHKS-007
**Zip code field rejects clearly invalid formats or accepts them by documented design**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (format classes) |
| Risk covered | `R-23` — Shipping form validation blocks valid input or accepts undeliverable input |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen with all other fields valid.

**Steps**

1. Enter 'ABCDEF' as the zip code and submit
2. Enter '!!!' as the zip code and submit
3. Enter a 40-digit number as the zip code and submit
4. Record the behaviour for each

**Expected result**

Record exactly what happens for each input. A form that accepts '!!!' as a postal code is a validation gap worth reporting even in a demo app, because an undeliverable address becomes a failed order downstream.

---

### TC-CHKS-008
**Full name field accepts names with accents, hyphens and apostrophes**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (character-set classes) |
| Risk covered | `R-24` — European names, characters and formats are mishandled |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Enter 'Anne-Marie O'Brien' and submit
2. Enter 'Björn Müller-Schäfer' and submit
3. Enter 'Ñuñez Ferrão' and submit

**Expected result**

All three are accepted and render correctly with no mojibake or replacement characters. Rejecting apostrophes or accented characters excludes a large share of real European customers, which for a German-market retailer is a substantive defect.

---

### TC-CHKS-010
**Script-like input in address fields is treated as plain text throughout checkout**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Input validation / output encoding |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Enter <b>Test</b> into the full name field
2. Complete the remaining fields and continue to payment
3. Continue to the review screen
4. Observe how the name is rendered on the review screen

**Expected result**

The value is displayed literally as typed, including the angle brackets. If the review screen renders it as bold text, the field content is being interpreted as markup rather than escaped — a real finding worth reporting.

---

### TC-CHKP-004
**Card number field rejects alphabetic and clearly malformed input**

| | |
|---|---|
| Priority | **P1** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Equivalence partitioning (format classes) |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen.

**Steps**

1. Enter 'ABCDEFGHIJKL' as the card number and submit
2. Enter '1234' as the card number and submit
3. Enter '!!!!!!!!!!!!' as the card number and submit
4. Record the behaviour and the exact message for each

**Expected result**

Each is rejected with a validation message. Note whether the message is specific enough to act on — a generic 'value looks invalid' tells the user something is wrong but not what, which is a usability weakness worth recording alongside the functional result.

---

### TC-WEB-004
**URL field does not execute injected script content**

| | |
|---|---|
| Priority | **P2** |
| Module | WebView |
| Journey stage | Cross-cutting |
| Technique | Input validation |
| Risk covered | `R-03` — Credentials or payment data are exposed on screen, in screenshots or in logs |
| Automation candidate | No |

**Preconditions**

User is on the WebView screen.

**Steps**

1. Enter javascript:alert(1) and submit
2. Observe whether any dialog appears
3. Enter data:text/html,<script>alert(1)</script> and submit
4. Observe the result

**Expected result**

Both are refused by the HTTPS validation. No dialog executes. This is a client-side input-validation check on a demo app, not an attempt to compromise anything.

---

## 7. What "done" looks like

- Every character-set class tested on the login, shipping and payment forms.
- Markup-like input followed through to the order review screen and its rendering recorded.
- Device log captured during a full login-and-checkout and searched for the credentials used.
- No case left recorded as "seemed fine" — each has an explicit observed behaviour.

---

## 8. Interview talking point

> "I'm careful to scope this as client-side input robustness, not security testing — I'm not
> attacking a backend, and being clear about that line matters. The case I find most instructive is
> typing `<b>Test</b>` into a name field and then looking at the order review screen two steps
> later. If it renders as bold text rather than literal angle brackets, user input is being
> interpreted as markup, and that's a real finding. Note where it surfaces — two screens from where
> I typed it, which is why I always follow a value through to every display point rather than
> stopping at the field."
