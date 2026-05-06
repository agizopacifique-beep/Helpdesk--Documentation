# Ticket Triage Process

## Why Triage Matters

A ticket queue without triage is just a list. Triage is what turns that list into a prioritized workload where the right issues get attention first and nothing falls through the cracks.

Every ticket that comes in goes through this process before a technician starts working it. The whole thing takes under two minutes. Skipping it costs more time than it saves.

---

## Ticket Intake Channels

TechBridge Solutions accepts support requests through three channels:

| Channel | How It Creates a Ticket | Response Expectation |
|---|---|---|
| osTicket web portal | User submits directly -- ticket created automatically | SLA clock starts immediately |
| Email to helpdesk@techbridge.local | Auto-converts to ticket in osTicket | SLA clock starts on email receipt |
| Phone call | Technician creates ticket manually during the call | SLA clock starts when ticket is created |

Walk-up requests are handled immediately if the technician is available. A ticket is still created for every walk-up -- no exceptions. If it was not in the system it did not happen.

---

## Triage Steps

### Step 1: Read the Full Ticket Before Doing Anything

Read the entire submission. Do not skim. Users often bury the most important detail in the last sentence. A ticket titled "email problem" might describe a full mailbox, a password issue, or a phishing attempt -- three completely different responses.

If the submission is too vague to triage, contact the user immediately for clarification before assigning a priority.

---

### Step 2: Assign Priority

Use the criteria below. When in doubt between two priorities, assign the higher one and adjust after speaking with the user.

**Ask these questions in order:**

1. Are multiple users affected?
   - Yes and they cannot work → **P1**
   - Yes but they have a workaround → **P3**

2. Can the user do their job at all?
   - No and no workaround exists → **P2**
   - No but a workaround exists → **P3**

3. Is the user impacted but still functional?
   - Yes → **P3**

4. Is this a question, request, or minor inconvenience?
   - Yes → **P4**

---

### Step 3: Assign a Category

Categories help with reporting and runbook matching. Assign one category per ticket:

| Category | Examples |
|---|---|
| Account Access | Password reset, account lockout, permission denied |
| Hardware | Laptop fault, monitor, keyboard, printer |
| Software | Installation, crash, licensing, updates |
| Network | No internet, slow connection, VPN, shared drive |
| Email | Cannot send or receive, configuration, full mailbox |
| New Request | New user setup, new hardware, access request |
| Security | Suspected phishing, malware, unauthorized access |
| Other | Anything that does not fit above |

---

### Step 4: Check for an Existing Runbook

Before starting work, check if a runbook exists for this ticket type. Runbooks are in `/runbooks/`. If one exists, follow it. Do not improvise a process that is already documented.

If no runbook exists and this ticket type has come up before, flag it for runbook creation after the ticket closes.

---

### Step 5: Assign the Ticket

| Situation | Action |
|---|---|
| P1 ticket | Assign to yourself, notify Tier 2 and IT Manager immediately |
| P2 or P3 ticket | Assign to the technician with the lightest current load |
| P4 ticket | Add to the queue -- assign when higher priority work clears |
| Outside IT scope | Redirect to the correct team and close with a note |

---

### Step 6: Send an Acknowledgement to the User

Every ticket gets a personal acknowledgement before any work begins. Auto-replies do not count.

**Template:**

```
