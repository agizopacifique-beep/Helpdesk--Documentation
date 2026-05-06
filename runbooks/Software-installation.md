# Runbook: Software Installation

**Category:** Software
**Priority:** P2 if user cannot work without it, P3 if it is a new request
**Target Resolution:** 15 minutes
**Tier:** 1

---

## When This Runbook Applies

- User requests installation of a new application
- An existing application needs to be reinstalled after a fault
- A software update is failing and needs manual intervention
- A licensed application needs to be activated on a new workstation

---

## What You Need Before Starting

- Local Administrator or Domain Administrator credentials on the target workstation
- Confirmation that the software is on the approved application list
- Valid license or license key if required
- User's workstation name or IP address

---

## Approved Application List

Only software on this list can be installed without IT Manager approval. Anything not on this list requires written approval before installation begins.

| Application | Use Case | License Required |
|---|---|---|
| Microsoft Office 365 | Productivity suite | Yes -- assigned per user |
| Google Chrome | Web browser | No |
| Mozilla Firefox | Web browser | No |
| Adobe Acrobat Reader | PDF viewing | No |
| 7-Zip | File compression | No |
| VLC Media Player | Media playback | No |
| Zoom | Video conferencing | Yes -- assigned per user |
| Slack | Team communication | Yes -- assigned per user |
| Notepad++ | Text editing | No |
| TeamViewer | Remote support | IT use only |

If the user requests software not on this list, do not install it. Create a request for IT Manager approval and set the ticket to Pending. Notify the user that approval is needed before you can proceed.

---

## Procedure

### Step 1: Verify the Request Is Approved

Check the ticket for IT Manager approval if the software is not on the approved list. If approval is missing, stop here and request it before continuing.

For approved list software, proceed immediately.

---

### Step 2: Check if Software Is Already Installed

Remote into the workstation and check before downloading anything:

```powershell
Get-WmiObject -Class Win32_Product | Where-Object {$_.Name -like "*Chrome*"} |
Select-Object Name, Version
```

Or check via Settings > Apps on the workstation. If the software is already installed but not working, this becomes a software fault ticket -- not an installation.

---

### Step 3: Check Available Disk Space

```powershell
Get-PSDrive C | Select-Object Used, Free
```

Minimum free space required before any installation:

| Installation Type | Minimum Free Space |
|---|---|
| Standard application | 2 GB |
| Microsoft Office | 10 GB |
| Development tools | 20 GB |

If disk space is insufficient, notify the user and IT Manager before proceeding. Do not attempt installation on a full disk.

---

### Step 4: Download or Locate the Installer

For approved list software:
- Use the official vendor website only
- Never download from third-party sites
- Confirm the file hash matches the official release if available

For licensed software:
- Retrieve the installer from the IT shared drive: `\\DC01\IT-Resources\Software\`
- Confirm the license key is available before starting

---

### Step 5: Install the Software

1. Remote into the workstation via RDP:
```
mstsc /v:[workstation IP or name]
```

2. Run the installer as Administrator:
   - Right-click the installer
   - Select **Run as Administrator**

3. Follow the installation wizard:
   - Accept license terms
   - Use default installation path unless IT has a standard otherwise
   - Deselect any bundled software or browser toolbars

4. Enter license key when prompted if required

5. Complete installation and restart if prompted

---

### Step 6: Verify the Installation

After installation:

1. Launch the application and confirm it opens without errors
2. If licensed, confirm activation status within the application
3. Confirm the application appears in Settings > Apps
4. Run a basic function test -- open a file, connect to a service, or perform a core task

Do not close the ticket until you have confirmed the application works for the user's intended purpose -- not just that it installed without errors.

---

### Step 7: Update the Ticket and Close

```
Software installed: [application name and version]
Workstation: [computer name]
Installation method: [standard installer / IT shared drive]
License: [activated / not required]
Verified: [what was tested and confirmed working]
Installation completed at [time].
```

---

## Common Installation Issues

| Issue | Likely Cause | Fix |
|---|---|---|
| Installer blocked by Software Restriction GPO | HR or Finance workstation -- GPO blocks unauthorized executables | Install via IT-Admins account or request GPO exception |
| Installation fails with permissions error | Not running as Administrator | Right-click > Run as Administrator |
| License activation fails | Wrong key or activation server unreachable | Verify key with IT Manager, check network connectivity |
| Disk space error during installation | Insufficient free space | Clear disk space before retrying |
| Application crashes immediately after install | Corrupt installer or missing dependency | Redownload installer from official source and retry |

---

## Software Restriction GPO Note

HR and Finance workstations have a Software Restriction GPO that blocks unauthorized executables. If you need to install approved software on one of these machines, either:

1. Log in as a Domain Administrator to run the installer -- the GPO applies to standard users not admins
2. Request a temporary GPO exception from Tier 2 for the specific application path

Do not disable the GPO entirely to perform an installation.

---

## Escalation Triggers

Escalate to Tier 2 if:

- Installation repeatedly fails with no clear cause after following this runbook
- Software requires changes to firewall rules or server-side configuration
- A Group Policy change is needed to allow the software to run
- The application requires SQL Server, IIS, or other server-side components
- Installation affects multiple workstations simultaneously

---

## Related

- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
