# 06 · Boundary Value Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**9 test cases** in this document — 2 P1, 3 P2, 4 P3 — across 5 modules.

---

## 1. What this type of testing is

**Boundary value testing** targets the edges of input ranges, because that is where
off-by-one errors live.

The premise is empirical: defects cluster at boundaries. A developer writing
`if (quantity > 0)` when they meant `>=`, or `<= max` when they meant `< max`, produces software
that works perfectly for every value except one. Testing the middle of a range finds nothing;
testing the edge finds it immediately.

For any ordered input with a valid range, you test **the boundary and its immediate
neighbours**:

    ─────┬──────────────────────┬─────
       min-1   min  ...  max  max+1
       (invalid) (valid)  (valid) (invalid)

That is up to six values per boundary pair, and it is dramatically more productive per test than
sampling the interior.

---

## 2. Why it matters for this application

This app has several genuine boundaries, and two of them touch money directly.

**Quantity, lower bound.** The quantity stepper on the product page and in the cart must not go
below its minimum. If it reaches zero, the app has a line item worth nothing; if it goes negative,
the total does too. A negative line total is not a cosmetic bug — it is a way to reduce an order
total, and in a real system that is a fraud vector.

**Quantity, upper bound.** A high quantity tests two things at once: whether a limit is enforced,
and whether the *arithmetic still holds* at that scale. A total that is correct at quantity 2 and
wrong at quantity 30 usually means an integer overflow or a rounding accumulation.

**Card expiry date.** The most instructive boundary in the app, because the correct answer is
non-obvious: a card is valid *through the end of* its expiry month. So the current month must be
accepted and the previous month must be rejected — and the boundary moves every month, which is
exactly the kind of thing a hardcoded test misses.

**Field lengths.** Not about money, but about layout: a 200-character address that renders fine
on the form and breaks the review screen two steps later is a classic boundary defect, and it is
the reason this file's cases follow the value through to the end of the journey.

---

## 3. Technique used

**Boundary value analysis (BVA).** For each boundary, test:

- one value just **outside** the valid partition (expect refusal)
- the **boundary value itself** (expect the documented behaviour — this is the one that finds bugs)
- one value just **inside** the valid partition (expect acceptance)

Note that "outside" means *below* a lower bound but *above* an upper bound. Stating it as
inside/outside rather than below/above avoids getting it backwards on the upper bounds — which is
most of the boundaries in this suite.

**Two-value vs three-value BVA.** Two-value BVA tests the boundary value and its nearest neighbour
*outside* the valid partition. Three-value BVA adds the nearest neighbour *inside* it. Three-value is
used here, because that inside neighbour is precisely where `>` versus `>=` errors surface.

**BVA on unbounded inputs.** Where no maximum is documented — a text field with no stated limit —
you probe for the *implicit* boundary: the point where the field stops accepting characters, or
where the layout breaks. Finding that no limit exists at all is itself a valid finding, because
every field has a limit somewhere, and a limit you have not chosen is a limit you have not tested.

---

## 4. How to execute these cases

- Record the **exact** boundary value at which behaviour changes, not just "it stopped
  eventually". "The field stops accepting input at 50 characters" is actionable; "it seems to have
  a limit" is not.
- For the expiry-date cases, work out today's actual month first and derive the test values from
  it. Do not copy a date from this document — the boundary has moved since it was written.
- For the quantity upper bound, **verify the arithmetic at the boundary**, not just that the
  number displays. The point of the high quantity is the multiplication, not the digit count.
