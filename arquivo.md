[root@LZFKDVAP1237 bin]# hostname
uptime
last reboot | head -5
sudo crontab -l
sudo ls -l /etc/cron.*
sudo grep -R "SoftwareExpress\|sitef" -n /etc/cron* 2>/dev/null
systemctl list-timers --all | head -200
sudo systemctl list-timers --all | grep -i -E "clean|tmp|sitef|software"
sudo grep -R "SoftwareExpress\|sitef" -n /etc 2>/dev/null | head -200
LZFKDVAP1237.1dc.com
 10:22:26 up 21 min,  1 user,  load average: 0.00, 0.11, 0.20
reboot   system boot  5.14.0-570.60.1. Fri Dec  5 10:01   still running
reboot   system boot  5.14.0-570.60.1. Thu Dec  4 10:01 - 00:01  (14:00)
reboot   system boot  5.14.0-570.60.1. Wed Dec  3 10:01 - 00:01  (14:00)
reboot   system boot  5.14.0-570.60.1. Tue Dec  2 10:01 - 00:01  (13:59)
reboot   system boot  5.14.0-570.60.1. Mon Dec  1 10:01 - 00:01  (14:00)
0 * * * * /opt/admin/agents/check_agents.sh
-rw-------. 1 root root  0 Nov  3 12:27 /etc/cron.allow

/etc/cron.d:
total 4
-rw-r--r--. 1 root root 128 Jun 20 12:23 0hourly

/etc/cron.daily:
total 0

/etc/cron.hourly:
total 4
-rwxr-xr-x. 1 root root 610 Jun 20 12:23 0anacron

/etc/cron.monthly:
total 0

/etc/cron.weekly:
total 0
NEXT                        LEFT         LAST                        PASSED       UNIT                         ACTIVATES
Fri 2025-12-05 10:26:30 UTC 4min 3s left Fri 2025-12-05 10:21:30 UTC 56s ago      nm-cloud-setup.timer         nm-cloud-setup.service
Fri 2025-12-05 10:27:34 UTC 5min left    -                           -            dnf-makecache.timer          dnf-makecache.service
Fri 2025-12-05 10:30:00 UTC 7min left    Fri 2025-12-05 10:20:02 UTC 2min 23s ago sysstat-collect.timer        sysstat-collect.service
Sat 2025-12-06 00:00:00 UTC 13h left     Fri 2025-12-05 00:00:01 UTC 10h ago      logrotate.timer              logrotate.service
Sat 2025-12-06 00:07:00 UTC 13h left     -                           -            sysstat-summary.timer        sysstat-summary.service
Sat 2025-12-06 10:16:25 UTC 23h left     Fri 2025-12-05 10:16:25 UTC 6min ago     systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service

6 timers listed.
Sat 2025-12-06 10:16:25 UTC 23h left     Fri 2025-12-05 10:16:25 UTC 6min ago     systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service
/etc/gshadow:46:sitef:!::
/etc/group:46:sitef:x:995:
/etc/shadow:29:sitefd:!!:20417::::::
/etc/passwd:29:sitefd:x:993:995::/opt/SoftwareExpress/sitef:/sbin/nologin
[root@LZFKDVAP1237 bin]#
