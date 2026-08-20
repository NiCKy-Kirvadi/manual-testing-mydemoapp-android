# 17 · Defect Reports
## My Demo App (Android) — `com.saucelabs.mydemoapp.android`

Written in **Jira ticket format** so each can be pasted straight onto a board.

> **How to use this document.** BUG-001 is a **fully worked example** showing the standard every
> ticket in this project follows. Section 3 gives you **eight concrete leads** — specific places
> where a defect is genuinely likely in this application. Go and look at each one on your own device,
> verify what is actually on your screen, and write up **only what you can reproduce**.
>
> Do not ship this file containing only the template. And never report a defect you have not
> reproduced yourself — you may be asked to demonstrate it live, and being unable to is far worse
> than having found fewer defects.

---

## 1. The ticket standard used in this project

| Field | Rule | Why it matters |
|---|---|---|
| **Summary** | `[Module] What happens, where, under what condition` | Must be readable in a backlog list without opening the ticket |
| **Environment** | Device, OS version, **app version**, network, account | A report without a version number is nearly useless — the engineer cannot tell whether the defect still exists |
| **Preconditions** | The exact state the app must be in before step 1 | Half of "cannot reproduce" closures are missing preconditions |
| **Steps to reproduce** | Numbered, atomic, no assumed knowledge | Someone who has never opened the app must be able to follow them |
| **Expected result** | What should happen **and why** — convention, consistency, or platform guideline | Without the "why", the engineer can dispute your expectation and you have no ground to stand on |
| **Actual result** | What did happen. Facts only, no speculation about cause | Speculating about the cause and being wrong costs you credibility |
| **Severity** | Critical / High / Medium / Low — technical impact | Set by QA |
| **Priority** | P1–P4 — business urgency | Agreed with the Product Owner, not decided unilaterally |
| **Frequency** | A hit rate: "Always (5/5)" or "Intermittent (3/5)" | An engineer plans completely differently for each |
| **Evidence** | Screenshot, recording and/or logcat, referenced by filename | A crash without a log usually cannot be fixed |
| **Journey stage** | Discovery / Consideration / Conversion / Account / Cross-cutting | Lets the Product Owner see funnel impact immediately |
| **Linked test case** | The `TC-` ID that caught it, or `Exploratory — Charter N` | Closes the loop between suite and defect |
| **Business impact** | One paragraph on what this costs the business | This is what actually gets it prioritised |

### The two fields most often omitted

**App version** and **frequency**. Both are cheap to record and both are the difference between a
ticket an engineer can act on and a ticket that comes back with a question.

---

## 2. BUG-001 — worked example

*Replace this with a real finding of your own.*

### Summary
`[Cart] Order total does not equal the sum of line totals plus the delivery charge`

| Field | Value |
|---|---|
| **Type** | Bug |
| **Severity** | **Critical** |
| **Priority** | P1 Blocker |
| **Frequency** | Always (5/5) |
| **Journey stage** | Conversion |
| **Component** | Cart / Checkout — Review |
| **Linked test case** | `TC-CART-003`, `TC-CHKR-004` |
| **Reported by** | Akanksh Gurupadappa Akki |
| **Date** | *(date)* |

### Environment

| | |
|---|---|
| Device | *(your device model)* |
| OS | Android 14 |
| App version | *(exact `versionName`)* |
| Network | WiFi |
| Account | Demo account from the app's own credential list |

### Preconditions
1. App installed and launched
2. Menu → **Reset App State** performed, so the cart is empty
3. User logged in with the standard demo account

### Steps to reproduce
1. Open the app and land on the Catalog
2. Tap the first product
3. Set the quantity to **2** using the increase control
4. Tap **Add to cart**
5. Press back to the Catalog
6. Tap a **different** product
7. Leave the quantity at **1** and tap **Add to cart**
8. Tap the cart icon in the header
9. Record the unit price and quantity of each line, and the displayed total
10. Calculate `(unit price A × 2) + (unit price B × 1)` by hand
11. Proceed to checkout, complete shipping and payment, and reach the Review screen
12. Record the delivery charge and the order total, and compare against
    `hand-calculated subtotal + delivery`

### Expected result
The order total equals the sum of every line total plus the stated delivery charge, to the cent.
This is the number the customer is asked to agree to pay, so it must be arithmetically derivable
from the values displayed alongside it.

