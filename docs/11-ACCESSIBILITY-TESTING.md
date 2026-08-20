# 11 · Accessibility Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**12 test cases** in this document — 1 P1, 10 P2, 1 P3 — across 7 modules.

---

## 1. What this type of testing is

**Accessibility testing** verifies that people with disabilities can use the application.
In practice that means people using a screen reader, people who need large text, people who cannot
distinguish certain colours, and people with limited fine motor control.

The reference standard is **WCAG 2.1 level AA**. It was written for the web but its success
criteria map cleanly onto mobile, and every case in this file names the specific criterion it
tests — which is what makes an accessibility finding citable rather than debatable.

The critical reframing: **accessibility is not a subset of usability, and it is not optional
polish.** If a blind customer cannot complete a purchase, the app does not have a usability
problem. It has a functional failure for that user, and increasingly a legal one.

---

## 2. Why it matters for this application

Three reasons, in ascending order of how much attention they get in a boardroom.

**1. It is the right thing to do**, and it affects more people than teams assume — roughly one in
six of the population has a disability, and the number of people using large text is far larger
than the number using a screen reader.

**2. It is a legal requirement in the EU.** The **European Accessibility Act** (Directive
2019/882) applies to e-commerce services, with obligations effective from **June 2025**. For a
German-market retailer this is compliance, not charity. Any candidate who can say this sentence
in an interview immediately sounds like someone who has worked in European retail.

**3. It is the same fix as automation testability.** This is the argument that actually gets
engineering time allocated, and it is worth understanding properly.

A screen reader announces an element using its accessibility label — `contentDescription` on
native Android views, `testTag` with `testTagsAsResourceId` in Compose. An automation framework
locates an element using the *same attribute*. So "add a stable accessibility label to every
interactive element" is **one change with three justifications**: it makes the app usable with
TalkBack, it makes automation possible and cheap to maintain, and it satisfies a compliance
requirement. Bringing that argument to an engineering team is a QA *manager's* contribution, not
a tester's.

---

## 3. Technique used

Four techniques, each mapped to WCAG criteria.

**1. Screen reader traversal (TalkBack).** Turn TalkBack on and complete a task using swipe
navigation only, never touching an element directly. You are checking four things:
- every interactive element **receives focus** (WCAG 2.1.1)
- every element is announced with a **meaningful label**, not "button" or "image" (WCAG 1.1.1, 4.1.2)
- focus order follows the **visual order** (WCAG 2.4.3)
- no **focus trap** prevents leaving a screen or an open menu (WCAG 2.1.2)

The decisive test is the end-to-end one: *can you buy something with your eyes closed?*

**2. Text scaling.** Set font size and display size to maximum and walk the journey (WCAG 1.4.4).
The failure to hunt for is **truncated monetary values** — a price or total cut off mid-digit is
not cosmetic, it means the user cannot see what they are paying.

**3. Colour independence.** Enable grayscale in Android's colour-correction settings and check
that every state is still perceivable (WCAG 1.4.1). The colour swatches on the product page are
the obvious risk: if selection is indicated by colour alone, it disappears in grayscale.

**4. Contrast and target size.** Use **Accessibility Scanner** (free, Play Store) — it automatically
flags contrast below 4.5:1 for body text and touch targets under 48 × 48 dp. Contrast is
**WCAG 1.4.3 at level AA**; the 48 dp figure comes from **Android's Material accessibility
guidance**, because WCAG 2.5.5 Target Size is level AAA and therefore outside this project's stated
AA reference standard. Run the scanner on each key screen and triage what it reports.

**An honest caveat to state in an interview:** automated scanners catch roughly a third of
accessibility issues. They will tell you a label is missing; they cannot tell you the label says
"button1". Manual traversal is not optional.

---

## 4. How to execute these cases

**Learn TalkBack's gestures before you start**, or you will mistake your own unfamiliarity
for a defect:

| Gesture | Action |
|---|---|
| Swipe right / left | Next / previous element |
| Double tap | Activate the focused element |
| Two-finger swipe | Scroll |
| Swipe up then right | Open TalkBack's global context menu |
| Volume up + volume down (hold 3s) | Toggle TalkBack off — **know this before you turn it on** |

Enable it at **Settings → Accessibility → TalkBack**.

- Run the **end-to-end TalkBack purchase** case last, once you are fluent with the gestures.
- Install **Accessibility Scanner** first and let it do the mechanical checks, so your manual time
  goes on the things it cannot see.
- Record findings with the **WCAG criterion number**. "Colour swatches announce as 'button' with
  no colour name — WCAG 1.1.1 Non-text Content" is citable. "Bad for screen readers" is not.
