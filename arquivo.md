## sitef-01.yml
host: "100.99.41.58"

package: "sitef-core-0.0.1-0"

env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"

tags:
  - name: sitef-2
  - environment: aws-dev
  - aws-account: 942632789850

## package/sitef-core-0.0.1-0.yml
script: deploy-sitef-0.0.1

components:
  - packages/linux/sitef-core-0.0.1-0.x86_64.rpm

##scripts\deploy-sitef-0.0.1\init_parallel.sh
#!/bin/bash
echo "Executando backup..." > parallel.txt
echo "Backup finalizado" >> parallel.txt
