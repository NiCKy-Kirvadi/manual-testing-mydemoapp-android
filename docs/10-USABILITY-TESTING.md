# 10 · Usability Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**9 test cases** in this document — 0 P1, 7 P2, 2 P3 — across 5 modules.

---

## 1. What this type of testing is

**Usability testing** evaluates whether the application is *pleasant and efficient* to use,
not merely whether it works.

The distinction is real and often dismissed. A checkout form where the keyboard covers the field
you are typing into is fully functional — every field accepts input, validation fires correctly,
the order completes. It is also miserable, and a measurable share of users will abandon it. That
is a defect, and it belongs in the defect log with a severity and a suggested fix.

There are two flavours, and this file is the second:

- **Empirical usability testing** — watch real users attempt tasks and count where they struggle.
  The gold standard, and it needs participants and a lab.
- **Heuristic evaluation** — a trained evaluator inspects the interface against established
  usability principles. Cheap, repeatable, and roughly as effective at catching the obvious
  problems. This is what a QA engineer can do inside a sprint, and it is what these cases are.

---

## 2. Why it matters for this application

Usability defects are commercially expensive and organisationally invisible, which is a
dangerous combination. Nobody files a support ticket saying "your form was annoying" — they just
leave. So these defects never appear in the ticket queue that drives prioritisation, and they
survive for years.

Four areas in this app carry disproportionate usability risk:

**Keyboard behaviour on the checkout forms.** Seven fields on the shipping form, most of them
below the midpoint of the screen. If the form does not scroll to keep the focused field and its
validation message above the keyboard, the user is typing blind at the most conversion-sensitive
moment in the funnel.

**Keyboard type per field.** A card number field that presents a full alphabetic keyboard forces
a manual mode switch on every payment. Small friction, applied at the worst possible point.

**Error message specificity.** A message that says a value "looks invalid" identifies neither the
field nor the problem. The user's only recourse is to guess. Recording these verbatim and
proposing better wording is one of the highest-value, lowest-cost contributions a QA engineer
makes.

**Unconfirmed destructive actions.** *Reset App State* wipes a full cart with no warning and no
undo. That is a two-line fix and a genuinely bad experience if you hit it by accident.

---

## 3. Technique used

**Heuristic evaluation against Nielsen's usability heuristics.** Each case below names the
heuristic it tests, which is what makes the finding arguable rather than a matter of taste — and
being able to argue a usability finding is the whole skill.

The heuristics used here:

| Heuristic | What it means | Where it applies in this app |
|---|---|---|
| **Visibility of system status** | The user always knows what is happening | Loading states, current sort, login state |
| **Error prevention** | Prevent mistakes rather than reporting them | Confirmation before Reset App State |
| **Recognition over recall** | Don't make users remember things | Retained form data on back navigation |
| **Flexibility and efficiency** | Support the frequent path efficiently | Correct keyboard type per field |
| **Help users recover from errors** | Errors say what is wrong, in plain language | Validation message specificity |
| **Aesthetic and minimalist design** | No irrelevant or competing information | Screen density, price legibility |

Also applied: **Fitts's law**, which is the underlying reason touch-target size matters — the time
to hit a target grows as the target shrinks and as it sits further from the thumb. That is why
small steppers and bottom-corner controls are worth flagging.

**How to keep a usability finding defensible.** State it in three parts and it stops being an
opinion:
1. The **heuristic** it violates.
2. The **user consequence**, ideally quantified or at least made concrete.
3. A **specific proposed fix**.

"The error message is bad" is an opinion. "The message 'Value looks invalid' violates *help users
recover from errors*: it names neither the field nor the problem, so a user with a mistyped expiry
date has no way to know which of three fields to correct. Suggested wording: 'Expiry date must be
in the future.'" is a defect report.

---

## 4. How to execute these cases

- **Test on a real phone, held in one hand.** An emulator on a desktop monitor with a mouse
  hides every reach and thumb-zone problem, which is most of mobile usability.
- **Do the tasks at natural speed**, as a customer would. Deliberate, careful tester-pace hides
  friction; the whole point is to feel it.
- For every finding, write the three parts above before moving on. If you cannot name the
  heuristic, you may be recording a preference rather than a defect — and that is worth knowing
  before you argue it with a designer.
- Severity for usability defects is usually **Low to Medium**, but priority can be high when the
  friction sits in the conversion path. Say so explicitly; that is the argument that gets it fixed.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-018`](#tc-login-018) | P3 | Login & Authentication | Account | Log Out message is grammatically correct and professionally worded |
| [`TC-LOGIN-019`](#tc-login-019) | P2 | Login & Authentication | Account | Listed usernames on the Login screen are spelled correctly |
| [`TC-CHKS-012`](#tc-chks-012) | P2 | Checkout — Shipping | Conversion | Keyboard does not obscure the field being typed into |
| [`TC-CHKS-013`](#tc-chks-013) | P3 | Checkout — Shipping | Conversion | Each field opens the most appropriate keyboard type |
| [`TC-CHKP-013`](#tc-chkp-013) | P2 | Checkout — Payment | Conversion | Payment fields open numeric keyboards |
| [`TC-CHKR-012`](#tc-chkr-012) | P2 | Checkout — Review | Conversion | Confirmation screen copy is correct, complete and professionally worded |
| [`TC-XCUT-017`](#tc-xcut-017) | P2 | Cross-cutting | Cross-cutting | Error and validation messages are specific enough to act on |
| [`TC-XCUT-018`](#tc-xcut-018) | P2 | Cross-cutting | Cross-cutting | Destructive actions are either confirmed or reversible |
| [`TC-XCUT-019`](#tc-xcut-019) | P2 | Cross-cutting | Cross-cutting | The user always knows what state the app is in |

---

## 6. Test cases — full detail

### TC-LOGIN-018
**Log Out message is grammatically correct and professionally worded**

| | |
|---|---|
| Priority | **P3** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Static review of user-facing copy |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

User is logged in.

**Steps**

1. Open the menu and tap 'Log Out'
2. Read the confirmation message character by character
3. Screenshot it

**Expected result**

The message is a well-formed English sentence with correct capitalisation. Mid-sentence capitalisation of an ordinary word, or a missing article, is a copy defect worth raising — customer-facing copy is part of the product.

---

### TC-LOGIN-019
**Listed usernames on the Login screen are spelled correctly**

| | |
|---|---|
| Priority | **P2** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Static review of user-facing copy |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

User is on the Login screen.

**Steps**

1. Read every username in the Usernames list letter by letter
2. Compare each against the conventional example-name spelling it appears to intend
3. Screenshot the list

**Expected result**

Every username is spelled as intended. A misspelled demo credential is a genuine defect: it looks careless, and anyone copying the value by eye will type the wrong thing. Report exactly what is on screen.

---

### TC-CHKS-012
**Keyboard does not obscure the field being typed into**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Usability heuristic (visibility of system state) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Tap the last field on the form, near the bottom of the screen
2. Observe whether the on-screen keyboard covers the field or the form scrolls to keep it visible
3. Repeat for each field down the form

**Expected result**

The form scrolls so the focused field and its validation message stay visible above the keyboard. Typing blind into a field hidden behind the keyboard is a common and highly disruptive mobile defect.

---

### TC-CHKS-013
**Each field opens the most appropriate keyboard type**

| | |
|---|---|
| Priority | **P3** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Usability heuristic (efficiency of use) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Tap the zip code field and note which keyboard appears
2. Tap the full name field and note the keyboard
3. Tap the city field and note the keyboard

**Expected result**

The zip code field should present a numeric or numeric-first keyboard; text fields present the standard keyboard. Forcing the user to switch keyboard modes manually for a numeric field is a small but real friction defect.

---

### TC-CHKP-013
**Payment fields open numeric keyboards**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Usability heuristic (efficiency of use) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

User is on the payment screen.

**Steps**

1. Tap the card number field and note the keyboard type
2. Tap the expiry date field and note the keyboard type
3. Tap the security code field and note the keyboard type

**Expected result**

All three present a numeric keyboard, since all three accept digits only. Presenting a full alphabetic keyboard for a card number forces an unnecessary mode switch at the most conversion-sensitive moment in the funnel.

---

### TC-CHKR-012
**Confirmation screen copy is correct, complete and professionally worded**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Static review of user-facing copy |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

User has just placed an order.

**Steps**

1. Read every line on the confirmation screen word by word
2. Check capitalisation, punctuation and grammar
3. Screenshot the screen

**Expected result**

The copy is well-formed English with consistent capitalisation and punctuation. This is the last screen the customer sees; errors here are disproportionately damaging to trust, so copy defects are legitimately reportable.

---

### TC-XCUT-017
**Error and validation messages are specific enough to act on**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Usability heuristic (help users recognise and recover from errors) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

Access to the login screen and both checkout form screens.

**Steps**

1. Trigger every validation error you can across login, shipping and payment
2. Record each message verbatim
3. For each, judge whether a first-time user could tell what to change

**Expected result**

Each message identifies the field and what is wrong with it. A message such as 'value looks invalid' names neither, and while not a functional failure it is a legitimate usability defect worth raising with concrete suggested wording.

---

### TC-XCUT-018
**Destructive actions are either confirmed or reversible**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Usability heuristic (error prevention) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

Cart contains items. User is logged in.

**Steps**

1. Remove an item from the cart and note whether any confirmation or undo is offered
2. Tap Reset App State and note whether it warns before clearing everything
3. Tap Log Out and note whether it confirms

**Expected result**

Actions that destroy user data should either ask first or offer an undo. Reset App State silently wiping a full cart with no warning is a real usability defect, and the fix is cheap.

---

### TC-XCUT-019
**The user always knows what state the app is in**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Usability heuristic (visibility of system status) |
| Risk covered | `R-25` — Friction in the checkout forms causes avoidable abandonment |
| Automation candidate | No |

**Preconditions**

App installed.

**Steps**

1. On every screen, note whether it is clear where you are and how to go back
2. During every action that takes more than a moment, note whether progress is indicated
3. Note whether the current sort and login state are visible without opening a menu

**Expected result**

Every screen identifies itself. Slow actions show progress rather than appearing frozen. Current selections such as sort order and login state are discoverable without trial and error.

---

## 7. What "done" looks like

- Every usability case executed on a physical device, one-handed.
- Every finding recorded with heuristic, user consequence and proposed fix.
- Error messages captured verbatim, with suggested replacement wording.

---

## 8. Interview talking point

> "I treat usability findings as defects, but I only raise them in a form that can be argued.
> Three parts: the heuristic it violates, the concrete user consequence, and a specific proposed
> fix. 'The error message is bad' is an opinion a designer can dismiss. '"Value looks invalid"
> names neither the field nor the problem, so a user with a mistyped expiry date can't tell which
> of three fields to fix — suggest "Expiry date must be in the future"' is a ticket. And I test
> one-handed on a real phone, because an emulator with a mouse hides every thumb-reach problem,
> which is most of mobile usability."
