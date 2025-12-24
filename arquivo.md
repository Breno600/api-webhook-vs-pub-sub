## dev000003-sitef-02-dev.log


---- pipeline deploy ----
deployment_ref=DEV000003
machine=sitef-02
host=100.99.57.128
stage=deploy
rc=0
ts=2025-12-24T19:00:24Z

---- stdout (ansible) ----
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 07:00:31 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ echo 'Instalacao de dependencias do python finalizada.'
Instalacao de dependencias do python finalizada.
+ echo '== Fim init_deploy.sh =='
== Fim init_deploy.sh ==
+ date
Wed Dec 24 07:00:31 PM UTC 2025

---- stderr (ansible) ----


---- init_deploy.log (tail) ----
+ set -euo pipefail
+++ dirname ./init_deploy.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts
+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ exec
++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:00:26 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.6 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.7 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 4.0 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    
Dependencies resolved.
===========================================================================================
 Package                    Arch    Version         Repository                         Size
===========================================================================================
Installing:
 python3.12                 x86_64  3.12.12-1.el9_7 rhel-9-for-x86_64-appstream-rpms   27 k
 python3.12-pip             noarch  23.2.1-5.el9    rhel-9-for-x86_64-appstream-rpms  3.2 M
Upgrading:
 openssl                    x86_64  1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     1.5 M
 openssl-fips-provider      x86_64  3.0.7-8.el9     rhel-9-for-x86_64-baseos-rpms     9.2 k
 openssl-fips-provider-so   x86_64  3.0.7-8.el9     rhel-9-for-x86_64-baseos-rpms     576 k
 openssl-libs               i686    1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     2.3 M
 openssl-libs               x86_64  1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     2.3 M
Installing dependencies:
 libnsl2                    x86_64  2.0.0-1.el9     rhel-9-for-x86_64-appstream-rpms   33 k
 libtirpc                   x86_64  1.3.3-9.el9     rhel-9-for-x86_64-baseos-rpms      97 k
 mpdecimal                  x86_64  2.5.1-3.el9     rhel-9-for-x86_64-appstream-rpms   88 k
 python3.12-libs            x86_64  3.12.12-1.el9_7 rhel-9-for-x86_64-appstream-rpms  9.7 M
 python3.12-pip-wheel       noarch  23.2.1-5.el9    rhel-9-for-x86_64-appstream-rpms  1.5 M
Installing weak dependencies:
 python3.12-setuptools      noarch  68.2.2-5.el9_6  rhel-9-for-x86_64-appstream-rpms  1.6 M

Transaction Summary
===========================================================================================
Install  8 Packages
Upgrade  5 Packages

