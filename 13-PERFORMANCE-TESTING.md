# 13 · Performance Testing (Client-Side)

**Application under test:** My Demo App (Android) — `com.saucelabs.mydemoapp.android`
**Part of:** Manual Testing Portfolio — see [`README.md`](../README.md)

**3 test cases** in this document — 1 P1, 2 P2, 0 P3 — across 2 modules.

---

## 1. What this type of testing is

**Performance testing** measures whether the application is fast and efficient enough that
users do not abandon it.

An important scoping distinction, and one worth stating explicitly in an interview so nobody
thinks you have confused the two:

- **Server-side performance testing** — load, stress and soak testing of backend APIs with tools
  like JMeter or k6. Measures throughput and latency under concurrent load. **Out of scope here**:
  this is a demo app with no backend to load, and load-testing infrastructure you do not own is
  neither permitted nor useful.
- **Client-side performance testing** — perceived responsiveness on the device: start-up time,
  scroll smoothness, interaction latency, memory growth. **This is what this file covers**, and it
  is what a mobile QA engineer measures in practice.

Client-side performance is a **functional requirement in disguise**. Users do not report slowness;
they uninstall.

---

## 2. Why it matters for this application

Mobile users abandon fast, and the thresholds are well established:

| Metric | Target | Why |
|---|---|---|
| **Cold start** | under ~3 s to interactive | Google's own guidance; beyond ~5 s abandonment rises steeply |
| **Warm start** | under ~1 s | Should feel instant; anything slower feels like a crash-and-restart |
| **Interaction response** | under ~100 ms | Threshold at which an action feels *caused* by the tap |
| **Scroll** | 60 fps, no dropped frames | Jank is the most-noticed and least-reported quality signal |
| **ANR** | never | Main thread blocked over 5 s triggers Android's "app not responding" dialog |

Two things in this app carry the most performance risk. **Catalogue scrolling** — an image-heavy
grid is where recycling and caching defects show up as stutter and reloading tiles. And **memory
growth over a session** — an app that leaks gets slower the longer it is used, which is invisible
in a five-minute test and obvious in a thirty-minute one. That asymmetry is exactly why the soak
case exists.

Note the distinction between **interactive** and **visible**. An app that draws its UI in one
second but does not respond to taps for another two has a three-second start-up as far as the user
is concerned. Always measure to interactive.

---

## 3. Technique used

**1. Stopwatch measurement with repetition.** Unglamorous and perfectly valid. The
discipline is in the method:
- measure **five times**, not once
- report the **median and the worst case**, never the best — the worst case is what a frustrated
  user actually experiences
- measure to **interactive**, not to first pixel
- fix the conditions: same device, same network, same battery state, screen already unlocked

**2. Frame timing via ADB.** Objective scroll measurement:
```
adb shell dumpsys gfxinfo com.saucelabs.mydemoapp.android
```
Read the frame histogram. Frames over 16 ms are dropped at 60 fps. This turns "it feels janky"
into a number.

**3. Memory profiling.** Sample memory at intervals during a long session:
```
adb shell dumpsys meminfo com.saucelabs.mydemoapp.android
```
Take a reading at the start, at ten minutes and at thirty. **Steady growth that never returns
after garbage collection indicates a leak.** A single high reading does not.

**4. Visual jank observation.** Scroll the full catalogue at a steady pace and watch for stutter,
blank tiles, and images that reload after having already loaded. Reloading already-loaded images
means the list is not caching — a specific, actionable finding.

**5. Soak / endurance.** Use the app continuously for thirty minutes and compare responsiveness at
the end against the start. Also re-verify the cart arithmetic — accumulating rounding errors
surface here and nowhere else.

**Honest limitation to state up front:** these are single-device, manual measurements on an
unloaded system. They catch order-of-magnitude problems, which is the large majority of real
mobile performance defects. They do not replace instrumented profiling or production RUM data, and
claiming otherwise in an interview would be a mistake.

---

## 4. How to execute these cases

**Control your conditions or your numbers mean nothing:**
- Same device, plugged in or at a consistent battery level (battery saver throttles the CPU).
- Same network for every run.
- Close other apps before measuring.
- Screen already on and unlocked before you start the stopwatch.
- Discard the very first run after an install — first-launch work is not representative.

