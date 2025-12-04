estranho pq meus ls acima nao estavam achando?

sh-5.1$ sudo su
[root@LZFKDVAP1236 bin]# ls -la /opt/SoftwareExpress/sitef/
package/ scripts/
[root@LZFKDVAP1236 bin]# ls -la /opt/SoftwareExpress/sitef/
package/ scripts/
[root@LZFKDVAP1236 bin]# ls -la /opt/SoftwareExpress/sitef/
total 0
drwxr-xr-x. 4 root root 36 Dec  4 13:01 .
drwxr-xr-x. 3 root root 19 Dec  4 11:39 ..
drwxr-xr-x. 3 root root 19 Dec  4 11:39 package
drwxr-xr-x. 3 root root 32 Dec  4 13:01 scripts
[root@LZFKDVAP1236 bin]# ls -la /opt/SoftwareExpress/sitef/
total 0
drwxr-xr-x. 4 root root 36 Dec  4 13:01 .
drwxr-xr-x. 3 root root 19 Dec  4 11:39 ..
drwxr-xr-x. 3 root root 19 Dec  4 11:39 package
drwxr-xr-x. 3 root root 32 Dec  4 13:01 scripts
[root@LZFKDVAP1236 bin]# # 1) confirma IP
ip a | grep 100.99.41.58 || hostname -I

# 2) lista os caminhos (com sudo, pra garantir)
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
10.218.238.144
drwxr-xr-x. 10 root root 134 Dec  4 11:39 /opt
drwxr-xr-x. 3 root root 19 Dec  4 11:39 /opt/SoftwareExpress
drwxr-xr-x. 4 root root 36 Dec  4 13:01 /opt/SoftwareExpress/sitef
total 0
drwxr-xr-x. 4 root root 36 Dec  4 13:01 .
drwxr-xr-x. 3 root root 19 Dec  4 11:39 ..
drwxr-xr-x. 3 root root 19 Dec  4 11:39 package
drwxr-xr-x. 3 root root 32 Dec  4 13:01 scripts
total 0
drwxr-xr-x. 3 root root 19 Dec  4 11:39 .
drwxr-xr-x. 4 root root 36 Dec  4 13:01 ..
drwxr-xr-x. 2 root root 63 Dec  4 11:56 linux
total 40
drwxr-xr-x. 2 root root    63 Dec  4 11:56 .
drwxr-xr-x. 3 root root    19 Dec  4 11:39 ..
-rw-r-----. 1 root root    39 Dec  4 11:56 parallel.txt
-rw-r--r--. 1 root root 34016 Dec  4 11:49 sitef-core-0.0.1-0.x86_64.rpm
total 0
drwxr-xr-x. 3 root root 32 Dec  4 13:01 .
drwxr-xr-x. 4 root root 36 Dec  4 13:01 ..
drwxr-xr-x. 2 root root 65 Dec  4 13:01 deploy-sitef-0.0.1
total 12
drwxr-xr-x. 2 root root  65 Dec  4 13:01 .
drwxr-xr-x. 3 root root  32 Dec  4 13:01 ..
-rwxr-xr-x. 1 root root  96 Dec  4 13:01 init_parallel.sh
-rwxr-xr-x. 1 root root 120 Dec  4 13:01 init.sh
-rw-r-----. 1 root root  39 Dec  4 15:00 parallel.txt
  File: /opt/SoftwareExpress/sitef/package/linux/sitef-core-0.0.1-0.x86_64.rpm
  Size: 34016           Blocks: 72         IO Block: 4096   regular file
Device: fd05h/64773d    Inode: 1572996     Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: system_u:object_r:usr_t:s0
Access: 2025-12-04 11:49:21.016388852 +0000
Modify: 2025-12-04 11:49:21.008388821 +0000
Change: 2025-12-04 11:49:21.015388848 +0000
 Birth: 2025-12-04 11:49:21.012388836 +0000
Executando backup...
Backup finalizado
[root@LZFKDVAP1236 bin]#