Total download size: 23 M
Downloading Packages:
(1/13): libnsl2-2.0.0-1.el9.x86_64.rpm           17 kB/s |  33 kB     00:01    
(2/13): libtirpc-1.3.3-9.el9.x86_64.rpm          45 kB/s |  97 kB     00:02    
(3/13): mpdecimal-2.5.1-3.el9.x86_64.rpm         38 kB/s |  88 kB     00:02    
(4/13): python3.12-setuptools-68.2.2-5.el9_6.no 382 kB/s | 1.6 MB     00:04    
(5/13): python3.12-pip-23.2.1-5.el9.noarch.rpm  240 kB/s | 3.2 MB     00:13    
(6/13): python3.12-pip-wheel-23.2.1-5.el9.noarc  86 kB/s | 1.5 MB     00:17    
(7/13): python3.12-3.12.12-1.el9_7.x86_64.rpm   2.0 kB/s |  27 kB     00:13    
(8/13): openssl-fips-provider-3.0.7-8.el9.x86_6 1.2 kB/s | 9.2 kB     00:07    
(9/13): openssl-fips-provider-so-3.0.7-8.el9.x8  57 kB/s | 576 kB     00:10    
(10/13): python3.12-libs-3.12.12-1.el9_7.x86_64 289 kB/s | 9.7 MB     00:34    
(11/13): openssl-3.5.1-4.el9_7.x86_64.rpm        50 kB/s | 1.5 MB     00:30    
(12/13): openssl-libs-3.5.1-4.el9_7.x86_64.rpm   82 kB/s | 2.3 MB     00:28    
(13/13): openssl-libs-3.5.1-4.el9_7.i686.rpm    204 kB/s | 2.3 MB     00:11    
--------------------------------------------------------------------------------
Total                                           378 kB/s |  23 MB     01:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : libtirpc-1.3.3-9.el9.x86_64                           1/18 
  Installing       : libnsl2-2.0.0-1.el9.x86_64                            2/18 
  Upgrading        : openssl-fips-provider-so-3.0.7-8.el9.x86_64           3/18 
  Upgrading        : openssl-fips-provider-3.0.7-8.el9.x86_64              4/18 
  Upgrading        : openssl-libs-1:3.5.1-4.el9_7.x86_64                   5/18 
  Installing       : python3.12-pip-wheel-23.2.1-5.el9.noarch              6/18 
  Installing       : mpdecimal-2.5.1-3.el9.x86_64                          7/18 
  Installing       : python3.12-3.12.12-1.el9_7.x86_64                     8/18 
  Installing       : python3.12-libs-3.12.12-1.el9_7.x86_64                9/18 
  Installing       : python3.12-setuptools-68.2.2-5.el9_6.noarch          10/18 
  Installing       : python3.12-pip-23.2.1-5.el9.noarch                   11/18 
  Upgrading        : openssl-1:3.5.1-4.el9_7.x86_64                       12/18 
  Upgrading        : openssl-libs-1:3.5.1-4.el9_7.i686                    13/18 
  Cleanup          : openssl-1:3.2.2-6.el9_5.1.x86_64                     14/18 
  Cleanup          : openssl-libs-1:3.2.2-6.el9_5.1.i686                  15/18 
  Cleanup          : openssl-libs-1:3.2.2-6.el9_5.1.x86_64                16/18 
  Cleanup          : openssl-fips-provider-3.0.7-6.el9_5.x86_64           17/18 
  Cleanup          : openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        18/18 
  Running scriptlet: openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        18/18 
  Verifying        : libtirpc-1.3.3-9.el9.x86_64                           1/18 
  Verifying        : libnsl2-2.0.0-1.el9.x86_64                            2/18 
  Verifying        : mpdecimal-2.5.1-3.el9.x86_64                          3/18 
  Verifying        : python3.12-setuptools-68.2.2-5.el9_6.noarch           4/18 
  Verifying        : python3.12-pip-23.2.1-5.el9.noarch                    5/18 
  Verifying        : python3.12-pip-wheel-23.2.1-5.el9.noarch              6/18 
  Verifying        : python3.12-3.12.12-1.el9_7.x86_64                     7/18 
  Verifying        : python3.12-libs-3.12.12-1.el9_7.x86_64                8/18 
  Verifying        : openssl-fips-provider-3.0.7-8.el9.x86_64              9/18 
  Verifying        : openssl-fips-provider-3.0.7-6.el9_5.x86_64           10/18 
  Verifying        : openssl-fips-provider-so-3.0.7-8.el9.x86_64          11/18 
  Verifying        : openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        12/18 
  Verifying        : openssl-1:3.5.1-4.el9_7.x86_64                       13/18 
  Verifying        : openssl-1:3.2.2-6.el9_5.1.x86_64                     14/18 
  Verifying        : openssl-libs-1:3.5.1-4.el9_7.x86_64                  15/18 
  Verifying        : openssl-libs-1:3.2.2-6.el9_5.1.x86_64                16/18 
  Verifying        : openssl-libs-1:3.5.1-4.el9_7.i686                    17/18 
  Verifying        : openssl-libs-1:3.2.2-6.el9_5.1.i686                  18/18 
Installed products updated.

Upgraded:
  openssl-1:3.5.1-4.el9_7.x86_64                                                
  openssl-fips-provider-3.0.7-8.el9.x86_64                                      
  openssl-fips-provider-so-3.0.7-8.el9.x86_64                                   
  openssl-libs-1:3.5.1-4.el9_7.i686                                             
  openssl-libs-1:3.5.1-4.el9_7.x86_64                                           
