Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=deploy
export GIT_TAG=DEV000000002
export STRATEGY=deploy
== DEPLOY PIPELINE ==
BRANCH   : develop
STRATEGY : deploy
GIT_TAG  : DEV000000002
MACHINES : sitef-02
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.tjyA4IgwJ2...
Cloning into '/tmp/tmp.tjyA4IgwJ2/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 847, done.
remote: Counting objects:   0% (1/797)
remote: Counting objects:   1% (8/797)
remote: Counting objects:   2% (16/797)
remote: Counting objects:   3% (24/797)
remote: Counting objects:   4% (32/797)
remote: Counting objects:   5% (40/797)
remote: Counting objects:   6% (48/797)
remote: Counting objects:   7% (56/797)
remote: Counting objects:   8% (64/797)
remote: Counting objects:   9% (72/797)
remote: Counting objects:  10% (80/797)
remote: Counting objects:  11% (88/797)
remote: Counting objects:  12% (96/797)
remote: Counting objects:  13% (104/797)
remote: Counting objects:  14% (112/797)
remote: Counting objects:  15% (120/797)
remote: Counting objects:  16% (128/797)
remote: Counting objects:  17% (136/797)
remote: Counting objects:  18% (144/797)
remote: Counting objects:  19% (152/797)
remote: Counting objects:  20% (160/797)
remote: Counting objects:  21% (168/797)
remote: Counting objects:  22% (176/797)
remote: Counting objects:  23% (184/797)
remote: Counting objects:  24% (192/797)
remote: Counting objects:  25% (200/797)
remote: Counting objects:  26% (208/797)
remote: Counting objects:  27% (216/797)
remote: Counting objects:  28% (224/797)
remote: Counting objects:  29% (232/797)
remote: Counting objects:  30% (240/797)
remote: Counting objects:  31% (248/797)
remote: Counting objects:  32% (256/797)
remote: Counting objects:  33% (264/797)
remote: Counting objects:  34% (271/797)
remote: Counting objects:  35% (279/797)
remote: Counting objects:  36% (287/797)
remote: Counting objects:  37% (295/797)
remote: Counting objects:  38% (303/797)
remote: Counting objects:  39% (311/797)
remote: Counting objects:  40% (319/797)
remote: Counting objects:  41% (327/797)
remote: Counting objects:  42% (335/797)
remote: Counting objects:  43% (343/797)
remote: Counting objects:  44% (351/797)
remote: Counting objects:  45% (359/797)
remote: Counting objects:  46% (367/797)
remote: Counting objects:  47% (375/797)
remote: Counting objects:  48% (383/797)
remote: Counting objects:  49% (391/797)
remote: Counting objects:  50% (399/797)
remote: Counting objects:  51% (407/797)
remote: Counting objects:  52% (415/797)
remote: Counting objects:  53% (423/797)
remote: Counting objects:  54% (431/797)
remote: Counting objects:  55% (439/797)
remote: Counting objects:  56% (447/797)
remote: Counting objects:  57% (455/797)
remote: Counting objects:  58% (463/797)
remote: Counting objects:  59% (471/797)
remote: Counting objects:  60% (479/797)
remote: Counting objects:  61% (487/797)
remote: Counting objects:  62% (495/797)
remote: Counting objects:  63% (503/797)
remote: Counting objects:  64% (511/797)
remote: Counting objects:  65% (519/797)
remote: Counting objects:  66% (527/797)
remote: Counting objects:  67% (534/797)
remote: Counting objects:  68% (542/797)
remote: Counting objects:  69% (550/797)
remote: Counting objects:  70% (558/797)
remote: Counting objects:  71% (566/797)
remote: Counting objects:  72% (574/797)
remote: Counting objects:  73% (582/797)
remote: Counting objects:  74% (590/797)
remote: Counting objects:  75% (598/797)
remote: Counting objects:  76% (606/797)
remote: Counting objects:  77% (614/797)
remote: Counting objects:  78% (622/797)
remote: Counting objects:  79% (630/797)
remote: Counting objects:  80% (638/797)
remote: Counting objects:  81% (646/797)
remote: Counting objects:  82% (654/797)
remote: Counting objects:  83% (662/797)
remote: Counting objects:  84% (670/797)
remote: Counting objects:  85% (678/797)
remote: Counting objects:  86% (686/797)
remote: Counting objects:  87% (694/797)
remote: Counting objects:  88% (702/797)
remote: Counting objects:  89% (710/797)
remote: Counting objects:  90% (718/797)
remote: Counting objects:  91% (726/797)
remote: Counting objects:  92% (734/797)
remote: Counting objects:  93% (742/797)
remote: Counting objects:  94% (750/797)
remote: Counting objects:  95% (758/797)
remote: Counting objects:  96% (766/797)
remote: Counting objects:  97% (774/797)
remote: Counting objects:  98% (782/797)
remote: Counting objects:  99% (790/797)
remote: Counting objects: 100% (797/797)
remote: Counting objects: 100% (797/797), done.
remote: Compressing objects:   0% (1/782)
remote: Compressing objects:   1% (8/782)
remote: Compressing objects:   2% (16/782)
remote: Compressing objects:   3% (24/782)
remote: Compressing objects:   4% (32/782)
remote: Compressing objects:   5% (40/782)
remote: Compressing objects:   6% (47/782)
remote: Compressing objects:   7% (55/782)
remote: Compressing objects:   8% (63/782)
remote: Compressing objects:   9% (71/782)
remote: Compressing objects:  10% (79/782)
remote: Compressing objects:  11% (87/782)
remote: Compressing objects:  12% (94/782)
remote: Compressing objects:  13% (102/782)
remote: Compressing objects:  14% (110/782)
remote: Compressing objects:  15% (118/782)
remote: Compressing objects:  16% (126/782)
remote: Compressing objects:  17% (133/782)
remote: Compressing objects:  18% (141/782)
remote: Compressing objects:  19% (149/782)
remote: Compressing objects:  20% (157/782)
remote: Compressing objects:  21% (165/782)
remote: Compressing objects:  22% (173/782)
remote: Compressing objects:  23% (180/782)
remote: Compressing objects:  24% (188/782)
remote: Compressing objects:  25% (196/782)
remote: Compressing objects:  26% (204/782)
remote: Compressing objects:  27% (212/782)
remote: Compressing objects:  28% (219/782)
remote: Compressing objects:  29% (227/782)
remote: Compressing objects:  30% (235/782)
remote: Compressing objects:  31% (243/782)
remote: Compressing objects:  32% (251/782)
remote: Compressing objects:  33% (259/782)
remote: Compressing objects:  34% (266/782)
remote: Compressing objects:  35% (274/782)
remote: Compressing objects:  36% (282/782)
remote: Compressing objects:  37% (290/782)
remote: Compressing objects:  38% (298/782)
remote: Compressing objects:  39% (305/782)
remote: Compressing objects:  40% (313/782)
remote: Compressing objects:  41% (321/782)
remote: Compressing objects:  42% (329/782)
remote: Compressing objects:  43% (337/782)
remote: Compressing objects:  44% (345/782)
remote: Compressing objects:  45% (352/782)
remote: Compressing objects:  46% (360/782)
remote: Compressing objects:  47% (368/782)
remote: Compressing objects:  48% (376/782)
remote: Compressing objects:  49% (384/782)
remote: Compressing objects:  50% (391/782)
remote: Compressing objects:  51% (399/782)
remote: Compressing objects:  52% (407/782)
remote: Compressing objects:  53% (415/782)
remote: Compressing objects:  54% (423/782)
remote: Compressing objects:  55% (431/782)
remote: Compressing objects:  56% (438/782)
remote: Compressing objects:  57% (446/782)
remote: Compressing objects:  58% (454/782)
remote: Compressing objects:  59% (462/782)
remote: Compressing objects:  60% (470/782)
remote: Compressing objects:  61% (478/782)
remote: Compressing objects:  62% (485/782)
remote: Compressing objects:  63% (493/782)
remote: Compressing objects:  64% (501/782)
remote: Compressing objects:  65% (509/782)
remote: Compressing objects:  66% (517/782)
remote: Compressing objects:  67% (524/782)
remote: Compressing objects:  68% (532/782)
remote: Compressing objects:  69% (540/782)
remote: Compressing objects:  70% (548/782)
remote: Compressing objects:  71% (556/782)
remote: Compressing objects:  72% (564/782)
remote: Compressing objects:  73% (571/782)
remote: Compressing objects:  74% (579/782)
remote: Compressing objects:  75% (587/782)
remote: Compressing objects:  76% (595/782)
remote: Compressing objects:  77% (603/782)
remote: Compressing objects:  78% (610/782)
remote: Compressing objects:  79% (618/782)
remote: Compressing objects:  80% (626/782)
remote: Compressing objects:  81% (634/782)
remote: Compressing objects:  82% (642/782)
remote: Compressing objects:  83% (650/782)
remote: Compressing objects:  84% (657/782)
remote: Compressing objects:  85% (665/782)
remote: Compressing objects:  86% (673/782)
remote: Compressing objects:  87% (681/782)
remote: Compressing objects:  88% (689/782)
remote: Compressing objects:  89% (696/782)
remote: Compressing objects:  90% (704/782)
remote: Compressing objects:  91% (712/782)
remote: Compressing objects:  92% (720/782)
remote: Compressing objects:  93% (728/782)
remote: Compressing objects:  94% (736/782)
remote: Compressing objects:  95% (743/782)
remote: Compressing objects:  96% (751/782)
remote: Compressing objects:  97% (759/782)
remote: Compressing objects:  98% (767/782)
remote: Compressing objects:  99% (775/782)
remote: Compressing objects: 100% (782/782)
remote: Compressing objects: 100% (782/782), done.
Receiving objects:   0% (1/847)
Receiving objects:   1% (9/847)
Receiving objects:   2% (17/847)
Receiving objects:   3% (26/847)
Receiving objects:   4% (34/847)
Receiving objects:   5% (43/847)
Receiving objects:   6% (51/847)
Receiving objects:   7% (60/847)
Receiving objects:   8% (68/847)
Receiving objects:   9% (77/847)
Receiving objects:  10% (85/847)
Receiving objects:  11% (94/847)
Receiving objects:  12% (102/847)
Receiving objects:  13% (111/847)
Receiving objects:  14% (119/847)
Receiving objects:  15% (128/847)
Receiving objects:  16% (136/847)
Receiving objects:  17% (144/847)
Receiving objects:  18% (153/847)
Receiving objects:  19% (161/847)
Receiving objects:  20% (170/847)
Receiving objects:  21% (178/847)
Receiving objects:  22% (187/847)
Receiving objects:  23% (195/847)
Receiving objects:  24% (204/847)
Receiving objects:  25% (212/847)
Receiving objects:  26% (221/847)
Receiving objects:  27% (229/847)
Receiving objects:  28% (238/847)
Receiving objects:  29% (246/847)
Receiving objects:  30% (255/847)
Receiving objects:  31% (263/847)
Receiving objects:  32% (272/847)
Receiving objects:  33% (280/847)
Receiving objects:  34% (288/847)
Receiving objects:  35% (297/847)
Receiving objects:  36% (305/847)
Receiving objects:  37% (314/847)
Receiving objects:  38% (322/847)
Receiving objects:  39% (331/847)
Receiving objects:  40% (339/847)
Receiving objects:  41% (348/847)
Receiving objects:  42% (356/847)
Receiving objects:  43% (365/847)
Receiving objects:  44% (373/847)
Receiving objects:  45% (382/847)
Receiving objects:  46% (390/847)
Receiving objects:  47% (399/847)
Receiving objects:  48% (407/847)
Receiving objects:  49% (416/847)
Receiving objects:  50% (424/847)
Receiving objects:  51% (432/847)
Receiving objects:  52% (441/847)
Receiving objects:  53% (449/847)
Receiving objects:  54% (458/847)
Receiving objects:  55% (466/847)
Receiving objects:  56% (475/847)
Receiving objects:  57% (483/847)
Receiving objects:  58% (492/847)
Receiving objects:  59% (500/847)
Receiving objects:  60% (509/847)
Receiving objects:  61% (517/847)
Receiving objects:  62% (526/847)
Receiving objects:  63% (534/847)
Receiving objects:  64% (543/847)
Receiving objects:  65% (551/847)
Receiving objects:  66% (560/847)
Receiving objects:  67% (568/847)
Receiving objects:  68% (576/847)
Receiving objects:  69% (585/847)
Receiving objects:  70% (593/847)
Receiving objects:  71% (602/847)
Receiving objects:  72% (610/847)
Receiving objects:  73% (619/847)
Receiving objects:  74% (627/847)
Receiving objects:  75% (636/847)
Receiving objects:  76% (644/847)
Receiving objects:  77% (653/847)
Receiving objects:  78% (661/847)
Receiving objects:  79% (670/847)
Receiving objects:  80% (678/847)
Receiving objects:  81% (687/847)
Receiving objects:  82% (695/847)
Receiving objects:  83% (704/847)
Receiving objects:  84% (712/847)
remote: Total 847 (delta 533), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (720/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  86% (729/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  87% (737/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  88% (746/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  89% (754/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  90% (763/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  91% (771/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  92% (780/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  93% (788/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  94% (797/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  95% (805/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  96% (814/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  97% (822/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  98% (831/847), 707.77 KiB | 1.35 MiB/s
Receiving objects:  99% (839/847), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (847/847), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (847/847), 925.94 KiB | 1.76 MiB/s, done.
Resolving deltas:   0% (0/552)
Resolving deltas:   1% (6/552)
Resolving deltas:   2% (12/552)
Resolving deltas:   3% (17/552)
Resolving deltas:   4% (23/552)
Resolving deltas:   5% (28/552)
Resolving deltas:   6% (34/552)
Resolving deltas:   7% (39/552)
Resolving deltas:   8% (45/552)
Resolving deltas:   9% (50/552)
Resolving deltas:  10% (56/552)
Resolving deltas:  11% (61/552)
Resolving deltas:  12% (67/552)
Resolving deltas:  13% (72/552)
Resolving deltas:  14% (78/552)
Resolving deltas:  15% (83/552)
Resolving deltas:  16% (89/552)
Resolving deltas:  17% (94/552)
Resolving deltas:  18% (101/552)
Resolving deltas:  19% (105/552)
Resolving deltas:  20% (111/552)
Resolving deltas:  21% (116/552)
Resolving deltas:  22% (122/552)
Resolving deltas:  23% (127/552)
Resolving deltas:  24% (133/552)
Resolving deltas:  25% (138/552)
Resolving deltas:  26% (144/552)
Resolving deltas:  27% (150/552)
Resolving deltas:  28% (155/552)
Resolving deltas:  29% (161/552)
Resolving deltas:  30% (166/552)
Resolving deltas:  31% (172/552)
Resolving deltas:  32% (177/552)
Resolving deltas:  33% (183/552)
Resolving deltas:  34% (188/552)
Resolving deltas:  35% (194/552)
Resolving deltas:  36% (199/552)
Resolving deltas:  37% (205/552)
Resolving deltas:  38% (210/552)
Resolving deltas:  39% (216/552)
Resolving deltas:  40% (221/552)
Resolving deltas:  41% (227/552)
Resolving deltas:  42% (232/552)
Resolving deltas:  43% (238/552)
Resolving deltas:  44% (243/552)
Resolving deltas:  45% (249/552)
Resolving deltas:  46% (254/552)
Resolving deltas:  47% (260/552)
Resolving deltas:  48% (265/552)
Resolving deltas:  49% (271/552)
Resolving deltas:  50% (276/552)
Resolving deltas:  51% (282/552)
Resolving deltas:  52% (288/552)
Resolving deltas:  53% (293/552)
Resolving deltas:  54% (299/552)
Resolving deltas:  55% (304/552)
Resolving deltas:  56% (310/552)
Resolving deltas:  57% (315/552)
Resolving deltas:  58% (321/552)
Resolving deltas:  59% (326/552)
Resolving deltas:  60% (332/552)
Resolving deltas:  61% (337/552)
Resolving deltas:  62% (343/552)
Resolving deltas:  63% (348/552)
Resolving deltas:  64% (354/552)
Resolving deltas:  65% (359/552)
Resolving deltas:  66% (365/552)
Resolving deltas:  67% (370/552)
Resolving deltas:  68% (376/552)
Resolving deltas:  69% (381/552)
Resolving deltas:  70% (387/552)
Resolving deltas:  71% (392/552)
Resolving deltas:  72% (398/552)
Resolving deltas:  73% (403/552)
Resolving deltas:  74% (409/552)
Resolving deltas:  75% (414/552)
Resolving deltas:  76% (420/552)
Resolving deltas:  77% (426/552)
Resolving deltas:  78% (431/552)
Resolving deltas:  79% (437/552)
Resolving deltas:  80% (442/552)
Resolving deltas:  81% (448/552)
Resolving deltas:  82% (453/552)
Resolving deltas:  83% (459/552)
Resolving deltas:  84% (464/552)
Resolving deltas:  85% (470/552)
Resolving deltas:  86% (475/552)
Resolving deltas:  87% (481/552)
Resolving deltas:  88% (486/552)
Resolving deltas:  89% (493/552)
Resolving deltas:  90% (497/552)
Resolving deltas:  91% (503/552)
Resolving deltas:  92% (508/552)
Resolving deltas:  93% (514/552)
Resolving deltas:  94% (519/552)
Resolving deltas:  95% (525/552)
Resolving deltas:  96% (531/552)
Resolving deltas:  97% (536/552)
Resolving deltas:  98% (541/552)
Resolving deltas:  99% (547/552)
Resolving deltas: 100% (552/552)
Resolving deltas: 100% (552/552), done.
====================================================
== DEPLOY -> sitef-02
====================================================
bash: line 78: FILESTORE_ENV: unbound variable
Command finished with status FAILURE


## Segue script de como esta no harness

#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
STRATEGY="${STRATEGY:-deploy}"      # deploy | rollback
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"
HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0}"

echo "== DEPLOY PIPELINE =="
echo "BRANCH   : ${BRANCH}"
echo "STRATEGY : ${STRATEGY}"
echo "GIT_TAG  : ${GIT_TAG}"
echo "MACHINES : ${MACHINES}"
echo

if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

# Normaliza lista:
# - aceita "sitef-01.yml", "sitef-01.yaml"
# - aceita separado por espaço ou vírgula
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${m}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}" \
    -e "filestore_env=${FILESTORE_ENV}" \
    -e "harness_x_api_key=${HARNESS_X_API_KEY}" 
done

echo
echo "== Pipeline finalizada com sucesso =="

# deploy_per_machine.yml
---
# =====================================================================================
# DEPLOY POR MÁQUINA
# Chamado por deploy_from_status.yml (PIPELINE 2)
#
# Responsabilidades:
# - Resolver machine_file (fallback)
# - Carregar machine_cfg + package_cfg
# - Montar env_vars efetivo
# - Rodar init_deploy.sh no host (com tee)
# - Ler deploy.txt e init_deploy.log
# - Gerar status.json local com append em history (deploy:queued -> deploy:ok|deploy:error)
# - Manter pipeline.log cumulativo local (append) e subir SEMPRE o conteúdo inteiro (efeito "append" no filestore)
# - Upload JSON + LOG para Harness File Store (via harness_filestore_upload.yml)
# - Falhar pipeline apenas DEPOIS do upload
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Paths e defaults
# -----------------------------------------------------------------------------
- name: "Deploy | Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ (machine_name | string | trim) }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_logs_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/logs"
    host_deploy_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log"

- name: "Deploy | Definir diretórios principais do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 2) Resolver machine_file (mesma estratégia do predeploy/rollback)
# -----------------------------------------------------------------------------
- name: "Deploy | Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Deploy | Verificar quais candidatos existem"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Deploy | Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

- name: "Deploy | Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: |
      [deploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 3) Carregar machine_cfg + package_cfg
# -----------------------------------------------------------------------------
- name: "Deploy | Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Deploy | Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Deploy | Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 4) SSH e host dinâmico
# -----------------------------------------------------------------------------
- name: "Deploy | Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default(machine_cfg.target_user | default('ec2-user')) }}"

- name: "Deploy | Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Deploy | Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{ target_ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

- name: "Deploy | Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 5) Montar env final (pacote + máquina + extras)
# -----------------------------------------------------------------------------
- name: "Deploy | Montar env final"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': (machine_cfg.host | default(machine_cfg.ip | default(''))),
            'DEPLOYMENT_REF': (deployment_ref | default('')),
            'PACKAGE_NAME': (machine_cfg.package | default('')),
            'ROLLBACK_PACKAGE': (machine_cfg.rollback | default(''))
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 6) Status file local + paths lógicos do filestore
# -----------------------------------------------------------------------------
- name: "Deploy | Definir arquivos de status/log local"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"

    # mantém hierarquia "dev/DEV000000007/" (deployment_ref como veio)
    env_folder: "{{ filestore_env | lower }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    deployment_ref_lower: "{{ deployment_ref | lower }}"

    filestore_json_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}.json"
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}.log"

- name: "Deploy | Garantir diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

# -----------------------------------------------------------------------------
# 6.1) Ler status existente (pra append no JSON)
# -----------------------------------------------------------------------------
- name: "Deploy | Verificar se status.json já existe (para append)"
  ansible.builtin.stat:
    path: "{{ status_file }}"
  register: status_stat

- name: "Deploy | Ler status.json existente (se existir)"
  ansible.builtin.slurp:
    path: "{{ status_file }}"
  register: status_slurp
  when: status_stat.stat.exists

- name: "Deploy | Parse do status existente (ou base vazio)"
  ansible.builtin.set_fact:
    status_obj: >-
      {{
        (status_slurp.content | b64decode | from_json)
          if (status_stat.stat.exists | default(false))
          else {}
      }}

# -----------------------------------------------------------------------------
# 6.2) Escrever/atualizar status inicial (deploy:queued) + append no history[]
# -----------------------------------------------------------------------------
- name: "Deploy | Atualizar status (deploy:queued) com append em history"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_obj
          | combine({
              "machine": current_machine,
              "host": (machine_cfg.host | default(machine_cfg.ip | default(''))),
              "package": (machine_cfg.package | default('')),
              "rollback": (machine_cfg.rollback | default('')),
              "deployment_ref": (deployment_ref | default('')),
              "log_path": filestore_log_path,
              "status": "deploy:queued",
              "timestamp": ansible_date_time.iso8601,
              "history": (
                (status_obj.history | default([]))
                + [ {
                      "stage": "deploy",
                      "status": "deploy:queued",
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# 7) Verificar script e executar deploy (com tee -a no host)
# -----------------------------------------------------------------------------
- name: "Deploy | Verificar init_deploy.sh no host"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ current_machine }}"
  register: deploy_script_stat

- name: "Deploy | Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

# Rodar mesmo se der erro para subir status+log e só depois falhar
- name: "Deploy | Executar init_deploy.sh no host (com tee)"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ host_deploy_scripts }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&1 | tee -a "{{ host_deploy_init_log }}"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true
  changed_when: true

- name: "Deploy | Ler deploy.txt do host (se existir)"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_txt
  changed_when: false
  failed_when: false

- name: "Deploy | Ler init_deploy.log do host (se existir)"
  become: true
  ansible.builtin.shell: |
    tail -n 2000 "{{ host_deploy_init_log }}" 2>/dev/null || echo "init_deploy.log nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_init_log_tail
  changed_when: false
  failed_when: false

# -----------------------------------------------------------------------------
# 8) Montar conteúdo do log padronizado (pipeline deploy)
# -----------------------------------------------------------------------------
- name: "Deploy | Montar conteúdo do log do DEPLOY"
  ansible.builtin.set_fact:
    deploy_log_content: |
      ---- pipeline deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=deploy
      rc={{ deploy_result.rc | default('') }}
      ts={{ ansible_date_time.iso8601 }}

      ---- stdout (ansible) ----
      {{ deploy_result.stdout | default('') }}

      ---- stderr (ansible) ----
      {{ deploy_result.stderr | default('') }}

      ---- init_deploy.log (tail) ----
      {{ deploy_init_log_tail.stdout | default('') }}

      ---- deploy.txt ----
      {{ deploy_txt.stdout | default('') }}
      ---- pipeline deploy ----

# -----------------------------------------------------------------------------
# 9) Atualizar status final (deploy:ok / deploy:error) com append em history[]
# -----------------------------------------------------------------------------
- name: "Deploy | Ler status.json atual (antes do append final)"
  ansible.builtin.slurp:
    path: "{{ status_file }}"
  register: status_after_queued_slurp

- name: "Deploy | Parse do status atual"
  ansible.builtin.set_fact:
    status_now: "{{ status_after_queued_slurp.content | b64decode | from_json }}"

- name: "Deploy | Atualizar status final (append em history)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_now
          | combine({
              "status": ("deploy:ok" if (deploy_result.rc | default(1)) == 0 else "deploy:error"),
              "timestamp": ansible_date_time.iso8601,
              "rc": (deploy_result.rc | default(1)),
              "history": (
                (status_now.history | default([]))
                + [ {
                      "stage": "deploy",
                      "status": ("deploy:ok" if (deploy_result.rc | default(1)) == 0 else "deploy:error"),
                      "rc": (deploy_result.rc | default(1)),
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# 9.5) Append no LOG cumulativo local (controller) e preparar conteúdo p/ upload
# -----------------------------------------------------------------------------
- name: "Deploy | Garantir arquivo de log cumulativo"
  ansible.builtin.file:
    path: "{{ pipeline_log_file }}"
    state: touch
    mode: "0644"

- name: "Deploy | Append do bloco de log do deploy no log cumulativo"
  ansible.builtin.blockinfile:
    path: "{{ pipeline_log_file }}"
    marker: ""
    insertafter: EOF
    block: |
      {{ deploy_log_content }}
  changed_when: true

- name: "Deploy | Carregar conteúdo completo do log cumulativo (para upload)"
  ansible.builtin.set_fact:
    log_content_to_upload: "{{ lookup('ansible.builtin.file', pipeline_log_file) }}"

# -----------------------------------------------------------------------------
# 10) Upload JSON + LOG para Harness File Store
# -----------------------------------------------------------------------------
- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ status_file }}"

    # SUBIR O LOG COMPLETO (cumulativo) => efeito "append" no filestore
    log_content: "{{ log_content_to_upload }}"

    # IMPORTANTE: seu harness_filestore_upload.yml usa parentIdentifier={{ deployment_ref_folder }}
    env_folder: "{{ env_folder }}"
    deployment_ref_folder: "{{ deployment_ref }}"

    # tag no padrão: dev000000007:deploy:ok | dev000000007:deploy:error
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}

    # (opcional) outras tags extras
    extra_tags: []

# -----------------------------------------------------------------------------
# 11) Falhar pipeline se deploy retornou erro (depois do upload)
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: (deploy_result.rc | default(1)) != 0
