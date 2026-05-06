# Runbook: Password Reset

**Category:** Account Access
**Priority:** P2 if user is completely locked out, P3 if they have a workaround
**Target Resolution:** 5 minutes
**Tier:** 1

---

## When This Runbook Applies

- User cannot log into their domain account
- User has forgotten their password
- User's password has expired and they cannot change it
- User is prompted for a password change but the new password is rejected

This runbook does not cover account lockouts. If the user's account is locked after too many failed attempts use `runbooks/account-lockout.md` instead. Check the account status in AD before starting.

---

## What You Need Before Starting

- Domain Administrator or Help Desk credentials
- User's full name and SamAccountName (e.g. `jsmith`)
- Verified user identity -- two factors required before any action is taken

---

## Identity Verification

This step is not optional. A password reset is one of the most common social engineering attacks. Someone calls pretending to be an employee and walks away with access to a legitimate account.

Verify two of the following before touching anything in AD:

- Employee ID number
- Manager's full name
- Department and job title
- Date of birth on file with HR

If the person cannot provide two items, do not proceed. Tell them you will need to verify their identity through HR and call them back at their registered extension. Document the attempt in the ticket.

Email-only reset requests are never processed. The user must call, submit a ticket themselves, or come in person.

---

## Procedure

### Step 1: Check Account Status in AD

Before resetting anything, confirm what you are dealing with:

```powershell
Get-ADUser -Identity jsmith -Properties LockedOut, PasswordExpired, Enabled
```

| Result | Action |
|---|---|
| LockedOut: True | Use account-lockout runbook first, then reset if needed |
| PasswordExpired: True | Reset password and ensure ChangePasswordAtLogon is set |
| Enabled: False | Do not reset -- account is disabled. Check with IT Manager before re-enabling |

---

### Step 2: Reset the Password

Via PowerShell:

```powershell
Set-ADAccountPassword -Identity "jsmith" `
    -Reset `
    -NewPassword (ConvertTo-SecureString "TempReset@2026!" -AsPlainText -Force)

Set-ADUser -Identity "jsmith" -ChangePasswordAtLogon $true
```

Via AD Users and Computers:

1. Locate the account in its departmental OU
2. Right-click the account and select **Reset Password**
3. Enter temporary password: `TempReset@2026!`
4. Check **User must change password at next logon**
5. Click **OK**

---

### Step 3: Unlock Account if Needed

Run this regardless -- a locked account will reject the new password even after a reset:

```powershell
Unlock-ADAccount -Identity "jsmith"
```

---

### Step 4: Confirm the Reset Worked

```powershell
Get-ADUser -Identity jsmith -Properties LockedOut, PasswordExpired, BadLogonCount |
Select-Object Name, LockedOut, PasswordExpired, BadLogonCount
```

All three should show clean values before you call the user.

---

### Step 5: Deliver the Temporary Password

Phone only. Read it aloud. Do not type it into the ticket, an email, or a chat message.

Tell the user:
- Their temporary password
- That they will be prompted to change it immediately at login
- That the new password must be at least 12 characters with uppercase, lowercase, number, and symbol
- That IT is available if they hit any issues during the change

Confirm they can log in successfully before ending the call.

---

### Step 6: Close the Ticket

Update the ticket with:

```
Password reset completed for [username] at [time].
Identity verified via [verification method 1] and [verification method 2].
Temporary password delivered by phone.
User confirmed successful login at [time].
```

Change ticket status to Resolved. Move to Closed once user confirms or after 24 hours with no response.

---

## Escalation Triggers

Escalate to Tier 2 if any of the following are true:

- Account shows Enabled: False and you do not know why
- Password reset completes without error but user still cannot log in
- User reports being locked out repeatedly throughout the day -- possible brute force
- User suspects someone else accessed their account -- treat as security incident

---

## Security Notes

Every password reset is logged automatically via the Audit Policy GPO. Event ID 4723 appears in the Security log on the Domain Controller with the timestamp and the admin account that performed the reset. If a reset is later disputed, that log is the record.

Temporary passwords must be changed at first login. The domain enforces a 10-password history and a 1-day minimum age -- users cannot cycle back to their previous password immediately.

---

## Related

- `runbooks/account-lockout.md`
- `sla-policies/sla-definitions.md`
- `ticket-workflows/escalation-matrix.md`
