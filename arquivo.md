hostname
uptime
last reboot | head -5
sudo crontab -l
sudo ls -l /etc/cron.*
sudo grep -R "SoftwareExpress\|sitef" -n /etc/cron* 2>/dev/null
systemctl list-timers --all | head -200
sudo systemctl list-timers --all | grep -i -E "clean|tmp|sitef|software"
sudo grep -R "SoftwareExpress\|sitef" -n /etc 2>/dev/null | head -200
