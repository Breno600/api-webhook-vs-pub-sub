# 1) confirma IP
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
