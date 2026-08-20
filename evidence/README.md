# Evidence

Screenshots, screen recordings and device logs captured during test execution.

---

## Naming convention

Prefix with the defect ID or test case ID so evidence is findable from the ticket.

```
BUG-001-cart-lines.png
BUG-001-cart-total.png
BUG-001-review-total.png
BUG-001-logcat.txt
BUG-002-duplicate-order.mp4
TC-CHKP-005-expired-card-accepted.png
charter-1-badge-mismatch.png
accessibility/scanner-catalog.png
performance/cold-start-runs.txt
```

Sub-folders are fine and encouraged once you have more than a dozen files:
`accessibility/`, `performance/`, `charters/`.

---

## Capturing evidence with ADB

### Screenshot
```
adb shell screencap /sdcard/shot.png
adb pull /sdcard/shot.png ./BUG-001-cart-total.png
adb shell rm /sdcard/shot.png
```

### Screen recording
Maximum 3 minutes per recording. Press `Ctrl+C` to stop.
```
adb shell screenrecord /sdcard/clip.mp4
adb pull /sdcard/clip.mp4 ./BUG-002-duplicate-order.mp4
adb shell rm /sdcard/clip.mp4
```

**Record video for every intermittent defect.** A recording proves something a screenshot cannot: the
sequence that caused it. For anything you can only reproduce 3 times out of 5, video is the difference
between a ticket that gets fixed and one closed as "cannot reproduce".

### Device log at the moment of failure
Always clear first, so the file contains only your reproduction:
```
adb logcat -c
```
…now reproduce the defect…
```
adb logcat -d > BUG-001-logcat.txt
```

Filter to just this app if the log is noisy:
```
adb logcat -d | findstr saucelabs > BUG-001-logcat.txt
```

**A crash report without a log usually cannot be fixed.** The stack trace is the single most valuable
thing you can attach.

### App version — record this in every ticket
```
adb shell dumpsys package com.saucelabs.mydemoapp.android | findstr versionName
```

### Performance measurements
```
adb shell dumpsys gfxinfo com.saucelabs.mydemoapp.android > performance/gfxinfo.txt
adb shell dumpsys meminfo com.saucelabs.mydemoapp.android > performance/meminfo-30min.txt
```

---

## What good evidence looks like

For an arithmetic defect, one screenshot is not enough. You need the **inputs and the output**:

| File | Shows |
|---|---|
| `BUG-001-cart-lines.png` | Each line's unit price and quantity — the inputs |
| `BUG-001-cart-total.png` | The total the app displayed — the output |
| `BUG-001-calculation.txt` | Your hand calculation — the expected value |

That set is self-contained: an engineer can verify the defect without a device. One screenshot of a
total proves nothing, because nobody can tell what it should have been.

For a state-loss defect, you need **before and after**:

| File | Shows |
|---|---|
| `BUG-00N-before-killing.png` | Cart contents and badge before `am force-stop` |
| `BUG-00N-after-relaunch.png` | The same screen after relaunch |

---

## ⚠️ Before committing anything

- [ ] **Blur or crop every piece of personal data** — name, email, address, phone. Use the Windows
      Snipping Tool or Paint.
- [ ] **Check screen recordings frame by frame.** Data often appears for a fraction of a second during
      a transition, and a recording is 30 frames per second of opportunities to leak something.
- [ ] **Open every logcat file and read it before committing.** Logs can contain device identifiers,
      account tokens and session keys. Redact anything that looks like an ID or a token.
- [ ] **Never commit a real card number**, even a partial one, even in a log.
- [ ] **Never commit credentials of any kind.**

Note that one of the test cases in this suite (`TC-XCUT-015`) exists specifically to check whether
the app leaks credentials into logcat. If it does, that log file is exactly the thing you must not
commit. Redact it, and report the finding.

A single leaked personal detail in a portfolio screenshot is a poor first impression with any employer
that handles customer data — which is all of them.

---

## Placeholder

This folder is empty until you execute the suite. `.gitignore` excludes raw and unredacted files by
pattern, so redact, rename, and then commit deliberately.