Installed:
  libnsl2-2.0.0-1.el9.x86_64                                                    
  libtirpc-1.3.3-9.el9.x86_64                                                   
  mpdecimal-2.5.1-3.el9.x86_64                                                  
  python3.12-3.12.12-1.el9_7.x86_64                                             
  python3.12-libs-3.12.12-1.el9_7.x86_64                                        
  python3.12-pip-23.2.1-5.el9.noarch                                            
  python3.12-pip-wheel-23.2.1-5.el9.noarch                                      
  python3.12-setuptools-68.2.2-5.el9_6.noarch                                   

Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'

+ set -euo pipefail
+++ dirname ./init_deploy.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts
+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ exec
++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:04:18 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.5 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.6 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ deactivate
./init_deploy.sh: line 23: deactivate: command not found
+ set -euo pipefail
+++ dirname ./init_deploy.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts
+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ exec
++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:07:22 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  3.5 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.2 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ set +x
./init_deploy.sh: line 22: RC: unbound variable
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:11:46 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
./init_deploy.sh: line 11: RC: unbound variable
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:14:42 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ echo '== Fim init_deploy.sh =='
== Fim init_deploy.sh ==
+ date
Wed Dec 24 06:14:42 PM UTC 2025
+ set -euo pipefail
+++ dirname ./init_deploy.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts
+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ exec
++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:27:20 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.5 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.5 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'

+ set -euo pipefail
+++ dirname ./init_deploy.sh
++ cd .
++ pwd
+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts
+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ exec
++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:32:04 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.4 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.1 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'

+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:46:00 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ echo 'Instalacao de dependencias do python finalizada.'
Instalacao de dependencias do python finalizada.
+ echo '== Fim init_deploy.sh =='
== Fim init_deploy.sh ==
+ date
Wed Dec 24 06:46:00 PM UTC 2025
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:50:51 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ echo 'Instalacao de dependencias do python finalizada.'
Instalacao de dependencias do python finalizada.
+ echo '== Fim init_deploy.sh =='
== Fim init_deploy.sh ==
+ date
Wed Dec 24 06:50:51 PM UTC 2025
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 07:00:31 PM UTC 2025
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ echo 'Instalacao de dependencias do python finalizada.'
Instalacao de dependencias do python finalizada.
+ echo '== Fim init_deploy.sh =='
== Fim init_deploy.sh ==
+ date
Wed Dec 24 07:00:31 PM UTC 2025

---- deploy.txt ----
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:00:26 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.6 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.7 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 4.0 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    
Dependencies resolved.
===========================================================================================
 Package                    Arch    Version         Repository                         Size
===========================================================================================
Installing:
 python3.12                 x86_64  3.12.12-1.el9_7 rhel-9-for-x86_64-appstream-rpms   27 k
 python3.12-pip             noarch  23.2.1-5.el9    rhel-9-for-x86_64-appstream-rpms  3.2 M
Upgrading:
 openssl                    x86_64  1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     1.5 M
 openssl-fips-provider      x86_64  3.0.7-8.el9     rhel-9-for-x86_64-baseos-rpms     9.2 k
 openssl-fips-provider-so   x86_64  3.0.7-8.el9     rhel-9-for-x86_64-baseos-rpms     576 k
 openssl-libs               i686    1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     2.3 M
 openssl-libs               x86_64  1:3.5.1-4.el9_7 rhel-9-for-x86_64-baseos-rpms     2.3 M
Installing dependencies:
 libnsl2                    x86_64  2.0.0-1.el9     rhel-9-for-x86_64-appstream-rpms   33 k
 libtirpc                   x86_64  1.3.3-9.el9     rhel-9-for-x86_64-baseos-rpms      97 k
 mpdecimal                  x86_64  2.5.1-3.el9     rhel-9-for-x86_64-appstream-rpms   88 k
 python3.12-libs            x86_64  3.12.12-1.el9_7 rhel-9-for-x86_64-appstream-rpms  9.7 M
 python3.12-pip-wheel       noarch  23.2.1-5.el9    rhel-9-for-x86_64-appstream-rpms  1.5 M
Installing weak dependencies:
 python3.12-setuptools      noarch  68.2.2-5.el9_6  rhel-9-for-x86_64-appstream-rpms  1.6 M

