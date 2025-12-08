#!/usr/bin/env bash
set -euo pipefail

# =========================
# Parâmetros da pipeline
# =========================
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
BRANCH="${BRANCH:-develop}"
ACTION="<+pipeline.variables.STRATEGY>"  # deploy | rollback
GIT_TAG="<+pipeline.variables.GIT_TAG>"
MACHINE="<+pipeline.variables.MACHINE>"

# Repo
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

echo "== DEPLOY PIPELINE =="
echo "BRANCH         : ${BRANCH}"
echo "STRATEGY         : ${STRATEGY}"
echo "GIT_TAG : <+pipeline.variables.GIT_TAG>"
echo "MACHINE : <+pipeline.variables.MACHINE>"
echo

# =========================
# Garantir chave no bastion/ansible host
# (você disse que a chave usada é id_rsa)
# =========================
if [[ ! -f "${HOME}/.ssh/id_rsa" ]]; then
  echo "ERRO: chave privada não encontrada em ${HOME}/.ssh/id_rsa"
  exit 1
fi
chmod 600 "${HOME}/.ssh/id_rsa"

# =========================
# Clonar repo
# =========================
WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "Repositorio em $(pwd)"
git rev-parse --abbrev-ref HEAD
git rev-parse HEAD
echo

if [[ -z "${MACHINE}" ]]; then
  echo "ERRO: nenhuma máquina encontrada em ${EXECUTION_FILE}"
  exit 1
fi

echo "Maquinas encontrada:"
echo "${MACHINE}"
echo

# =========================
# Rodar deploy por máquina
# =========================
for MACHINE in ${MACHINE}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${MACHINE}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${MACHINE}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "action=${STRATEGY}"
done

echo
echo "== Pipeline finalizada com sucesso =="


