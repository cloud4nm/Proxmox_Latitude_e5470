# Setting Up an Old Laptop for Proxmox with Lid Closed, Screen Off & Battery Status

This guide explains how to set up an old laptop (in this case, a Dell Latitude e5470) as a Proxmox server, keeping the 
laptop lid closed, screen off, and checking the battery status. 
The assumption is that you have already installed Proxmox and it's up and running.

---

## Table of Contents
- [Step 1: Make the Laptop Work with the Lid Closed](#step-1-make-the-laptop-work-with-the-lid-closed)
- [Step 2: Turn Off the Laptop Screen](#step-2-turn-off-the-laptop-screen)
- [Step 3: Check Laptop Battery Status/Percentage in Proxmox](#step-3-check-laptop-battery-statuspercentage-in-proxmox)
- [Summary](#done-and-dusted-summary)

---

## Step 1: Make the Laptop Work with the Lid Closed

By default, laptops may go to sleep or shut down when the lid is closed, but we can tweak that behavior to allow the laptop to run continuously.

1. Open the configuration file for systemd logind:
    ```bash
    root@lab:~# vim /etc/systemd/logind.conf
    ```

2. Update the following settings (uncomment them if needed):
    ```bash
    HandleLidSwitch=ignore
    HandleLidSwitchExternalPower=ignore
    HandleLidSwitchDocked=ignore
    LidSwitchIgnoreInhibited=no
    ```

3. Restart the logind service to apply the changes:
    ```bash
    root@lab:~# systemctl restart systemd-logind
    root@lab:~# systemctl status systemd-logind
    ```

Now your laptop will keep running happily with the lid closed, leaving you with more desk space. 🎉

---

## Step 2: Turn Off the Laptop Screen

Since you’ll mostly access Proxmox via SSH, there’s no need for the laptop screen to stay on. Here are two ways to handle it:

### Option 2.1: A Quick Temporary Fix

Run this command to make the screen go blank after 5 minutes of inactivity:
```bash
root@lab:~# setterm -blank 5
```
---
> **Heads up:** This setting will reset when you reboot the laptop.

### Option 2.2: A Permanent Solution

1. Open the GRUB configuration file:
    ```bash
    root@lab:~# vim /etc/default/grub
    ```

2. Add `consoleblank=300` (300 seconds = 5 minutes) to the `GRUB_CMDLINE_LINUX` line:
    ```bash
    GRUB_CMDLINE_LINUX="consoleblank=300"
    ```

3. Update the GRUB configuration and reboot:
    ```bash
    root@lab:~# update-grub
    root@lab:~# reboot
    ```

This ensures the screen will always turn off after the specified time, even after reboots.

---

## Step 3: Check Laptop Battery Status/Percentage in Proxmox

Proxmox doesn't provide a direct GUI method for checking the laptop's battery percentage. However, you can use Linux commands to retrieve battery status from the underlying Debian system.

### Option 3.1: Using `upower`

`upower` is a utility that provides detailed power information, including the battery percentage and status.

1. Update the package list:
    ```bash
    apt update
    ```

2. Install `upower` utility:
    ```bash
    apt install upower -y
    ```

3. Run the following command to list battery information:
    ```bash
    upower -i $(upower -e | grep BAT)
    ```

4. Interpret the output. Look for the `percentage` field:
    ```bash
    percentage:          85%
    state:               discharging
    ```

   - **Percentage:** The current battery charge level.
   - **State:** Indicates whether the battery is charging, discharging, or fully charged.

### Option 3.2: Using `acpi`

`acpi` is a lightweight tool that provides detailed battery and power information in a concise format.

1. Install `acpi` utility:
    ```bash
    apt install acpi
    ```

2. Run the following command to view battery status:
    ```bash
    acpi -i
    ```

3. Review the output. It will look something like:
    ```bash
    Battery 0: Discharging, 85%, 3:12 remaining
    ```

   - **Battery 0:** The identifier for the battery.
   - **Discharging:** Indicates whether the battery is discharging or charging.
   - **85%:** Current battery percentage.
   - **3:12 remaining:** Estimated time left until the battery is empty (if discharging) or fully charged (if charging).

---

## Done and Dusted! Summary

With these simple tweaks, you've turned an old laptop into a Proxmox server that works efficiently with the lid closed and the screen off. It's a neat, space-saving setup perfect for your homelab! 🚀

```

This README.md file summarizes the steps and provides easy-to-follow instructions for the setup process. You can copy and paste this into your project repository on GitHub or other platforms.
