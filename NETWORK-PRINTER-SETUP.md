# NETWORK PRINTER SETUP AND TROUBLESHOOTING

**Category:** Hardware Support
**Tier:** 1
**Average Handle Time:** 15-30 minutes
**Escalation:** Tier 2 if VLAN configuration required or print server is unresponsive

---

## OVERVIEW

This runbook covers installation, configuration, and troubleshooting of network printers on Windows 10/11 client machines in a business environment. Network printers communicate over TCP/IP and require a static or DHCP-reserved IP address for reliable multi-user operation.

---

## PREREQUISITES

Before starting confirm the following:

- Printer is powered on and connected to the network via Ethernet cable
- Printer has a static IP address or DHCP reservation configured
- You have the printer model number to download the correct driver
- Client machine is on the same network segment as the printer

---

## STEP 1 — FIND THE PRINTER IP ADDRESS

**Method A — Print a configuration page from the printer panel:**
1. On the printer press **Menu** or **Settings**
2. Navigate to **Reports** or **Information**
3. Select **Configuration Page** or **Network Summary**
4. The IP address is listed under TCP/IP settings

**Method B — Find it from the router or DHCP server:**
1. Log in to the router admin page (commonly 192.168.1.1 or 10.0.0.1)
2. Navigate to **Connected Devices** or **DHCP Lease Table**
3. Locate the printer by MAC address or hostname
4. Note the assigned IP address

**Method C — Use command line:**
