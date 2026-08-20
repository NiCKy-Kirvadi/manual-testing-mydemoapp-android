# 19 · Journey Scope Split
## Discovery & Consideration vs the full funnel

---

## 1. Why this document exists

E-commerce quality work is normally organised around the **customer journey**, not around
screens. That is because the journey stages have genuinely different failure economics, and a team
is usually assigned to one of them.

| Stage | The customer is… | Screens in this app |
|---|---|---|
| **Discovery** | *finding* a product | Catalog, Sorting |
| **Consideration** | *evaluating* a product | Product Detail |
| **Conversion** | *buying* | Cart, Checkout (shipping, payment, review), order placement |
| **Retention** | *returning* | Not present in this app |

The reason the split matters is that **defects in Discovery and Consideration are silent**. If
checkout breaks, the customer complains — they were committed, they had entered their address, they
want their order. If sorting returns a wrong order or a product page shows a stale price, the
customer simply leaves, and nobody ever finds out. Those defects do not generate support tickets, so
they do not appear in the queue that drives prioritisation, and they can survive for years.

This suite covers the whole funnel, but every case carries a **journey stage** tag so it can be read
either way.

---

## 2. The two scopes

| Scope | Cases | Modules | Filter the workbook by |
|---|---:|---|---|
| **Discovery & Consideration only** | **42** | Catalog, Sorting, Product Detail | `Journey Stage` = Discovery or Consideration |
| **Full funnel** | **164** | All 15 | no filter |

### Full breakdown by stage

| Journey stage | Cases | Share (rounded) | Modules |
|---|---:|---:|---|
| Discovery | 25 | 15% | Catalog, Sorting |
| Consideration | 17 | 10% | Product Detail |
| Conversion | 60 | 37% | Cart, Checkout — Shipping, Checkout — Payment, Checkout — Review, End-to-End |
| Account | 22 | 13% | Login & Authentication |
| Cross-cutting | 40 | 24% | Menu & Navigation, WebView, Geo Location, Drawing, QR Scanner, Cross-cutting |
| **Total** | **164** | **100%** | |

Note that **Account** and **Cross-cutting** sit outside the funnel stages on purpose. Login is
not a journey stage — it is a gate that can appear at several points. Cross-cutting concerns
(lifecycle, accessibility, performance, localisation) apply to every screen, so grouping them avoids
duplicating the same case fifteen times.

---

## 3. The Discovery & Consideration subset in full

These **42 cases** are the narrower scope. If you are reviewing this suite against a team
whose remit stops before the cart, this is the relevant set.

