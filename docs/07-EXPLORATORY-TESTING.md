# 07 · Exploratory Testing
### Session-Based Test Management · 5 charters × 30 minutes

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** [Manual Testing Portfolio](../README.md)

**5 charters** — this document contains no scripted test cases, deliberately. That is what makes
it exploratory.

---

## 1. What this type of testing is

**Exploratory testing** is simultaneous learning, test design and test execution. You do not follow
a script; you form a hypothesis about where a defect might be, design a test on the spot, run it,
and let the result shape the next test.

The distinction that matters:

| Scripted testing | Exploratory testing |
|---|---|
| Confirms what you already believed | Discovers what nobody considered |
| Designed in advance, executed later | Designed and executed together |
| Repeatable and delegable | Depends on the tester's judgement |
| Finds **known-unknowns** | Finds **unknown-unknowns** |

**Exploratory testing is not "clicking around".** That misconception is the single biggest reason
it gets cut from sprint plans — because unstructured work is impossible to estimate, report or
defend. So this document uses **Session-Based Test Management (SBTM)**, which gives exploration a
structure without giving it a script.

The four elements of SBTM:

1. **A charter** — a one-sentence mission, written *before* you start.
2. **A time box** — 30 minutes here, timer running. When it rings, you stop.
3. **A session sheet** — notes taken *during* the session, not reconstructed afterwards.
4. **A debrief** — 10 minutes: what did you learn, what would you test next, what did you not reach.

That structure is what makes exploratory work **estimable, reportable and therefore fundable**.

---

## 2. Why it matters for this application

Two arguments, and the second is the one that changes minds.

**The obvious argument:** the 164 scripted cases in this suite were designed by one person with
finite imagination. Every defect they find is a defect somebody already thought of. The defects that
reach production are, almost by definition, the ones nobody thought of — so a suite with no
exploratory component has a systematic blind spot exactly where it hurts most.

**The argument worth making in an interview:** exploratory testing usually has a **higher defect
detection rate per hour** than scripted execution. Not always, but often, and especially on a mature
feature where the scripted cases have all passed several times already. Section 5 of this document
asks you to measure both and compare — and if exploration wins, that measurement is the argument for
protected exploratory time every sprint. It converts "I'd like some time to poke at it" into "here
is the detection rate; here is what we lose by cutting it".

**Where the charters are aimed.** Five 30-minute sessions is 2.5 hours, so they are pointed at the
areas where the scripted suite is weakest — combinations and sequences, which is exactly what scripted
cases handle badly:

- **Charter 1** — the cart state machine driven through unusual sequences
- **Charter 2** — the three-screen checkout form driven backwards and sideways
- **Charter 3** — state persistence under lifecycle abuse
- **Charter 4** — a deliberate hunt for copy, content and consistency defects
- **Charter 5** — sequences no scripted case would ever prescribe

---

## 3. Technique used

**Session-Based Test Management** (Jonathan and James Bach), with test ideas seeded from
**heuristics**.

### The task breakdown

At the end of every session, split your 30 minutes three ways. It should total 100%:

| Category | What it covers |
|---|---|
| **Test design & execution** | Time actually testing |
| **Bug investigation & reporting** | Time chasing and writing up something you found |
| **Session setup** | Time lost to environment, data, waiting, reinstalling |

**If setup exceeds 25%, your environment is the problem, not the app.** That number is worth
tracking across sessions, because it tells you when to invest in tooling rather than in more testing.

### Heuristics used to seed test ideas

You are not inventing tests from nothing — you are applying patterns. The ones used here:

| Heuristic | The question it asks |
|---|---|
| **Goldilocks** | What happens with too few, too many, and just right? |
| **CRUD** | Can I create, read, update and delete this? In every order? |
| **Interruption** | What if I stop halfway? Back out? Kill it? |
| **Sequence** | What if I do these in an order nobody expected? |
| **Zero / one / many** | Empty cart, one item, thirty items |
| **Repetition** | What if I do the same thing twice? Ten times? |
| **Boundary crossing** | What if I enter a value here and view it *there*? |
| **Configuration change** | What if the device rotates, or the font grows, mid-task? |

### What separates a charter from wandering

A charter names three things: an **area**, an **activity**, and a **class of defect you are hunting**.

> *Explore* the **cart and its quantity controls** *with* **unusual add, edit and remove sequences**
> *to discover* **arithmetic and state-synchronisation defects.**

If your charter does not name what you are hunting, you will drift. If it names it too narrowly, you
have written a test case and lost the point.

---

## 4. How to execute these charters

**Before you start:**
- Complete the scripted functional suite first. Exploration is far more productive once you know the
  app — you cannot notice something odd until you know what normal looks like.
- Reset the app: menu → **Reset App State**.
- Set a **30-minute timer** on your phone. Actually set it.
- Have the session sheet open and ready to type into.

**During:**
- **Take notes as you go**, not afterwards. Reconstructed notes lose exactly the detail that makes a
  defect reproducible — what you had just done before it happened.
- When you find something, decide immediately: **investigate now, or note and move on?** Both are
  valid. Chasing everything means you cover one area; noting everything means you investigate
  nothing. Record which you chose in the task breakdown.
- **Stop when the timer rings**, even mid-thought. The time box is what makes the session comparable
  to other sessions, and comparability is what makes it estimable.
- Capture evidence at the moment of failure — see [`evidence/README.md`](../evidence/README.md).

**After — the debrief is not optional:**
Spend 10 minutes writing: bugs found, **questions raised** (things you would ask the Product Owner,
which are not defects), coverage gaps, and the task breakdown.

The **questions raised** section is the one people skip and the one that most demonstrates
judgement. "Is it intended that the cart persists after logout?" is not a defect — you do not know
the intent. Logging it as a defect burns credibility with the team. Logging it as a question earns
it.

---

# Charter 1 — Cart arithmetic and state synchronisation

| Field | Value |
|---|---|
| **Charter** | *Explore* the **cart, quantity steppers and cart badge** *with* **unusual add, edit and remove sequences** *to discover* **arithmetic errors and state desynchronisation between the badge, the line items and the total** |
| **Areas** | Product Detail quantity stepper · Cart line steppers · Remove item · Cart badge · Cart total |
| **Time box** | 30 min |
| **Priority** | Highest — this is where the money is |
| **Tester** | Akanksh Gurupadappa Akki |
| **Date** | *(fill in)* |
| **Build** | *(app version)* |

### Test ideas to seed the session

- Add the same product **three separate times** at quantity 1. Does the cart hold one line of 3, or
  three lines of 1? Does the badge agree with the total either way?
- Add a product at quantity 5, then reduce it to 1 **in the cart**. Then increase it back to 5. Is
  the total identical to the original?
- Add product A, add product B, remove A, add A again. Is the order of lines stable? Is the total right?
- Set a quantity to a high value (20+). Does the arithmetic still hold? Does the number still fit
  inside its control?
- Increase and decrease a quantity **rapidly** — 20 taps in a few seconds. Does the total keep up, or
  lag behind by one interaction?
- Remove the **last** item. Then the **first**. Then the **middle** one from a list of three. Does the
  right line disappear each time?
- Add items, go to checkout, press **back** to the cart, edit a quantity, go forward again. Does the
  review screen reflect the edit?
- Empty the cart completely, then add one item. Is the empty state fully cleared?
- Does the badge ever disagree with the number of items actually in the cart? Try to make it happen.
- Add an item, **Reset App State**, then add a different item. Is anything left over from before?

### Session sheet — *fill in during the session*

| Time | Observation | Investigate now / note? |
|---|---|---|
| 00:00 | | |
| 00:05 | | |
| 00:10 | | |
| 00:15 | | |
| 00:20 | | |
| 00:25 | | |

### Debrief

- **Bugs found:**
- **Questions raised (not defects):**
- **Areas not covered — next session should include:**
- **Task breakdown:** design & execution ___% · bug investigation ___% · setup ___%

---

# Charter 2 — The three-screen checkout form, driven backwards

| Field | Value |
|---|---|
| **Charter** | *Explore* the **shipping, payment and review screens** *with* **backward and out-of-order navigation and partial data entry** *to discover* **data loss, validation inconsistencies and state divergence between what was entered and what is reviewed** |
| **Areas** | Shipping form (7 fields) · Payment form (3 fields + checkbox) · Review screen · Back navigation |
| **Time box** | 30 min |

### Test ideas to seed the session

- Fill shipping fully, go to payment, press **back**. Is every shipping field still populated?
- Fill shipping, go to payment, fill payment, press back **twice**. Where do you land? What survives?
- Fill shipping **partially** (three of seven fields), press back, come forward again. Are the three
  retained?
- Enter a value in shipping, go all the way to review, come back, **change** it, go to review again.
  Does the review show the new value or the old one?
- Enter `<b>Test</b>` as the full name. Check how it renders on the **review** screen — literal text
  or bold?
- Enter a 200-character address line. Check the **review** screen layout, not just the form.
- Untick "billing address same as shipping". Does anything appear? Re-tick it. Does it disappear cleanly?
- Fill the payment form, then rotate the device. Is the data still there?
- Enter an expiry date in the past. Then this month. Then next month. Where exactly is the boundary?
- Submit each form with exactly one field missing, cycling through which field. Is the message always
  specific to the right field?
- Tap the field nearest the bottom of the shipping form. Does the keyboard cover it?
- Which keyboard appears for the zip code? For the card number? For the security code?
- Reach review, press back to payment, change the card, return to review. Does the masked card update?

### Session sheet

| Time | Observation | Investigate now / note? |
|---|---|---|
| | | |

### Debrief

- **Bugs found:**
- **Questions raised:**
- **Areas not covered:**
- **Task breakdown:** ___% · ___% · ___%

---

# Charter 3 — State persistence under lifecycle abuse

| Field | Value |
|---|---|
| **Charter** | *Explore* **cart, session and selection persistence** *with* **process death, backgrounding, rotation and configuration change at every point in the journey** *to discover* **state loss and inconsistent restoration** |
| **Areas** | Whole journey · Cart · Login session · Sort selection · Partially completed forms |
| **Time box** | 30 min |
| **Setup** | Enable **Developer options → Don't keep activities** for part of this session. **Turn it off afterwards.** |

### Test ideas to seed the session

- Kill the app at each of these points and observe what survives: on the catalogue · with a sort
  applied · with items in the cart · on the shipping form half filled · on the payment form ·
  **on the review screen just before placing the order**.
  ```
  adb shell am force-stop com.saucelabs.mydemoapp.android
  ```
- The review-screen case is the important one: after relaunch, **was the order placed or not?** Is the
  cart empty or intact? An empty cart with no order is silent data loss.
- Enable **Don't keep activities**, then navigate Catalog → Product → Cart, background, return, and
  press back twice. Any crash? Any blank screen?
- Rotate the device on every screen, **mid-task** rather than idle. Half-filled forms are where the
  defect is.
- Background the app for **10 minutes** with a half-filled payment form, then return.
- Change the system font size to maximum **while the app is backgrounded**, then return. Does the app
  handle the configuration change or restart?
- Log in, kill the app, relaunch. Still logged in? Log out, kill, relaunch. Still logged out?
- Apply a sort, kill the app, relaunch. Is the sort retained? Nothing in the app states what should
  happen here, so record the behaviour and raise it as a **question**, not a defect.
- Use **Reset App State** and then immediately kill and relaunch. Is the reset itself persisted?
- Toggle airplane mode on and off at each stage of checkout.

### Session sheet

| Time | Observation | Investigate now / note? |
|---|---|---|
| | | |

### Debrief

- **Bugs found:**
- **Questions raised:**
- **Areas not covered:**
- **Task breakdown:** ___% · ___% · ___%

