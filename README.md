## 🎯 Goal
Set up a Linux log monitoring system that detects suspicious events (e.g., failed SSH logins) from /var/log/auth.log, and automatically sends alerts via email.

---

## 🔹 Phase 1: Setup Environment
1. Provision an Ubuntu 22.04 VM (AWS / VirtualBox / VMware).
2. Update system:
   sudo apt update && sudo apt upgrade -y
3. Install mail utilities:
   sudo apt install mailutils -y

---

## 🔹 Phase 2: Identify Logs to Monitor
- Security logs on Ubuntu:
  - /var/log/auth.log → records login attempts

Test with:
   sudo tail -f /var/log/auth.log

From another machine:
   ssh wronguser@<server-ip>

---

## 🔹 Phase 3: Write Log Monitoring Script
1. Create script:
   sudo nano /usr/local/bin/log_monitor.sh

2. Add content:
   #!/bin/bash
   LOG_FILE="/var/log/auth.log"
   ALERT_EMAIL="your_email@example.com"

   FAILED_LOGINS=$(grep "Failed password" $LOG_FILE | grep "$(date --date='10 minutes ago' '+%b %e %H:')")

   if [ ! -z "$FAILED_LOGINS" ]; then
       echo -e "⚠️ ALERT: Failed SSH login attempts detected!\n\n$FAILED_LOGINS" \
       | mail -s "SSH Login Alert on $(hostname)" $ALERT_EMAIL
   fi

3. Make it executable:
   sudo chmod +x /usr/local/bin/log_monitor.sh

---

## 🔹 Phase 4: Automate with Cron
1. Edit cron:
   crontab -e

2. Add line:
   */10 * * * * /usr/local/bin/log_monitor.sh

3. Verify:
   crontab -l

---

## 🔹 Phase 5: Test the System
1. From another machine, attempt wrong SSH logins:
   ssh wronguser@<server-ip>
2. Wait 10 mins → script triggers email.
3. Check logs:
   tail -f /var/log/mail.log

---

⚡ This project demonstrates Linux logs, shell scripting, cron jobs, and server security automation.