| Case ID | Pri | Type | Module | Stage | Title |
|---|:---:|---|---|---|---|
| `TC-CAT-001` | P1 | Smoke | Catalog | Discovery | Catalog opens on launch and displays the product grid |
| `TC-CAT-002` | P1 | Smoke | Catalog | Discovery | Every product tile shows an image, a name and a price |
| `TC-CAT-003` | P2 | Functional | Catalog | Discovery | The catalogue contains a stable, repeatable number of products |
| `TC-CAT-004` | P2 | Functional | Catalog | Discovery | Every product tile displays a star rating |
| `TC-CAT-005` | P1 | Functional | Catalog | Discovery | Prices are displayed in a consistent currency format across the whole catalogue |
| `TC-CAT-006` | P1 | Smoke | Catalog | Discovery | Tapping a product tile opens the matching product detail page |
| `TC-CAT-007` | P2 | Performance | Catalog | Discovery | Catalogue scrolling is smooth and images do not repeatedly reload |
| `TC-CAT-008` | P2 | Functional | Catalog | Discovery | Scroll position is preserved when returning from a product detail page |
| `TC-CAT-009` | P1 | Functional | Catalog | Discovery | Cart badge on the Catalog header reflects the true cart contents |
| `TC-CAT-010` | P2 | Compatibility | Catalog | Discovery | Catalogue renders correctly in landscape orientation |
| `TC-CAT-011` | P2 | Accessibility | Catalog | Discovery | Catalogue remains usable at maximum system font and display size |
| `TC-CAT-012` | P2 | Accessibility | Catalog | Discovery | Screen reader announces every product tile meaningfully |
| `TC-CAT-013` | P2 | Destructive | Catalog | Discovery | Catalogue loads without a network connection |
| `TC-CAT-014` | P2 | Destructive | Catalog | Discovery | Rapid repeated taps on a product tile open exactly one detail page |
| `TC-SORT-001` | P1 | Smoke | Sorting | Discovery | Sort control opens and lists all four sort options |
| `TC-SORT-002` | P1 | Functional | Sorting | Discovery | Sort by name ascending orders the catalogue A to Z |
| `TC-SORT-003` | P2 | Functional | Sorting | Discovery | Sort by name descending orders the catalogue Z to A |
| `TC-SORT-004` | P1 | Functional | Sorting | Discovery | Sort by price ascending never steps down in price |
| `TC-SORT-005` | P2 | Functional | Sorting | Discovery | Sort by price descending never steps up in price |
| `TC-SORT-006` | P1 | Functional | Sorting | Discovery | Sorting changes only the order, never the set of products |
| `TC-SORT-007` | P2 | Functional | Sorting | Discovery | The selected sort option persists when returning from a product detail page |
| `TC-SORT-008` | P3 | Negative | Sorting | Discovery | Applying the same sort option twice does not reverse or corrupt the order |
| `TC-SORT-009` | P2 | Destructive | Sorting | Discovery | Rapidly switching between all four sort options leaves a correct final state |
| `TC-SORT-010` | P3 | Functional | Sorting | Discovery | Sort persists across an app restart |
| `TC-SORT-011` | P3 | Accessibility | Sorting | Discovery | Sort control is reachable and announced by a screen reader |
| `TC-PDP-001` | P1 | Smoke | Product Detail | Consideration | Product detail page displays name, price, image, rating and description |
| `TC-PDP-002` | P1 | Functional | Product Detail | Consideration | The price on the detail page matches the price on the catalogue tile |
| `TC-PDP-003` | P1 | Functional | Product Detail | Consideration | Colour variant selection updates the displayed product image |
| `TC-PDP-004` | P1 | Functional | Product Detail | Consideration | Quantity can be increased and decreased with the stepper controls |
| `TC-PDP-005` | P1 | Boundary | Product Detail | Consideration | Quantity cannot be decreased below its minimum |
| `TC-PDP-006` | P2 | Boundary | Product Detail | Consideration | Quantity behaviour at a high upper value is defined and does not break the layout |
| `TC-PDP-007` | P1 | Functional | Product Detail | Consideration | Add to cart increments the cart badge by exactly the selected quantity |
| `TC-PDP-008` | P2 | Functional | Product Detail | Consideration | Adding the same product twice consolidates rather than duplicating |
| `TC-PDP-009` | P3 | Functional | Product Detail | Consideration | Star rating control responds to taps and reflects the selected rating |
| `TC-PDP-010` | P2 | Functional | Product Detail | Consideration | Product image gallery can be swiped through in both directions |
| `TC-PDP-011` | P2 | Functional | Product Detail | Consideration | Product description and highlights are complete and free of encoding errors |
| `TC-PDP-012` | P3 | Functional | Product Detail | Consideration | Social media links on the product page open the correct destinations |
| `TC-PDP-013` | P1 | Functional | Product Detail | Consideration | Back navigation from the product detail page returns to the Catalog |
| `TC-PDP-014` | P2 | Accessibility | Product Detail | Consideration | Product detail page remains usable at maximum font size |
| `TC-PDP-015` | P2 | Accessibility | Product Detail | Consideration | Colour options are distinguishable without relying on colour alone |
| `TC-PDP-016` | P2 | Destructive | Product Detail | Consideration | Adding to cart while offline behaves predictably |
| `TC-PDP-017` | P1 | Destructive | Product Detail | Consideration | Rapid repeated taps on Add to cart add a defined, correct number of items |

**Subset totals:** 42 cases — P1 17 · P2 20 · P3 5.

By type: Smoke 5 · Functional 22 · Negative 1 · Boundary 2 · Destructive 5 · Accessibility 5 · Compatibility 1 · Performance 1.

---

## 4. What the wider funnel adds

The remaining **122 cases** cover authentication, the cart, all three checkout stages,
order placement, navigation, the supporting screens, and the cross-cutting non-functional
concerns. They are what let this project demonstrate things the narrower scope cannot:

| Only testable in the wider funnel | Which cases |
|---|---|
| **Arithmetic verification** — line totals, subtotals, delivery, order total | Cart and Checkout — Review |
| **Multi-step form validation** across three sequential screens | Checkout — Shipping and Payment |
| **Duplicate submission** — the highest-severity destructive finding in e-commerce | `TC-CHKR-008` |
| **Process death at a critical moment** — order placed or not? | `TC-CHKR-010`, `TC-E2E-004` |
| **Authentication gating** and the login handoff mid-checkout | `TC-CHKS-001`, `TC-E2E-001` |
| **Output encoding** followed from entry to display two screens later | `TC-CHKS-010` |
| **End-to-end journeys** and the seams between modules | All 5 `TC-E2E-*` cases |
| **Credential and payment privacy** — masking, screenshots, device logs | `TC-LOGIN-010`, `TC-CHKP-008`, `TC-CHKR-002`, `TC-XCUT-015` |

---

## 5. How to present this

If you are asked why the suite covers more than one team's remit, the honest and stronger
answer is that the scoping decision is visible rather than assumed:

> "I tagged every case with a journey stage, so the suite reads two ways. The full 164 cases cover
> the whole funnel, and filtering to Discovery and Consideration gives you the 42 that match a
> discovery-focused team's remit.
>
> I deliberately went wider than one stage here for two reasons. First, the arithmetic and
> duplicate-submission cases only exist downstream, and those are the highest-severity defect classes
> in retail software — I wanted to demonstrate that I test them. Second, the seams between stages are
> where defects actually hide: the login handoff mid-checkout is the clearest example, and you can
> only find it with a case that crosses the boundary.
>
> The scoping principle I'd apply on a real team is different, though. I'd scope to the journey stage
> the team owns, and then explicitly agree who owns the seams — because a defect in the handoff
> between two teams is the one that survives longest."
