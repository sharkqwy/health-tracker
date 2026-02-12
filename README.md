# 🏥 Health Tracker — OpenClaw Skill

A universal health and medication tracker for [OpenClaw](https://github.com/openclaw/openclaw) AI agents. Manages prescriptions, supplements, medical instructions, appointments, health metrics, and refill schedules with intelligent reminders.

## Features

- 💊 **Medication & Supplement Tracking** — Dosage, frequency, food requirements, timed reminders
- 📋 **Medical Instructions** — Post-surgery care, doctor's orders with step-by-step reminders
- 📅 **Appointment Management** — Multi-stage reminders with prep checklists
- 📊 **Health Metrics** — Blood pressure, blood sugar, weight with target ranges & trend tracking
- 🔄 **Refill Alerts** — Automatic supply tracking with advance warnings
- ✅ **Adherence Logging** — Reply "taken" to log doses, track missed medications
- 🔒 **Privacy-First** — All data stored locally, never leaves your machine

## Install

```bash
openclaw skill install health-tracker
```

Or manually copy `SKILL.md` into your OpenClaw skills directory.

## Quick Start

Just tell your agent naturally:

> "I take Metformin 500mg twice daily with food"
> "Doctor said to change wound dressing twice daily for 6 weeks"
> "Remind me about my annual physical on March 15 at 10am"
> "Log my blood pressure: 125/82"

The agent parses your input, creates structured entries, and sets up cron-based reminders delivered to your preferred channel (Slack, Telegram, WhatsApp, etc.)

## How It Works

1. **Tell your agent** about medications, supplements, or doctor instructions in natural language
2. **Agent confirms** the structured details with you
3. **Reminders auto-schedule** via OpenClaw cron jobs
4. **Reply to confirm** adherence — agent tracks your history
5. **Ask for status** anytime — get a dashboard of meds, appointments, metrics & trends

## Data Storage

All health data lives in a single local JSON file (`health-tracker.json`). Supports:
- Multiple medications and supplements
- Medical instructions with step-by-step reminders
- Appointments with prep checklists
- Health metrics with target ranges
- Full adherence log history

## Safety

- ⚠️ **Never diagnoses or recommends medications** — only tracks what you or your doctor provide
- 🔒 All data stays local on your machine
- 👨‍⚕️ Tracks prescriber provenance for every medication
- 🚨 For medical emergencies, always call emergency services

## Multi-Person Support

Maintain separate tracker files for family members (e.g., `health-tracker-mom.json`).

## Links

- 🌐 **Landing Page**: [sharkqwy.github.io/health-tracker](https://sharkqwy.github.io/health-tracker/)
- 🐾 **ClawHub**: Coming soon

## License

MIT
