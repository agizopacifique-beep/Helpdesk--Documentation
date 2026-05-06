# Runbook: Printer Setup

**Category:** Hardware
**Priority:** P3 if another printer is available, P2 if user has no printing option
**Target Resolution:** 10 minutes
**Tier:** 1

---

## When This Runbook Applies

- User cannot print and needs a printer configured
- A new printer needs to be added to a workstation
- A previously working printer has disappeared from the device list
- User is printing to the wrong printer and needs the default changed
- A network printer needs to be connected after a workstation was reimaged

---

## What You Need Before Starting

- Printer name or IP address
- User's workstation name
- Confirmation the printer is powered on and online
- Network access to the printer from the workstation

---

## Printer Inventory

| Printer Name | Location | IP Address | Type |
|---|---|---|---|
| TB-PRINT-IT | IT Office | 192.168.56.50 | Network |
| TB-PRINT-HR | HR Office | 192.168.56.51 | Network |
| TB-PRINT-FIN | Finance Office | 192.168.56.52 | Network |
| TB-PRINT-SHARED | Main Floor | 192.168.56.53 | Network |

Department printers are accessible only to their respective department security groups. The shared printer on the main floor is accessible to all staff.

---

## Procedure

### Step 1: Confirm the Printer Is Online

Before touching the workstation, verify the printer is reachable on the network:

```powershell
ping 192.168.56.51
```

| Result | Action |
|---|---|
| Reply received | Printer is online -- proceed |
| Request timed out | Printer is offline -- check power, cable, and network connection before continuing |

If the printer does not respond to ping, physically check the device before spending time on the workstation side. A printer that is off or disconnected will waste the entire troubleshooting session.

---

### Step 2: Check if the Printer Is Already Installed

Remote into the workstation:

```
mstsc /v:[workstation name]
```

Check existing printers:

```powershell
Get-Printer | Select-Object Name, DriverName, PortName, Default
```

If the printer is listed but not working, this is a printer fault -- skip to the Common Issues section. If it is not listed, continue with installation.

---

### Step 3: Install the Network Printer

**Method 1: Add via IP Address (quickest)**

1. Open **Settings > Devices > Printers and Scanners**
2. Click **"Add a printer or scanner"**
3. Wait for the scan to complete
4. If the printer does not appear click **"The printer that I want isn't listed"**
5. Select **"Add a printer using a TCP/IP address or hostname"**
6. Enter the printer IP address (e.g. `192.168.56.51`)
7. Click **Next** -- Windows will detect the driver automatically
8. Name the printer using the standard naming convention (e.g. `TB-PRINT-HR`)
9. Click **Finish**

**Method 2: Add via PowerShell**

```powershell
Add-Printer -Name "TB-PRINT-HR" -DriverName "Generic / Text Only" -PortName "192.168.56.51"
```

Replace the driver name with the correct driver for the printer model if available.

---

### Step 4: Install the Correct Driver

If Windows does not detect the driver automatically:

1. Go to the printer manufacturer website
2. Download the driver for the correct printer model and Windows version
3. Run the driver installer as Administrator
4. Return to Printers and Scanners and add the printer again -- it should now detect correctly

Driver downloads must come from the official manufacturer website only.

---

### Step 5: Set as Default Printer if Required

```powershell
Set-Printer -Name "TB-PRINT-HR" -Shared $false
(Get-WmiObject -Class Win32_Printer -Filter "Name='TB-PRINT-HR'").SetDefaultPrinter()
```

Or via Settings > Devices > Printers and Scanners:

1. Click the printer name
2. Click **Manage**
3. Click **Set as default**

Only set as default if the user specifically requests it. Do not change defaults without asking.

---

### Step 6: Print a Test Page

Always print a test page before closing the ticket:

```powershell
(Get-WmiObject -Class Win32_Printer -Filter "Name='TB-PRINT-HR'").PrintTestPage()
```

Or via Settings:

1. Click the printer name
2. Click **Manage**
3. Click **Print a test page**

Confirm the page prints correctly before telling the user the setup is complete. A test page that errors or does not print means the setup is not done.

---

### Step 7: Close the Ticket

```
Printer setup completed on [workstation name].
Printer added: [printer name] at [IP address]
Driver: [driver name and version]
Test page: confirmed printed successfully
Default printer: [changed / not changed] per user request
Setup completed at [time].
```

---

## Common Issues

| Issue | Likely Cause | Fix |
|---|---|---|
| Printer not found during scan | Printer offline or wrong subnet | Ping the printer IP first -- check power and cable |
| Driver not found automatically | Printer model not in Windows driver library | Download driver from manufacturer website |
| Print job stuck in queue | Previous failed job blocking the queue | Clear the print spooler |
| Printer shows offline in Windows | Port configuration incorrect | Verify port IP matches printer IP in printer properties |
| Access denied when printing | User not in correct security group | Confirm group membership -- HR printer requires HR-Staff group |
| Test page prints but user documents do not | Application-level print settings issue | Check print settings within the specific application |

---

## Clear Print Spooler

If jobs are stuck in the queue and the printer will not print:

```powershell
Stop-Service -Name Spooler
Remove-Item -Path "C:\Windows\System32\spool\PRINTERS\*" -Force
Start-Service -Name Spooler
```

This clears all pending jobs. Warn the user that any queued print jobs will be lost before running this.

---

## Escalation Triggers

Escalate to Tier 2 if:

- Network printer requires server-side configuration or a shared printer queue on DC01
- Driver installation fails repeatedly with no clear cause
- Printer is accessible to some users but not others despite correct group membership
- A new printer needs to be added to the network and assigned an IP address

---

## Related

- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