logs:
Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-01
export MACHINE=sitef-01
export ACTION=deploy
export GIT_TAG=DEV0001
export STRATEGY=deploy
== DEPLOY PIPELINE ==
BRANCH         : develop
STRATEGY         : deploy
GIT_TAG : null
MACHINE : null
Clonando repo em /tmp/tmp.2VxcfqsQ7r...
Cloning into '/tmp/tmp.2VxcfqsQ7r/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 419, done.
remote: Counting objects:   0% (1/369)
remote: Counting objects:   1% (4/369)
remote: Counting objects:   2% (8/369)
remote: Counting objects:   3% (12/369)
remote: Counting objects:   4% (15/369)
remote: Counting objects:   5% (19/369)
remote: Counting objects:   6% (23/369)
remote: Counting objects:   7% (26/369)
remote: Counting objects:   8% (30/369)
remote: Counting objects:   9% (34/369)
remote: Counting objects:  10% (37/369)
remote: Counting objects:  11% (41/369)
remote: Counting objects:  12% (45/369)
remote: Counting objects:  13% (48/369)
remote: Counting objects:  14% (52/369)
remote: Counting objects:  15% (56/369)
remote: Counting objects:  16% (60/369)
remote: Counting objects:  17% (63/369)
remote: Counting objects:  18% (67/369)
remote: Counting objects:  19% (71/369)
remote: Counting objects:  20% (74/369)
remote: Counting objects:  21% (78/369)
remote: Counting objects:  22% (82/369)
remote: Counting objects:  23% (85/369)
remote: Counting objects:  24% (89/369)
remote: Counting objects:  25% (93/369)
remote: Counting objects:  26% (96/369)
remote: Counting objects:  27% (100/369)
remote: Counting objects:  28% (104/369)
remote: Counting objects:  29% (108/369)
remote: Counting objects:  30% (111/369)
remote: Counting objects:  31% (115/369)
remote: Counting objects:  32% (119/369)
remote: Counting objects:  33% (122/369)
remote: Counting objects:  34% (126/369)
remote: Counting objects:  35% (130/369)
remote: Counting objects:  36% (133/369)
remote: Counting objects:  37% (137/369)
remote: Counting objects:  38% (141/369)
remote: Counting objects:  39% (144/369)
remote: Counting objects:  40% (148/369)
remote: Counting objects:  41% (152/369)
remote: Counting objects:  42% (155/369)
remote: Counting objects:  43% (159/369)
remote: Counting objects:  44% (163/369)
remote: Counting objects:  45% (167/369)
remote: Counting objects:  46% (170/369)
remote: Counting objects:  47% (174/369)
remote: Counting objects:  48% (178/369)
remote: Counting objects:  49% (181/369)
remote: Counting objects:  50% (185/369)
remote: Counting objects:  51% (189/369)
remote: Counting objects:  52% (192/369)
remote: Counting objects:  53% (196/369)
remote: Counting objects:  54% (200/369)
remote: Counting objects:  55% (203/369)
remote: Counting objects:  56% (207/369)
remote: Counting objects:  57% (211/369)
remote: Counting objects:  58% (215/369)
remote: Counting objects:  59% (218/369)
remote: Counting objects:  60% (222/369)
remote: Counting objects:  61% (226/369)
remote: Counting objects:  62% (229/369)
remote: Counting objects:  63% (233/369)
remote: Counting objects:  64% (237/369)
remote: Counting objects:  65% (240/369)
remote: Counting objects:  66% (244/369)
remote: Counting objects:  67% (248/369)
remote: Counting objects:  68% (251/369)
remote: Counting objects:  69% (255/369)
remote: Counting objects:  70% (259/369)
remote: Counting objects:  71% (262/369)
remote: Counting objects:  72% (266/369)
remote: Counting objects:  73% (270/369)
remote: Counting objects:  74% (274/369)
remote: Counting objects:  75% (277/369)
remote: Counting objects:  76% (281/369)
remote: Counting objects:  77% (285/369)
remote: Counting objects:  78% (288/369)
remote: Counting objects:  79% (292/369)
remote: Counting objects:  80% (296/369)
remote: Counting objects:  81% (299/369)
remote: Counting objects:  82% (303/369)
remote: Counting objects:  83% (307/369)
remote: Counting objects:  84% (310/369)
remote: Counting objects:  85% (314/369)
remote: Counting objects:  86% (318/369)
remote: Counting objects:  87% (322/369)
remote: Counting objects:  88% (325/369)
remote: Counting objects:  89% (329/369)
remote: Counting objects:  90% (333/369)
remote: Counting objects:  91% (336/369)
remote: Counting objects:  92% (340/369)
remote: Counting objects:  93% (344/369)
remote: Counting objects:  94% (347/369)
remote: Counting objects:  95% (351/369)
remote: Counting objects:  96% (355/369)
remote: Counting objects:  97% (358/369)
remote: Counting objects:  98% (362/369)
remote: Counting objects:  99% (366/369)
remote: Counting objects: 100% (369/369)
remote: Counting objects: 100% (369/369), done.
remote: Compressing objects:   0% (1/356)
remote: Compressing objects:   1% (4/356)
remote: Compressing objects:   2% (8/356)
remote: Compressing objects:   3% (11/356)
remote: Compressing objects:   4% (15/356)
remote: Compressing objects:   5% (18/356)
remote: Compressing objects:   6% (22/356)
remote: Compressing objects:   7% (25/356)
remote: Compressing objects:   8% (29/356)
remote: Compressing objects:   9% (33/356)
remote: Compressing objects:  10% (36/356)
remote: Compressing objects:  11% (40/356)
remote: Compressing objects:  12% (43/356)
remote: Compressing objects:  13% (47/356)
remote: Compressing objects:  14% (50/356)
remote: Compressing objects:  15% (54/356)
remote: Compressing objects:  16% (57/356)
remote: Compressing objects:  17% (61/356)
remote: Compressing objects:  18% (65/356)
remote: Compressing objects:  19% (68/356)
remote: Compressing objects:  20% (72/356)
remote: Compressing objects:  21% (75/356)
remote: Compressing objects:  22% (79/356)
remote: Compressing objects:  23% (82/356)
remote: Compressing objects:  24% (86/356)
remote: Compressing objects:  25% (89/356)
remote: Compressing objects:  26% (93/356)
remote: Compressing objects:  27% (97/356)
remote: Compressing objects:  28% (100/356)
remote: Compressing objects:  29% (104/356)
remote: Compressing objects:  30% (107/356)
remote: Compressing objects:  31% (111/356)
remote: Compressing objects:  32% (114/356)
remote: Compressing objects:  33% (118/356)
remote: Compressing objects:  34% (122/356)
remote: Compressing objects:  35% (125/356)
remote: Compressing objects:  36% (129/356)
remote: Compressing objects:  37% (132/356)
remote: Compressing objects:  38% (136/356)
remote: Compressing objects:  39% (139/356)
remote: Compressing objects:  40% (143/356)
remote: Compressing objects:  41% (146/356)
remote: Compressing objects:  42% (150/356)
remote: Compressing objects:  43% (154/356)
remote: Compressing objects:  44% (157/356)
remote: Compressing objects:  45% (161/356)
remote: Compressing objects:  46% (164/356)
remote: Compressing objects:  47% (168/356)
remote: Compressing objects:  48% (171/356)
remote: Compressing objects:  49% (175/356)
remote: Compressing objects:  50% (178/356)
remote: Compressing objects:  51% (182/356)
remote: Compressing objects:  52% (186/356)
remote: Compressing objects:  53% (189/356)
remote: Compressing objects:  54% (193/356)
remote: Compressing objects:  55% (196/356)
remote: Compressing objects:  56% (200/356)
remote: Compressing objects:  57% (203/356)
remote: Compressing objects:  58% (207/356)
remote: Compressing objects:  59% (211/356)
remote: Compressing objects:  60% (214/356)
remote: Compressing objects:  61% (218/356)
remote: Compressing objects:  62% (221/356)
remote: Compressing objects:  63% (225/356)
remote: Compressing objects:  64% (228/356)
remote: Compressing objects:  65% (232/356)
remote: Compressing objects:  66% (235/356)
remote: Compressing objects:  67% (239/356)
remote: Compressing objects:  68% (243/356)
remote: Compressing objects:  69% (246/356)
remote: Compressing objects:  70% (250/356)
remote: Compressing objects:  71% (253/356)
remote: Compressing objects:  72% (257/356)
remote: Compressing objects:  73% (260/356)
remote: Compressing objects:  74% (264/356)
remote: Compressing objects:  75% (267/356)
remote: Compressing objects:  76% (271/356)
remote: Compressing objects:  77% (275/356)
remote: Compressing objects:  78% (278/356)
remote: Compressing objects:  79% (282/356)
remote: Compressing objects:  80% (285/356)
remote: Compressing objects:  81% (289/356)
remote: Compressing objects:  82% (292/356)
remote: Compressing objects:  83% (296/356)
remote: Compressing objects:  84% (300/356)
remote: Compressing objects:  85% (303/356)
remote: Compressing objects:  86% (307/356)
remote: Compressing objects:  87% (310/356)
remote: Compressing objects:  88% (314/356)
remote: Compressing objects:  89% (317/356)
remote: Compressing objects:  90% (321/356)
remote: Compressing objects:  91% (324/356)
remote: Compressing objects:  92% (328/356)
remote: Compressing objects:  93% (332/356)
remote: Compressing objects:  94% (335/356)
remote: Compressing objects:  95% (339/356)
remote: Compressing objects:  96% (342/356)
remote: Compressing objects:  97% (346/356)
remote: Compressing objects:  98% (349/356)
remote: Compressing objects:  99% (353/356)
remote: Compressing objects: 100% (356/356)
remote: Compressing objects: 100% (356/356), done.
remote: Total 419 (delta 208), reused 0 (delta 0), pack-reused 50
Receiving objects:   0% (1/419)
Receiving objects:   1% (5/419)
Receiving objects:   2% (9/419)
Receiving objects:   3% (13/419)
Receiving objects:   4% (17/419)
Receiving objects:   5% (21/419)
Receiving objects:   6% (26/419)
Receiving objects:   7% (30/419)
Receiving objects:   8% (34/419)
Receiving objects:   9% (38/419)
Receiving objects:  10% (42/419)
Receiving objects:  11% (47/419)
Receiving objects:  12% (51/419)
Receiving objects:  13% (55/419)
Receiving objects:  14% (59/419)
Receiving objects:  15% (63/419)
Receiving objects:  16% (68/419)
Receiving objects:  17% (72/419)
Receiving objects:  18% (76/419)
Receiving objects:  19% (80/419)
Receiving objects:  20% (84/419)
Receiving objects:  21% (88/419)
Receiving objects:  22% (93/419)
Receiving objects:  23% (97/419)
Receiving objects:  24% (101/419)
Receiving objects:  25% (105/419)
Receiving objects:  26% (109/419)
Receiving objects:  27% (114/419)
Receiving objects:  28% (118/419)
Receiving objects:  29% (122/419)
Receiving objects:  30% (126/419)
Receiving objects:  31% (130/419)
Receiving objects:  32% (135/419)
Receiving objects:  33% (139/419)
Receiving objects:  34% (143/419)
Receiving objects:  35% (147/419)
Receiving objects:  36% (151/419)
Receiving objects:  37% (156/419)
Receiving objects:  38% (160/419)
Receiving objects:  39% (164/419)
Receiving objects:  40% (168/419)
Receiving objects:  41% (172/419)
Receiving objects:  42% (176/419)
Receiving objects:  43% (181/419)
Receiving objects:  44% (185/419)
Receiving objects:  45% (189/419)
Receiving objects:  46% (193/419)
Receiving objects:  47% (197/419)
Receiving objects:  48% (202/419)
Receiving objects:  49% (206/419)
Receiving objects:  50% (210/419)
Receiving objects:  51% (214/419)
Receiving objects:  52% (218/419)
Receiving objects:  53% (223/419)
Receiving objects:  54% (227/419)
Receiving objects:  55% (231/419)
Receiving objects:  56% (235/419)
Receiving objects:  57% (239/419)
Receiving objects:  58% (244/419)
Receiving objects:  59% (248/419)
Receiving objects:  60% (252/419)
Receiving objects:  61% (256/419)
Receiving objects:  62% (260/419)
Receiving objects:  63% (264/419)
Receiving objects:  64% (269/419)
Receiving objects:  65% (273/419)
Receiving objects:  66% (277/419)
Receiving objects:  67% (281/419)
Receiving objects:  68% (285/419)
Receiving objects:  69% (290/419)
Receiving objects:  70% (294/419)
Receiving objects:  71% (298/419)
Receiving objects:  72% (302/419)
Receiving objects:  73% (306/419)
Receiving objects:  74% (311/419)
Receiving objects:  75% (315/419)
Receiving objects:  76% (319/419)
Receiving objects:  77% (323/419)
Receiving objects:  78% (327/419)
Receiving objects:  79% (332/419)
Receiving objects:  80% (336/419)
Receiving objects:  81% (340/419)
Receiving objects:  82% (344/419)
Receiving objects:  83% (348/419)
Receiving objects:  84% (352/419)
Receiving objects:  85% (357/419)
Receiving objects:  86% (361/419)
Receiving objects:  87% (365/419)
Receiving objects:  88% (369/419)
Receiving objects:  89% (373/419)
Receiving objects:  90% (378/419)
Receiving objects:  91% (382/419)
Receiving objects:  92% (386/419)
Receiving objects:  93% (390/419)
Receiving objects:  94% (394/419)
Receiving objects:  95% (399/419)
Receiving objects:  96% (403/419)
Receiving objects:  97% (407/419)
Receiving objects:  98% (411/419)
Receiving objects:  99% (415/419)
Receiving objects: 100% (419/419)
Receiving objects: 100% (419/419), 62.34 KiB | 12.47 MiB/s, done.
Resolving deltas:   0% (0/227)
Resolving deltas:   1% (3/227)
Resolving deltas:   2% (5/227)
Resolving deltas:   3% (7/227)
Resolving deltas:   4% (10/227)
Resolving deltas:   5% (12/227)
Resolving deltas:   6% (14/227)
Resolving deltas:   7% (16/227)
Resolving deltas:   8% (19/227)
Resolving deltas:   9% (21/227)
Resolving deltas:  10% (23/227)
Resolving deltas:  11% (25/227)
Resolving deltas:  12% (28/227)
Resolving deltas:  13% (30/227)
Resolving deltas:  14% (32/227)
Resolving deltas:  15% (36/227)
Resolving deltas:  16% (37/227)
Resolving deltas:  17% (39/227)
Resolving deltas:  18% (41/227)
Resolving deltas:  19% (44/227)
Resolving deltas:  20% (46/227)
Resolving deltas:  21% (48/227)
Resolving deltas:  22% (50/227)
Resolving deltas:  23% (53/227)
Resolving deltas:  24% (55/227)
Resolving deltas:  25% (57/227)
Resolving deltas:  26% (60/227)
Resolving deltas:  27% (62/227)
Resolving deltas:  28% (64/227)
Resolving deltas:  29% (67/227)
Resolving deltas:  30% (69/227)
Resolving deltas:  31% (71/227)
Resolving deltas:  32% (73/227)
Resolving deltas:  33% (75/227)
Resolving deltas:  34% (78/227)
Resolving deltas:  35% (80/227)
Resolving deltas:  36% (82/227)
Resolving deltas:  37% (84/227)
Resolving deltas:  38% (87/227)
Resolving deltas:  39% (89/227)
Resolving deltas:  40% (91/227)
Resolving deltas:  41% (94/227)
Resolving deltas:  42% (96/227)
Resolving deltas:  43% (98/227)
Resolving deltas:  44% (100/227)
Resolving deltas:  45% (103/227)
Resolving deltas:  46% (105/227)
Resolving deltas:  47% (107/227)
Resolving deltas:  48% (109/227)
Resolving deltas:  49% (112/227)
Resolving deltas:  50% (114/227)
Resolving deltas:  51% (116/227)
Resolving deltas:  52% (119/227)
Resolving deltas:  53% (121/227)
Resolving deltas:  54% (123/227)
Resolving deltas:  55% (125/227)
Resolving deltas:  56% (128/227)
Resolving deltas:  57% (130/227)
Resolving deltas:  58% (132/227)
Resolving deltas:  59% (135/227)
Resolving deltas:  60% (137/227)
Resolving deltas:  61% (139/227)
Resolving deltas:  62% (141/227)
Resolving deltas:  63% (144/227)
Resolving deltas:  64% (146/227)
Resolving deltas:  65% (148/227)
Resolving deltas:  66% (150/227)
Resolving deltas:  67% (153/227)
Resolving deltas:  68% (155/227)
Resolving deltas:  69% (157/227)
Resolving deltas:  70% (159/227)
Resolving deltas:  71% (162/227)
Resolving deltas:  72% (164/227)
Resolving deltas:  73% (166/227)
Resolving deltas:  74% (168/227)
Resolving deltas:  75% (171/227)
Resolving deltas:  76% (173/227)
Resolving deltas:  77% (175/227)
Resolving deltas:  78% (178/227)
Resolving deltas:  79% (180/227)
Resolving deltas:  80% (182/227)
Resolving deltas:  81% (184/227)
Resolving deltas:  82% (187/227)
Resolving deltas:  83% (189/227)
Resolving deltas:  84% (191/227)
Resolving deltas:  85% (193/227)
Resolving deltas:  86% (196/227)
Resolving deltas:  87% (198/227)
Resolving deltas:  88% (200/227)
Resolving deltas:  89% (203/227)
Resolving deltas:  90% (205/227)
Resolving deltas:  91% (207/227)
Resolving deltas:  92% (209/227)
Resolving deltas:  93% (212/227)
Resolving deltas:  94% (214/227)
Resolving deltas:  95% (216/227)
Resolving deltas:  96% (218/227)
Resolving deltas:  97% (221/227)
Resolving deltas:  98% (223/227)
Resolving deltas:  99% (225/227)
Resolving deltas: 100% (227/227)
Resolving deltas: 100% (227/227), done.
Repositorio em /tmp/tmp.2VxcfqsQ7r/elastic-compute-cloud-sitef
develop
b1c90f8355b37c2b28d732b03bac7318574e052f
Maquinas encontrada:
null
====================================================
== DEPLOY -> null
====================================================
[WARNING]: Found variable using reserved name: action
PLAY [[CONTROLLER] Preparar deploy/rollback de uma máquina] ********************
TASK [Validar se arquivo de status existe] *************************************
ok: [localhost]
TASK [Falhar se status da máquina não existe para essa TAG] ********************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Status nao encontrado para machine=null, tag=null em /tmp/tmp.2VxcfqsQ7r/elastic-compute-cloud-sitef/status/null/predeploy-null.json"}
PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
Command finished with status FAILURE
