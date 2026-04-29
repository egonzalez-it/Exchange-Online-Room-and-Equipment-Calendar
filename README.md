# Exchange Online Room and Equipment Calendar Booking Troubleshooting

![PowerShell](https://img.shields.io/badge/PowerShell-Ready-blue)
![Microsoft 365](https://img.shields.io/badge/Microsoft%20365-Exchange%20Online-green)
![Status](https://img.shields.io/badge/Status-Production%20Tested-success)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## 📌 Overview

This repository provides a practical troubleshooting guide for issues related to **room and equipment mailbox bookings in Exchange Online**.

It covers common real-world scenarios such as:

- Meetings accepted but not visible
- Calendar sync issues in Outlook
- Room calendars showing only time blocks without details
- Permission-related visibility limitations

> Based on a real L2 support incident in a production Microsoft 365 environment.

---

## ⚡ Quick Diagnosis

| Symptom                                      | Likely Cause                |
|---------------------------------------------|-----------------------------|
| Meeting not visible in room calendar        | Outlook sync issue          |
| Meeting accepted but delayed in view        | Client caching issue        |
| Meeting shows only time (no details)        | Permission (AvailabilityOnly) |

---

## 🧠 Scenario Covered

A user reports:

- A meeting was booked in a room (Board Room)
- The room accepted the meeting
- The meeting was not visible initially
- After synchronization, the meeting appears but **without details**

---

## 🔍 Root Cause Categories

### 1. Outlook Sync Issues
- Cached Outlook not up to date
- Calendar pane not refreshing
- Local client inconsistency

### 2. Room Mailbox Processing
- Booking accepted but delayed in UI
- Processing behavior impacting visibility

### 3. Permissions (Most Common)
- Calendar configured as **AvailabilityOnly**
- Users can only see free/busy status

---

## 🛠️ Troubleshooting Steps

### Step 1 — Confirm Room Acceptance

Open the meeting invite and verify:

> "Room has accepted this meeting"

---

### Step 2 — Validate in Outlook Web (OWA)

👉 https://outlook.office.com

- Open the room calendar
- Confirm the meeting exists

✔️ If visible → Outlook client issue  
❌ If not → Booking or mailbox issue  

---

### Step 3 — Refresh Outlook Client

- Restart Outlook
- Switch between calendar views (Month / Week)
- Remove and re-add the room calendar

---

### Step 4 — Check Calendar Permissions (Admin)

Connect to Exchange Online PowerShell:

```powershell
Connect-ExchangeOnline
```

Check current permissions:

```powershell
Get-MailboxFolderPermission "Board Room:\Calendar"
```

🔐 Permission Levels Explained

| Access Level     | What Users See       |
| ---------------- | -------------------- |
| AvailabilityOnly | Busy/Free only       |
| LimitedDetails   | Subject + Organizer  |
| Reviewer         | Full meeting details |


⚙️ Fix: Show Meeting Details

Recommended (Balanced Visibility)

```powershell
Set-MailboxFolderPermission "Board Room:\Calendar" -User Default -AccessRights LimitedDetails
```

Full Visibility (Less common)

```powershell
Set-MailboxFolderPermission "Board Room:\Calendar" -User Default -AccessRights Reviewer
```


🧪 Validation

After applying changes:

1. Refresh Outlook or use OWA
2. Open Room Calendar
3. Confirm:
    -   Meeting subject visible
    -   Organizer visible
