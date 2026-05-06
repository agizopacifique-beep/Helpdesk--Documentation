# Escalation Matrix

## What Escalation Is For

Escalation is not a last resort. It is a defined step in the support process that happens when specific criteria are met. The criteria exist so that the decision is never based on how confident a technician feels -- it is based on facts about the ticket.

A Tier 1 technician who escalates at the right time is doing their job correctly. A technician who holds onto a ticket past the escalation threshold because they want to solve it themselves is making the user wait longer than they should.

---

## Escalation Levels

| Level | Who Handles It | Contact |
|---|---|---|
| Tier 1 | Help Desk Technician | First point of contact for all tickets |
| Tier 2 | Systems Administrator | Complex AD, server, network, and security issues |
| Tier 3 | External Vendor | Hardware warranty, ISP, software licensing, cloud services |
| IT Manager | Management escalation | SLA breach, user complaint, security incident |

---

## When to Escalate to Tier 2

Escalate immediately when any of the following are true:

| Situation | Reason |
|---|---|
| Issue affects multiple users or a full department | Blast radius too large for Tier 1 to handle alone |
| Domain Controller, server, or network infrastructure involved | Outside Tier 1 scope |
| Active Directory issue beyond password reset or account unlock | Requires elevated AD permissions |
| Security incident suspected -- malware, phishing, unauthorized access | Requires immediate Tier 2 and IT Manager notification |
| P1 ticket of any type | All P1s involve Tier 2 from the start |
| Tier 1 has followed the full runbook with no resolution | Runbook exhausted -- do not keep trying the same steps |
| Issue requires changes to GPO, DNS, DHCP, or firewall | Requires Tier 2 access and approval |
| P2 ticket unresolved after 2 hours | SLA-triggered escalation |

---

## When to Escalate to Tier 3 (Vendor)

| Situation | Vendor |
|---|---|
| Hardware failure under warranty | Device manufacturer support line |
| Internet or WAN connectivity issue confirmed at ISP level | Internet Service Provider |
| Licensed software error that cannot be resolved locally | Software vendor support |
| Cloud service outage (email, backup, SaaS tools) | Cloud service provider |
| Printer hardware fault after standard troubleshooting exhausted | Printer manufacturer |

Before escalating to a vendor, confirm the issue is not on the TechBridge side. Vendors will ask for this confirmation and escalating without it wastes time.

---

## When to Notify the IT Manager

| Situation | Action |
|---|---|
| P1 ticket created | Notify immediately by phone -- do not wait for them to see the ticket |
| Any SLA breach or near-breach | Notify at 75% of resolution window |
| User has complained about IT support directly | Notify before responding to the complaint |
| Security incident of any kind | Notify immediately alongside Tier 2 |
| A ticket has been escalated to a vendor | Notify so they are aware of external involvement |
| A runbook fails to resolve a recurring issue | Flag for process review |

---

## How to Escalate Correctly

Escalating a ticket is not just reassigning it. The receiving technician or team needs enough context to continue without starting over.

**Before escalating, document in the ticket:**

1. What the user reported
2. What you checked and what you found
3. Every step you took and the result of each
4. Why you are escalating -- which criteria were met
5. Any time-sensitive information (user has a meeting in 30 minutes, issue started during a Windows update, etc.)

**Escalation note template:**

```
