# 00 · Setup and How to Run
### Beginner-level, Windows, step by step

This document gets you from "nothing installed" to "executing test cases". Every command is
explained. If a step looks obvious, it is still worth doing in order — the later steps depend on it.

---

## 0. What you need and what each thing is for

| Thing | What it actually does | Do I need it? |
|---|---|---|
| **An Android phone** | Runs the app you are testing | Yes — or an emulator instead |
| **A USB data cable** | Connects the phone to the PC for logs and screenshots | Yes, if using a phone |
| **ADB** (Android Debug Bridge) | Command-line tool to talk to the phone: install apps, take screenshots, read logs, kill the app | Yes |
| **Android Studio** | The easiest way to get ADB and the emulator. You will not write any code in it | Yes |
| **Excel or LibreOffice** | Opens the test-case workbook where you record results | Yes |
| **Git + GitHub account** | Publishes the work so it can be reviewed | Yes |
| **Accessibility Scanner** (phone app) | Automatically flags contrast and touch-target problems | Recommended |

You do **not** need Python, Java, Appium, or any programming tool. This project has no code.

---

## 1. Install Android Studio (this is how you get ADB)

**Why:** ADB is the single most useful tool in mobile manual testing. It takes screenshots, records
video, reads crash logs and kills the app on demand — all things you need for evidence.

1. Go to <https://developer.android.com/studio> and download **Android Studio** for Windows.
2. Run the installer and accept all the defaults. Click Next through every screen.
3. On first launch a **Setup Wizard** appears. Choose **Standard**, accept the licences, and let it
   download. It is a large download — start it and go and do something else.
4. When you reach the welcome screen, click the **three-dot / More Actions** menu → **SDK Manager**.
5. Note the **Android SDK Location** shown at the top. It is normally:
   ```
   C:\Users\<YourName>\AppData\Local\Android\Sdk
   ```
   **Copy that path.** You need it in the next step.

---

## 2. Make ADB work from any terminal

Right now `adb` exists on your computer but Windows does not know where to find it. This step fixes
that.

**How to open a terminal:** press the `Windows` key, type `cmd`, press Enter. The black window that
opens is **Command Prompt**. Every command in this document goes there.

1. Press `Windows`, type `environment`, and click **"Edit the system environment variables"**.
2. Click the **Environment Variables…** button at the bottom.
3. In the **top box** (User variables), click **New…**
   - Variable name: `ANDROID_HOME`
   - Variable value: the SDK path you copied in step 1.5
   - Click OK.
4. Still in the top box, find the row named **Path**, select it, click **Edit…**, then **New**, and
   add these two lines (one per line):
   ```
   %ANDROID_HOME%\platform-tools
   %ANDROID_HOME%\emulator
   ```
5. Click **OK** on all three windows.
6. **Close every Command Prompt window and open a brand new one.** Windows only loads new settings
   into fresh windows — this is the single most common reason this step appears to fail.

**Verify:**
```
adb version
```
Expected: `Android Debug Bridge version 1.0.xx`

If you get `'adb' is not recognized`, your `ANDROID_HOME` value is wrong. Go back to step 1.5 and
copy the path exactly as shown in the SDK Manager.

---

## 3. Get a device connected

### Option A — a real Android phone (recommended)

A real phone is better for manual testing than an emulator, because usability, touch targets,
one-handed reach and real network behaviour are most of what you are assessing.

1. On the phone: **Settings → About phone**. Find **Build number** and tap it **seven times**. You
   will see "You are now a developer."
2. Go to **Settings → System → Developer options**. (The exact location varies by brand — search
   "Developer" in Settings if you cannot find it.)
3. Turn **ON**:
   - **USB debugging** — lets ADB talk to the phone
   - **Stay awake** — stops the screen sleeping mid-test
4. Leave **Don't keep activities** OFF for now. You will turn it on later for two specific
   destructive test cases, then turn it back off.
5. Connect the phone to the PC with a **data** USB cable (a charge-only cable will not work).
6. A popup appears on the phone: **"Allow USB debugging?"** → tick "Always allow from this
   computer" → **Allow**.

**Verify:**
```
adb devices
```
Expected:
```
List of devices attached
R58M12ABCDE     device
```