- Remember to turn TalkBack and maximum font size **off** afterwards.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-CAT-011`](#tc-cat-011) | P2 | Catalog | Discovery | Catalogue remains usable at maximum system font and display size |
| [`TC-CAT-012`](#tc-cat-012) | P2 | Catalog | Discovery | Screen reader announces every product tile meaningfully |
| [`TC-SORT-011`](#tc-sort-011) | P3 | Sorting | Discovery | Sort control is reachable and announced by a screen reader |
| [`TC-PDP-014`](#tc-pdp-014) | P2 | Product Detail | Consideration | Product detail page remains usable at maximum font size |
| [`TC-PDP-015`](#tc-pdp-015) | P2 | Product Detail | Consideration | Colour options are distinguishable without relying on colour alone |
| [`TC-CART-014`](#tc-cart-014) | P2 | Cart | Conversion | Cart is fully navigable with a screen reader |
| [`TC-CART-015`](#tc-cart-015) | P2 | Cart | Conversion | Cart totals remain readable at maximum font size |
| [`TC-CHKR-013`](#tc-chkr-013) | P2 | Checkout — Review | Conversion | Review and confirmation screens remain usable at maximum font size |
| [`TC-NAV-007`](#tc-nav-007) | P2 | Menu & Navigation | Cross-cutting | Menu is navigable with a screen reader |
| [`TC-XCUT-009`](#tc-xcut-009) | P1 | Cross-cutting | Cross-cutting | Every screen is traversable end to end with a screen reader |
| [`TC-XCUT-010`](#tc-xcut-010) | P2 | Cross-cutting | Cross-cutting | Touch targets meet the minimum recommended size |
| [`TC-XCUT-011`](#tc-xcut-011) | P2 | Cross-cutting | Cross-cutting | Text contrast is sufficient against its background |

---

## 6. Test cases — full detail

### TC-CAT-011
**Catalogue remains usable at maximum system font and display size**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Accessibility (WCAG 1.4.4 Resize Text) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

Android Settings: Font size and Display size both set to the largest option.

**Steps**

1. Set font size and display size to maximum in Android accessibility settings
2. Open the app and go to Catalog
3. Inspect every tile for truncated names, truncated prices and overlapping elements
4. Confirm every tile is still tappable

**Expected result**

Product names may wrap or ellipsize, but prices and ratings must remain fully readable — a truncated price is a commercial defect, not a cosmetic one. No element overlaps another. Every tile remains tappable.

---

### TC-CAT-012
**Screen reader announces every product tile meaningfully**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Accessibility (WCAG 1.1.1, 4.1.2) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

TalkBack enabled. User is on the Catalog screen.

**Steps**

1. Enable TalkBack in Android accessibility settings
2. Swipe right repeatedly to traverse the Catalog screen
3. Note what is announced for each tile, the menu icon and the cart icon
4. Note any element that is skipped or announced only as 'button' or 'image'

**Expected result**

Every interactive element receives focus and is announced with a meaningful label. Product images are described rather than announced as bare 'image'. The cart icon announces its purpose and its item count. Focus order follows the visual order.

---

### TC-SORT-011
**Sort control is reachable and announced by a screen reader**

| | |
|---|---|
| Priority | **P3** |
| Module | Sorting |
| Journey stage | Discovery |
| Technique | Accessibility (WCAG 4.1.2 Name, Role, Value) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

TalkBack enabled. User is on the Catalog screen.

**Steps**

1. Traverse to the sort control using TalkBack
2. Note the announcement, including whether the current selection is spoken
3. Activate it and traverse the options

**Expected result**

The control announces both its purpose and the currently selected sort. Each option is announced distinctly. A user who cannot see the highlight must still be able to tell which sort is active.

---

### TC-PDP-014
**Product detail page remains usable at maximum font size**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Accessibility (WCAG 1.4.4) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

Font size and display size set to maximum.

**Steps**

1. Set font and display size to maximum
2. Open a product detail page
3. Inspect the price, quantity value, colour labels and Add to cart button
4. Confirm Add to cart is fully visible and tappable

**Expected result**

The price and quantity remain fully readable and are never truncated. The Add to cart button stays fully on screen and tappable. No text overlaps another element.

---

### TC-PDP-015
**Colour options are distinguishable without relying on colour alone**

| | |
|---|---|
| Priority | **P2** |
| Module | Product Detail |
| Journey stage | Consideration |
| Technique | Accessibility (WCAG 1.4.1 Use of Colour) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

User is on a product detail page with multiple colour options. TalkBack enabled.

**Steps**

1. Traverse the colour options with TalkBack and note what is announced for each
2. Turn TalkBack off and use Android's grayscale or colour-correction setting
3. Determine whether you can still tell which colour is selected

**Expected result**

Each colour option is announced by name, not merely as 'button'. The selected state is conveyed by more than colour alone — a border, checkmark or label — so it remains perceivable in grayscale.

---

### TC-CART-014
**Cart is fully navigable with a screen reader**

| | |
|---|---|
| Priority | **P2** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Accessibility (WCAG 1.1.1, 2.4.6, 4.1.2) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

TalkBack enabled. Cart contains two products.

**Steps**

1. Traverse the cart screen with TalkBack
2. Note the announcement for each line, each stepper control and each remove control
3. Attempt to change a quantity and remove a line using TalkBack only

**Expected result**

Every line announces the product, its quantity and its price. Stepper and remove controls announce their action and which product they affect — a bare 'button' gives a blind user no way to know what they are about to delete.

---

### TC-CART-015
**Cart totals remain readable at maximum font size**

| | |
|---|---|
| Priority | **P2** |
| Module | Cart |
| Journey stage | Conversion |
| Technique | Accessibility (WCAG 1.4.4) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

Font and display size set to maximum. Cart contains two products.

**Steps**

1. Set font and display size to maximum
2. Open the cart
3. Inspect every price, quantity and the total
4. Confirm the checkout control is fully visible and tappable

**Expected result**

Every monetary value is fully readable and never truncated. The checkout control remains on screen and tappable. A truncated total is a commercial defect: the customer cannot see what they are about to pay.

---

### TC-CHKR-013
**Review and confirmation screens remain usable at maximum font size**

| | |
|---|---|
| Priority | **P2** |
| Module | Checkout — Review |
| Journey stage | Conversion |
| Technique | Accessibility (WCAG 1.4.4) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

Font and display size set to maximum.

**Steps**

1. Set font and display size to maximum
2. Walk through checkout to the review screen
3. Inspect the address, payment method, line prices, delivery charge and total
4. Confirm Place Order is fully visible and tappable

**Expected result**

Every monetary value remains fully readable and untruncated, and Place Order stays on screen and tappable. A user who cannot see the total or reach the button cannot complete a purchase at all.

---

### TC-NAV-007
**Menu is navigable with a screen reader**

| | |
|---|---|
| Priority | **P2** |
| Module | Menu & Navigation |
| Journey stage | Cross-cutting |
| Technique | Accessibility (WCAG 2.4.3 Focus Order) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

TalkBack enabled.

**Steps**

1. Traverse to the menu icon with TalkBack and activate it
2. Traverse every menu entry
3. Confirm each is announced by its name and that focus does not escape the open menu

**Expected result**

The menu icon announces its purpose. Every entry is announced by name. While the menu is open, focus stays within it rather than reaching the obscured content behind — otherwise a blind user can activate something they cannot see.

---

### TC-XCUT-009
**Every screen is traversable end to end with a screen reader**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Accessibility (WCAG 2.1 AA, end-to-end) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

TalkBack enabled.

**Steps**

1. With TalkBack on, complete a full purchase journey using swipe navigation only, never touching an element directly
2. Note every point where you get stuck, where focus is lost, or where an element is announced meaninglessly

**Expected result**

The entire purchase journey is completable with TalkBack alone. No focus trap prevents leaving a screen. Every control needed to buy something is reachable and meaningfully announced. If the journey cannot be completed, that is a high-severity accessibility defect and, for an EU e-commerce service, a compliance concern.

---

### TC-XCUT-010
**Touch targets meet the minimum recommended size**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Accessibility (Android Material 48dp guidance; WCAG 2.5.5 is AAA) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

App installed. A way to measure on-screen elements, such as the Android layout-bounds developer option.

**Steps**

1. Enable 'Show layout bounds' in Developer options
2. Inspect the quantity stepper controls, the remove-item control and the colour swatches
3. Estimate whether each is at least 48 by 48 density-independent pixels

**Expected result**

Every interactive control meets roughly 48 by 48 dp, which is Android's Material accessibility guidance. Small steppers and colour swatches are the usual offenders, and they disproportionately affect users with limited dexterity. Note that the equivalent WCAG criterion, 2.5.5 Target Size, is level AAA rather than AA, so this is a strong recommendation rather than an AA conformance failure.

---

### TC-XCUT-011
**Text contrast is sufficient against its background**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Accessibility (WCAG 1.4.3 Contrast) |
| Risk covered | `R-13` — Users relying on a screen reader, large text or colour alternatives cannot complete a purchase |
| Automation candidate | No |

**Preconditions**

App installed. A contrast-checking tool or the Android Accessibility Scanner installed.

**Steps**

1. Install Accessibility Scanner from the Play Store, or use a contrast checker on screenshots
2. Scan the Catalog, product detail, cart and review screens
3. Record every element flagged for insufficient contrast

**Expected result**

Body text achieves at least a 4.5 to 1 contrast ratio and large text at least 3 to 1. Prices and validation error messages matter most — an error the user cannot read is functionally invisible.

---

## 7. What "done" looks like

- Accessibility Scanner run on Catalog, Product Detail, Cart, Checkout and Review, with every
  flagged item triaged.
- Full purchase journey attempted with TalkBack; the outcome recorded either way.
- Maximum font size pass completed with every monetary value confirmed readable.
- Grayscale pass completed on the product page colour selector.
- Every finding carries its WCAG criterion.

---

## 8. Interview talking point

> "I run accessibility as four passes: TalkBack traversal, maximum text scaling, grayscale, and
> Accessibility Scanner for contrast and target size. The decisive test is whether I can complete a
> purchase with TalkBack alone — if I can't, that's a functional failure for that user, not a
> polish item. And the argument I'd bring to the app engineers is that the accessibility label a
> screen reader reads is the same attribute an automation framework locates. One change, three
> justifications: TalkBack support, cheap automation, and European Accessibility Act compliance,
> which has applied to e-commerce since June 2025. That's how you get the engineering time."
