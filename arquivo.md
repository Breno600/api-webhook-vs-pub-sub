[ec2-user@LZFKDVAP1237 .ssh]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 02:77:40:9d:2d:99 brd ff:ff:ff:ff:ff:ff
    altname enp0s5
    altname ens5
    inet 100.99.41.58/18 brd 100.99.63.255 scope global dynamic noprefixroute eth0
       valid_lft 2443sec preferred_lft 2443sec
[ec2-user@LZFKDVAP1237 .ssh]$ sudo bash -lc '
echo "== DIRS ==" &&
ls -ld /opt/SoftwareExpress/sitef \
       /opt/SoftwareExpress/sitef/package/linux \
       /opt/SoftwareExpress/sitef/scripts &&

echo "== RPM ==" &&
ls -l /opt/SoftwareExpress/sitef/package/linux &&

echo "== SCRIPTS ==" &&
ls -la /opt/SoftwareExpress/sitef/scripts &&

echo "== INIT FILES ==" &&
test -f /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/init_parallel.sh && echo "OK init_parallel.sh" &&
test -f /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/init.sh && echo "OK init.sh" &&

echo "== PROBE ==" &&
ls -la /opt/SoftwareExpress/sitef/.predeploy_probe_* 2>/dev/null || echo "Probe nao encontrado" &&

echo "== PARALLEL.TXT ==" &&
cat /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt 2>/dev/null || echo "parallel.txt nao existe" &&

echo "== PKGS INSTALADOS (se aplicavel) ==" &&
rpm -qa | grep -i sitef || true
'
== DIRS ==
drwxr-xr-x. 4 root root 4096 Dec  5 11:06 /opt/SoftwareExpress/sitef
drwxr-xr-x. 2 root root   43 Dec  5 10:48 /opt/SoftwareExpress/sitef/package/linux
drwxr-xr-x. 3 root root   32 Dec  5 10:48 /opt/SoftwareExpress/sitef/scripts
== RPM ==
total 36
-rw-r--r--. 1 root root 34016 Dec  5 10:48 sitef-core-0.0.1-0.x86_64.rpm
== SCRIPTS ==
total 4
drwxr-xr-x. 3 root root   32 Dec  5 10:48 .
drwxr-xr-x. 4 root root 4096 Dec  5 11:06 ..
drwxr-xr-x. 2 root root   65 Dec  5 10:48 deploy-sitef-0.0.1
== INIT FILES ==
OK init_parallel.sh
OK init.sh
== PROBE ==
-rw-r--r--. 1 root root 115 Dec  5 10:48 /opt/SoftwareExpress/sitef/.predeploy_probe_DEV000000003_sitef-01_1764931702
-rw-r--r--. 1 root root 115 Dec  5 11:01 /opt/SoftwareExpress/sitef/.predeploy_probe_DEV000000003_sitef-01_1764932465
-rw-r--r--. 1 root root 115 Dec  5 11:04 /opt/SoftwareExpress/sitef/.predeploy_probe_DEV000000003_sitef-01_1764932640
-rw-r--r--. 1 root root 115 Dec  5 11:06 /opt/SoftwareExpress/sitef/.predeploy_probe_DEV000000003_sitef-01_1764932782
== PARALLEL.TXT ==
Executando backup...
Backup finalizado
== PKGS INSTALADOS (se aplicavel) ==
[ec2-user@LZFKDVAP1237 .ssh]$
