rollback
- exportar as variáveis em tempo de execução
- limpar a pasta /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- jogar o package para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package/
- baixar novamente dos components do nexus para /opt/SoftwareExpress/sitef-pipeline/rollback/  -->> Mudar para /opt/SoftwareExpress/sitef-pipeline/rollback/components/(path do nexus especifico. ex: package/linux/sitef-core-0.0.1-0.rpm)
- copiar o script para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- entrar na pasta do scripts e chamar o init_rollback.sh
