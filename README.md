# Exchange Online Room Mailbox Troubleshooting Guide

![PowerShell](https://img.shields.io/badge/PowerShell-Ready-blue)
![Microsoft 365](https://img.shields.io/badge/Microsoft%20365-Exchange%20Online-green)
![Status](https://img.shields.io/badge/Status-Production%20Tested-success)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

---

## 📌 Overview

This repository provides a practical troubleshooting guide for issues related to **room/equipment mailbox bookings in Exchange Online**, including:

- Meetings accepted but not visible
- Calendar sync issues in Outlook
- Room calendar showing only time but no details
- Permission-related visibility limitations

---

## 🧠 Scenario Covered

A user reports:

- A meeting was booked in a room (Board Room)
- The room accepted the meeting
- The meeting was not visible in the room calendar initially
- After sync, the meeting appears but **without details**

---

## 🔍 Root Cause Categories

### 1. Outlook Sync Issues
- Cached Outlook not updated
- Calendar pane not refreshing
- Local client issue

### 2. Room Mailbox Processing
- Booking accepted but delayed in UI
- Processing policies affecting visibility

### 3. Permissions (MOST COMMON)
- Calendar set to **AvailabilityOnly**
- Users cannot see subject or organizer

---

## 🛠️ Troubleshooting Steps

### Step 1 — Confirm Room Acceptance

Check if the room accepted the meeting:

- Open meeting invite
- Look for:
  > "Room has accepted this meeting"

---

### Step 2 — Validate in Outlook Web

👉 https://outlook.office.com

- Open Room Calendar
- Confirm meeting exists

✔️ If visible → Outlook client issue  
❌ If not → booking issue

---

### Step 3 — Refresh Outlook

- Restart Outlook
- Switch calendar views
- Re-add room calendar

---

### Step 4 — Check Calendar Permissions (Admin)

#### Get current permissions:

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
