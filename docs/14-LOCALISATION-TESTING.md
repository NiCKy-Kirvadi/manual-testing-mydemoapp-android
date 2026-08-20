# 14 · Localisation & Internationalisation Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**4 test cases** in this document — 1 P1, 1 P2, 2 P3 — across 2 modules.

---

## 1. What this type of testing is

Two related but distinct concerns, and mixing them up is a common interview stumble:

**Internationalisation (i18n)** — is the app *built* so it can be adapted? Are strings externalised
rather than hardcoded? Are dates, numbers and currencies formatted using locale-aware APIs? Does
the layout tolerate text that expands when translated? This is an architectural property, and you
test it by observing symptoms.

**Localisation (l10n)** — is a *specific* locale correct? Are the translations right, the currency
symbol correct and correctly positioned, the decimal separator right for that market?

The relationship is one-directional: **you cannot localise an app that was not internationalised.**
If prices are built by concatenating a hardcoded `"$"` with a number, no amount of translation work
will produce `19,99 €`.

---

## 2. Why it matters for this application

This matters directly for a German-market e-commerce employer, and the specifics are worth
knowing:

| Convention | German | English (US) |
|---|---|---|
| Decimal separator | comma — `19,99` | full stop — `19.99` |
| Thousands separator | full stop — `1.299,00` | comma — `1,299.00` |
| Currency position | after, with a space — `19,99 €` | before — `$19.99` |
| Date | `20.08.2026` | `08/20/2026` |
| Text length | ~30% longer than English | baseline |

That last row is the one that breaks layouts. German compound nouns are long —
*"Zahlungsinformationen"* against *"Payment details"* — and a button sized to fit English text
truncates. Testing at maximum font size and in the longest target language finds the same class of
defect from two directions.

**Symptoms of poor internationalisation, which is what you can actually observe as a black-box
tester:**
- prices with the currency symbol hardcoded in the wrong position
- the same value formatted differently on different screens — strong evidence of hardcoded strings
  rather than one formatting function
- a hardcoded copyright year, which cannot be localised and is also simply wrong after January
- visible placeholder strings or raw resource keys, which prove strings are not being managed
- two differently worded versions of the same footer text on different screens

---

## 3. Technique used

**1. Pseudo-localisation by inspection.** Without a translated build, you look for the
*symptoms* of hardcoding. Record the exact same value — one product's price — on all four screens
that display it (catalogue, product detail, cart, review). If the format differs anywhere, the
value is being constructed in more than one place, which guarantees a divergence eventually.
This is the highest-yield localisation check available on a black-box app.

**2. Locale switch.** Change the device language and relaunch. You are checking three things,
in order of severity:
- does the app **crash or show empty strings**? (defect)
- does it fall back to English? (acceptable if no translation exists — document it as a limitation)
- do **numbers and currency** still respect the locale even when the text does not? (this is the
  interesting one, and the answer is often no)

**3. Character-set handling.** Enter accented and non-Latin characters into every text field and
follow them through to the review screen. `Björn Müller-Schäfer` must survive intact. Mojibake such
as `MÃ¼ller` indicates an encoding defect in the pipeline, not in the keyboard.

**4. Static content review.** Read every user-facing string carefully. Placeholder text, raw
resource keys, hardcoded years and inconsistent wording between screens are all unambiguous,
easily-reported defects — and they are the kind of finding that demonstrates genuine attention to
detail rather than mechanical clicking.

**5. Text expansion tolerance.** Where a translated build is unavailable, maximum font size is a
reasonable proxy: both expose containers sized to exactly fit English text.

---

## 4. How to execute these cases

- Record every price string **verbatim, character by character**, including spaces around the
  currency symbol. `19,99 €` and `19,99€` are different findings.
- Change language at **Android Settings → System → Languages & input → Languages**, then
  **force-stop and relaunch** the app — many apps only re-read the locale at startup, and testing
  without the restart gives a false pass.
- Remember to change the device language **back** afterwards.
- For character-set cases, follow the value all the way to the review screen. Encoding defects
  usually appear at a boundary between components, not at the point of entry.
