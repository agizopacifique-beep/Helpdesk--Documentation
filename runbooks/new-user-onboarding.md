# Runbook: New User Onboarding

**Category:** New Request
**Priority:** P2 if start date is today, P3 if start date is future
**Target Resolution:** 8 minutes
**Tier:** 1

---

## When This Runbook Applies

- New employee is joining TechBridge Solutions
- HR has submitted an onboarding request with employee details
- A contractor or temporary staff member needs domain access

This runbook covers account creation, workstation setup, and first-day access confirmation. It does not cover hardware procurement -- if a new workstation is needed that goes through a separate hardware request.

---

## What You Need Before Starting

- Domain Administrator credentials
- Employee details from HR:
  - First name and last name
  - Department (IT, HR, or Finance)
  - Start date
  - Manager name
- Confirmation that request arrived at least 24 hours before start date

If the request arrives same day, treat as P2 and start immediately. Flag the late submission to the IT Manager after the account is created -- not before.

---

## Pre-Creation Checklist

Before creating anything in AD:

- [ ] Confirm department is one of the three supported: IT, HR, Finance
- [ ] Check for duplicate username:
```powershell
Get-ADUser -Filter {SamAccountName -eq "jsmith"}
```
- [ ] Confirm start date with HR if any doubt
- [ ] Confirm a workstation is available and domain joined

If a duplicate username exists, add a number to differentiate (e.g. `jsmith2`) and note it in the ticket.

---

## Procedure

### Step 1: Create the AD Account

Run the onboarding script:

```powershell
.\New-EmployeeOnboarding.ps1 -FirstName "John" -LastName "Smith" -Department "IT"
```

The script creates the account in the correct OU, sets the temporary password, assigns the department security group, adds the user to All-Users, and forces a password change at first login.

If running manually:

1. Open **Active Directory Users and Computers**
2. Navigate to the correct department OU under TechBridge Users
3. Right-click the OU and select **New > User**
4. Fill in:
   - First name, last name
   - User logon name: first initial + last name (e.g. `jsmith`)
5. Click **Next**
6. Set temporary password: `Welcome@2026!`
7. Check **User must change password at next logon**
8. Click **Finish**

---

### Step 2: Assign Security Groups

Confirm the user is in the correct groups:

```powershell
Get-ADUser -Identity jsmith -Properties MemberOf |
Select-Object -ExpandProperty MemberOf
```

Expected groups:

| Department | Required Groups |
|---|---|
| IT | IT-Admins, All-Users |
| HR | HR-Staff, All-Users |
| Finance | Finance-Staff, All-Users |

If a group is missing add it:

```powershell
Add-ADGroupMember -Identity "HR-Staff" -Members "jsmith"
Add-ADGroupMember -Identity "All-Users" -Members "jsmith"
```

---

### Step 3: Prepare the Workstation

1. Confirm the assigned workstation is domain joined:
   - Check the Workstations OU in AD for the computer account
   - If not joined follow the domain join process before continuing

2. Confirm the Managed By attribute is set to the new user:
   - Open the computer account in AD
   - Click **Managed By** tab
   - Set to the new employee's account

3. Log into the workstation as Administrator and confirm:
   - GPOs are applied: `gpupdate /force` then `gpresult /r`
   - TechBridge wallpaper is showing
   - Network drives are accessible

---

### Step 4: Test the New Account

Log into the workstation with the new user's credentials to confirm everything works before the employee arrives:

1. Log out of the Administrator session
2. Log in as `TECHBRIDGE\jsmith` with `Welcome@2026!`
3. Confirm the password change prompt appears
4. Change to a test password to verify the policy accepts it
5. Confirm the desktop loads with the TechBridge wallpaper
6. Confirm network access and shared folder permissions match the department
7. Log out

If anything fails at this step resolve it before the employee starts. Finding an access problem during onboarding testing is a 5-minute fix. Finding it when the employee sits down on day one is a bad first impression.

---

### Step 5: Prepare the Welcome Information

Write down the following to hand to the new employee:

```
Welcome to TechBridge Solutions

Your domain username: jsmith
Temporary password:   Welcome@2026!

You will be asked to change your password at first login.
Your new password must be at least 12 characters and include
uppercase, lowercase, a number, and a symbol.

IT Helpdesk contact: helpdesk@techbridge.local
Helpdesk phone: [extension]
```

Hand this in person or read it over the phone. Do not email credentials.

---

### Step 6: Close the Ticket

```
New user account created for [Full Name] ([username]).
Department: [department] -- OU: [OU path]
Groups assigned: [list groups]
Workstation: [computer name] -- domain joined and GPO verified
Account tested successfully before employee start date.
Credentials handed off [in person / by phone] on [date].
```

---

## Escalation Triggers

Escalate to Tier 2 if:

- Department requires access beyond the standard three groups
- Custom permissions are needed on shared folders or servers
- The workstation needs to be domain joined and you cannot reach DC01
- The new employee needs a service account or admin rights beyond IT-Admins

---

## Related

- `runbooks/password-reset.md`
- `sla-policies/sla-definitions.md`
- `ticket-workflows/escalation-matrix.md`
- AD and Group Policy Project: `scripts/New-EmployeeOnboarding.ps1`
