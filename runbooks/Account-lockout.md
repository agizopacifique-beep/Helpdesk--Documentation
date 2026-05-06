# Runbook: Account Lockout

**Category:** Account Access
**Priority:** P2 if user cannot work at all, P3 if they have another way in
**Target Resolution:** 3 minutes
**Tier:** 1

---

## When This Runbook Applies

- User receives "Your account has been locked" message at login
- User reports being unable to log in despite using the correct password
- User was locked out after multiple failed attempts
- Account locked out repeatedly throughout the day

The fastest ticket type on the board. Three minutes start to finish if everything is straightforward. The part that takes the most time is figuring out why the lockout keeps happening -- which matters more than the unlock itself.

---

## What You Need Before Starting

- Domain Administrator or Help Desk credentials
- User's SamAccountName
- Basic identity confirmation -- one factor is sufficient for a lockout unless the account shows signs of compromise

---

## Procedure

### Step 1: Confirm the Account Is Actually Locked

Do not assume. Confirm before touching anything:

```powershell
Get-ADUser -Identity jsmith -Properties LockedOut, BadLogonCount, LastBadPasswordAttempt |
Select-Object Name, LockedOut, BadLogonCount, LastBadPasswordAttempt
```

| Result | What It Means |
|---|---|
| LockedOut: True | Account is locked -- proceed with unlock |
| LockedOut: False | Account is not locked -- check password or other issue |
| BadLogonCount high | Multiple failed attempts -- investigate before unlocking |
| LastBadPasswordAttempt recent | Recent failed attempts -- ask user what happened |

If LockedOut is False and the user still cannot log in, switch to the password reset runbook.

---

### Step 2: Check How Many Times This Has Happened

A single lockout is usually a forgotten password or a cached credential on an old device. Repeated lockouts throughout the day are a different problem -- either a device is hammering AD with wrong credentials or someone is trying to brute force the account.

```powershell
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LastLogonDate
```

If this user appears in repeated lockout reports, flag for investigation before unlocking.

---

### Step 3: Unlock the Account

```powershell
Unlock-ADAccount -Identity "jsmith"
```

Via AD Users and Computers:

1. Locate the account in its departmental OU
2. Right-click the account and select **Properties**
3. Click the **Account** tab
4. Check **Unlock account**
5. Click **Apply > OK**

---

### Step 4: Find the Lockout Source

This step separates a technician who fixes the symptom from one who fixes the problem. If you unlock the account without finding the source, it will lock again.

Common lockout sources:

| Source | What Causes It | How to Fix It |
|---|---|---|
| Old password saved on phone | Mobile device authenticating with expired credentials | Update saved password on the device |
| Mapped network drive | Drive mapped with old credentials | Remap drive with current credentials |
| Scheduled task | Task running under the user's account with old password | Update task credentials |
| Another workstation | User left themselves logged in somewhere else | Log out the other session |
| Browser saved password | Browser attempting auto-login with old password | Clear saved credentials |

Ask the user:

- Did you recently change your password?
- Do you have any devices connected to your work account -- phone, tablet, home computer?
- Do you have any mapped drives or saved network connections?

Most lockouts are solved by clearing a saved credential on a device the user forgot about.

---

### Step 5: Confirm the Unlock Worked

```powershell
Get-ADUser -Identity jsmith -Properties LockedOut, BadLogonCount |
Select-Object Name, LockedOut, BadLogonCount
```

LockedOut should show False and BadLogonCount should show 0 before you tell the user to try again.

---

### Step 6: Update and Close the Ticket

```
Account lockout confirmed for [username].
BadLogonCount: [number] -- LastBadPasswordAttempt: [time]
Lockout source identified as: [source or "under investigation"]
Account unlocked at [time].
User confirmed successful login at [time].
```

If the lockout source was not identified, note what was checked and set a follow-up to check whether the account locks again within 24 hours.

---

## Escalation Triggers

Escalate to Tier 2 if any of the following are true:

- Account locks out again within 1 hour of being unlocked
- BadLogonCount is unusually high with no explanation from the user
- LastBadPasswordAttempt timestamps show attempts outside business hours
- User did not initiate the login attempts that caused the lockout
- Multiple accounts locking out simultaneously -- possible network attack

Repeated or unexplained lockouts are treated as a potential security incident until proven otherwise.

---

## Security Notes

Account lockouts are logged via the Audit Policy GPO. Event ID 4740 in the Security log shows the account that locked out, the source machine that triggered it, and the timestamp. If the source machine is unexpected -- a server, a machine the user does not use, or an external IP -- escalate immediately.

---

## Related

- `runbooks/password-reset.md`
- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
