# 12 · Compatibility Testing

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**4 test cases** in this document — 1 P1, 3 P2, 0 P3 — across 2 modules.

---

## 1. What this type of testing is

**Compatibility testing** verifies the application behaves correctly across the range of
devices, OS versions, screen sizes and configurations that real users have.

On Android this is the hardest testing problem there is, because the device population is
effectively unbounded — thousands of models, a dozen live OS versions, manufacturer skins that
override system behaviour, and screen sizes from small phones to tablets.

The correct response is not to attempt coverage. It is to **sample deliberately**, and to be able
to defend the sample. This distinction is the whole discipline: you are not claiming device
coverage, you are claiming **risk coverage**.

---

## 2. Why it matters for this application

Defects do not distribute randomly across devices — they cluster along specific axes,
and knowing the axes is what makes a small sample effective:

| Axis | Why defects cluster here | What it catches |
|---|---|---|
| **OS version** | Permission models, back-gesture behaviour and background limits changed between releases | Permission prompts, navigation, notifications |
| **Screen size / aspect ratio** | Layouts are usually designed at one size | Truncation, overlap, controls off screen |
| **Density (dpi)** | Wrong asset selection, mis-scaled touch targets | Blurry images, targets under 48 dp |
| **RAM tier** | Low-memory devices kill backgrounded processes aggressively | State loss, process-death crashes |
| **Manufacturer skin** | Samsung One UI and Xiaomi MIUI override system behaviour | Back gesture, permissions, battery optimisation |
| **Configuration change** | Rotation destroys and recreates the activity | Lost form data — worst during checkout |

**Rotation deserves special emphasis.** On Android, rotating the device destroys and recreates the
activity by default. Any state the developer did not explicitly save is gone. A half-completed
checkout form wiped by an accidental rotation is a high-severity defect, and it takes four seconds
to test on every screen. It is the highest-value compatibility case in this suite.

---

## 3. Technique used

**Risk-based device sampling.** Choose a small set that spans the axes above, and document
what each device is in the matrix *to catch*. That last part is what makes it defensible.

| # | Profile | Purpose |
|---|---|---|
| D1 | Your physical device | Primary execution — real touch, real network, real OEM skin |
| D2 | Pixel emulator, newest API | Reference AOSP behaviour on the latest platform |
| D3 | Emulator, older API, smaller screen | Platform-version differences and layout truncation |
| D4 | Emulator, low RAM (2 GB) | Process death and memory pressure |
| D5 | Tablet emulator | Layout at large widths and unusual aspect ratios |

**Equivalence partitioning applied to devices.** The same technique used for input values applies
to hardware: you are not testing "the Pixel 7", you are testing *one representative of the class
"recent mid-size phone on the current API"*. That reframing is what justifies five devices instead
of fifty.

**Configuration-change testing.** For each key screen, force each configuration change the OS can
impose — rotation, font scale, display size, dark mode, language — and verify state survives.

**Suite allocation** — you do not run everything everywhere:

| Device | What runs on it |
|---|---|
| D1 | Full suite plus all exploratory charters |
| D2 | Full suite |
| D3 | P1 and P2 cases |
| D4 | Smoke pack plus every destructive lifecycle case |
| D5 | Smoke pack plus layout inspection |

---

## 4. How to execute these cases

- **Install the identical APK build on every device.** Comparing different builds tells you
  nothing about compatibility.
- Record the **exact OS version, screen size, density and RAM** for every device in your matrix.
  A compatibility defect without a device profile attached is not reproducible.
- For rotation cases, test **mid-task**, not on an idle screen — rotate while a form is half
  filled. An empty form survives rotation; a half-filled one is where the defect is.
- To create the low-RAM emulator: Android Studio → Virtual Device Manager → Create Device → select
  a profile → **Show Advanced Settings** → set RAM to 2048 MB.
- Note every difference between devices even when nothing is broken. "The grid shows two columns
  on D3 and three on D2" may be intended, but it belongs in the report so someone can confirm it.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-CAT-010`](#tc-cat-010) | P2 | Catalog | Discovery | Catalogue renders correctly in landscape orientation |
| [`TC-XCUT-005`](#tc-xcut-005) | P1 | Cross-cutting | Cross-cutting | Rotating the device on every key screen preserves state without crashing |
| [`TC-XCUT-006`](#tc-xcut-006) | P2 | Cross-cutting | Cross-cutting | App behaves correctly across the supported Android version range |
| [`TC-XCUT-007`](#tc-xcut-007) | P2 | Cross-cutting | Cross-cutting | App renders correctly across different screen sizes and densities |

---

## 6. Test cases — full detail

### TC-CAT-010
**Catalogue renders correctly in landscape orientation**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Compatibility / configuration change |
| Risk covered | `R-12` — Rotation or a configuration change loses data or breaks the layout |
| Automation candidate | No |

**Preconditions**

Device auto-rotate is enabled. User is on the Catalog screen.

**Steps**

1. Note the number of grid columns in portrait
2. Rotate the device to landscape
3. Observe the layout, column count, and whether the scroll position is kept
4. Rotate back to portrait

**Expected result**

The grid reflows to use the wider screen. No content is cut off, no text overlaps, prices remain fully readable, and the scroll position is approximately preserved through both rotations. The app does not crash or restart.

---

### TC-XCUT-005
**Rotating the device on every key screen preserves state without crashing**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Compatibility (configuration change) |
| Risk covered | `R-12` — Rotation or a configuration change loses data or breaks the layout |
| Automation candidate | No |

**Preconditions**

Auto-rotate enabled.

**Steps**

1. On each of Catalog, product detail, cart, checkout shipping, checkout payment and review: rotate to landscape, observe, then rotate back
2. After each rotation verify the layout, the data on screen and the scroll position

**Expected result**

No screen crashes or loses entered data. Partially filled forms retain their values through rotation. Layouts reflow without truncation or overlap. Form data lost on rotation is a high-severity defect during checkout.

---

### TC-XCUT-006
**App behaves correctly across the supported Android version range**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Compatibility (platform matrix) |
| Risk covered | `R-12` — Rotation or a configuration change loses data or breaks the layout |
| Automation candidate | No |

**Preconditions**

Access to at least two Android versions, for example API 31 and API 34.

**Steps**

1. Install the same APK build on each device or emulator
2. Run the full smoke pack on each
3. Note any behavioural or visual difference between them

**Expected result**

Behaviour is equivalent on all tested versions. Any difference is documented with the API level, since platform changes to permissions, back-gesture handling and notification behaviour commonly cause version-specific defects.

---

### TC-XCUT-007
**App renders correctly across different screen sizes and densities**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Compatibility (device matrix) |
| Risk covered | `R-12` — Rotation or a configuration change loses data or breaks the layout |
| Automation candidate | No |

**Preconditions**

Access to a small phone, a large phone and a tablet profile.

**Steps**

1. Run the smoke pack on each screen profile
2. Compare the catalogue grid column count and tile proportions
3. Check that no control is cut off or unreachable on the smallest screen

**Expected result**

Layouts adapt to each screen size. Nothing is cut off on the smallest profile and nothing is absurdly stretched on the largest. Every interactive control remains reachable and tappable everywhere.

---

## 7. What "done" looks like

- The smoke pack passing on every device in the matrix.
- Rotation tested mid-task on Catalog, Product Detail, Cart, Shipping, Payment and Review.
- Every device-specific difference documented with the full device profile.
- Any state loss on rotation raised as a defect, with severity High if it occurs during checkout.

---

## 8. Interview talking point

> "I don't claim device coverage — I claim risk coverage. I sample across OS version, screen
> size, density, RAM tier and manufacturer skin, and I can defend what each device in the matrix is
> there to catch. It's equivalence partitioning applied to hardware: I'm not testing a Pixel 7,
> I'm testing one representative of 'recent mid-size phone on the current API'. And the single
> highest-value case is rotation mid-task, because Android destroys and recreates the activity by
> default — so a half-filled checkout form is where you find the defect, not an idle screen."