> **Remember to turn *Don't keep activities* back off**, or every later test runs under
> unrepresentative conditions.

---

# Charter 4 — A deliberate hunt for copy, content and consistency defects

| Field | Value |
|---|---|
| **Charter** | *Explore* **every user-facing string in the application** *with* **careful character-by-character reading and cross-screen comparison** *to discover* **spelling errors, grammatical errors, placeholder text, inconsistent wording and stale content** |
| **Areas** | Every screen · every error message · every empty state · footers · button labels |
| **Time box** | 30 min |

### Why this charter is worth 30 minutes

Copy defects are the easiest defects to find and the easiest to dismiss, and dismissing them is a
mistake. They are **unambiguous** — there is no argument about whether a misspelling is a
misspelling — they are **visible to every single customer**, and they are the cheapest possible fix.

They are also the clearest demonstration of attention to detail available to you. A tester who
reports a misspelled credential on the login screen has demonstrably *read the screen* rather than
just tapped through it.

This app is open source, which means its user-facing strings are published and readable. That gave
this project several concrete leads — see [document 17](17-DEFECT-REPORTS.md), section 3. **Go and
look at each one on your own device and report only what you actually see.**

### Test ideas to seed the session

- Read every **username in the login credential list** letter by letter. Compare each against the
  conventional example name it appears to intend.
- Read the **logout confirmation message** word by word. Check capitalisation mid-sentence.
- Trigger the **biometrics unavailable** message on a device with no fingerprint enrolled. Read it as
  a sentence. Is it grammatical?
- Find every **footer / legal / copyright** string in the app. Compare them against each other for
  identical wording and punctuation. Check the copyright **year** against the current year.
- Read every **validation message**. Does each name the field and the problem, or just say something
  is wrong?
- Read every **empty state**. Cart empty. WebView with no URL entered. QR scanner with the camera
  permission denied. Do they offer a way forward?
- Look for any visible **placeholder string** or raw resource key — text that was clearly meant for
  developers.
- Compare the **same concept's wording across screens**: is it "Log In" everywhere, or "Login"
  somewhere and "Sign in" elsewhere?
- Check every **price** for consistent currency format across all four screens that display one.
- Read the **order confirmation** screen. It is the last thing a customer sees; errors there cost the
  most trust.
- Check **button labels** for consistency: title case or sentence case? Pick one and see if the app did.

### Session sheet

| Time | Screen | Exact text observed | Issue |
|---|---|---|---|
| | | | |

### Debrief

- **Bugs found:**
- **Questions raised:**
- **Areas not covered:**
- **Task breakdown:** ___% · ___% · ___%

---

# Charter 5 — Sequences nobody would prescribe

| Field | Value |
|---|---|
| **Charter** | *Explore* **the whole application** *with* **deliberately strange but plausible action sequences** *to discover* **defects arising from unexpected ordering, repetition and interleaving** |
| **Areas** | Whole app · menu · permissions · WebView · drawing · QR scanner · interleaved journeys |
| **Time box** | 30 min |

### Why sequences are the right target for the last session

Scripted test cases are, structurally, a **single ordered path** through a feature. They are very
good at that and almost blind to everything else. But a real user's session is not a path — it is a
meander: they start checking out, get distracted, browse something else, come back, change their
mind, and finish.

Every one of those detours is a state transition nobody wrote a case for. This charter goes looking
for them.

### Test ideas to seed the session

- Start checkout, abandon at the payment screen, go back to the catalogue, add **more** items, return
  to checkout. Does the review screen show the new total?
- Log out **while** on the payment screen. What happens?
- Add to cart from the product page, then immediately open the **menu** before the badge updates.
- Open the **QR scanner**, deny the camera permission, go to the catalogue, come back to the scanner.
  Is it asked again? Handled gracefully?
