# 🛡️ KeySentinel — Windows Keylogger Detection & Security Monitoring System

Build a **complete, professional, working Windows security-monitoring application called “KeySentinel” using Python**.

KeySentinel is a **defensive security tool** designed to monitor potentially suspicious keyboard-hook activity, identify the associated processes, and provide transparent real-time alerts to the user.

The application must **NOT act as a keylogger**. It must never record, save, display, transmit, or analyze the actual characters typed by the user.

The primary goal is to create a **professional security-monitoring starter project** that demonstrates Windows API integration, process monitoring, threat detection concepts, GUI development, threading, and user-controlled mitigation.

---

# 1. Technology Stack

Use:

* Python 3.x
* `ctypes`
* `wintypes`
* `threading`
* `time`
* `psutil`
* `tkinter`
* Windows `user32.dll`

Only external dependency:

```bash
pip install psutil
```

Do not introduce unnecessary third-party libraries.

---

# 2. Project Structure

Create the project with a clean structure:

```text
KeySentinel/
│
├── keylogger_detector.py
├── requirements.txt
├── README.md
└── logs/
```

If additional modules improve maintainability, use a clean structure such as:

```text
KeySentinel/
│
├── app.py
├── detector.py
├── process_monitor.py
├── security_logger.py
├── config.py
├── ui.py
├── requirements.txt
├── README.md
└── logs/
```

For the starter version, however, a single-file implementation is acceptable.

---

# 3. Main Objective

The application should:

1. Start a transparent security-monitoring service.
2. Monitor Windows low-level keyboard-hook activity.
3. Identify relevant foreground processes.
4. Check processes against a configurable trusted-process list.
5. Assign a suspicious-activity confidence level.
6. Display a real-time security alert.
7. Provide process information to the user.
8. Allow the user to manually terminate a flagged process.
9. Maintain a local security-event history.
10. Never capture or store actual keystrokes.

---

# 4. Windows API Integration

Use Python `ctypes` to interact with Windows `user32.dll`.

Use APIs such as:

```text
SetWindowsHookEx
CallNextHookEx
UnhookWindowsHookEx
GetMessage
TranslateMessage
DispatchMessage
GetForegroundWindow
GetWindowThreadProcessId
```

Use:

```python
WH_KEYBOARD_LL = 13
WM_KEYDOWN = 0x0100
```

Define the necessary keyboard hook structures using `ctypes.Structure`.

The implementation must correctly maintain the Windows message loop.

Ensure the callback function remains referenced for the lifetime of the hook so that it cannot be garbage-collected unexpectedly.

---

# 5. Critical Privacy Requirement

This application is strictly a **defensive monitoring tool**.

DO NOT:

* Record keystrokes.
* Store typed characters.
* Store passwords.
* Capture text typed into applications.
* Send keyboard data over the network.
* Upload keyboard activity.
* Create hidden keylogging functionality.
* Run secretly.
* Hide the application from the user.

The hook should be used only as a **security-monitoring signal**.

The application should never need to know what the user typed.

---

# 6. Process Detection

When relevant keyboard-hook activity occurs:

1. Identify the current foreground window.
2. Retrieve its process ID.
3. Resolve the process using `psutil`.
4. Retrieve:

   * Process name
   * PID
   * Executable path where permitted
   * Process creation time where available
5. Compare the process against the trusted-process configuration.
6. Generate a security event when appropriate.

Handle:

```python
psutil.NoSuchProcess
psutil.AccessDenied
psutil.ZombieProcess
```

without crashing the application.

---

# 7. Important Detection Accuracy Requirement

Do **not** automatically claim that a process is a confirmed keylogger simply because keyboard-hook activity is associated with it.

Use wording such as:

```text
Potentially Suspicious Keyboard Activity
```

or:

```text
Suspicious Keyboard Hook Activity
```

The application should explain that keyboard-hook activity can have legitimate uses.

Avoid unnecessary false positives.

---

# 8. Detection Confidence System

Implement a simple confidence system.

Example:

### 🟢 Low

Normal or trusted activity.

### 🟡 Medium

Unknown process associated with suspicious keyboard-hook behavior.

### 🔴 High

Multiple suspicious indicators associated with the same process.

