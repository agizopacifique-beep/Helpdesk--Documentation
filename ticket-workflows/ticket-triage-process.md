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
Hi [Name],

Thanks for reaching out. I have logged your request as ticket #[number] 
and assigned it [Priority] priority. 

I will [respond with an update / have this resolved] by [time based on SLA].

Let me know if anything changes or gets worse in the meantime.

[Your name]
IT Support -- TechBridge Solutions
```

This takes 60 seconds. It prevents follow-up calls, duplicate tickets, and users walking over to IT.

---

### Step 7: Start Working the Ticket

Pull the relevant runbook and follow it. Document every action taken in the ticket notes as you go -- not after the fact.

Notes should answer three questions:
- What did you find?
- What did you do?
- What was the result?

Vague notes like "checked settings" are not useful. "Checked DNS settings on CLIENT01 -- primary DNS was pointing to 8.8.8.8 instead of DC01 at 192.168.56.10. Updated to correct address and confirmed connectivity restored." is useful.

---

## Ticket Status Definitions

| Status | Meaning |
|---|---|
| Open | Ticket received, not yet assigned or worked |
| In Progress | Technician actively working the ticket |
| Pending User Response | Waiting on information from the user -- SLA clock paused |
| Escalated | Passed to Tier 2 or vendor |
| Resolved | Fix applied, waiting for user confirmation |
| Closed | User confirmed resolution or ticket auto-closed after 48 hours |

Never skip from In Progress to Closed without going through Resolved first. User confirmation is required.

---

## End of Day Queue Check

Before finishing for the day every technician checks:

- [ ] All P1 and P2 tickets are resolved or actively escalated
- [ ] All P3 tickets have had at least one update to the user today
- [ ] No ticket has breached SLA without a documented reason
- [ ] All tickets worked today have complete notes
- [ ] Any ticket approaching SLA breach tomorrow is flagged for early attention

---

## Related Documents

- `sla-policies/sla-definitions.md`
- `ticket-workflows/escalation-matrix.md`
- `runbooks/` -- all runbooks
