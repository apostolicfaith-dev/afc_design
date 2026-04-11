# Event Registration Platform — Technical Specification

**Organization:** Apostolic Faith Church  
**Prepared for:** ITAC Technical Team  
**Version:** 0.1 (Draft)  
**Date:** April 2026

---

## Executive Summary

This document specifies a multi-event registration platform built for the Apostolic Faith Church. The platform is designed as a configurable, reusable system — not a one-off tool for a single event. The first implementation targets the **Northwest Youth Camp** (Mayfield Lake, WA). The same core system will serve the **International Campmeeting** and other future events with different configurations.

Key capabilities:

- Persistent participant profiles (register once, return for every event)
- Configurable eligibility, registration types, and form fields per event
- Multi-stage acknowledgement workflow (branch leader + optional admin)
- Flexible payment routing: individual or branch-level bulk payment
- Accommodation management with auto-assignment and manual override
- Role-based visibility of sensitive medical data
- Pre-event room allocation notifications

The platform is built on an existing, live implementation already processing thousands of registrations in another region. Portland HQ inherits a proven system rather than a prototype.

---

## Table of Contents

1. [System Architecture Overview](#1-system-architecture-overview)
2. [Core Concepts & Terminology](#2-core-concepts--terminology)
3. [Event Configuration Model](#3-event-configuration-model)
4. [User Roles & Permissions](#4-user-roles--permissions)
5. [Registration Module](#5-registration-module)
6. [Acknowledgement Workflow](#6-acknowledgement-workflow)
7. [Payment Module](#7-payment-module)
8. [Accommodation & Room Assignment](#8-accommodation--room-assignment)
9. [Communications Module](#9-communications-module)
10. [ID Card Generation](#10-id-card-generation)
11. [Admin Dashboards & Exports](#11-admin-dashboards--exports)
12. [Event Configurations — Youth Camp vs International Camp](#12-event-configurations--youth-camp-vs-international-camp)
13. [Data Model](#13-data-model)
14. [Non-Functional Requirements](#14-non-functional-requirements)

---

## 1. System Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Frontend (Next.js)                    │
│  Participant Portal │ Branch Leader Portal │ Admin Panel │
└────────────────────────────┬────────────────────────────┘
                             │ REST API
┌────────────────────────────▼────────────────────────────┐
│                   Backend (ASP.NET / C#)                 │
│                                                          │
│  Event Config  │  Registration  │  Workflow Engine       │
│  Payment       │  Room Assign   │  Notifications         │
└──────────┬─────────────────────────────┬────────────────┘
           │                             │
    ┌──────▼──────┐              ┌───────▼──────┐
    │  PostgreSQL  │              │    Redis      │
    │  (primary)   │              │  (sessions,   │
    └─────────────┘              │   cache, jobs)│
                                 └──────────────┘
                                        │
                              ┌─────────▼────────┐
                              │  Subsplash        │
                              │  (payments)       │
                              └──────────────────┘
```

**Tech stack summary:**

- Backend: ASP.NET (C#), RESTful API
- Frontend: Next.js (React), mobile-responsive
- Database: PostgreSQL
- Cache / background jobs: Redis
- Payments: Subsplash integration
- Email: transactional email service (e.g. SendGrid / AWS SES)

---

## 2. Core Concepts & Terminology

| Term                                  | Definition                                                                                                                                                                                                                                                                            |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Event**                             | A configurable registration instance (e.g. Youth Camp 2026, Campmeeting 2026)                                                                                                                                                                                                         |
| **Event Config**                      | The set of rules and modules enabled for a specific event                                                                                                                                                                                                                             |
| **Registration**                      | A single participant's application to attend an event                                                                                                                                                                                                                                 |
| **Registration Type**                 | The role a participant is registering as (Camper, Counsellor, etc.) — defined per event                                                                                                                                                                                               |
| **Branch**                            | A local church unit. Branches are pre-configured at the organisation level and shared across events                                                                                                                                                                                   |
| **Branch Leader**                     | A user assigned by an event admin to lead a branch for a specific event. Assignment is per event (e.g. the Youth Camp branch leader may differ from the Campmeeting branch leader). Assignment is done by email; the person receives a login with branch leader access for that event |
| **Acknowledgement**                   | A branch leader or admin reviewing and confirming/rejecting a registration                                                                                                                                                                                                            |
| **Bulk Payment**                      | A branch leader paying for multiple registrations in a single transaction                                                                                                                                                                                                             |
| **Room**                              | A physical accommodation unit with a configured capacity (e.g. cabin, bunk room)                                                                                                                                                                                                      |
| **Building**                          | A grouping of rooms (e.g. Cabin Block A)                                                                                                                                                                                                                                              |
| **Counsellor**                        | A registration type for older participants who are assigned to rooms to supervise campers                                                                                                                                                                                             |
| **Camp Medic / Kitchen / Counsellor** | Staff roles with specific access to participant medical data                                                                                                                                                                                                                          |

---

## 3. Event Configuration Model

Every event is created from a configuration object. This is what makes the system reusable. Administrators configure an event without changing code.

### 3.1 Core Event Settings

| Setting                        | Type     | Description                                                                                                        |
| ------------------------------ | -------- | ------------------------------------------------------------------------------------------------------------------ |
| `name`                         | string   | Display name (e.g. "Northwest Youth Camp 2026")                                                                    |
| `start_date`                   | date     | Start of the camp                                                                                                  |
| `end_date`                     | date     | End of the camp                                                                                                    |
| `registration_open`            | datetime | When registration opens                                                                                            |
| `registration_close`           | datetime | Auto-close deadline                                                                                                |
| `capacity`                     | integer  | Maximum total registrations across all types (optional)                                                            |
| `capacity_per_type`            | map      | Optional per-registration-type caps (e.g. `{"COUNSELLOR": 20}`). Unspecified types share the overall capacity pool |
| `is_registration_open`         | boolean  | Manual override — admin can force-close                                                                            |
| `fee`                          | decimal  | Base registration fee (0 if free)                                                                                  |
| `registration_types`           | list     | Configurable types available for this event (see §3.2)                                                             |
| `eligibility_rules`            | object   | Grade range, age range, membership criteria                                                                        |
| `acknowledgement_stages`       | list     | Who acknowledges and in what order (see §6)                                                                        |
| `payment_config`               | object   | Modes enabled (individual, bulk), visibility rules                                                                 |
| `accommodation_config`         | object   | Required/optional, free/paid, room rules                                                                           |
| `room_assignment_enabled`      | boolean  | Whether the event uses room/accommodation assignment at all. False for day events or self-arranged accommodation   |
| `form_sections`                | list     | Which data sections appear on the registration form                                                                |
| `medical_collection`           | boolean  | Whether medical info is collected                                                                                  |
| `activity_permissions_enabled` | boolean  | Whether an activity list is defined and shown during registration                                                  |
| `activities`                   | list     | Admin-defined list of activities (name + icon); empty if feature is off                                            |
| `photo_upload_enabled`         | boolean  | Whether participants upload a photo during registration (used on ID cards)                                         |

### 3.2 Branch Setup

Branches are configured at the organisation level (not per event) by a super admin. Each branch has a name and an optional region/area tag. This list is shared across all events.

During registration, participants select their branch from this pre-configured list.

**Branch leader assignment** is per event:

- A super admin or event admin assigns a branch leader by entering their login email
- The system creates or links an account for that person with Branch Leader access scoped to that event
- Assignment is copyable from a previous event of the same type — e.g. when creating Youth Camp 2027, the admin can copy branch leader assignments from Youth Camp 2026 and adjust as needed
- One person can be a branch leader for multiple branches or multiple events simultaneously; their dashboard shows all

### 3.3 Registration Types (per event)

Each event defines its own list of registration types. Examples:

**Youth Camp:**

- `CAMPER` — standard camper; eligibility-gated (grade 4–12, implicitly under 18). Parent/guardian form and liability waiver required. Selecting this type while outside the grade range is blocked at submission.
- `COUNSELLOR` — older participant (18+) registering as a cabin counsellor. Self-form only. No grade gate.
- Additional roles are configurable per event (e.g. `STAFF`, `MEDIC`, `KITCHEN`)

**Design rule:** There is no separate "18+ camper" type. If a participant is 18 or older, the `CAMPER` type is ineligible and they must select a non-camper role. Roles other than `CAMPER` do not require a parent/guardian section.

**International Camp:**

- `REGISTRANT` — single type for all attendees

Each registration type can have its own:

- Form sections required
- Eligibility conditions (age, grade, membership)
- Accommodation rules
- Whether a liability waiver is required
- Whether parent/guardian section is required

### 3.4 Eligibility Rules

Configured per event, checked at registration submission:

```json
{
  "grade_range": { "min": 4, "max": 12 },
  "age_range": { "min": null, "max": null },
  "require_sunday_school_membership": true,
  "require_good_standing": true
}
```

If a participant does not meet eligibility, registration is blocked with a clear message.

---

## 4. User Roles & Permissions

### 4.1 Role Definitions

| Role              | Scope                 | Key Capabilities                                                                                                                                                              |
| ----------------- | --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Participant**   | Own registrations     | Register, view status, pay                                                                                                                                                    |
| **Branch Leader** | Own branch, per event | View/manage branch registrations, acknowledge, initiate bulk payment                                                                                                          |
| **Event Admin**   | Single event          | Full registration view, second-stage acknowledgement, room assignment, export                                                                                                 |
| **Super Admin**   | All events            | Configure events, manage branch leaders, all admin capabilities                                                                                                               |
| **Camp Medic**    | Single event          | View medications and medical conditions for their event                                                                                                                       |
| **Kitchen Staff** | Single event          | View food restrictions and allergies for their event                                                                                                                          |
| **Counsellor**    | Assigned room only    | View behavioral/sleep conditions of campers in their assigned room. Auto-granted when a participant registers as the `COUNSELLOR` registration type and is assigned to a room |

**Multi-role support:** A user account can hold multiple system roles simultaneously, assigned by an admin. For example, a person could be a Branch Leader for one event and a Camp Medic for another. Within a single event, a person could be both an Event Admin and a Counsellor. The system evaluates the union of all roles when determining access. The `Counsellor` role specifically is auto-granted on room assignment when the registration type is `COUNSELLOR` — no manual admin step required for that one.

### 4.2 Medical Data Visibility Matrix

| Data Field                                  | Participant | Branch Leader | Event Admin | Camp Medic | Kitchen | Counsellor      |
| ------------------------------------------- | ----------- | ------------- | ----------- | ---------- | ------- | --------------- |
| Medications                                 | Own only    | No            | Yes         | Yes        | No      | Their room only |
| Food restrictions                           | Own only    | No            | Yes         | No         | Yes     | No              |
| Allergies                                   | Own only    | No            | Yes         | No         | Yes     | Their room only |
| Behavioral conditions                       | Own only    | No            | Yes         | No         | No      | Their room only |
| Night terrors / bed wetting / sleep walking | Own only    | No            | Yes         | No         | No      | Their room only |
| Insurance info                              | Own only    | No            | Yes         | Yes        | No      | No              |

---

## 5. Registration Module

### 5.1 Participant Flow

```
Create Account / Log In
        │
        ▼
Select Event + Registration Type
        │
        ▼
Eligibility Check (grade, age, membership)
        │ (fail → blocked with message)
        ▼
Complete Registration Form
        │
        ▼
Review & Submit
        │
        ▼
Status: PENDING ACKNOWLEDGEMENT
```

**Account model for under-18 campers:** The parent/guardian owns the account. A parent logs in and manages child profiles from their account — one account can hold multiple child profiles (e.g. siblings). When registering for an event, the parent selects which child they are registering, and that child's profile (personal info, emergency contacts, medical history) is pre-filled from the previous event. They confirm or update what has changed. For 18+ registration types (Counsellor, etc.), participants own their own account and register directly.

On return visits, all pre-fillable sections are populated from the most recent registration for that participant. The parent or participant confirms or updates what has changed before submitting.

### 5.2 Registration Form Sections

Sections are toggled per event config and per registration type:

#### Section A — Participant Details (all events)

- Full name
- Gender
- Date of birth
- Grade (next school year) — if applicable
- Photo upload (when `photo_upload_enabled = true`; appears on ID cards)

#### Section B — Contact / Guardian (CAMPER registration type only)

Since `CAMPER` is eligibility-gated to grade 4–12 (under 18), this section is always shown for campers and never for other roles.

- Parent/Guardian: name, address, city/state/zip, email, home/work/cell
- Emergency Contact #1: name, relationship, address, email, home/work/cell
- Emergency Contact #2: name, relationship, address, email, home/work/cell

#### Section C — Medical & Health (when `medical_collection` is enabled)

- Medications currently in use (free text + note about original containers)
- Food restrictions
- Allergies
- Medical or behavioral conditions (free text)
- Flags: night terrors (y/n), bed wetting (y/n), sleep walking (y/n)
- Primary medical insurance: provider + group/ID #

#### Section D — Transportation (when enabled, per event)

- Choice: church-provided transport / own arrangement (describe)
- For international camp: flight details (airline, flight number, arrival date/time, departure, pickup needed, notes)

#### Section E — Accommodation Preference (when accommodation is enabled)

- Request accommodation: yes / no (when optional)
- Cabin friend preference: one name, same grade (youth camp specific, drives room assignment)

#### Section F — Activity Permissions (when `activity_permissions_enabled = true` in event config)

Event admins pre-define the list of activities for the event (e.g. Swimming, Waterslide, Hiking, Crafts, Ball Games). This section only appears if the event has an activity list configured — it is not shown for events where no activities are defined (e.g. International Camp in its current form).

During registration, the parent/guardian (or participant, for non-camper roles) reviews the list and explicitly marks each activity as **permitted** or **excluded**.

This is not free text — it is a structured checklist against the admin-defined activity list. This serves two purposes:

1. Gives the camp a clear, per-participant record of parental consent per activity
2. Feeds directly into **ID card generation**: each printed ID card will display symbols or icons indicating which activities are permitted for that participant. Counsellors and activity staff can check the ID on-site.

Default state: all activities are assumed permitted unless explicitly excluded.

#### Section G — Liability Waiver (when enabled)

- Display of waiver text
- Digital acknowledgement by parent/guardian (CAMPER type) or participant (all other types)

### 5.3 Registration Statuses

```
PENDING_ACKNOWLEDGEMENT
        │
        ├──► REJECTED (with reason)
        │         │
        │         └──► [if registration still open]
        │                    RE-SUBMITTED → PENDING_ACKNOWLEDGEMENT (new record)
        │
        ▼
ACKNOWLEDGED_BY_BRANCH
        │
        ├──► [if optional admin stage enabled]
        │         │
        │         ▼
        │   ACKNOWLEDGED_BY_ADMIN (or REJECTED_BY_ADMIN → re-submit path above)
        │
        ▼
        ├──► [if fee > 0]
        │         │
        │         ▼
        │   PAYMENT_PENDING
        │         │
        │         ├──► PAYMENT_INITIATED (individual link sent or bulk invoiced)
        │         │
        │         ▼
        │        PAID
        │         │
        ▼         ▼
        [if fee = 0, skip payment states entirely]
        │
        ▼
        ├──► [if room_assignment_enabled = true]
        │         │
        │         ▼
        │   ROOM_ASSIGNED
        │         │
        │         ▼
        │   CONFIRMED (room allocation email sent)
        │
        └──► [if room_assignment_enabled = false]
                  │
                  ▼
            CONFIRMED (confirmation email sent, no room details)
```

**Key branching rules:**

- If `fee = 0`: registration jumps from ACKNOWLEDGED directly to the room/confirm step — no payment states
- If `room_assignment_enabled = false`: registration goes directly to CONFIRMED after payment (or after acknowledgement if free)
- REJECTED registrations can be re-submitted as a new record while `is_registration_open = true`

---

## 6. Acknowledgement Workflow

### 6.1 Overview

Acknowledgement is the gate between registration submission and payment generation. It is not just approval — it is capacity management and validation. Each stage is configurable per event.

### 6.2 Stage Configuration

```json
{
  "acknowledgement_stages": [
    {
      "stage": 1,
      "actor": "BRANCH_LEADER",
      "required": true,
      "label": "Branch Leader Review"
    },
    {
      "stage": 2,
      "actor": "EVENT_ADMIN",
      "required": false,
      "label": "Camp Admin Confirmation"
    }
  ]
}
```

### 6.3 Branch Leader Acknowledgement (Stage 1)

- Branch leader sees all registrations from their branch in their dashboard
- For each registration, they can: **Acknowledge** or **Reject**
- On rejection, they must provide a reason (free text or predefined options):
  - "No space available in your requested accommodation"
  - "Camp is at capacity"
  - "Registration information is incomplete"
  - "Other" (free text)
- On rejection, the registrant is notified by email with the reason
- The registrant may re-submit as long as `is_registration_open = true`. Re-submission creates a new registration record; the rejected one is archived. The re-submitted registration goes back through the full acknowledgement flow.

### 6.4 Event Admin Acknowledgement (Stage 2 — Optional)

- Triggered after branch leader acknowledges, if stage 2 is enabled
- Event admin sees all branch-acknowledged registrations pending their review
- Same acknowledge / reject options
- Use cases: room availability confirmation, special case validation, capacity overflow

### 6.5 Manual Registration Closure

- Event admins can set `is_registration_open = false` at any time
- This blocks new submissions immediately
- Existing registrations in-flight are not affected

---

## 7. Payment Module

### 7.1 Payment Modes (per event config)

| Mode         | Description                        | Youth Camp | International |
| ------------ | ---------------------------------- | ---------- | ------------- |
| `INDIVIDUAL` | Registrant / parent pays directly  | Yes        | Yes           |
| `BULK`       | Branch leader pays for their group | Yes        | No            |

Both modes can be enabled simultaneously for the same event. The branch leader decides per registration which mode to use.

### 7.2 Individual Payment Flow

1. After acknowledgement, a payment link is generated and emailed to the registrant (or parent/guardian for under 18)
2. Payment is completed via Subsplash
3. System receives webhook confirmation → registration status → `PAID`

### 7.3 Bulk Payment Flow (Branch Leader)

From the branch leader dashboard:

1. Leader sees their group's acknowledged registrations, each with payment status
2. Leader selects registrations to **pay in bulk** vs **send individual payment links**
3. For bulk: a single consolidated invoice is generated showing:
   - Total amount due
   - Drillable breakdown: per-person amount, name, registration type
4. Leader pays via Subsplash (single transaction)
5. System allocates payment across all selected registrations → each marked `PAID`
6. For those sent individual: payment links go out to registrants directly

**Mixed scenario:** Leader can pay for some registrations in bulk and send individual links for others. The system manages both simultaneously.

### 7.4 Payment Deadline

A payment deadline is configurable per event in the payment config:

```json
{
  "payment_config": {
    "modes": ["INDIVIDUAL", "BULK"],
    "payment_deadline": "2026-06-20",
    "reminder_days_before": [7, 3, 1]
  }
}
```

- `payment_deadline` — date by which payment must be completed
- `reminder_days_before` — system sends reminder emails to unpaid registrants at each configured interval before the deadline

**What happens if unpaid after deadline:** The system does not automatically cancel or penalise. The registration remains in `PAYMENT_PENDING` status. It will simply be ineligible for room assignment and ID card generation until paid. The branch leader and event admin can see all unpaid registrations and handle it administratively.

### 7.5 Fundraising Credit (Informational Display)

The local branch may subsidize participant fees through fundraising activities. This is managed entirely at the branch level and does not affect the amount the system charges. The system always collects the full configured fee. The subsidy from the branch's fundraising is their internal accounting.

Optionally, a **credit note** field can be attached to a registration (entered by the branch leader) as an informational flag for their own records. This does not alter payment logic.

### 7.6 Payment Visibility Rules

| Role                                   | Can See Payment Amount                  |
| -------------------------------------- | --------------------------------------- |
| Participant                            | Own amount only                         |
| Branch Leader (youth camp)             | Yes — their group                       |
| Branch Leader / Pastor (international) | No — amounts are private per registrant |
| Event Admin                            | Yes — all                               |

---

## 8. Accommodation & Room Assignment

### 8.1 Configuration

Accommodation is configured per event:

```json
{
  "accommodation_config": {
    "required": true,
    "is_paid": false,
    "buildings": [...],
    "counsellor_required_per_room": true,
    "min_counsellors_per_room": 1,
    "auto_assignment_rules": [...]
  }
}
```

For international camp: `required = false`, participant opts in. Pricing is set per room — each room has its own `price` value (0 = free). When a participant is assigned to (or selects) a paid room, a payment is triggered for that room's price in addition to (or instead of) the base event fee. The two can be combined or the base fee can be 0 with pricing entirely at the room level.

### 8.2 Room Structure

```
Building (e.g. "Cabin Block A")
  └── Room (e.g. "Cabin 3")
        ├── capacity: 8  (total individuals)
        ├── gender: MALE / FEMALE / MIXED (configurable)
        └── slots: [filled dynamically]
```

Capacity is a single integer — the number of individuals the room can hold. No distinction between bed types is tracked; that is a physical concern managed on-site.

### 8.3 Counsellor Rule (Youth Camp Only)

When `counsellor_required_per_room = true`:

- At least one counsellor must be assigned to each room that has campers
- When auto-assigning campers, one slot per room is reserved for a counsellor
- Counsellors are assigned after campers are placed; they fill reserved slots
- If there are more counsellors than rooms, excess counsellors are doubled into rooms (no room is left with zero counsellors)
- This rule does not apply to international camp (disabled in config)

### 8.4 Auto-Assignment Rules

Rules are configurable per event. Each rule has a priority weight. The assignment engine attempts to honour rules in order:

| Rule                | Description                                                                   |
| ------------------- | ----------------------------------------------------------------------------- |
| `GENDER_MATCH`      | Same gender participants in same room                                         |
| `GRADE_RANGE`       | Group by grade band (e.g. 4th-6th, 7th-9th, 10th-12th)                        |
| `AGE_RANGE`         | Group by age band                                                             |
| `BRANCH_TOGETHER`   | Prefer same-branch participants in same room                                  |
| `FRIEND_PREFERENCE` | Honour stated cabin friend preference (best effort, same grade required)      |
| `REGISTRATION_TYPE` | Counsellors separated from general campers (except for counsellor rule above) |

Auto-assignment runs as a batch job. Admins trigger it and review the output before it is committed. It is a **proposal**, not an automatic commit.

### 8.5 Manual Assignment UI

Admins have a visual room management interface:

- Grid/board view: buildings on columns, rooms as cards showing capacity used / total
- Each room card shows assigned participants (name, registration type, grade)
- Drag-and-drop to move participants between rooms
- Filter/search by branch, grade, gender, registration type
- Conflicts (over capacity, counsellor rule violation) are highlighted in real time

### 8.6 Pre-Camp Room Allocation Email

Before camp starts, admins can trigger a **room allocation email** to all confirmed participants. The email includes:

- Participant name
- Assigned building and room
- Cabin counsellor name(s)
- Any relevant notes (configurable template)

Timing is configured per event (e.g. 1 week before start date).

---

## 9. Communications Module

### 9.1 Triggered Emails

| Trigger                              | Recipient                        | Content                                                                                                           |
| ------------------------------------ | -------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Registration submitted               | Participant / Parent             | Confirmation of submission, status: pending review                                                                |
| New registration in branch           | Branch Leader                    | A new registration in your branch is awaiting your review                                                         |
| Branch leader acknowledgement        | Participant / Parent             | Acknowledged — payment info to follow                                                                             |
| Rejection (any stage)                | Participant / Parent             | Reason for rejection                                                                                              |
| Payment link generated               | Participant / Parent             | Unique payment link, amount, deadline                                                                             |
| Individual payment received (branch) | Branch Leader                    | Notification that a registrant in their branch has paid individually (when bulk payment is enabled for the event) |
| Bulk invoice generated               | Branch Leader                    | Invoice total, per-person breakdown, payment link                                                                 |
| Payment confirmed                    | Participant / Parent             | Payment receipt                                                                                                   |
| Room assigned                        | (internal only until batch send) | —                                                                                                                 |
| Room allocation batch email          | All confirmed participants       | Room, building, counsellor names                                                                                  |
| Registration closed                  | Anyone attempting to register    | Event is no longer accepting registrations                                                                        |
| Payment deadline approaching         | Participant / Parent (unpaid)    | Reminder that payment deadline is N days away (configurable)                                                      |

### 9.2 Email Templates

All email templates are configurable per event by admins. They support variable substitution (participant name, event name, amount, room, etc.).

---

## 10. ID Card Generation

### 10.1 Overview

After a registration reaches `ROOM_ASSIGNED` status, an ID card can be generated for that participant. Cards are generated in batch by event admins and printed on-site.

Each ID card contains:

- Participant name
- Photo (if submitted during registration — optional field)
- Registration type (CAMPER / COUNSELLOR / etc.)
- Branch name
- Assigned building and room
- QR code (links to the participant's registration record; used for on-site check-in and verification)
- **Activity permission indicators** — icons or symbols for each configured activity, showing whether the participant is permitted or excluded

### 10.2 Activity Permission Indicators on ID Cards

The activity list is defined by admins in the event config. For each activity, a distinct icon is assigned (configurable). On the printed ID card:

- **Permitted activity** — icon is shown (filled / coloured)
- **Excluded activity** — icon is absent, greyed out, or marked with an X (configurable display)

This allows counsellors, lifeguards, and activity staff to check a camper's card on-site without needing to look up the registration in the system.

Example activities for Youth Camp: Swimming, Waterslide, Hiking, Crafts, Ball Games

### 10.3 QR Code Validation

Each ID card's QR code encodes a unique registration token. Scanning the QR code in the admin panel or a dedicated validator screen shows:

- Participant name and photo
- Registration status (confirmed / flagged)
- Room assignment
- Activity permissions

This supports on-site check-in at camp arrival and can be used at activity stations if needed.

### 10.4 Batch Generation

Admins trigger ID card generation in batch (all confirmed registrations, or filtered by building/room/branch). Output is a print-ready PDF. Cards are sized for standard ID card stock.

---

## 11. Admin Dashboards & Exports

### 11.1 Event Overview Dashboard (Event Admin)

- Total registrations by status (pie / bar)
- Capacity utilization (registered vs. cap)
- Payment status summary (paid, pending, bulk pending)
- Rooms: assigned vs. unassigned
- Quick filters: branch, registration type, payment status, acknowledgement status

### 11.2 Branch Leader Dashboard

- List of their branch's registrations with status
- Payment routing controls per registration (bulk / individual)
- Bulk invoice generation
- Acknowledgement action (stage 1)

### 11.3 Room Assignment Dashboard

- Building / room grid view
- Auto-assign trigger + review
- Manual drag-and-drop
- Counsellor rule compliance indicator

### 11.4 Medical Dashboard (Camp Medic)

- List of participants with medications
- Filterable by cabin/room
- Flag for "medication container received on arrival"

### 11.5 Transport / Flight Tracking

- **Youth Camp:** list of participants by transport mode (church bus / own). Bus list with headcount.
- **International Camp:** full flight tracking view — airline, flight number, arrival date/time, pickup needed. Filterable by arrival date. Exportable to spreadsheet.

### 11.6 Exports

All list views are exportable to CSV/Excel. Specific export templates:

- Full registrations export (all fields, for event records)
- Room allocation export (room-per-row with occupants)
- Payment summary export
- Medical summary export (restricted by role)
- Transport / bus list export

---

## 12. Event Configurations — Youth Camp vs International Camp

| Feature                                               | Youth Camp                                              | International Camp             |
| ----------------------------------------------------- | ------------------------------------------------------- | ------------------------------ |
| Registration types                                    | Camper (grade 4–12 only), Counsellor (18+), other roles | Registrant                     |
| Eligibility gate                                      | Grade 4–12 for Camper; 18+ must pick non-camper role    | (configurable)                 |
| Parent/guardian form                                  | Required for Camper type                                | Not applicable                 |
| Emergency contacts                                    | Required                                                | Optional                       |
| Medical collection                                    | Full (medications, food, allergies, behavioral, flags)  | Full                           |
| Liability waiver                                      | Required                                                | Required                       |
| Transport section                                     | Church bus vs own                                       | Flight details                 |
| Activity permissions (`activity_permissions_enabled`) | Yes — Swimming, Waterslide, Hiking, Crafts, Ball Games  | No (not configured)            |
| Cabin friend preference                               | Yes                                                     | No                             |
| Acknowledgement Stage 1                               | Branch Leader (Sunday school teacher)                   | Branch Pastor                  |
| Acknowledgement Stage 2                               | Event Admin (optional)                                  | Event Admin (optional)         |
| Bulk payment                                          | Yes (branch leader)                                     | No                             |
| Leader sees payment amounts                           | Yes                                                     | No                             |
| Accommodation                                         | Required, included in fee                               | Optional; some free, some paid |
| Counsellor room rule                                  | Yes (min 1 per room)                                    | No                             |
| Auto-assignment rules                                 | Gender, grade, branch, friend preference                | Gender, branch, age            |
| Pre-camp room email                                   | Yes                                                     | Yes                            |

---

## 13. Data Model

### 13.1 Key Entities

```
Account
  ├── email / login credentials
  ├── account_type (PARENT | SELF)
  └── participants[]  (one for SELF accounts; one or more children for PARENT accounts)

Participant
  ├── account_id
  ├── profile (name, dob, gender, address, contact)
  ├── guardian (populated from parent account for under-18)
  ├── emergency_contacts[]
  └── branch_id

Event
  ├── config (EventConfig)
  ├── buildings[]
  └── registrations[]

Registration
  ├── participant_id
  ├── event_id
  ├── registration_type
  ├── status (enum)
  ├── medical_info (MedicalInfo)
  ├── transport_info (TransportInfo)
  ├── accommodation_request (AccommodationRequest)
  ├── activity_permissions[] (ActivityPermission: activity_id, permitted bool)
  ├── liability_waiver_signed (bool + timestamp)
  ├── payment_mode (INDIVIDUAL | BULK | null)
  └── acknowledgements[]

Acknowledgement
  ├── registration_id
  ├── stage (1 | 2)
  ├── actor_role
  ├── actor_user_id
  ├── decision (ACKNOWLEDGED | REJECTED)
  ├── reason (nullable)
  └── timestamp

Payment
  ├── type (INDIVIDUAL | BULK)
  ├── amount
  ├── status (PENDING | PAID | FAILED)
  ├── subsplash_ref
  └── registrations[] (1 for individual, many for bulk)

Building
  ├── event_id
  ├── name
  └── rooms[]

Room
  ├── building_id
  ├── name / number
  ├── capacity (integer)
  ├── gender (MALE | FEMALE | MIXED)
  ├── price (decimal, 0.00 = free)
  └── assignments[]

RoomAssignment
  ├── room_id
  ├── registration_id
  └── role (CAMPER | COUNSELLOR)

MedicalInfo
  ├── medications (text)
  ├── food_restrictions (text)
  ├── allergies (text)
  ├── conditions (text)
  ├── night_terrors (bool)
  ├── bed_wetting (bool)
  ├── sleep_walking (bool)
  └── insurance_provider / group_id

TransportInfo
  ├── mode (CHURCH | OWN | FLIGHT)
  ├── own_arrangement_description (text)
  └── flight (airline, flight_number, arrival_datetime, departure_datetime, pickup_needed, notes)
```

### 13.2 Key Relationships

- One `Participant` has many `Registration`s (one per event)
- One `Event` has one `EventConfig`
- One `Registration` has one `MedicalInfo`, one `TransportInfo`, one `AccommodationRequest`
- One `Registration` has many `Acknowledgement`s (one per stage)
- One `Payment` (BULK) links to many `Registration`s
- One `Room` has many `RoomAssignment`s up to its capacity

---

## 14. Non-Functional Requirements

### 14.1 Performance

- Target: support 500+ concurrent active users during registration open periods
- Room assignment batch job: complete within 30 seconds for up to 500 registrations
- API response time: < 300ms for 95th percentile under normal load

### 14.2 Security

- All participant and medical data encrypted at rest (PostgreSQL encryption)
- HTTPS enforced on all endpoints
- Role-based access control enforced server-side (not just frontend)
- Medical data access logged (audit trail)
- Session management via Redis with configurable TTL

### 14.3 Data Ownership

- All data is owned by Apostolic Faith Church
- No third-party lock-in for core data (Subsplash is integration only)
- Full data export available to super admins at any time

### 14.4 Extensibility

- New events are created via configuration, not code changes
- New registration types are addable via config
- New acknowledgement stages are addable without schema changes
- Medical fields, form sections, and email templates are configurable per event

### 14.5 Availability

- Target uptime: 99.5% during registration open windows
- Graceful degradation: if Subsplash is unavailable, registrations still save and payment links are queued

---

_Document status: Draft — pending review by ITAC technical team._  
_Next step: Review, clarify open questions, then move to implementation planning._