Do not claim malware/keylogger confirmation unless there is reliable evidence supporting that conclusion.

Display the reason for the confidence level.

Example:

```text
Risk Level: MEDIUM

Reason:
Unknown process associated with keyboard-hook activity.
```

---

# 9. Trusted Applications

Create a configurable whitelist:

```python
TRUSTED_PROCESSES = [
    "explorer.exe",
    "textinputhost.exe",
    "ctfmon.exe",
    "python.exe"
]
```

Allow the user to add/remove trusted applications from the settings interface.

Do not rely solely on a filename for permanent trust.

Where possible, display the executable path and other process metadata.

---

# 10. Modern Security Dashboard

Create a professional Windows-style security dashboard using Tkinter.

The dashboard should contain:

### Header

```text
🛡️ KeySentinel
Windows Security Monitor
```

### Monitoring status

```text
🟢 PROTECTION ACTIVE
```

### Statistics

Display cards such as:

```text
Processes Reviewed
Security Events
Suspicious Processes
Actions Taken
```

### Main activity area

Show recent security events:

```text
Time        Process        PID       Risk       Status
12:41:20    example.exe    4312      Medium     Review
12:42:08    app.exe        2014      Low        Trusted
```

---

# 11. Floating Security Alert

In addition to the main dashboard, create an optional compact always-on-top alert banner.

Use:

```python
root.attributes("-topmost", True)
root.overrideredirect(True)
```

where appropriate.

Normal state:

```text
🛡️ KeySentinel Active

No suspicious activity detected.
```

Suspicious state:

```text
⚠️ Potentially Suspicious Activity

Process: example.exe
PID: 4312
Risk: Medium

Review this process.
```

The alert should be visible but should not unnecessarily interfere with the user's work.

---

# 12. Alert Deduplication

Prevent the same process from creating hundreds of identical alerts.

Implement logic such as:

```text
Process A detected
        ↓
Alert generated
        ↓
Same process detected again
        ↓
Update existing event / ignore duplicate
```

Provide a configurable cooldown period.

---

# 13. Security Event History

Maintain a local history of security events.

Each event can contain:

```text
Timestamp
Process Name
PID
Executable Path
Risk Level
Reason
User Action
```

Never store actual keyboard input.

Example:

```text
2026-09-30 17:05
Process: example.exe
PID: 4312
Risk: Medium
Reason: Unknown keyboard-hook activity
Action: None
```

---

# 14. Local Logging

Create local security logs.

Example:

```text
logs/security_events.json
```

or:

```text
logs/security_events.csv
```

Logs should contain only security metadata.

Never store:

```text
actual keystrokes
passwords
typed text
clipboard contents
```

---

# 15. Process Details Panel

When the user selects a suspicious process, display:

```text
Process Name
PID
Executable Path
Creation Time
Risk Level
Detection Reason
Current Status
```

Add:

```text
[ Refresh ]
```

to retrieve updated process information.

---

# 16. User-Controlled Mitigation

Provide:

```text
[ Terminate Process ]
```

The application must **never automatically kill a process**.

Termination must require explicit user interaction.

Before termination, display confirmation:

```text
Terminate Process?

Process:
example.exe

PID:
4312

Are you sure you want to terminate this process?

[ Cancel ] [ Terminate ]
```

After success:

```text
✅ Process Terminated

The selected process was terminated successfully.
```

If termination fails:

```text
❌ Termination Failed

The process could not be terminated.
You may need additional permissions.
```

---

# 17. Allow / Trust Process

Provide a user option:

```text
[ Trust Process ]
```

When selected, allow the user to add the application to the trusted list.

Show the executable path when possible so the user can distinguish applications with identical filenames.

---

# 18. Block / Review Workflow

Do not silently block applications.

Instead provide:

```text
Review
Trust
Terminate
Ignore
```

This gives the user control over what action should be taken.

---

# 19. Scan Now Feature

Add:

```text
🔍 Scan Now
```

The user should be able to manually initiate a process/security scan.

Display:

```text
Scanning...

Processes reviewed: 124
Suspicious events: 2
```

Then show the results.

---

# 20. Monitoring Controls

Add controls:

```text
Monitoring: ON/OFF
Desktop Alerts: ON/OFF
Sound Alerts: ON/OFF
Auto-start: ON/OFF
```

Monitoring should be enabled by default.

---

# 21. Settings Panel

Create a settings section containing:

### Monitoring

* Enable/disable monitoring
* Alert sensitivity
* Duplicate alert cooldown

### Trusted Applications

* View trusted applications
* Add application
* Remove application

### Notifications

* Desktop alerts
* Sound alerts

### Startup

* Optional Start with Windows

### Privacy

Display:

```text
KeySentinel does not record or transmit your keystrokes.
Only security-related process metadata is stored locally.
```

---

# 22. System Tray Support

If practical, implement a system-tray/minimized mode.

When minimized:

```text
🛡️ KeySentinel
Protection Active
```

Tray menu:

```text
Open Dashboard
Scan Now
Pause Monitoring
Settings
Exit
```

If system-tray support requires an additional dependency, clearly document it and keep it optional rather than making it mandatory for the starter version.

---

# 23. Background Thread

Run monitoring separately from the GUI.

Example:

```python
threading.Thread(
    target=self.start_hook_monitor,
    daemon=True
)
```

The UI must remain responsive.

Never update Tkinter widgets directly from the monitoring thread.

Use:

```python
root.after(...)
```

to safely communicate with the GUI thread.

---

# 24. Clean Shutdown

When the application exits:

1. Stop monitoring.
2. Unregister the Windows hook.
3. Stop background threads where applicable.
4. Save security-event information.
5. Close files safely.
6. Destroy the Tkinter application.

The application must not leave an active Windows hook behind.

---

# 25. Error Handling

Handle:

* Missing `psutil`
* Unsupported operating system
* Windows API errors
* Hook registration failure
* Permission errors
* Process disappearing
* Process access denied
* Process termination failure
* Invalid PID
* GUI errors

Never crash because one process cannot be inspected.

Show user-friendly errors.

Example:

```text
❌ Monitoring Error

Unable to initialize Windows keyboard monitoring.

Please verify that you are running KeySentinel
on a supported Windows system.
```

---

# 26. Windows Compatibility

The project should target modern Windows systems.

Before starting:

```python
if os.name != "nt":
    ...
```

Display an appropriate message if the application is executed on a non-Windows system.

---

# 27. Security Status States

Implement clear states:

### 🟢 Protected

```text
🛡️ PROTECTION ACTIVE
No suspicious activity detected.
```

### 🟡 Review Required

```text
⚠️ REVIEW REQUIRED
Potentially suspicious activity detected.
```

### 🔴 High Risk

```text
🔴 HIGH RISK
Multiple suspicious indicators detected.
```

### ⚫ Error

```text
❌ MONITORING ERROR
Security monitoring is currently unavailable.
```

---

# 28. UI/UX Design

Make the interface:

* Professional
* Modern
* Clean
* Responsive
* Easy for beginners to understand
* Windows-security inspired
* Dark-theme friendly
* Clearly color-coded by status
* Properly spaced
* Free from unnecessary visual clutter

Use icons/emojis where appropriate.

Avoid excessive animations.

Use subtle transitions where Tkinter supports them without reducing stability.

---

# 29. Dashboard Layout

Suggested layout:

```text
┌──────────────────────────────────────────────┐
│ 🛡️ KeySentinel                ⚙ Settings    │
│ Windows Security Monitor                    │
├──────────────────────────────────────────────┤
│                                              │
│  🟢 PROTECTION ACTIVE                       │
│  Monitoring Windows security activity        │
│                                              │
├────────────┬────────────┬────────────────────┤
│ Processes  │ Events     │ Suspicious         │
│ 124        │ 7          │ 2                  │
├────────────┴────────────┴────────────────────┤
│                                              │
│ Recent Security Events                      │
│                                              │
│ Process      PID     Risk      Action       │
│ app.exe      4312    Medium    Review       │
│ tool.exe     8291    Low       Trusted      │
│                                              │
├──────────────────────────────────────────────┤
│ [ 🔍 Scan Now ]  [ ⚙ Settings ]             │
└──────────────────────────────────────────────┘
```

---

# 30. Security Event Detail