Transaction Summary
===========================================================================================
Install  8 Packages
Upgrade  5 Packages

Total download size: 23 M
Downloading Packages:
(1/13): libnsl2-2.0.0-1.el9.x86_64.rpm           17 kB/s |  33 kB     00:01    
(2/13): libtirpc-1.3.3-9.el9.x86_64.rpm          45 kB/s |  97 kB     00:02    
(3/13): mpdecimal-2.5.1-3.el9.x86_64.rpm         38 kB/s |  88 kB     00:02    
(4/13): python3.12-setuptools-68.2.2-5.el9_6.no 382 kB/s | 1.6 MB     00:04    
(5/13): python3.12-pip-23.2.1-5.el9.noarch.rpm  240 kB/s | 3.2 MB     00:13    
(6/13): python3.12-pip-wheel-23.2.1-5.el9.noarc  86 kB/s | 1.5 MB     00:17    
(7/13): python3.12-3.12.12-1.el9_7.x86_64.rpm   2.0 kB/s |  27 kB     00:13    
(8/13): openssl-fips-provider-3.0.7-8.el9.x86_6 1.2 kB/s | 9.2 kB     00:07    
(9/13): openssl-fips-provider-so-3.0.7-8.el9.x8  57 kB/s | 576 kB     00:10    
(10/13): python3.12-libs-3.12.12-1.el9_7.x86_64 289 kB/s | 9.7 MB     00:34    
(11/13): openssl-3.5.1-4.el9_7.x86_64.rpm        50 kB/s | 1.5 MB     00:30    
(12/13): openssl-libs-3.5.1-4.el9_7.x86_64.rpm   82 kB/s | 2.3 MB     00:28    
(13/13): openssl-libs-3.5.1-4.el9_7.i686.rpm    204 kB/s | 2.3 MB     00:11    
--------------------------------------------------------------------------------
Total                                           378 kB/s |  23 MB     01:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : libtirpc-1.3.3-9.el9.x86_64                           1/18 
  Installing       : libnsl2-2.0.0-1.el9.x86_64                            2/18 
  Upgrading        : openssl-fips-provider-so-3.0.7-8.el9.x86_64           3/18 
  Upgrading        : openssl-fips-provider-3.0.7-8.el9.x86_64              4/18 
  Upgrading        : openssl-libs-1:3.5.1-4.el9_7.x86_64                   5/18 
  Installing       : python3.12-pip-wheel-23.2.1-5.el9.noarch              6/18 
  Installing       : mpdecimal-2.5.1-3.el9.x86_64                          7/18 
  Installing       : python3.12-3.12.12-1.el9_7.x86_64                     8/18 
  Installing       : python3.12-libs-3.12.12-1.el9_7.x86_64                9/18 
  Installing       : python3.12-setuptools-68.2.2-5.el9_6.noarch          10/18 
  Installing       : python3.12-pip-23.2.1-5.el9.noarch                   11/18 
  Upgrading        : openssl-1:3.5.1-4.el9_7.x86_64                       12/18 
  Upgrading        : openssl-libs-1:3.5.1-4.el9_7.i686                    13/18 
  Cleanup          : openssl-1:3.2.2-6.el9_5.1.x86_64                     14/18 
  Cleanup          : openssl-libs-1:3.2.2-6.el9_5.1.i686                  15/18 
  Cleanup          : openssl-libs-1:3.2.2-6.el9_5.1.x86_64                16/18 
  Cleanup          : openssl-fips-provider-3.0.7-6.el9_5.x86_64           17/18 
  Cleanup          : openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        18/18 
  Running scriptlet: openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        18/18 
  Verifying        : libtirpc-1.3.3-9.el9.x86_64                           1/18 
  Verifying        : libnsl2-2.0.0-1.el9.x86_64                            2/18 
  Verifying        : mpdecimal-2.5.1-3.el9.x86_64                          3/18 
  Verifying        : python3.12-setuptools-68.2.2-5.el9_6.noarch           4/18 
  Verifying        : python3.12-pip-23.2.1-5.el9.noarch                    5/18 
  Verifying        : python3.12-pip-wheel-23.2.1-5.el9.noarch              6/18 
  Verifying        : python3.12-3.12.12-1.el9_7.x86_64                     7/18 
  Verifying        : python3.12-libs-3.12.12-1.el9_7.x86_64                8/18 
  Verifying        : openssl-fips-provider-3.0.7-8.el9.x86_64              9/18 
  Verifying        : openssl-fips-provider-3.0.7-6.el9_5.x86_64           10/18 
  Verifying        : openssl-fips-provider-so-3.0.7-8.el9.x86_64          11/18 
  Verifying        : openssl-fips-provider-so-3.0.7-6.el9_5.x86_64        12/18 
  Verifying        : openssl-1:3.5.1-4.el9_7.x86_64                       13/18 
  Verifying        : openssl-1:3.2.2-6.el9_5.1.x86_64                     14/18 
  Verifying        : openssl-libs-1:3.5.1-4.el9_7.x86_64                  15/18 
  Verifying        : openssl-libs-1:3.2.2-6.el9_5.1.x86_64                16/18 
  Verifying        : openssl-libs-1:3.5.1-4.el9_7.i686                    17/18 
  Verifying        : openssl-libs-1:3.2.2-6.el9_5.1.i686                  18/18 