| Problem | Fix |
|---|---|
| Says `unauthorized` | Unlock the phone and accept the popup |
| List is empty | Try a different cable, a different USB port, or install your phone brand's USB driver |

### Option B — an emulator

1. Android Studio → **More Actions** → **Virtual Device Manager** → **Create Device**.
2. Choose **Pixel 7** → Next.
3. Choose a system image with **Android 14** → download it → Next.
4. Name it `Pixel7_API34` → **Finish**.
5. Click the ▶ play button to boot it.

**Verify:** `adb devices` should list `emulator-5554`.

> **A note on emulators for this project:** they are fine for functional and destructive testing, but
> they will mislead you on usability (no real one-handed use), performance (desktop CPU) and
> accessibility (TalkBack gestures behave differently with a mouse). Use a real phone for
> documents 10, 11 and 13 if you possibly can.

---

## 4. Install the app under test

The app is **My Demo App — Android**, an open-source e-commerce app published by Sauce Labs
specifically for QA practice.

1. Go to <https://github.com/saucelabs/my-demo-app-android/releases>
2. Open the **latest release**.
3. Under **Assets**, download the file ending in **`.apk`**.
4. Note where it saved — probably `C:\Users\<YourName>\Downloads\`.
5. Install it from Command Prompt:
   ```
   adb install "C:\Users\<YourName>\Downloads\<the-file-you-downloaded>.apk"
   ```
   Expected output ends with `Success`.

   > **Tip:** instead of typing the long path, type `adb install ` (with a trailing space) and then
   > drag the APK file from Explorer into the Command Prompt window. Windows fills in the path.

**Verify it is installed:**
```
adb shell pm list packages | findstr saucelabs
```
Expected: `package:com.saucelabs.mydemoapp.android`

**Record the app version — you need this in every defect report:**
```
adb shell dumpsys package com.saucelabs.mydemoapp.android | findstr versionName
```
Write it down. **A defect report without a version number is close to useless to an engineer**,
because they cannot tell whether they are looking at a bug that still exists.

**Open the app** from the phone's app drawer and have a look around before you test anything. Tap
through the menu, open a product, look at the cart. Fifteen minutes of unstructured familiarisation
makes the next several hours far more productive.

---

## 5. Learn the six ADB commands you will actually use

These are your evidence-capture toolkit. Try each one now, before you need it under pressure.

**Take a screenshot**
```
adb shell screencap /sdcard/shot.png
adb pull /sdcard/shot.png ./evidence/
adb shell rm /sdcard/shot.png
```
*Line 1 captures on the phone, line 2 copies it to your PC, line 3 tidies up.*

**Record video** (maximum 3 minutes; press `Ctrl+C` to stop)
```
:: press Ctrl+C to stop the recording
adb shell screenrecord /sdcard/clip.mp4
adb pull /sdcard/clip.mp4 ./evidence/
```
*Essential for intermittent defects — a recording proves something a screenshot cannot.*

**Capture the crash log at the moment of failure**
```
adb logcat -c
```
*…now reproduce the bug…*
```
adb logcat -d > evidence/BUG-001-logcat.txt
```
*`-c` clears the log first so the file contains only your reproduction. `-d` dumps and exits.*
**A crash report without a log usually cannot be fixed.**

**Kill the app** (used by many destructive and state-persistence cases)
```
adb shell am force-stop com.saucelabs.mydemoapp.android
```
*Simulates Android killing the app to reclaim memory — the real-world condition that finds
state-loss defects.*

**Check memory during a long session**
```
adb shell dumpsys meminfo com.saucelabs.mydemoapp.android
```

**Check scroll smoothness objectively**
```
adb shell dumpsys gfxinfo com.saucelabs.mydemoapp.android
```
*Frames over 16 ms are dropped at 60 fps. Turns "it feels janky" into a number.*

---

## 6. Install Accessibility Scanner (5 minutes, saves an hour)

1. On the phone, open the **Play Store** and install **Accessibility Scanner** (by Google).
2. Open it and follow the setup — it asks you to enable it in accessibility settings.
3. A floating blue button appears. Open the app under test, navigate to a screen, and tap the
   button to scan it.

It automatically flags insufficient contrast, touch targets under 48 dp and missing labels. Let it
do the mechanical work so your manual time goes on the things it cannot see — like whether a label
actually says something meaningful.

---

## 7. Set up the repository on GitHub

You already have a GitHub account, so this is short.

1. On <https://github.com>, click **+** → **New repository**.
2. **Name:** `manual-testing-mydemoapp-android`
3. **Description:** `Manual QA portfolio — 164 test cases across 12 testing types against an open-source Android e-commerce app`
4. Select **Public** (a private repo is invisible to recruiters, which defeats the purpose).
5. Tick **Add a README file**, then **Create repository**.
6. Open **GitHub Desktop** → **File → Clone repository** → select the new repo → choose a local
   folder → **Clone**.
7. In Windows Explorer, copy the **contents** of this project folder into the cloned folder — so
   that `README.md` sits at the top level of the repo, not inside a sub-folder.
8. Back in GitHub Desktop, type a summary and click **Commit to main**, then **Push origin**.

**Commit in stages, not one giant upload.** A history that shows steady progress looks like real
work, because it is:

```
docs: add test plan and risk register
docs: add smoke and functional testing documents
test: record execution results for login and catalog modules
fix: correct expected result in TC-CART-006
docs: add defect reports for cart arithmetic findings
docs: complete test summary report with cycle 1 metrics
```

---

## 8. How to actually execute the suite

### The order to work in

| Session | Document | Time | Why this order |
|---|---|---|---|
| 1 | [03 Smoke](03-SMOKE-TESTING.md) | 20 min | Confirms your setup works and the build is sound |
| 2 | [04 Functional](04-FUNCTIONAL-TESTING.md) | 4–5 h | The bulk of the coverage; split across two sittings |
| 3 | [05 Negative](05-NEGATIVE-TESTING.md) + [06 Boundary](06-BOUNDARY-VALUE-TESTING.md) + [15 Input Validation](15-INPUT-VALIDATION-TESTING.md) | 2 h | All three hit the same forms, so do them together |
| 4 | [09 End-to-End](09-END-TO-END-TESTING.md) | 1 h | Now that you know the app, the journeys go quickly |
| 5 | [08 Destructive](08-DESTRUCTIVE-TESTING.md) | 2 h | Needs care and evidence capture; do it fresh |
| 6 | [07 Exploratory](07-EXPLORATORY-TESTING.md) | 3.5 h | Five 30-minute charters plus a 10-minute debrief each. Do this **after** you know the app well |
| 7 | [10 Usability](10-USABILITY-TESTING.md) + [11 Accessibility](11-ACCESSIBILITY-TESTING.md) | 2 h | Real phone, held one-handed |
| 8 | [12 Compatibility](12-COMPATIBILITY-TESTING.md) + [13 Performance](13-PERFORMANCE-TESTING.md) + [14 Localisation](14-LOCALISATION-TESTING.md) | 2 h | Needs a second device profile |
| 9 | [17 Defects](17-DEFECT-REPORTS.md) + [18 Summary](18-TEST-SUMMARY-REPORT.md) | 2 h | Write everything up properly |

**Total: roughly 19 hours** of execution and write-up — about **21 hours** including environment
setup and familiarisation. See [01 · Test Plan](01-TEST-PLAN.md), section 7, for the full breakdown.
Spread over two weeks of evenings, that is very achievable.

### Recording results

1. Open `test-cases/MyDemoApp-MasterTestSuite.xlsx`.
2. Read the **Read Me First** tab once.
3. Go to the **Test Cases** tab. Use the autofilter on the **Type** column to show only the
   document you are working through.
4. For every case, fill in the yellow columns: **Status**, **Actual Result**, **Executed On**,
   **Defect ID**, **Device**, **Notes**.
5. The **Dashboard** tab updates automatically. Do not type into it.

### Three rules that make the difference

**1. Write a real Actual Result, even on a pass.**
"Pass" is not evidence. `Pass — total $64.97 matched hand calculation of 2 × $29.49 + $5.99` is.
When an interviewer asks how you know something worked, this column is your answer.

**Note: this app prices in US dollars** — its delivery charge is a hardcoded `$5.99`. Record whatever
currency symbol is actually on your screen.

**2. Use Blocked, not Fail, when an earlier defect made a case impossible to run.**
- **Fail** = I ran it and it behaved wrongly.
- **Blocked** = I could not run it because something upstream was broken.

That distinction is what keeps your coverage report honest. Forty "Failed" rows suggests a
catastrophe; one Failed and thirty-nine Blocked correctly says "one screen is broken and it hid
everything behind it".

**3. Capture evidence *before* you move on from a failure.**
You will not be able to reproduce it later as easily as you can right now. Screenshot, recording,
logcat — then continue.

### Expect to find real defects

Every app has them, and this one is no exception. [Document 17](17-DEFECT-REPORTS.md) gives you
**eight specific, concrete leads** — places where a defect is genuinely likely, derived from the
app's own published source strings. Go and look at each one, verify what is actually on your screen,
and report only what you can reproduce.

**A test cycle where all 164 cases pass looks fake, because it is.** Real cycles find things. Your
credibility in an interview comes from the defects you found and can reproduce live if asked.

---

## 9. Data protection rules — non-negotiable

- **Fictional names only.** `Anne-Marie O'Brien`, `Björn Müller-Schäfer` — never your own.
- **Obvious dummy card numbers only.** `4111111111111111`. Never a real card, even on a demo app.
- **Blur or crop every screenshot** before committing. Use Windows Snipping Tool or Paint.
- **Read every logcat file before you attach it.** They can contain device identifiers and tokens.
  Redact anything that looks like an ID, token or session key.
