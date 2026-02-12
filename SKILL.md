---
name: health-tracker
description: >
  Universal health and medication tracker for managing prescriptions, supplements,
  medical instructions, appointments, health metrics, and refill schedules with
  intelligent reminders. Use when user mentions medications, prescriptions, supplements,
  vitamins, doctor instructions, post-surgery care, health tracking, medication reminders,
  pill schedule, refill reminder, appointment reminder, health metrics, blood pressure,
  blood sugar, symptom tracking, chronic condition management, or any medical adherence task.
  Triggers: "track my meds", "medication reminder", "doctor told me to", "health routine",
  "supplement stack", "prescription refill", "log my vitals", "medical instructions".
---

# Health Tracker

A universal skill for managing health regimens — medications, supplements, medical instructions, appointments, and vitals — with intelligent reminders via OpenClaw cron jobs.

## Data Model

All health data lives in a single JSON file the user chooses (default: `health-tracker.json` in workspace). Structure:

```json
{
  "version": 1,
  "profile": {
    "name": "string",
    "timezone": "Asia/Shanghai",
    "emergencyContact": "optional"
  },
  "medications": [],
  "supplements": [],
  "instructions": [],
  "appointments": [],
  "metrics": [],
  "logs": []
}
```

### Medication/Supplement Entry

```json
{
  "id": "med_001",
  "name": "Metformin",
  "type": "medication",
  "dosage": "500mg",
  "frequency": "twice daily",
  "times": ["08:00", "20:00"],
  "withFood": true,
  "startDate": "2026-01-15",
  "endDate": null,
  "supply": { "count": 60, "startedOn": "2026-02-01", "refillLeadDays": 5 },
  "notes": "Take with meals to reduce GI side effects",
  "prescribedBy": "Dr. Wang",
  "active": true
}
```

### Medical Instruction Entry

```json
{
  "id": "ins_001",
  "title": "Post-surgery wound care",
  "source": "Dr. Li, 2026-02-10",
  "steps": [
    "Change dressing twice daily",
    "No heavy lifting for 6 weeks",
    "Follow-up appointment Feb 24"
  ],
  "startDate": "2026-02-10",
  "endDate": "2026-03-24",
  "reminders": [
    { "what": "Change dressing", "cron": "0 8,20 * * *" },
    { "what": "Follow-up appointment", "at": "2026-02-24T09:00:00" }
  ],
  "active": true
}
```

### Appointment Entry

```json
{
  "id": "apt_001",
  "title": "Annual physical",
  "doctor": "Dr. Zhang",
  "location": "City Hospital, Room 302",
  "dateTime": "2026-03-15T10:00:00",
  "remindBefore": ["1d", "2h"],
  "prep": ["Fast 12h before", "Bring insurance card"],
  "active": true
}
```

### Health Metric Entry

```json
{
  "id": "metric_001",
  "type": "blood_pressure",
  "schedule": "daily at 07:00",
  "unit": "mmHg",
  "targetRange": { "systolic": [90, 130], "diastolic": [60, 85] },
  "active": true
}
```

### Log Entry

```json
{
  "timestamp": "2026-02-12T08:00:00+08:00",
  "type": "medication_taken",
  "refId": "med_001",
  "value": "taken",
  "notes": ""
}
```

## Core Workflows

### 1. Setup — Add Health Items

When user provides medication/supplement/instruction info:

1. Parse natural language into structured entry (ask to confirm details)
2. Read or create `health-tracker.json`
3. Add entry with generated ID
4. Set up cron reminders (see Reminder Setup below)
5. Confirm what was added and what reminders are scheduled

**Key questions to ask if unclear:**
- Dosage and frequency?
- With or without food?
- Start/end dates?
- Prescribing doctor?
- Current supply count?
- Preferred reminder channel?

### 2. Reminder Setup

Convert health items into OpenClaw cron jobs:

**Medication/supplement reminders:**
```
cron add → schedule: { kind: "cron", expr: "<time-based>", tz: "<user tz>" }
         → payload: { kind: "agentTurn", message: "Health reminder: Take [name] [dosage]. [notes]" }
         → sessionTarget: "isolated"
         → delivery: { mode: "announce", channel: "<preferred channel>" }
```

**Refill reminders:** Calculate `supplyRunsOut = startedOn + (count / dailyDoses) days`, then schedule alert `refillLeadDays` before.

**Appointment reminders:** Create one-shot "at" cron for each `remindBefore` interval.

**Metric tracking reminders:** Cron at scheduled time prompting user to log their reading.

**Instruction-based reminders:** Convert instruction reminders to cron entries.

### 3. Logging Health Data

When user reports a metric or confirms medication taken:

1. Append to `logs` array with timestamp
2. For metrics with `targetRange`, flag if out of range
3. Provide brief acknowledgment

### 4. Dashboard / Status Check

When user asks "how am I doing" or "health status":

1. Read `health-tracker.json`
2. Show active medications with next dose time
3. Show upcoming appointments
4. Show recent metric trends (last 7 entries)
5. Flag any missed doses (scheduled but no log entry)
6. Show refill urgency

### 5. Modify or Discontinue

When user changes dosage, stops a med, or completes instructions:

1. Update the entry (set `active: false` for discontinued)
2. Remove associated cron jobs
3. Confirm changes

## Reminder Message Format

Keep reminders warm, clear, and actionable:

```
💊 Time for Metformin (500mg)
Take with food. Next dose: 8:00 PM
Reply "taken" to log ✓
```

```
📅 Appointment tomorrow: Dr. Zhang — Annual physical
🕐 10:00 AM at City Hospital, Room 302
📋 Prep: Fast 12h before, bring insurance card
```

```
📊 Time to check blood pressure
Log your reading and I'll track the trend.
```

```
⚠️ Refill alert: Metformin
~5 days of supply remaining. Contact pharmacy soon.
```

## Privacy & Safety

- **Never diagnose or recommend medications.** Only track what user/doctor provides.
- **Never modify dosages** without explicit user instruction.
- All data stays local in the JSON file.
- For medical emergencies, remind user to call emergency services.
- Include `prescribedBy` field to maintain provenance.

## Channel Delivery

Default: deliver to the channel where the user set up the tracker. User can specify a preferred channel (Slack channel, Telegram, etc.) per item or globally in `profile.deliveryChannel`.

## Multi-Person Support

The data file is per-person. For family use, maintain separate files (e.g., `health-tracker-mom.json`). Reference the correct file by name.