Record measurements as a table, not a sentence:

| Run | Cold start (s) |
|---|---|
| 1 | 2.4 |
| 2 | 2.1 |
| 3 | 2.6 |
| 4 | 2.2 |
| 5 | 3.1 |
| **Median** | **2.4** |
| **Worst** | **3.1** |

Report both figures and the device profile. A performance number without its conditions attached
is not evidence.

---

## 5. Test cases — summary

| Case ID | Pri | Module | Journey stage | Title |
|---|---|---|---|---|
| [`TC-CAT-007`](#tc-cat-007) | P2 | Catalog | Discovery | Catalogue scrolling is smooth and images do not repeatedly reload |
| [`TC-XCUT-001`](#tc-xcut-001) | P1 | Cross-cutting | Cross-cutting | Cold start reaches an interactive first screen within an acceptable time |
| [`TC-XCUT-002`](#tc-xcut-002) | P2 | Cross-cutting | Cross-cutting | Warm start from the app switcher is near-instant |

---

## 6. Test cases — full detail

### TC-CAT-007
**Catalogue scrolling is smooth and images do not repeatedly reload**

| | |
|---|---|
| Priority | **P2** |
| Module | Catalog |
| Journey stage | Discovery |
| Technique | Non-functional observation |
| Risk covered | `R-09` — App is slow to start, janky to scroll, or degrades over a session |
| Automation candidate | No |

**Preconditions**

User is on the Catalog screen.

**Steps**

1. Scroll from the top of the catalogue to the bottom at a steady pace
2. Scroll back to the top
3. Watch for stutter, blank tiles, and images that reload after having already loaded

**Expected result**

Scrolling is visually smooth with no freeze longer than a fraction of a second. Images that have already loaded stay loaded when scrolled back into view, which indicates the list is caching them rather than re-fetching.

---

### TC-XCUT-001
**Cold start reaches an interactive first screen within an acceptable time**

| | |
|---|---|
| Priority | **P1** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Non-functional measurement |
| Risk covered | `R-09` — App is slow to start, janky to scroll, or degrades over a session |
| Automation candidate | No |

**Preconditions**

App fully closed.

**Steps**

1. Force-stop the app
2. Start a stopwatch and tap the app icon simultaneously
3. Stop the stopwatch when the Catalog is interactive, not merely visible
4. Repeat five times and record all five timings

**Expected result**

All five cold starts complete within about three seconds on a mid-range device. Report the median and the worst case, not just the best — the worst case is what a frustrated user experiences.

---

### TC-XCUT-002
**Warm start from the app switcher is near-instant**

| | |
|---|---|
| Priority | **P2** |
| Module | Cross-cutting |
| Journey stage | Cross-cutting |
| Technique | Non-functional measurement |
| Risk covered | `R-09` — App is slow to start, janky to scroll, or degrades over a session |
| Automation candidate | No |

**Preconditions**

App has been used and then backgrounded.

**Steps**

1. Use the app, then background it with the home gesture
2. Wait ten seconds
3. Reopen from recents, timing until interactive
4. Repeat five times

**Expected result**

Warm starts are markedly faster than cold starts and consistently under about one second. The app returns to the screen it left rather than restarting at the Catalog.

---

## 7. What "done" looks like

- Cold and warm start each measured five times, with median and worst case recorded.
- Full-catalogue scroll observed for jank, with `gfxinfo` captured.
- Thirty-minute soak completed with memory sampled at three points.
- Any measurement exceeding its target raised as a defect with the full run table attached.

---

## 8. Interview talking point

> "I keep client-side and server-side performance clearly separate — load testing a backend I
> don't own isn't in scope, and saying so is part of scoping properly. What I do measure is
> perceived performance: cold start to *interactive*, not to first pixel, five runs, and I report
> the median and the worst case rather than the best, because the worst case is what a frustrated
> user experiences. For scroll I use dumpsys gfxinfo so 'it feels janky' becomes a frame count. And
> I run a thirty-minute soak, because a memory leak is invisible in five minutes and obvious in
> thirty."
