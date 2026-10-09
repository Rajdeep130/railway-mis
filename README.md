# Railway Projects MIS

A working management information system for a railway infrastructure contractor, built with **Google Sheets, AppSheet, Google Apps Script and Looker Studio**. It follows one job from tender to payment: tenders, contracts, projects, site progress, billing and receipts.

Built as a portfolio project for the MIS & Business Systems Executive role at Rawatsons Engineers Pvt. Ltd.

> **All data is demo data.** Names, tenders, amounts and email addresses are fictional or test addresses. This is not a production system. It runs on free Google plans.

## Links

- AppSheet app: `<add link>`
- Looker Studio dashboard: `<add link>`
- Google Sheet (database): `<add link>`
- Demo video: `<add link>`

## What it does

| Area | What the system does |
|---|---|
| Tenders | Tracks each tender from Draft to Awarded or Closed, with a due date and an owner. Emails reminders at 7, 3, 1 and 0 days, and a daily overdue notice. |
| Projects | Links Tender, Contract, Project, Site and BOQ items, so every number traces back to its tender. |
| Site progress | Site engineers enter executed quantity. Progress % and balance quantity are calculated. A project manager approves each entry. |
| Billing | Tracks Billed, Certified, Deductions, Net Payable, Received and Outstanding for each bill, with ageing buckets (0-30, 31-60, 60+ days) and an overdue flag. |
| Reports | A weekly summary email with Excel and PDF attached. A live dashboard for management. |
| Alerts | An email to the project manager when a progress entry is added. A daily digest of tenders to the tendering executive. |

## How it fits together

```
AppSheet app (data entry, approvals, role-based views)
        |
Google Sheet (28 tabs: master data, transactions, Rpt_ report tabs)
        |
Apps Script (reminders, notifications, report tabs, weekly report)
        |
Looker Studio dashboard (reads the Rpt_ tabs)
```

- **Rpt_ tabs** are calculated by `refreshReportTabs` every 15 minutes. The dashboard reads only these tabs, so it is fast and the numbers match the app.
- **Reminder_Log, Notification_Log, Report_Log** record every email sent, so nothing is sent twice.
- **TEST / LIVE switch:** the scripts can send all email to the script owner for testing, and then to the real recipients when switched live.

## Roles and access

| Role | Can do |
|---|---|
| Site Engineer, Yard Supervisor | Add progress entries and view projects and progress. Cannot approve, and cannot see tenders or bills. |
| Project Manager | Everything above, plus approve or reject progress. |
| Tender Executive | Manages tenders. Receives the tender reminders and the digest. |
| Billing Executive | Manages bills and receipts. |
| CEO, Admin | Full view, including the Home dashboard. |

Nobody approves their own work: the approval fields are not on the entry form and the Approve and Reject buttons are limited by role.

## Key numbers (demo data, 4-9 Oct 2026)

- Open tender pipeline: Rs. 22.24 crore
- Win rate: 75% (3 of 4 decided tenders)
- Billed: Rs. 2.25 crore; Net payable: Rs. 1.29 crore; Received: Rs. 0.83 crore
- Outstanding: Rs. 0.46 crore, of which Rs. 0.12 crore is overdue (more than 30 days after certification)

Overdue age grows daily, so these figures change when the sheet is refreshed.

## Definitions

- **Billed** = the bill value submitted. **Certified** = the amount the railway accepted. **Net payable** = certified minus deductions.
- **Outstanding** = net payable minus amount received. A submitted but uncertified bill is "waiting certification" and is not counted as outstanding.
- **Overdue** = certified, unpaid, and more than 30 days since certification.
- **L1** = lowest qualified bidder, who wins the tender.

## Setup (short)

1. Copy the Google Sheet, keeping the tab names.
2. Open Extensions, then Apps Script. Run `Mis Setup`, then create the time triggers: daily tender reminders, 10-minute notifications, 15-minute report refresh, Monday weekly report.
3. In `TenderReminders.gs` and `Notifications.gs`, set `TEST_MODE` to `true` first and send a test. Switch to `false` only when the Employees emails are addresses you control.
4. Connect AppSheet to the sheet, turn on sign-in and role management using the Employees table (App Role column).
5. Point the Looker Studio data sources at the Rpt_ tabs.

## Limits (honest list)

- Demo data only. No real client or railway data.
- Free plans: Apps Script email quotas and trigger limits apply.
- Not hardened for production: no audit trail beyond the log tabs, and roles rely on the AppSheet sign-in.
- Built with AI assistance. I can explain every formula, script and design choice, and I verified the dashboard numbers against the sheet.

## What I would do next

Photo evidence on progress entries, SMS or WhatsApp alerts, budget against actual cost per project, and a move from Sheets to a proper database if volume grows.
