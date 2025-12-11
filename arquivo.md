sudo ls -laR /opt/SoftwareExpress/sitef-pipeline/deploy
sudo ls -laR /opt/SoftwareExpress/sitef-pipeline/deploy/components
sudo ls -laR /opt/SoftwareExpress/sitef-pipeline/deploy/scripts

rpm -qa | grep -i sitef
# ou, mais específico:
rpm -qa | grep -i sitef-core
rpm -qi sitef-core
sudo systemctl status sitef-core.service
sudo cat /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt
