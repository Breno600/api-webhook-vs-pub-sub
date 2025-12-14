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
 