Clicking an event should open a detail panel:

```text
Security Event

Process:
example.exe

PID:
4312

Risk:
Medium

Path:
C:\Example\example.exe

Reason:
Unknown process associated with keyboard-hook activity.

First Detected:
17:05:23

Actions:

[ Trust Process ]
[ Terminate Process ]
[ Ignore ]
```

---

# 31. Requirements File

Create:

```text
requirements.txt
```

with:

```text
psutil
```

If optional dependencies are introduced, clearly separate them.

---

# 32. README Documentation

Create a complete `README.md`.

Include:

## Project Overview

Explain what KeySentinel does.

## Features

List all major features.

## Installation

```bash
pip install -r requirements.txt
```

## Run

```bash
python keylogger_detector.py
```

## Architecture

Explain:

```text
Windows API
     ↓
Keyboard Hook Monitor
     ↓
Process Identification
     ↓
Risk Analysis
     ↓
Security Event
     ↓
Dashboard / Alert
     ↓
User Decision
```

## Privacy

Clearly explain that actual keystrokes are never recorded or transmitted.

## Limitations

Explain that keyboard-hook activity alone does not prove that a process is a malicious keylogger.

## Troubleshooting

Include common Windows permission/API issues.

---

# 33. Testing Requirements

Include a basic testing strategy.

Test:

### Test 1 — Application Startup

Verify that the dashboard launches correctly.

### Test 2 — Monitoring

Verify that monitoring starts successfully.

### Test 3 — Process Identification

Verify that process metadata can be retrieved.

### Test 4 — AccessDenied

Verify that inaccessible processes do not crash the application.

### Test 5 — Process Exit

Verify that processes disappearing during detection are handled.

### Test 6 — Alert

Verify that suspicious events generate a GUI notification.

### Test 7 — Duplicate Detection

Verify that repeated events from the same process do not flood the UI.

### Test 8 — Termination

Verify that user-controlled process termination works correctly.

### Test 9 — Clean Exit

Verify that the Windows hook is removed when the application closes.

---

# 34. Code Quality Requirements

Write clean, maintainable Python.

Use:

* Classes
* Functions
* Constants
* Type hints where useful
* Docstrings
* Exception handling
* Meaningful variable names
* Modular architecture
* Comments for Windows API-specific code

Avoid:

* Hard-coded repeated values
* Unnecessary global variables
* Duplicate logic
* Blocking the GUI thread
* Unsafe thread/UI communication

---

# 35. Important Security Disclaimer

The README should explicitly explain:

> KeySentinel is a defensive security-monitoring prototype. Detection of keyboard-hook activity does not automatically prove that a process is malicious or is recording keystrokes. Legitimate applications may use keyboard hooks for accessibility, input methods, hotkeys, gaming, automation, or other functionality.

The UI should therefore use terms such as:

```text
Potentially Suspicious
Review Required
Unknown Activity
```

rather than making unsupported claims such as:

```text
Confirmed Malware
Confirmed Keylogger
```

---

# 36. Final Deliverables

Provide a complete working project containing:

```text
✅ Python application
✅ Windows API integration
✅ Keyboard-hook monitoring
✅ Process identification
✅ Risk/confidence system
✅ Trusted-process management
✅ Real-time security alerts
✅ Professional dashboard
✅ Alert history
✅ Local security logging
✅ Scan Now
✅ Process details
✅ User-controlled termination
✅ Trust Process
✅ Settings
✅ Monitoring controls
✅ Error handling
✅ Clean shutdown
✅ README.md
✅ requirements.txt
```

---

# 37. Final Development Goal

The final result should feel like a **real Windows defensive security utility**, not a simple Python demo.

The application should demonstrate:

```text
Python
   +
Windows API
   +
Process Monitoring
   +
Security Detection
   +
Threat Assessment
   +
Desktop GUI
   +
User-Controlled Mitigation
   +
Privacy Protection
```

Build the project so that it is:

**Professional + Secure + Transparent + Maintainable + Beginner-friendly + Extensible**

The architecture should also make it possible to add future defensive features such as stronger process verification, digital-signature inspection, security reports, configurable detection rules, and additional Windows security telemetry without redesigning the entire application.
