# Runbook: Network Troubleshooting

**Category:** Network
**Priority:** P1 if all users affected, P2 if one user completely blocked, P3 if degraded
**Target Resolution:** 20 minutes
**Tier:** 1 (workstation level) -- Tier 2 (infrastructure level)

---

## When This Runbook Applies

- User reports no internet access
- User cannot reach shared drives or domain resources
- User reports slow or intermittent connectivity
- VPN connection failing
- User cannot reach a specific application or website

---

## What You Need Before Starting

- User's workstation name and IP address
- Access to DC01 for DNS and domain verification
- Remote Desktop access to the affected workstation

---

## The Diagnostic Approach

Network issues look different depending on where the break is. The fastest way to find it is to work from the workstation outward -- confirm what the machine can reach and where it stops. Each test either confirms a layer is working or points to where the problem is.

```
Workstation NIC
      ↓
IP Configuration (DHCP or static)
      ↓
Default Gateway (router/switch)
      ↓
DNS Resolution (DC01)
      ↓
Domain Connectivity (techbridge.local)
      ↓
Internet Access
      ↓
Specific Application or Resource
```

Start at the top and work down. Stop when you find where connectivity breaks.

---

## Procedure

### Step 1: Check the Physical Connection

Before running any commands, confirm the basics:

- Is the network cable plugged in on both ends?
- Does the network adapter show a link light?
- If on Wi-Fi -- is the user connected to the correct network?

Ask the user these questions before remoting in. Half of "no internet" tickets are a disconnected cable.

---

### Step 2: Check IP Configuration

Remote into the workstation:

```
mstsc /v:[workstation name or IP]
```

Run ipconfig and check the output:

```powershell
ipconfig /all
```

| What You See | What It Means |
|---|---|
| 169.254.x.x (APIPA) | DHCP failed -- workstation could not get an IP address |
| 192.168.56.x | Correct subnet -- IP assigned properly |
| No default gateway | Cannot reach anything outside the local network |
| DNS server not 192.168.56.10 | DNS not pointing to DC01 -- domain resources will fail |

If IP is APIPA:

```powershell
ipconfig /release
ipconfig /renew
```

If IP does not renew, the issue is at the DHCP server or network infrastructure -- escalate to Tier 2.

---

### Step 3: Ping the Default Gateway

```powershell
ping 192.168.56.1
```

| Result | Meaning |
|---|---|
| Reply received | Workstation can reach the router -- local network is working |
| Request timed out | Cannot reach the gateway -- switch, cable, or NIC issue |

If gateway ping fails and cable is connected, try a different port on the switch or a different cable before escalating.

---

### Step 4: Ping DC01

```powershell
ping 192.168.56.10
```

| Result | Meaning |
|---|---|
| Reply received | Can reach the Domain Controller |
| Request timed out | DC01 unreachable -- check firewall rules or DC01 status |

If DC01 is unreachable but the gateway is reachable, check whether DC01 is online and whether the firewall rule for the AD-Lab network is active.

---

### Step 5: Test DNS Resolution

```powershell
nslookup techbridge.local
```

| Result | Meaning |
|---|---|
| Returns DC01 IP | DNS is working correctly |
| Non-existent domain | DNS is not resolving -- check DNS server setting in ipconfig |
| Timeout | DNS server unreachable |

If DNS is failing, verify the workstation's DNS server is set to `192.168.56.10`:

```powershell
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.56.10
```

---

### Step 6: Test Domain Connectivity

```powershell
nltest /sc_verify:techbridge.local
```

A successful result confirms the workstation has a valid secure channel to the domain. If this fails:

```powershell
Test-ComputerSecureChannel -Repair
```

If the secure channel cannot be repaired, the workstation may need to be rejoined to the domain -- escalate to Tier 2.

---

### Step 7: Test Internet Access

```powershell
ping 8.8.8.8
```

| Result | Meaning |
|---|---|
| Reply received | Internet connectivity is working at IP level |
| Request timed out | No internet -- issue is at router or ISP level |

If IP-level internet works but websites do not load, DNS is resolving to the wrong server for external addresses. Check the DNS configuration.

If ping to 8.8.8.8 fails but the gateway responds, the issue is upstream from the router -- contact the ISP or escalate to Tier 2.

---

### Step 8: Test Specific Resource Access

If the user can reach the internet but not a specific application or shared drive:

```powershell
# Test shared drive access
Test-Path "\\DC01\SharedFolder"

# Test specific port connectivity
Test-NetConnection -ComputerName DC01 -Port 445
```

| Port | Service |
|---|---|
| 445 | SMB -- shared folders |
| 389 | LDAP -- Active Directory |
| 3389 | RDP -- Remote Desktop |
| 443 | HTTPS |
| 80 | HTTP |

If a specific port is blocked, the issue is firewall rules -- escalate to Tier 2.

---

### Step 9: Flush DNS Cache and Reset Network Stack

If connectivity is intermittent or partially working, clear the cache and reset:

```powershell
ipconfig /flushdns
netsh winsock reset
netsh int ip reset
```

Restart the workstation after running these commands. Confirm connectivity after reboot before closing the ticket.

---

### Step 10: Update and Close the Ticket

```
Network issue reported by [user] on [workstation].

Diagnostic steps taken:
- IP configuration: [result]
- Gateway ping: [result]
- DC01 ping: [result]
- DNS resolution: [result]
- Domain secure channel: [result]
- Internet connectivity: [result]

Root cause identified: [description]
Fix applied: [what was done]
Connectivity confirmed restored at [time].
```

---

## Common Scenarios and Fixes

| Symptom | Likely Cause | Fix |
|---|---|---|
| APIPA address (169.254.x.x) | DHCP failure | ipconfig /release /renew -- escalate if fails |
| Can ping gateway, cannot reach domain | DNS not pointing to DC01 | Update DNS to 192.168.56.10 |
| Can reach internet, cannot reach shared drives | SMB port blocked or secure channel broken | Test-NetConnection port 445, repair secure channel |
| Intermittent drops | Faulty cable or NIC | Replace cable, test on different switch port |
| All users affected simultaneously | Switch, router, or DC01 down | Escalate to Tier 2 immediately -- P1 |
| VPN not connecting | VPN client config or server issue | Use VPN runbook -- escalate if server side |

---

## Escalation Triggers

Escalate to Tier 2 immediately if:

- Multiple users are affected simultaneously
- Gateway or DC01 is unreachable from multiple workstations
- IP renewal fails after ipconfig /release /renew
- Secure channel repair fails
- The issue is at the switch, router, or firewall level
- ISP-level outage is suspected

---

## Related

- `runbooks/vpn-access-request.md`
- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
