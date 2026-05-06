# Runbook: VPN Access Request

**Category:** Network
**Priority:** P2 if user is remote and completely blocked, P3 if in office with a workaround
**Target Resolution:** 10 minutes
**Tier:** 1 (client side) -- Tier 2 (server side)

---

## When This Runbook Applies

- A user needs VPN access set up for the first time
- A user cannot connect to VPN from a remote location
- VPN connection drops repeatedly during a session
- User gets an authentication error when connecting to VPN
- A user's VPN access needs to be revoked

---

## What You Need Before Starting

- Confirmation that the user is authorized for remote access
- IT Manager approval for new VPN access requests
- User's domain credentials
- User's device type (Windows, Mac, mobile)

---

## VPN Authorization

Not every employee automatically gets VPN access. It requires explicit authorization from the IT Manager.

Before setting up VPN for a new user confirm:

- [ ] IT Manager has approved remote access for this user
- [ ] The user has a legitimate business reason for remote access
- [ ] The request is documented in the ticket with approval noted

If approval is missing, do not proceed. Set the ticket to Pending and request approval before continuing.

---

## Procedure

### Step 1: Confirm User Is in the VPN Access Group

VPN access is controlled via AD security group. Check group membership first:

```powershell
Get-ADGroupMember -Identity "VPN-Users" | Where-Object {$_.SamAccountName -eq "jsmith"}
```

If the user is not in the VPN-Users group and IT Manager approval is confirmed:

```powershell
Add-ADGroupMember -Identity "VPN-Users" -Members "jsmith"
```

---

### Step 2: Install the VPN Client

If this is a new setup, the VPN client needs to be installed on the user's device.

**For Windows:**

1. Download the approved VPN client from the IT shared drive:
```
\\DC01\IT-Resources\Software\VPN\
```
2. Run the installer as Administrator
3. Accept default settings unless IT has a specific configuration
4. Complete installation and restart if prompted

**For Mac or mobile:**
- Direct the user to the approved client from the App Store or vendor website
- Provide the VPN server address and connection profile

---

### Step 3: Configure the VPN Connection

**Server address:** `vpn.techbridge.local`
**Connection type:** SSL/TLS
**Authentication:** Domain credentials (TECHBRIDGE\username)

Walk the user through the configuration:

1. Open the VPN client
2. Click **Add Connection** or **New Profile**
3. Enter server address: `vpn.techbridge.local`
4. Set authentication to **Username and Password**
5. Save the profile

---

### Step 4: Test the Connection

Have the user connect using their domain credentials:

- Username: `TECHBRIDGE\jsmith`
- Password: their current domain password

**Successful connection confirms:**
- VPN client connects without error
- User can ping DC01: `ping 192.168.56.10`
- User can access shared drives: `\\DC01\SharedFolder`
- User can reach internal resources they need for their role

Do not close the ticket until the user confirms the resources they need are accessible -- not just that the VPN connected.

---

### Step 5: Troubleshoot Connection Issues

**Authentication failure:**

```powershell
# Confirm account is active and not locked
Get-ADUser -Identity jsmith -Properties LockedOut, Enabled |
Select-Object Name, LockedOut, Enabled
```

If account is locked, follow the account lockout runbook before retrying.

**Connection timeout:**

- Confirm the VPN server is reachable from the user's network
- Ask the user to try from a different network (phone hotspot) to rule out ISP blocking
- Check if port 443 or 1194 is blocked by the user's home firewall or ISP

**Connected but cannot reach internal resources:**

```powershell
# Test from user's machine after VPN connects
ping 192.168.56.10
nslookup techbridge.local
```

If DNS resolution fails after connecting, the VPN split tunneling configuration may need adjustment -- escalate to Tier 2.

---

### Step 6: Revoke VPN Access

When an employee leaves or no longer needs remote access:

```powershell
Remove-ADGroupMember -Identity "VPN-Users" -Members "jsmith" -Confirm:$false
```

VPN access revocation is part of the standard offboarding process. Confirm it is completed alongside account disable during any offboarding ticket.

---

### Step 7: Close the Ticket

**For new setup:**
```
VPN access configured for [username] on [device type].
IT Manager approval confirmed: [ticket or email reference]
User added to VPN-Users group at [time].
VPN client installed and configured.
Connection tested successfully -- user confirmed access to [specific resources].
Setup completed at [time].
```

**For troubleshooting:**
```
VPN connection issue reported by [username].
Root cause: [description]
Fix applied: [what was done]
Connection confirmed working at [time].
```

---

## Common Issues

| Issue | Likely Cause | Fix |
|---|---|---|
| Authentication failed | Wrong credentials or account locked | Verify credentials, check account status in AD |
| Cannot reach VPN server | Port blocked by ISP or home firewall | Test on different network, check port 443 |
| Connected but no internal access | DNS not resolving internal addresses | Check VPN DNS settings -- escalate to Tier 2 |
| VPN drops repeatedly | Unstable internet connection or MTU issue | Test on wired connection, escalate if persists |
| Client will not install | Insufficient permissions or OS incompatibility | Run as Administrator, verify OS version |

---

## Escalation Triggers

Escalate to Tier 2 if:

- VPN server configuration needs to be changed
- Split tunneling or routing needs adjustment
- Multiple users cannot connect simultaneously
- Port forwarding or firewall rules need to be modified on the server side
- VPN client connects but no internal resources are reachable after DNS check

---

## Related

- `runbooks/network-troubleshooting.md`
- `runbooks/account-lockout.md`
- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
