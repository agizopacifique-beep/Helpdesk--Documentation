# MAC OS BASIC TROUBLESHOOTING

**Category:** Desktop Support
**Tier:** 1
**Average Handle Time:** 15-45 minutes
**Escalation:** Tier 2 if hardware failure suspected or OS reinstall required

---

## OVERVIEW

This runbook covers the most common Mac OS support issues encountered in a business environment. Mac OS troubleshooting follows similar logical steps to Windows but uses different menus, commands, and tools. This guide covers connectivity, performance, software, account, and hardware issues on Mac OS Ventura, Monterey, and Sonoma.

---

## QUICK REFERENCE — MAC OS VS WINDOWS EQUIVALENTS

| Task | Windows | Mac OS |
|------|---------|--------|
| Task Manager | Ctrl + Alt + Delete | Cmd + Space > Activity Monitor |
| Command Prompt | cmd.exe | Terminal (Applications > Utilities) |
| Control Panel | Settings | System Preferences / System Settings |
| Device Manager | devmgmt.msc | System Information (About This Mac) |
| File Explorer | Windows Explorer | Finder |
| Safe Mode | F8 on boot | Hold Shift on boot |
| Force quit app | Alt + F4 | Cmd + Option + Esc |
| Restart | Start > Restart | Apple Menu > Restart |
| Check IP address | ipconfig | ifconfig or System Settings > Wi-Fi |

---

## STEP 1 — BASIC RESTART AND FIRST STEPS

Before any troubleshooting always try:

1. Click **Apple Menu** (top left) > **Restart**
2. Wait for full reboot and test the issue again
3. Check for pending Mac OS updates:
   - **Apple Menu** > **System Settings** > **General** > **Software Update**
4. Install any available updates and restart

---

## STEP 2 — NETWORK AND WI-FI TROUBLESHOOTING

**Check IP address and connection status:**
1. Click **Apple Menu** > **System Settings** > **Wi-Fi**
2. Confirm connected to correct network
3. Click the network name and check IP address is valid (not 169.254.x.x)

**Run network diagnostics from Terminal:**
```
ping google.com
```
```
ifconfig en0
```
```
nslookup google.com
```
