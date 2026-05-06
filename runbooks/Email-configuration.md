# Runbook: Email Configuration

**Category:** Email
**Priority:** P2 if user cannot send or receive at all, P3 if partially working
**Target Resolution:** 10 minutes
**Tier:** 1 (client side) -- Tier 2 (server side)

---

## When This Runbook Applies

- New employee needs email configured on their workstation
- User cannot send or receive email
- Email client is not connecting to the mail server
- User is getting certificate errors in their email client
- Email is going to junk or not arriving at all
- User needs email configured on a mobile device

---

## What You Need Before Starting

- User's domain credentials
- Email address format: `firstname.lastname@techbridge.local`
- Mail server details:
  - Incoming (IMAP): `mail.techbridge.local` -- Port 993 (SSL)
  - Outgoing (SMTP): `mail.techbridge.local` -- Port 587 (TLS)
- Access to the user's workstation via RDP

---

## Email Client Reference

TechBridge Solutions uses Microsoft Outlook as the standard email client. All configuration in this runbook is based on Outlook. If a user is on a non-standard client, follow the same server settings and adjust the interface steps accordingly.

---

## Procedure

### Step 1: Confirm the Account Exists

Before configuring anything on the client side, confirm the mailbox exists:

```powershell
Get-ADUser -Identity jsmith -Properties EmailAddress |
Select-Object Name, EmailAddress, Enabled
```

If the email address field is blank, the mailbox has not been created. This is a Tier 2 task -- escalate before continuing.

If the account shows Enabled: False, the user is disabled in AD. Do not configure email for a disabled account without IT Manager approval.

---

### Step 2: Check Existing Email Configuration

Remote into the workstation:

```
mstsc /v:[workstation name]
```

Open Outlook and check:

1. Click **File > Account Settings > Account Settings**
2. Check if an account already exists
3. If an account exists but is not working, note the server settings before making changes

If no account exists, proceed with fresh configuration.

---

### Step 3: Configure Outlook

**For a new profile:**

1. Close Outlook if open
2. Open **Control Panel > Mail > Show Profiles**
3. Click **Add** and name the profile (use the employee's full name)
4. Select **Set up Outlook manually**
5. Choose **IMAP/POP** (not Exchange unless specifically configured)
6. Fill in:

| Field | Value |
|---|---|
| Your Name | Employee full name |
| Email Address | firstname.lastname@techbridge.local |
| Account Type | IMAP |
| Incoming Mail Server | mail.techbridge.local |
| Incoming Port | 993 |
| Incoming Encryption | SSL/TLS |
| Outgoing Mail Server | mail.techbridge.local |
| Outgoing Port | 587 |
| Outgoing Encryption | STARTTLS |
| Username | TECHBRIDGE\jsmith |
| Password | User's domain password |

7. Click **Next** -- Outlook will test the connection
8. If the test passes click **Finish**
9. Set the new profile as default if this is the user's primary workstation

---

### Step 4: Test Send and Receive

After configuration:

1. Send a test email from the user's account to `helpdesk@techbridge.local`
2. Confirm the email arrives in the helpdesk inbox
3. Reply from the helpdesk inbox to the user's account
4. Confirm the user receives the reply

Both directions must work before the ticket closes. An outbox that sends but cannot receive, or an inbox that receives but cannot send, is not a resolved ticket.

---

### Step 5: Configure Mobile Device if Requested

For iOS:

1. Go to **Settings > Mail > Accounts > Add Account**
2. Select **Other > Add Mail Account**
3. Enter name, email, password, and description
4. Select **IMAP** and enter server settings from Step 3
5. Save and confirm mail syncs

For Android:

1. Open the email app and select **Add Account**
2. Choose **Other** or **Manual Setup**
3. Select **IMAP** and enter server settings from Step 3
4. Complete setup and confirm sync

Mobile setup is P4 priority unless the user has no other way to access email.

---

### Step 6: Troubleshoot Connection Issues

**Cannot connect to mail server:**

```powershell
Test-NetConnection -ComputerName mail.techbridge.local -Port 993
Test-NetConnection -ComputerName mail.techbridge.local -Port 587
```

| Result | Action |
|---|---|
| TcpTestSucceeded: True | Port is open -- check credentials |
| TcpTestSucceeded: False | Port is blocked -- firewall or server issue, escalate to Tier 2 |

**Certificate error:**

- Confirm the certificate on the mail server is valid and not expired
- If the certificate is self-signed, import it to the trusted root store on the workstation
- If the certificate is expired -- escalate to Tier 2 immediately

**Authentication failure:**

- Confirm the user's domain password is correct
- Check the account is not locked:
```powershell
Get-ADUser -Identity jsmith -Properties LockedOut | Select-Object LockedOut
```
- Confirm the username format is `TECHBRIDGE\jsmith` not just `jsmith`

**Email going to junk:**

- Check the sender's domain is not on a block list
- Add the sender to the Safe Senders list in Outlook
- If the problem is widespread across multiple users -- escalate to Tier 2 for mail server configuration review

---

### Step 7: Close the Ticket

```
Email configured for [username] on [workstation / mobile device].
Client: [Outlook version / mobile OS]
Server: mail.techbridge.local -- IMAP port 993 / SMTP port 587
Send test: confirmed delivered to helpdesk inbox
Receive test: confirmed reply received by user
Configuration completed at [time].
```

---

## Common Issues

| Issue | Likely Cause | Fix |
|---|---|---|
| Cannot connect to server | Wrong server address or port | Verify settings match this runbook exactly |
| Authentication failure | Wrong username format or locked account | Use TECHBRIDGE\username format, check account status |
| Certificate error | Expired or untrusted certificate | Import certificate or escalate for renewal |
| Outlook not opening | Corrupt profile | Create new Outlook profile |
| Email sends but not received | DNS or mail routing issue | Escalate to Tier 2 |
| Large mailbox slow to sync | Too many emails cached locally | Adjust cached exchange mode settings |

---

## Escalation Triggers

Escalate to Tier 2 if:

- Mailbox does not exist and needs to be created
- Mail server ports are unreachable from the workstation
- Certificate is expired or invalid on the server
- Email routing issues affect multiple users
- User needs a shared mailbox or distribution list configured
- Mail server configuration changes are needed

---

## Related

- `runbooks/new-user-onboarding.md`
- `runbooks/network-troubleshooting.md`
- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
