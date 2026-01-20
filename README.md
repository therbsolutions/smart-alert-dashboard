# Smart-Alert-Dashboard
This project implements a robust Linux-based monitoring solution designed for Western Digital Purple (surveillance-grade) storage. It bridges the gap between low-level hardware diagnostics and high-level visual management by integrating Postfix SMTP relays for instant Gmail notifications and the Cockpit Dashboard for real-time temperature and health visualization.




To replicate the **Smart-Alert-Dashboard** system we have built for your HP Workstation and your 1.8TB WD Purple drive, you can use the following operational code blocks. This configuration ensures that your surveillance-grade storage is monitored, visualized, and alerts you via Gmail if any issues arise.

---

### 1. The Core Monitoring Engine (`smartd`)

This code configures `smartd` to monitor your drive and execute the specific test schedule you requested (Short test at 12:00 PM and Long test at 1:05 PM).

**File:** `/etc/smartd.conf`

```text
# Disable default scanning to focus on the WD Purple Drive
# -a: Monitor all SMART attributes
# -o/S on: Enable automatic offline testing and attribute saving
# -s: Schedule tests (Short at 12:00, Long at 13:00/1:00 PM)
# -m: Send alerts to root (aliased to your Gmail)
/dev/sda -a -o on -S on -s (S/../.././12|L/../.././13) -m root

```

### 2. The Secure Alert Relay (`Postfix`)

This code handles the encrypted handoff of system alerts to Gmail.

**File:** `/etc/postfix/main.cf`

```text
# Gmail SMTP Relay Settings
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = encrypt
header_size_limit = 40960

```

**Credential File:** `/etc/postfix/sasl_passwd`

```text
[smtp.gmail.com]:587 your-email@gmail.com:your-app-password

```

*Note: Run `sudo postmap /etc/postfix/sasl_passwd` after saving.*

### 3. Visual Dashboard Installation

These commands install the Cockpit interface and the storage-specific module needed to see your WD Purple's temperature and health.

```bash
# Install Cockpit and Storage Module
sudo apt update && sudo apt install cockpit cockpit-storaged -y

# Enable and Start the service
sudo systemctl enable --now cockpit.socket

# Allow through Firewall
sudo ufw allow 9090/tcp

```