- For long-string cases, follow the value all the way to the review screen. The defect is usually
  downstream of where you typed it.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-LOGIN-012`](#tc-login-012) | P3 | Login & Authentication | Account | Username field accepts a maximum-length input without breaking the layout |
| [`TC-LOGIN-013`](#tc-login-013) | P3 | Login & Authentication | Account | Single-character username and password are handled at the lower boundary |
| [`TC-PDP-005`](#tc-pdp-005) | P1 | Product Detail | Consideration | Quantity cannot be decreased below its minimum |
| [`TC-PDP-006`](#tc-pdp-006) | P2 | Product Detail | Consideration | Quantity behaviour at a high upper value is defined and does not break the layout |
| [`TC-CART-006`](#tc-cart-006) | P1 | Cart | Conversion | Decreasing quantity to zero either removes the line or is prevented |
| [`TC-CHKS-009`](#tc-chks-009) | P3 | Checkout — Shipping | Conversion | Very long values in address fields do not break the layout or the review screen |
| [`TC-CHKP-006`](#tc-chkp-006) | P2 | Checkout — Payment | Conversion | Expiry date exactly at the current month is handled at the boundary |
| [`TC-CHKP-007`](#tc-chkp-007) | P2 | Checkout — Payment | Conversion | Security code enforces a sensible length |
| [`TC-CHKP-009`](#tc-chkp-009) | P3 | Checkout — Payment | Conversion | Card number field does not accept an unreasonable number of digits |

---

## 6. Test cases — full detail

### TC-LOGIN-012
**Username field accepts a maximum-length input without breaking the layout**

| | |
|---|---|
| Priority | **P3** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Boundary value analysis (upper bound) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | No |

**Preconditions**

User is on the Login screen. A 256-character string is on the clipboard.

**Steps**

1. Paste a 256-character string into the Username field
2. Observe whether the field truncates, scrolls or overflows
3. Tap Login
4. Observe the result

**Expected result**

The field either enforces a maximum length or scrolls its content. Text does not overflow outside the field's bounds and does not push other controls off screen. Login is refused cleanly with no crash.

---

### TC-LOGIN-013
**Single-character username and password are handled at the lower boundary**

| | |
|---|---|
| Priority | **P3** |
| Module | Login & Authentication |
| Journey stage | Account |
| Technique | Boundary value analysis (lower bound) |
| Risk covered | `R-02` — Form validation is absent or misleading, so users cannot self-correct |
| Automation candidate | Yes |

**Preconditions**

User is on the Login screen.

**Steps**

1. Type 'a' into Username
2. Type 'a' into Password
3. Tap Login
4. Observe the result

**Expected result**

Either a validation message states the minimum length, or a normal credential error is shown. In neither case does the app crash, hang, or accept the login.

---

### TC-PDP-005
**Quantity cannot be decreased below its minimum**

| | |
|---|---|
| Priority | **P1** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Boundary value analysis (lower bound) |
| Risk covered | `R-17` — Quantity controls allow an invalid quantity or miscalculate the line total |
| Automation candidate | Yes |

**Preconditions**

User is on a product detail page with quantity at its default.

**Steps**

1. Tap the decrease control repeatedly, at least ten times
2. Observe the quantity value after each tap
3. Attempt to add to cart at the resulting quantity

**Expected result**

The quantity stops at its minimum and never becomes zero or negative. The decrease control is disabled or ignored at the minimum. It must be impossible to add a zero or negative quantity to the cart.

---

### TC-PDP-006
**Quantity behaviour at a high upper value is defined and does not break the layout**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Boundary value analysis (upper bound) |
| Risk covered | `R-17` — Quantity controls allow an invalid quantity or miscalculate the line total |
| Automation candidate | Yes |

**Preconditions**

User is on a product detail page.

**Steps**

1. Tap the increase control repeatedly until the value stops rising or reaches 99
2. Note the highest value reached and whether a limit message appears
3. Observe whether the number still fits inside its control
4. Add to cart and check the cart total

**Expected result**

Either a maximum is enforced with clear feedback, or the value keeps rising. In either case the number stays inside its control without overlapping neighbours, and the cart total calculated from that quantity is arithmetically correct.

---

### TC-CART-006
**Decreasing quantity to zero either removes the line or is prevented**

| | |
|---|---|
| Priority | **P1** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Boundary value analysis (lower bound) |
| Risk covered | `R-21` — Order arithmetic is wrong — the customer is shown an incorrect total |
| Automation candidate | Yes |

**Preconditions**

Cart contains one product with quantity 1.

**Steps**

1. Tap the decrease control on that line
2. Observe whether the line is removed, the quantity stops at 1, or the quantity becomes 0
3. Check the total and the badge afterwards

**Expected result**

Either the line is removed cleanly with the total and badge updated, or the decrement stops at 1. A line sitting in the cart with quantity 0 while still contributing to the total is a defect.

---

### TC-CHKS-009
**Very long values in address fields do not break the layout or the review screen**

| | |
|---|---|
| Priority | **P3** |
| Module | Checkout — Shipping |
| Journey stage | Conversion |
| Technique | Boundary value analysis (upper bound) |
| Risk covered | `R-23` — Shipping form validation blocks valid input or accepts undeliverable input |
| Automation candidate | No |

**Preconditions**

User is on the shipping address screen.

**Steps**

1. Enter a 200-character string into address line 1
2. Fill the remaining mandatory fields normally and continue
3. Proceed to the review screen and inspect how the address is rendered

**Expected result**

The field either enforces a limit or scrolls. Crucially, the long value must not break the layout of the review screen further along — a defect that only surfaces two screens later is exactly what boundary testing is for.

---

### TC-CHKP-006
**Expiry date exactly at the current month is handled at the boundary**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Boundary value analysis (on and around the boundary) |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | No |

**Preconditions**

User is on the payment screen.

**Steps**

1. Enter the current month and year as the expiry date
2. Submit and record the result
3. Enter next month and submit
4. Enter a date twenty years in the future and submit

**Expected result**

The current month should be accepted, since cards remain valid through their expiry month. Next month is accepted. Record what happens with a far-future date — an unbounded upper limit is worth noting.

---

### TC-CHKP-007
**Security code enforces a sensible length**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Boundary value analysis |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | Yes |

**Preconditions**

User is on the payment screen. Read the on-screen hint about how many digits are expected.

**Steps**

1. Read the security-code hint text and note the number of digits it specifies
2. Enter one digit fewer than specified and submit
3. Enter exactly the specified number and submit
4. Enter ten digits and submit

**Expected result**

Too few digits is rejected, the correct length is accepted, and an over-long value is either truncated at the limit or rejected. The behaviour must match the on-screen hint — a hint that contradicts the validation is itself a defect.

---

### TC-CHKP-009
**Card number field does not accept an unreasonable number of digits**

| | |
|---|---|
| Priority | **P3** |
| Module | Checkout — Payment |
| Journey stage | Conversion |
| Technique | Boundary value analysis (upper bound) |
| Risk covered | `R-27` — Payment validation accepts invalid card data, guaranteeing a later failure |
| Automation candidate | No |

**Preconditions**

User is on the payment screen.

**Steps**

1. Enter a 15-digit number and note whether it is accepted
2. Enter a 16-digit number and note the result
3. Enter a 19-digit number and note the result
4. Attempt to enter 50 digits and observe whether the field stops accepting input

**Expected result**

Real card numbers are 13 to 19 digits. The field should stop accepting input at a sensible maximum. A field that accepts fifty digits without complaint indicates no length validation at all.

---

## 7. What "done" looks like

- Every boundary case executed with the exact transition value recorded.
- Quantity arithmetic verified at both the lower and upper bound.
- Any field found to have no effective maximum length raised as a finding, even if nothing visibly
  broke.

---

## 8. Interview talking point

> "Boundary testing is the highest-yield technique per test case, because defects genuinely
> cluster at edges. My favourite example in this app is the card expiry date — the correct
> behaviour is that a card is valid through the end of its expiry month, so the current month must
> be accepted and last month must be rejected. And the boundary moves every month, which means a
> test written with a hardcoded date silently stops testing anything. I derive the values from
> today's date at execution time."