Installed products updated.

Upgraded:
  openssl-1:3.5.1-4.el9_7.x86_64                                                
  openssl-fips-provider-3.0.7-8.el9.x86_64                                      
  openssl-fips-provider-so-3.0.7-8.el9.x86_64                                   
  openssl-libs-1:3.5.1-4.el9_7.i686                                             
  openssl-libs-1:3.5.1-4.el9_7.x86_64                                           
Installed:
  libnsl2-2.0.0-1.el9.x86_64                                                    
  libtirpc-1.3.3-9.el9.x86_64                                                   
  mpdecimal-2.5.1-3.el9.x86_64                                                  
  python3.12-3.12.12-1.el9_7.x86_64                                             
  python3.12-libs-3.12.12-1.el9_7.x86_64                                        
  python3.12-pip-23.2.1-5.el9.noarch                                            
  python3.12-pip-wheel-23.2.1-5.el9.noarch                                      
  python3.12-setuptools-68.2.2-5.el9_6.noarch                                   

Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'

+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:04:18 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.5 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.6 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ deactivate
./init_deploy.sh: line 23: deactivate: command not found
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:07:22 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  3.5 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.2 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ set +x
./init_deploy.sh: line 22: RC: unbound variable
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:27:20 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.5 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.5 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'

+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Wed Dec 24 06:32:04 PM UTC 2025
+ set -x
+ echo 'Instalacao de dependencias do sistema...'
Instalacao de dependencias do sistema...
+ dnf install -y --nodocs python3.12 python3.12-pip
Updating Subscription Management repositories.

This system has release set to 9 and it receives updates only for this release.

Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    
Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.4 kB/s | 4.5 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    
Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.1 kB/s | 4.5 kB     00:01    
EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    
Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.
Package python3.12-pip-23.2.1-5.el9.noarch is already installed.
Dependencies resolved.
Nothing to do.
Complete!
+ echo 'Instalacao de dependencias do sistema finalizada.'
Instalacao de dependencias do sistema finalizada.
+ mkdir -p /opt/SoftwareExpress/sitef-setup
+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv
+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate
++ deactivate nondestructive
++ '[' -n '' ']'
++ '[' -n '' ']'
++ hash -r
++ '[' -n '' ']'
++ unset VIRTUAL_ENV
++ unset VIRTUAL_ENV_PROMPT
++ '[' '!' nondestructive = nondestructive ']'
++ case "$(uname)" in
+++ uname
++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv
++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin
++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin
++ export PATH
++ VIRTUAL_ENV_PROMPT='(venv) '
++ export VIRTUAL_ENV_PROMPT
++ '[' -n '' ']'
++ '[' -z '' ']'
++ _OLD_VIRTUAL_PS1=
++ PS1='((venv) ) '
++ export PS1
++ hash -r
+ echo 'Instalacao de dependencias do python...'
Instalacao de dependencias do python...
+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist
Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl
ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'
---- pipeline deploy ----
