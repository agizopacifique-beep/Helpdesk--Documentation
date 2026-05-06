## What This Project Taught Me About IT Support

Most people think help desk is about knowing the answers. It is not. It is about having a system that works when you do not know the answer -- a process that gets you to the right solution without making the user wait while you figure it out.

That shift in thinking is what building this documentation forced me to confront.

---

### Triage Is a Skill, Not a Checklist

The hardest part of writing the ticket triage workflow was not the steps -- it was deciding what makes something P1 versus P2. A server down is obvious. But what about one user who cannot access a shared folder that three other people in the same department can access fine? Is that P2 or P3?

Getting that wrong in either direction has consequences. Call it P1 and you pull a technician off something more critical. Call it P4 and a person sits blocked for hours waiting for a response that should have come in 30 minutes.

Triage is a judgment call that has to be made in under two minutes on every ticket. Building the escalation matrix taught me that good triage comes from having clear criteria written down before the ticket arrives -- not from deciding in the moment.

---

### Documentation Is Written for the Worst Day, Not the Best One

Every runbook in this repo was written assuming the technician reading it is handling their eighth ticket of the day, has two more waiting, and has never seen this specific error before.

That constraint changed everything about how I wrote them. Steps like "check the network" got replaced with specific commands. "Contact the user" got replaced with exact language to use. Escalation triggers went from vague ("if it gets complicated") to specific ("if the issue persists after completing Step 4 and the user is completely unable to work").

Vague documentation looks professional until someone needs to use it under pressure. Then it is useless.

---

### Escalation Is Not Failure

One of the most important things a Tier 1 technician can demonstrate is knowing when to stop. Trying to solve a problem that belongs at Tier 2 wastes time, frustrates the user, and can make the underlying issue worse.

Building the escalation matrix taught me to think about escalation as a decision with criteria, not an admission of defeat. The criteria are defined before the ticket arrives. When they are met, escalation happens immediately -- not after 45 minutes of trying things that are not working.

That is the professional response. Employers want technicians who know their lane.

---

### SLA Is a Promise, Not a Target

Response and resolution times in this documentation are not aspirational. They are commitments. A P2 ticket that gets a first response in 35 minutes instead of 30 is a breach -- even if the issue gets resolved perfectly.

Understanding SLA changed how I think about ticket management. Every open ticket is a running clock. Priority determines how fast that clock runs. Managing a queue means knowing which clocks are about to run out and acting before they do -- not just working through tickets in the order they arrived.

---

### Communication Is Half the Job

Every runbook in this repo includes a step for communicating with the user. Not because it needed to be there for completeness, but because I kept realizing while writing that the technical steps alone were not enough.

A user who does not know what is happening with their ticket will submit another one, call the helpdesk, or walk over to IT -- all of which interrupt the technician trying to fix the original issue. A 30-second update message prevents all of that.

The best technical fix still produces a bad experience if the user is left in the dark. That is the part of help desk work that does not show up in certifications but shows up immediately on the job.

---

### What Employers Are Actually Looking For

After building this I understand why help desk job descriptions ask for "strong communication skills" and "ability to prioritize in a fast-paced environment." Those are not filler phrases.

They describe the two things that separate someone who can fix computers from someone who can run a support operation:

- Can you tell a non-technical person what is happening in plain language while you are working on it?
- Can you look at five open tickets and immediately know which one needs your attention first?

This project was built to demonstrate both.
