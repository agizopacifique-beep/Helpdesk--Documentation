# HELP DESK DOCUMENTATION SYSTEM

![Support](https://img.shields.io/badge/IT-Help%20Desk%20Support-blue) ![Runbooks](https://img.shields.io/badge/Runbooks-9-green) ![MSP](https://img.shields.io/badge/Environment-MSP%20Ready-orange) ![Tier](https://img.shields.io/badge/Support-Tier%201%20%26%202-purple)

## PROJECT OVERVIEW

Built a production-ready help desk documentation system modeled after how a real Managed Service Provider (MSP) operates across multiple customer networks. This project demonstrates the ability to work support tickets independently, manage priority in a fast-paced environment, escalate appropriately, and create and update detailed documentation and diagrams.

Every runbook in this repo was written assuming the technician reading it is handling their eighth ticket of the day, has two more waiting, and has never seen this specific issue before. That constraint drives every formatting decision — specific commands instead of vague steps, exact escalation triggers instead of judgment calls, and ticket closure checklists instead of open-ended sign-offs.

---

## WHAT IS INCLUDED

| Folder | Contents |
|--------|---------|
| runbooks/ | Step-by-step resolution guides for the most common help desk ticket types |
| sla-policies/ | Response and resolution time commitments by priority level |
| ticket-workflows/ | Ticket triage, escalation matrix, and queue management procedures |

---

## RUNBOOK LIBRARY

| # | Runbook | Category | Tier |
|---|---------|----------|------|
| 01 | Password Reset | Account Management | Tier 1 |
| 02 | Account Lockout | Account Management | Tier 1 |
| 03 | New User Onboarding | Account Management | Tier 1 |
| 04 | Software Installation | Desktop Support | Tier 1 |
| 05 | Network Troubleshooting (TCP/IP, DNS, DHCP, VPN) | Networking | Tier 1/2 |
| 06 | Email Configuration (POP, IMAP, Exchange, Outlook) | Email Support | Tier 1 |
| 07 | VPN Access Request | Remote Access | Tier 1 |
| 08 | Network Printer Setup & Troubleshooting | Hardware Support | Tier 1 |
| 09 | Mac OS Basic Troubleshooting | Desktop Support | Tier 1 |

---

## KEY SKILLS DEMONSTRATED

- Working support tickets independently with accurate status updates
- Triage and priority management across concurrent tickets
- Escalation procedures with clear criteria for Tier 1 to Tier 2 handoff
- SLA response and resolution time management
- Documentation standards used in real MSP environments
- Communication templates for keeping users informed during resolution
- After-hours support rotation guidelines

---

## REAL-WORLD APPLICATION

This documentation system is built to reflect MSP operations across multiple customer networks. Each runbook includes:

- Exact resolution steps with command line references
- Escalation triggers with specific conditions
- Ticket closure checklists
- User communication language
- Common failure points and their fixes

---

## LESSONS LEARNED

### TRIAGE IS A SKILL, NOT A CHECKLIST
The hardest part of building this system was defining what makes a ticket P1 versus P2. Getting that wrong in either direction has consequences — pull a technician off something critical or leave a blocked user waiting too long. Good triage requires clear criteria written down before the ticket arrives, not decisions made in the moment.

### DOCUMENTATION IS WRITTEN FOR THE WORST DAY
Every runbook was written assuming the technician is on their eighth ticket of the day with two more waiting. Vague steps like "check the network" were replaced with specific commands. Escalation triggers went from "if it gets complicated" to exact conditions tied to specific steps.

### ESCALATION IS NOT FAILURE
Knowing when to stop and escalate is one of the most valuable Tier 1 skills. This project taught me to treat escalation as a decision with predefined criteria — not an admission of defeat. When the criteria are met, escalation happens immediately.

### SLA IS A PROMISE, NOT A TARGET
Every open ticket is a running clock. Priority determines how fast that clock runs. Managing a queue means knowing which clocks are about to expire and acting before they do — not working tickets in arrival order.

### COMMUNICATION IS HALF THE JOB
Every runbook includes a user communication step because a user who does not know what is happening will submit another ticket, call the helpdesk, or walk over to IT — all of which interrupt the technician fixing the original issue. A 30-second update prevents all of that.

---

## RELATED PROJECTS

- [AWS Config + Lambda Security Remediation](https://github.com/agizopacifique-beep/AWS-Config-Lambda-Security-Remediation)
- [Active Directory & Group Policy Project](https://github.com/agizopacifique-beep/AD-and-Group-Policy-Project)

---

*Built by Pacifique Agizo | CompTIA Security+ Certified | B.S. Cybersecurity in Progress*
