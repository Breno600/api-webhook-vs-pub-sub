[root@LZFKDVAP1237 bin]# # 2) lista os caminhos (com sudo, pra garantir)
sudo ls -ld /opt
sudo ls -ld /opt/SoftwareExpress
sudo ls -ld /opt/SoftwareExpress/sitef
sudo ls -la /opt/SoftwareExpress/sitef
sudo ls -la /opt/SoftwareExpress/sitef/package
sudo ls -la /opt/SoftwareExpress/sitef/package/linux
sudo ls -la /opt/SoftwareExpress/sitef/scripts
sudo ls -la /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1

# 3) tenta ver o arquivo diretamente
sudo stat /opt/SoftwareExpress/sitef/package/linux/sitef-core-0.0.1-0.x86_64.rpm
sudo cat  /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt || echo "parallel.txt não existe"
drwxr-xr-x. 10 root root 134 Nov 25 10:08 /opt
drwxr-xr-x. 3 root root 19 Nov 25 10:08 /opt/SoftwareExpress
drwxr-xr-x. 2 root root 6 Nov 25 14:57 /opt/SoftwareExpress/sitef
total 0
drwxr-xr-x. 2 root root  6 Nov 25 14:57 .
drwxr-xr-x. 3 root root 19 Nov 25 10:08 ..
ls: cannot access '/opt/SoftwareExpress/sitef/package': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/package/linux': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/scripts': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1': No such file or directory
stat: cannot statx '/opt/SoftwareExpress/sitef/package/linux/sitef-core-0.0.1-0.x86_64.rpm': No such file or directory
cat: /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt: No such file or directory
parallel.txt não existe
[root@LZFKDVAP1237 bin]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 02:77:40:9d:2d:99 brd ff:ff:ff:ff:ff:ff
    altname enp0s5
    altname ens5
    inet 100.99.41.58/18 brd 100.99.63.255 scope global dynamic noprefixroute eth0
       valid_lft 2641sec preferred_lft 2641sec
[root@LZFKDVAP1237 bin]#

[root@LZFKDVAP1238 sitef]# # 2) lista os caminhos (com sudo, pra garantir)
sudo ls -ld /opt
sudo ls -ld /opt/SoftwareExpress
sudo ls -ld /opt/SoftwareExpress/sitef
sudo ls -la /opt/SoftwareExpress/sitef
sudo ls -la /opt/SoftwareExpress/sitef/package
sudo ls -la /opt/SoftwareExpress/sitef/package/linux
sudo ls -la /opt/SoftwareExpress/sitef/scripts
sudo ls -la /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1

# 3) tenta ver o arquivo diretamente
sudo stat /opt/SoftwareExpress/sitef/package/linux/sitef-core-0.0.1-0.x86_64.rpm
sudo cat  /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt || echo "parallel.txt não existe"
drwxr-xr-x. 10 root root 134 Nov 25 10:44 /opt
drwxr-xr-x. 3 root root 19 Nov 25 10:44 /opt/SoftwareExpress
drwxr-xr-x. 2 root root 6 Nov 25 14:57 /opt/SoftwareExpress/sitef
total 0
drwxr-xr-x. 2 root root  6 Nov 25 14:57 .
drwxr-xr-x. 3 root root 19 Nov 25 10:44 ..
ls: cannot access '/opt/SoftwareExpress/sitef/package': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/package/linux': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/scripts': No such file or directory
ls: cannot access '/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1': No such file or directory
stat: cannot statx '/opt/SoftwareExpress/sitef/package/linux/sitef-core-0.0.1-0.x86_64.rpm': No such file or directory
cat: /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt: No such file or directory
parallel.txt não existe
[root@LZFKDVAP1238 sitef]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc mq state UP group default qlen 1000
    link/ether 02:79:79:ae:3f:1f brd ff:ff:ff:ff:ff:ff
    altname enp0s5
    altname ens5
    inet 100.99.61.184/18 brd 100.99.63.255 scope global dynamic noprefixroute eth0
       valid_lft 2661sec preferred_lft 2661sec
[root@LZFKDVAP1238 sitef]#
