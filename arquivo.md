-- pipeline

predeploy:
- git clone e entra na tag especifica como parametro
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- limpar pasta /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
- jogar o package para /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package/
- fazer o download do nexus dos componentes /opt/SoftwareExpress/sitef-pipeline/deploy/(path do nexus especifico. ex: package/linux/sitef-core-0.0.1-0.rpm)
- copiar o script para /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/
- entrar na pasta do scripts e chamar o init_parallel.sh
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

deploy:
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- entrar na pasta do scripts e chamar o init_deploy.sh  /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

rollback
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- limpar a pasta /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- jogar o package para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package/
- baixar novamente dos components do nexus para /opt/SoftwareExpress/sitef-pipeline/rollback/
- copiar o script para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- entrar na pasta do scripts e chamar o init_rollback.sh
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

harness-file-store:
dev/DEV000000007/dev000000007-sitef-01-dev.json  -- se der erro na pipeline 01 ent coloca dev000000007:pre-deploy:error, se fazer o rerun da pipeline 01 e ir com sucesso então atualizada a tag de pre-deploy para ok e adiciona a tag dev000000007:deploy:pending e se der certo dev000000007:pre-deploy:ok e dev000000007:deploy:pending, pipeline 02 atualizo apenas o status do deploy dev000000007:deploy:ok se der problema ent dev000000007:deploy:error, se precisar de rollback ent adiciona a tag dev000000007:rollback:status(ok, error)

{
    "machine": "sitef-01",
    "host": "100.99.41.58",
    "package": "sitef-core-0.0.2-0",
    "rollback": "",
    "status": "success",
    "deployment_ref": "DEV000000007",
    "timestamp": "2025-12-08T18:15:52Z"
    "log_path": "dev/DEV000000007/dev000000007-sitef-01-dev.log"
}
dev/DEV000000007/dev000000007-sitef-01-dev.log

---- pipeline pre deploy ----
log
---- pipeline pre deploy ----

---- pipeline deploy -----
log
---- pipeline deploy -----

dev/DEV000000007/dev000000007-sitef-02-dev.json
dev/DEV000000007/dev000000007-sitef-02-dev.log

cat/CAT000000007/
prd/PRD000000007-(change_id)/sitef-01.json
