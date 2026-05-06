# SLA Definitions

## What SLA Means in Practice

An SLA is a commitment, not a target. When a ticket comes in and gets assigned a priority, the clock starts. Response time and resolution time are promises made to the user -- not goals to aim for when things are going well.

Breaching an SLA is not just a metric problem. It means a user waited longer than IT committed to, which affects trust in the support operation. Every technician working a ticket queue needs to know which clocks are running and how much time is left.

---

## Priority Definitions

### P1 -- Critical

**What it looks like:**
- Server or core infrastructure down affecting multiple users
- All users in a department unable to work
- Security incident in progress
- Domain controller unreachable

**Response time:** 15 minutes
**Resolution time:** 2 hours
**Escalation:** Immediate -- Tier 1 notifies Tier 2 and IT Manager simultaneously on ticket creation

P1 tickets drop everything else. No other ticket takes priority over a P1 in progress.

---

### P2 -- High

**What it looks like:**
- Single user completely unable to perform their job function
- Business-critical application down for one person
- Account locked with no workaround available
- VPN completely non-functional for a remote worker

**Response time:** 30 minutes
**Resolution time:** 4 hours
**Escalation:** If unresolved after 2 hours, escalate to Tier 2

The test for P2 is simple: can the user do their job at all? If the answer is no and there is no workaround, it is P2.

---

### P3 -- Medium

**What it looks like:**
- Performance degraded but user can still work
- Printer offline with another printer available
- Software error with a known workaround
- Peripheral not functioning (mouse, keyboard, second monitor)

**Response time:** 2 hours
**Resolution time:** 8 hours
**Escalation:** If unresolved by end of business day, notify IT Manager

The test for P3 is whether a workaround exists. If the user can still do their work -- even if it is inconvenient -- it is P3 or lower.

---

### P4 -- Low

**What it looks like:**
- General how-to questions
- Non-urgent software requests
- Minor cosmetic issues (wallpaper, font size)
- Requests that can be batched (e.g. new peripheral for next week)

**Response time:** 4 hours
**Resolution time:** 24 hours
**Escalation:** None unless user escalates directly

P4 tickets are handled in order when higher priority tickets are clear. They are never ignored but they wait their turn.

---

## SLA Clock Rules

**When the clock starts:** The moment the ticket is created in osTicket -- not when the technician picks it up.

**What counts as a response:** A personal reply to the user acknowledging the ticket and giving an expected next step. Auto-acknowledgement emails do not count.

**What counts as resolution:** The user has confirmed the issue is resolved and the ticket is closed. A technician marking a ticket resolved without user confirmation does not count.

**Clock pauses:** The SLA clock pauses when IT is waiting on the user for information and the ticket is marked "Pending User Response." It resumes the moment the user replies.

---

## SLA Breach Procedure

When a ticket is approaching its resolution deadline without a solution:

1. Notify the IT Manager at 75% of the resolution window
2. Escalate to Tier 2 immediately if not already escalated
3. Update the user with current status and revised estimate
4. Document the reason for the breach in the ticket notes

Breaches are reviewed weekly. Patterns -- the same ticket type breaching repeatedly -- trigger a runbook update or a process change.

---

## Related Documents

- `ticket-workflows/ticket-triage-process.md`
- `ticket-workflows/escalation-matrix.md`