- A hardcoded copyright year is worth reporting even though it feels trivial: it is unambiguous,
  it is visible to every customer, and it demonstrates that the string is not managed.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-CHKR-014`](#tc-chkr-014) | P3 | Checkout — Review | Conversion | Footer legal text is consistent and its copyright year is current |
| [`TC-XCUT-012`](#tc-xcut-012) | P2 | Cross-cutting | Cross-cutting | All user-facing text is in a single consistent language |
| [`TC-XCUT-013`](#tc-xcut-013) | P3 | Cross-cutting | Cross-cutting | App behaves correctly when the device language is changed to German |
| [`TC-XCUT-014`](#tc-xcut-014) | P1 | Cross-cutting | Cross-cutting | Currency and decimal formatting is consistent everywhere a price appears |

---

## 6. Test cases — full detail

### TC-CHKR-014
**Footer legal text is consistent and its copyright year is current**

| | |
|---|---|
| Priority | **P3** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Static consistency review |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

User can see footer text on the review or confirmation screen.

**Steps**

1. Record the footer text on every screen that shows one, character by character
2. Compare the wording and punctuation between screens
3. Note the copyright year and compare it against the current year

**Expected result**

Footer text is identical wherever it appears and the copyright year is current. Two differently worded footers, or a year several years out of date, are genuine content defects that suggest hardcoded strings rather than a maintained single source.

---

### TC-XCUT-012
**All user-facing text is in a single consistent language**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Localisation review |
| Risk covered | `R-04` — User-facing copy contains errors, placeholders or inconsistencies |
| Automation candidate | No |

**Preconditions**

App installed with the device set to English.

**Steps**

1. Visit every screen in the app
2. Record any text that is not in the expected language, or that mixes languages within one screen
3. Note any untranslated placeholder or raw resource key visible to the user

**Expected result**

Every screen is in one consistent language. No raw resource identifiers, no placeholder strings such as 'Hello blank fragment', and no mixed-language screens. A visible placeholder string is an unambiguous defect.

---

### TC-XCUT-013
**App behaves correctly when the device language is changed to German**

| | |
|---|---|
| Priority | **P3** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Localisation (locale switch) |
| Risk covered | `R-24` — European names, characters and formats are mishandled |
| Automation candidate | No |

**Preconditions**

Device language can be changed in Android settings.

**Steps**

1. Change the device language to German
2. Relaunch the app and walk through the purchase journey
3. Note whether strings are translated, and how prices, dates and decimal separators are formatted

**Expected result**

Record the behaviour. If the app has no German translation, English strings are an acceptable fallback, but numbers and currency should still respect the locale. Falling back to English is a limitation to document; crashing or showing empty strings is a defect.

---

### TC-XCUT-014
**Currency and decimal formatting is consistent everywhere a price appears**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Static consistency oracle |
| Risk covered | `R-07` — The same price is displayed inconsistently across screens |
| Automation candidate | Yes |

**Preconditions**

Cart contains items. Access to Catalog, product detail, cart and review screens.

**Steps**

1. Record the exact price string for the same product on the Catalog, the product detail page, the cart and the review screen
2. Compare the symbol, its position, the decimal separator and the number of decimal places
3. Repeat for the delivery charge and the order total

**Expected result**

Every monetary value uses an identical format on every screen. A price shown one way on the catalogue and another way in the cart undermines confidence in the total, and mixed formats are a strong signal of hardcoded strings.

---

## 7. What "done" looks like

- The same product's price recorded on all four screens and compared character by character.
- A locale switch performed with a full app restart, and the behaviour documented.
- Accented and non-Latin names followed through to the review screen.
- Every placeholder string, raw resource key, hardcoded year and inconsistent footer recorded as a
  defect.

---

## 8. Interview talking point

> "I keep internationalisation and localisation separate: i18n is whether the app is *built* to
> be adapted, l10n is whether a specific locale is *correct*. You can't localise an app that wasn't
> internationalised. As a black-box tester I look for the symptoms of hardcoding — and the
> highest-yield check is recording the same product's price on all four screens that show it. If the
> format differs anywhere, the value is being built in more than one place, and it will diverge
> eventually. For a German market that matters a lot: comma decimal separator, currency after the
> number with a space, and German text runs about thirty percent longer than English, which is what
> breaks buttons sized to fit English."