### Actual result
*(Record the exact figures. For example: "Cart displayed a total of $64.98. Hand calculation of
2 × $29.49 + $5.99 gives $64.97. The total is one cent higher than the sum of its parts, consistently
across five attempts with different product combinations.")*

**Note: this app prices in US dollars** — its delivery charge is a hardcoded `$5.99`. Record whatever
currency is actually on your screen.

### Evidence
- `evidence/BUG-001-cart-lines.png` — cart showing each line's unit price and quantity
- `evidence/BUG-001-cart-total.png` — the displayed total
- `evidence/BUG-001-review-total.png` — review screen showing delivery and order total
- `evidence/BUG-001-calculation.txt` — the hand calculation written out

### Business impact
The order total is the figure the customer legally agrees to pay. A discrepancy between the total and
the sum of its displayed components is the highest-severity defect class in an e-commerce
application: it is a direct financial error, it is visible to any customer who checks, and in the EU
it engages consumer-protection obligations around transparent pricing. Any consistent rounding
discrepancy also compounds across every order, so the aggregate effect is far larger than one cent.

### Suggested investigation
Determine whether the total is computed from the same rounded values that are displayed, or from
unrounded internal figures that are rounded only at the point of display. A mismatch between
"round then sum" and "sum then round" produces exactly this symptom, and it would affect every
order rather than only this combination.

---

## 3. Eight concrete leads — where to look first

These are **not confirmed defects.** They are places where a defect is genuinely likely in this
application, derived from reading its published open-source string resources and from common
e-commerce failure modes.

**Your job:** go to each one on your own device, look carefully, and report only what you can
actually see and reproduce. Some of these will be real. Verify before you write.

### Lead 1 — Spelling of the demo usernames on the login screen
**Where:** Menu → Log In → the Usernames list.
**What to do:** read each listed username **letter by letter**. Compare each against the conventional
example name it appears to intend — the standard placeholder names in demo software are `alice` and
`bob`.
**Why it matters if wrong:** anyone typing the value by eye instead of tapping it will fail to
authenticate, and the login screen is the first impression of the app's care level.
**Note:** the app lists three accounts. One is marked as locked out and one is named for visual
testing — those are deliberate, not typos. Look carefully at the third.
**Likely classification:** Low severity, P2 priority — trivial to fix, visible to everyone.
**Related case:** `TC-LOGIN-019`

### Lead 2 — Grammar of the logout confirmation message
**Where:** Log in, then Menu → Log Out.
**What to do:** read the confirmation message as a sentence. Check the capitalisation of every word,
particularly mid-sentence, and check the article and verb agreement.
**Why it matters if wrong:** user-facing copy is product. Mid-sentence capitalisation of an ordinary
word is a copy defect.
**Likely classification:** Low severity, P3 priority.
**Related case:** `TC-LOGIN-018`

### Lead 3 — The biometrics-unavailable message
**Where:** Menu → Biometrics, on a device with **no fingerprint enrolled**.
**What to do:** attempt to enable the fingerprint toggle and read the resulting message carefully as
a full sentence. Check whether it parses grammatically.
**Why it matters if wrong:** an error message the user cannot parse is functionally no message at
all — they are told something is wrong but not what to do.
**Likely classification:** Low severity, P2 priority — it appears at a moment of user confusion,
which is exactly when clarity matters most.
**Related case:** `TC-LOGIN-022`

### Lead 4 — Footer text consistency and copyright year
**Where:** Every screen with footer, legal or copyright text — check the review and confirmation
screens particularly.
**What to do:** transcribe each footer **character by character**, including punctuation and the
copyright symbol. Compare them against each other. Then compare the year against the current year.
**Why it matters if wrong:** two differently worded versions of the same legal footer proves the
string is duplicated rather than managed centrally — which means it will diverge further. A stale
copyright year is visible to every customer and unambiguously wrong after January.
**Likely classification:** Low severity, P3 priority — but two findings from one check.
**Related case:** `TC-CHKR-014`, `TC-XCUT-012`

### Lead 5 — Validation message specificity
**Where:** The payment form, and the shipping form.
**What to do:** trigger every validation error you can. Transcribe each message **verbatim**. For
each, ask: could a first-time user tell *which field* and *what is wrong with it*?
**Why it matters if wrong:** a message that says a value "looks invalid" names neither the field nor
the problem. The user's only recourse is to guess, at the most conversion-sensitive point in the app.
**Likely classification:** Low severity, P2 priority. **Propose specific replacement wording in the
ticket** — that is what turns a complaint into an actionable defect.
**Related case:** `TC-XCUT-017`, `TC-CHKP-004`

### Lead 6 — Placeholder or developer-facing strings visible to users
**Where:** Anywhere. Check every screen, including ones you would not normally linger on, and every
transient state.
**What to do:** look for text that was clearly written for a developer rather than a customer —
placeholder sentences, raw resource keys, "TODO", or Lorem-ipsum-style filler.
**Why it matters if wrong:** an unambiguous defect requiring no argument, and strong evidence that
strings are not being reviewed.
**Likely classification:** Low to Medium severity depending on visibility, P2 priority.
**Related case:** `TC-XCUT-012`

### Lead 7 — Currency format consistency across all four price screens
**Where:** Catalog tile → Product Detail → Cart line → Review screen, for the **same** product.
**What to do:** transcribe the price string exactly on all four screens, including the currency
symbol, its position, the decimal separator, and the number of decimal places. Then do the same for
the delivery charge and the order total.
**Why it matters if wrong:** inconsistent formatting is strong evidence that prices are built by
string concatenation in several places rather than by one formatting function. That guarantees a
divergence eventually, and it makes localisation impossible.
**Likely classification:** Medium severity, P2 priority — and a genuinely insightful finding, because
it points at an architectural cause rather than a surface symptom.
**Related case:** `TC-XCUT-014`, `TC-CAT-005`, `TC-PDP-002`

### Lead 8 — Duplicate order submission
**Where:** The Review screen, at the **Place Order** button.
**What to do:** tap **Place Order** ten times in under two seconds. Count how many confirmation
screens appear. Press back and check the cart badge and contents.
**Why it matters if wrong:** in a real payment system, an unguarded submit handler charges the
customer more than once. This is the **highest-severity defect class in the entire application** and
it takes ten seconds to test.
**Likely classification:** Critical severity, P1 Blocker — if it reproduces.
**Related case:** `TC-CHKR-008`

---

## 4. Other high-probability areas

Beyond the specific leads above, these are the recurring weak spots in e-commerce mobile apps.
Use them as places to *look*, and report only what you reproduce.

| Area | What to look for |
|---|---|
| **Cart badge vs actual contents** | Badge says one number, the cart holds another — especially after rapid adds |
| **Quantity stepper lower bound** | Reaching zero or negative; a zero-quantity line still contributing to the total |
| **State loss on process death** | Cart or session gone after `am force-stop` and relaunch |
| **Rotation during checkout** | A half-filled form wiped by rotating the device |
| **Back navigation** | Scroll position lost; sort reset; form data cleared |
| **Sort correctness** | An order that breaks at a scroll boundary; a product dropped or duplicated by sorting |
| **Offline behaviour** | Infinite spinner with no error and no retry; a false success indication |
| **Keyboard obscuring fields** | The last field on the shipping form hidden behind the keyboard |
| **Keyboard type** | An alphabetic keyboard on a numeric-only field |
| **Accessibility labels** | Colour swatches, steppers or the remove control announced only as "button" |
| **Text truncation at 200% font** | A price or total cut off mid-digit |
| **Touch target size** | Quantity steppers and colour swatches under 48 × 48 dp |
| **Unconfirmed destructive actions** | Reset App State wiping a full cart with no warning |
| **Credentials in logs** | Password or card number appearing in `adb logcat` output |

---

## 5. Ticket template — copy this for each finding

```markdown
# BUG-0NN — <short title>

### Summary
`[Module] What happens, where, under what condition`

| Field | Value |
|---|---|
| **Type** | Bug |
| **Severity** | Critical / High / Medium / Low |
| **Priority** | P1 Blocker / P2 High / P3 Medium / P4 Low |
| **Frequency** | Always (5/5) / Intermittent (N/5) |
| **Journey stage** | Discovery / Consideration / Conversion / Account / Cross-cutting |
| **Component** | |
| **Linked test case** | TC-…  or  Exploratory — Charter N |
| **Reported by** | Akanksh Gurupadappa Akki |
| **Date** | |

### Environment
| | |
|---|---|
| Device | |
| OS | |
| App version | |
| Network | |

### Preconditions
1.

### Steps to reproduce
1.
2.
3.

### Expected result
<what should happen, AND why — convention, consistency, or platform guideline>

### Actual result
<facts only; no speculation about the cause>

### Evidence
- `evidence/BUG-0NN-….png`
- `evidence/BUG-0NN-logcat.txt`

### Business impact
<one paragraph: what this costs the business, and who it affects>

### Suggested investigation
<optional — where you would look, stated as a question rather than a conclusion>
```

---

## 6. Triage summary

*Complete after your cycle. This feeds section 3 of the
[test summary report](18-TEST-SUMMARY-REPORT.md).*

| Severity | Count | P1 | P2 | P3 | P4 |
|---|---:|---:|---:|---:|---:|
| Critical | | | | | |
| High | | | | | |
| Medium | | | | | |
| Low | | | | | |
| **Total** | | | | | |

### By journey stage

| Stage | Defects | Notes |
|---|---:|---|
| Discovery | | |
| Consideration | | |
| Conversion | | |
| Account | | |
| Cross-cutting | | |

### By discovery method

| Found by | Defects | Hours | Defects per hour |
|---|---:|---:|---:|
| Scripted execution | | ~14 | |
| Exploratory charters | | 2.5 | |

**Release recommendation:** *(Go / Go with conditions / No-go — with the reasoning and a named owner
for the decision)*

---

## 7. Interview talking point

> "The two fields people leave out of a bug report are the app version and the frequency, and both
> are the difference between a ticket an engineer can act on and one that comes back with a question.
> Frequency has to be a hit rate — 'intermittent, three of five attempts' — not 'sometimes', because
> an engineer plans completely differently for a defect that reproduces every time.
>
> The other thing I always include is *why* the expected result is expected. If I just write 'the
> total should be $64.97', that's my assertion against theirs. If I write 'the total should equal the
> sum of the displayed line totals plus the stated delivery charge, because that is the figure the
> customer is agreeing to pay', then the expectation is grounded in something we both accept and the
> conversation is about the defect rather than about my opinion."
