# **Homelab Guide: Automated Security Updates & Scheduled Reboots**

This guide configures a Linux server (Debian/Ubuntu) to automatically install **security updates only** and perform a reboot at a specific time **only if required**.

## **1\. Install & Enable Unattended Upgrades**

First, ensure the utility is installed and the periodic trigger is active.

```shell
sudo apt update && sudo apt install unattended-upgrades -y
```

### **Enable the Daily Trigger**

Edit /etc/apt/apt.conf.d/20auto-upgrades to ensure the background service runs daily:

```shell
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

**Ensure the file matches this exactly:**

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

---

## **2\. Configure Security-Only Filter**

To prevent the system from accidentally upgrading application versions that might break configurations, we limit the "Allowed Origins" to security repositories.

```shell
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

**Modify the Unattended-Upgrade::Allowed-Origins block:**

* Comment out the base ${distro\_id}:${distro\_codename} line with //.  
* Ensure security and ESM lines are active.

```
Unattended-Upgrade::Allowed-Origins {
//      "${distro_id}:${distro_codename}";
        "${distro_id}:${distro_codename}-security";
        "${distro_id}ESMApps:${distro_codename}-apps-security";
        "${distro_id}ESM:${distro_codename}-infra-security";
};
```

**Disable built-in auto-reboot:**

Scroll down and ensure this is set to false (we will handle reboots via Cron for better control).

```
Unattended-Upgrade::Automatic-Reboot "false";
```

---

## **3\. Set Saturday Reboot (Cron)**

We want the server to reboot at **10:00 UTC every Saturday**, but only if an update (like a Kernel patch) actually requires it.

1. Open the root crontab:

```shell
sudo crontab -e
```

2. Add the following line at the bottom:

```
# Reboot at 10:00 every Saturday ONLY if /var/run/reboot-required exists
0 10 * * 6 [ -f /var/run/reboot-required ] && /sbin/reboot
```

---

## **4\. Verification Commands**

Always verify the configuration before walking away.

| Action | Command |
| :---- | :---- |
| **Test Configuration** | `sudo unattended-upgrade --dry-run --debug` |
| **Check Next Run Time** | `systemctl list-timers apt-daily-upgrade.timer` |
| **Check Update Logs** | `cat /var/log/unattended-upgrades/unattended-upgrades.log` |
| **Check Reboot Flag** | `ls /var/run/reboot-required` |

---

## **Why This Method?**

* **Safety:** By excluding the main ${distro\_codename} repository, you avoid "feature" updates that often break specific app dependencies.  
* **Uptime:** The server only reboots when absolutely necessary (e.g., a new Kernel is installed).  
* **Predictability:** Reboots are restricted to your Saturday maintenance window.

