# Runbook: Hardware Fault Reporting

**Category:** Hardware
**Priority:** P2 if user cannot work, P3 if workaround exists
**Target Resolution:** 15 minutes to assess and document -- replacement timeline varies
**Tier:** 1

---

## When This Runbook Applies

- User reports a physical hardware fault -- laptop, desktop, monitor, keyboard, mouse
- A device is not powering on
- Screen is cracked, damaged, or displaying incorrectly
- A peripheral stopped working without an obvious software cause
- A device is making unusual sounds suggesting mechanical failure
- Battery no longer holds charge on a laptop

This runbook covers fault assessment and documentation. Physical repair and replacement timelines depend on warranty status and hardware availability.

---

## What You Need Before Starting

- Device make, model, and serial number
- User's name and department
- Description of the fault from the user
- Warranty status if known

---

## Procedure

### Step 1: Gather Fault Information

Before touching the device, get a clear picture of what happened:

Ask the user:
- When did the fault start?
- Did anything happen before it started -- drop, spill, power outage, update?
- Is the fault constant or intermittent?
- Have they tried restarting the device?

Document everything the user tells you. Their description often contains the most important clue.

---

### Step 2: Identify the Device

Locate the device details:

```powershell
# Run on the affected workstation if it can still boot
Get-WmiObject -Class Win32_ComputerSystem | Select-Object Manufacturer, Model
Get-WmiObject -Class Win32_BIOS | Select-Object SerialNumber
```

Or read the label on the physical device. Every managed device at TechBridge Solutions has an asset tag. Record:

- Asset tag number
- Manufacturer and model
- Serial number
- Assigned user from AD Managed By attribute

---

### Step 3: Assess the Fault

**For devices that will not power on:**

1. Check the power cable and adapter -- try a known working cable
2. Check the power outlet -- try a different socket
3. For laptops -- remove battery, hold power for 30 seconds, reconnect and try
4. If still no power -- hardware fault confirmed, proceed to Step 5

**For display issues:**

1. Check cable connections between monitor and workstation
2. Try a different cable
3. Connect to a different monitor to isolate whether the fault is the monitor or the GPU
4. Update or roll back display drivers:
```powershell
# Check current driver
Get-WmiObject Win32_VideoController | Select-Object Name, DriverVersion
```

**For peripheral faults (keyboard, mouse):**

1. Try a different USB port
2. Test the peripheral on a different workstation
3. If it fails on another workstation -- peripheral is faulty, replace
4. If it works on another workstation -- USB port or driver issue on the original machine

**For unusual sounds:**

- Clicking or grinding from storage -- potential hard drive failure. Back up data immediately before doing anything else
- Fan noise -- dust buildup or failing fan. Do not ignore -- thermal damage will follow

---

### Step 4: Back Up Data if At Risk

If the fault suggests storage failure or the device may not be recoverable:

1. Attempt to boot into Safe Mode if normal boot fails
2. Copy critical user files to a network share immediately:
```powershell
Copy-Item -Path "C:\Users\jsmith\Documents" -Destination "\\DC01\UserBackups\jsmith" -Recurse
```
3. Note what was backed up and when in the ticket

Data backup takes priority over everything else when storage failure is suspected. Hardware can be replaced. Data that was not backed up cannot.

---

### Step 5: Check Warranty Status

Check the manufacturer website using the device serial number:

| Manufacturer | Warranty Check |
|---|---|
| Dell | dell.com/support |
| HP | support.hp.com |
| Lenovo | support.lenovo.com |
| Apple | checkcoverage.apple.com |

| Warranty Status | Action |
|---|---|
| Under warranty | Log a warranty claim with the manufacturer |
| Out of warranty | Escalate to IT Manager for replacement approval |
| Unknown | Check purchase records with Finance before proceeding |

---

### Step 6: Provide a Temporary Workaround

While the fault is being resolved the user still needs to work. Options in order of preference:

1. **Loan device** -- issue a spare workstation if available
2. **Peripheral swap** -- replace faulty mouse, keyboard, or monitor from IT spares
3. **Remote access** -- set up RDP to another domain workstation if no spare hardware
4. **Shared workstation** -- temporary use of an unassigned machine

Document the workaround provided in the ticket. If a loan device was issued, record the asset tag of the loaned device and who it was issued to.

---

### Step 7: Log the Fault

Every hardware fault gets logged regardless of severity. This builds the asset history and helps identify patterns -- a device that has had three faults in six months is a replacement candidate, not a repair candidate.

```
Hardware fault logged for [asset tag] -- [make and model]
Assigned user: [name and department]
Serial number: [serial]
Fault description: [what the user reported]
Fault assessment: [what was found during testing]
Warranty status: [under warranty / out of warranty / unknown]
Action taken: [warranty claim logged / escalated for replacement / repaired]
Workaround provided: [description or none]
Data backup: [completed / not required / attempted but failed]
Ticket logged at [time].
```

---

### Step 8: Follow Up

Hardware faults do not close when the fault is logged. They close when the user has a working device.

- Check warranty claim status within 48 hours if one was submitted
- Confirm replacement approval from IT Manager if out of warranty
- Update the user every 24 hours on the status of their device
- Close the ticket only when the user confirms their replacement or repaired device is working

---

## Common Fault Patterns

| Symptom | Likely Cause | Action |
|---|---|---|
| No power | Faulty adapter or dead battery | Try known good adapter, check warranty |
| Blue screen repeatedly | Driver conflict or failing RAM | Run memory diagnostic, check event logs |
| Slow performance | Full disk or failing HDD | Check disk space and run disk health check |
| Overheating and shutdown | Dust buildup or failing fan | Clean vents, replace fan if needed |
| Cracked screen | Physical damage | Check warranty -- accidental damage coverage |
| USB ports not working | Driver issue or port failure | Test other ports, update USB drivers |

---

## Disk Health Check

Run this before deciding whether a slow or unstable device needs a replacement drive:

```powershell
Get-PhysicalDisk | Select-Object FriendlyName, HealthStatus, OperationalStatus
```

Any result other than Healthy and OK is a flag for data backup and drive replacement.

---

## Escalation Triggers

Escalate to Tier 2 if:

- Fault affects shared infrastructure -- a switch, server, or network device
- Data recovery is needed beyond basic file copy
- Device requires physical repair beyond peripheral swap
- Fault pattern suggests a widespread hardware issue across multiple devices
- Replacement requires procurement and budget approval

---

## Related

- `ticket-workflows/escalation-matrix.md`
- `sla-policies/sla-definitions.md`
- `runbooks/workstation-reimaging.md`