- Open **geo location**, start observing, leave the screen mid-observation, come back.
- Draw on the drawing pad, navigate away **without** saving, come back. What happened to the drawing?
- Use the **WebView** to load a page, then press back repeatedly. Where do you end up?
- Interleave: product → cart → product → cart → product, ten times. Any accumulation? Any leak?
- Place an order, then immediately press **back** from the confirmation screen. Where do you go? Can
  you place the same order twice?
- Rate a product 5 stars, then 1 star, then 5 again, rapidly.
- Change the sort **while** the catalogue is still scrolling.
- Add 30 items to the cart and then try to check out. Does the review screen cope?
- Open every menu destination in turn without returning to the catalogue between them.
- Do something genuinely odd that occurs to you in the moment — **and write down what made you try
  it.** That note is often more valuable than the result, because it is a reusable heuristic.

### Session sheet

| Time | Sequence attempted | Observation |
|---|---|---|
| | | |

### Debrief

- **Bugs found:**
- **Questions raised:**
- **Areas not covered:**
- **Task breakdown:** ___% · ___% · ___%

---

## 5. Consolidated results

*Complete after all five sessions. This table goes into the
[test summary report](18-TEST-SUMMARY-REPORT.md).*

| Charter | Duration | Bugs found | Highest severity | Questions raised | Key insight |
|---|---|---:|---|---:|---|
| C1 — Cart arithmetic & state sync | 30 min | | | | |
| C2 — Checkout form, backwards | 30 min | | | | |
| C3 — Lifecycle persistence | 30 min | | | | |
| C4 — Copy, content & consistency | 30 min | | | | |
| C5 — Unprescribed sequences | 30 min | | | | |
| **Total** | **2.5 h in session** | | | | |

*(Plus roughly 50 minutes of debriefs — budget **3.5 hours** in total for this document.)*

### The comparison that matters

| | Defects found | Hours | Defects per hour |
|---|---:|---:|---:|
| Scripted execution (164 cases) | | ~14 | |
| Exploratory (5 charters) | | 2.5 | |

*(Use in-session time — 2.5 h — for the rate calculation, so it is comparable to scripted execution time.)*

**If the exploratory rate is higher, you have your argument for protected exploratory time every
sprint** — with a number attached. This is the single most useful measurement in the whole project,
because it converts a preference into a business case.

### Recommended next charters

Based on the coverage gaps identified in the debriefs, the natural follow-ups are:

1. Deep links into the app from an external source
2. Full TalkBack traversal treated as exploration rather than as a checklist
3. Behaviour under sustained poor network conditions rather than binary on/off
4. Interaction between biometric login and the standard credential path

---

## 6. What "done" looks like

- All five charters completed within their time boxes.
- Every session sheet filled in **during** the session, not reconstructed afterwards.
- Every debrief completed, including the **questions raised** section.
- Task breakdowns totalling 100% for each session, with setup time under 25%.
- The scripted-versus-exploratory detection rate calculated and recorded.
- Every reproducible defect written up in [document 17](17-DEFECT-REPORTS.md) with evidence.

---

## 7. Interview talking point

> "I don't treat exploratory testing as unstructured, because unstructured work can't be estimated
> and therefore always gets cut. I use session-based test management: a written charter that names the
> area, the activity and the class of defect I'm hunting, a 30-minute time box, notes taken live, and
> a debrief that reports bugs found, questions raised, coverage gaps and a task breakdown.
>
> Two details I'd point at specifically. The task breakdown splits the session into test design, bug
> investigation and setup — and if setup is over 25%, that tells me the environment is the problem,
> not the app. And the debrief separates **bugs** from **questions**: 'is it intended that the cart
> survives logout?' isn't a defect, because I don't know the intent. Raising that as a bug burns
> credibility with the team; raising it as a question earns it.
>
> The measurement I care about most is the detection rate comparison. On this project I tracked
> defects per hour for scripted execution against exploratory sessions. When exploration wins — which
> it usually does on a feature where the scripted cases have already passed a few times — that number
> is the business case for protecting exploratory time in every sprint."