- **Check screen recordings frame by frame** — personal data often appears for a moment during a
  transition.
- **Never commit credentials of any kind.**

One leaked personal detail in a portfolio screenshot is a poor first impression with any employer
that handles customer data — which is all of them.

---

## 10. Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| `'adb' is not recognized` | `ANDROID_HOME` wrong, or Path not reloaded | Recheck section 2; **open a new** Command Prompt |
| `adb devices` shows `unauthorized` | USB debugging popup not accepted | Unlock the phone, replug, tap Allow |
| `adb devices` shows nothing | Charge-only cable, or missing OEM driver | Try another cable and port; install your phone brand's USB driver |
| `adb install` fails with `INSTALL_FAILED_ALREADY_EXISTS` | Already installed | Add `-r` to reinstall: `adb install -r <file>.apk` |
| `adb install` fails with `INSTALL_FAILED_NO_MATCHING_ABIS` | Emulator architecture mismatch | Use an x86_64 emulator image, or a real phone |
| `screenrecord` produces a 0-byte file | Stopped too early, or ran out of space | Record for at least 3 seconds; check phone storage |
| App does not appear in the app drawer | Install failed silently | Re-run the `pm list packages` check in section 4 |
| Excel says the file is read-only | Downloaded from the internet | Right-click the file → Properties → tick **Unblock** → OK |
| Dashboard tab shows blank cells | Formulas not yet calculated | Open in Excel or LibreOffice and allow calculation; do not open in a preview-only viewer |
| TalkBack is on and you cannot turn it off | It changes every gesture | Hold **volume up + volume down together for 3 seconds** |
| Everything is slow after a destructive test | `Don't keep activities` left on | Turn it off in Developer options |

---

## 11. Quick reference card

Print this or keep it open.

```
:: --- device ---
adb devices
adb shell pm list packages | findstr saucelabs
adb shell dumpsys package com.saucelabs.mydemoapp.android | findstr versionName

:: --- evidence ---
adb shell screencap /sdcard/s.png
adb pull /sdcard/s.png ./evidence/

:: screen recording — press Ctrl+C to stop
adb shell screenrecord /sdcard/c.mp4
adb pull /sdcard/c.mp4 ./evidence/

:: clear the log, then reproduce the defect, then dump
adb logcat -c
adb logcat -d > evidence/BUG-001-logcat.txt

:: --- destructive ---
adb shell am force-stop com.saucelabs.mydemoapp.android

:: scripted rapid taps at a screen coordinate
adb shell input tap 500 1200

:: --- measurement ---
adb shell dumpsys meminfo com.saucelabs.mydemoapp.android
adb shell dumpsys gfxinfo com.saucelabs.mydemoapp.android

:: --- reinstall clean ---
adb uninstall com.saucelabs.mydemoapp.android
adb install "C:\path\to\app.apk"
```
