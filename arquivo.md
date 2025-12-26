## Log na pipeline deploy
Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=deploy
export GIT_TAG=DEV000002
export STRATEGY=deploy
== DEPLOY/ROLLBACK PIPELINE ==
BRANCH            : develop-testes
STRATEGY          : deploy
GIT_TAG           : DEV000002
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000002
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.tO7pF4XvNQ...
Cloning into '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1242, done.
remote: Counting objects:   0% (1/1192)
remote: Counting objects:   1% (12/1192)
remote: Counting objects:   2% (24/1192)
remote: Counting objects:   3% (36/1192)
remote: Counting objects:   4% (48/1192)
remote: Counting objects:   5% (60/1192)
remote: Counting objects:   6% (72/1192)
remote: Counting objects:   7% (84/1192)
remote: Counting objects:   8% (96/1192)
remote: Counting objects:   9% (108/1192)
remote: Counting objects:  10% (120/1192)
remote: Counting objects:  11% (132/1192)
remote: Counting objects:  12% (144/1192)
remote: Counting objects:  13% (155/1192)
remote: Counting objects:  14% (167/1192)
remote: Counting objects:  15% (179/1192)
remote: Counting objects:  16% (191/1192)
remote: Counting objects:  17% (203/1192)
remote: Counting objects:  18% (215/1192)
remote: Counting objects:  19% (227/1192)
remote: Counting objects:  20% (239/1192)
remote: Counting objects:  21% (251/1192)
remote: Counting objects:  22% (263/1192)
remote: Counting objects:  23% (275/1192)
remote: Counting objects:  24% (287/1192)
remote: Counting objects:  25% (298/1192)
remote: Counting objects:  26% (310/1192)
remote: Counting objects:  27% (322/1192)
remote: Counting objects:  28% (334/1192)
remote: Counting objects:  29% (346/1192)
remote: Counting objects:  30% (358/1192)
remote: Counting objects:  31% (370/1192)
remote: Counting objects:  32% (382/1192)
remote: Counting objects:  33% (394/1192)
remote: Counting objects:  34% (406/1192)
remote: Counting objects:  35% (418/1192)
remote: Counting objects:  36% (430/1192)
remote: Counting objects:  37% (442/1192)
remote: Counting objects:  38% (453/1192)
remote: Counting objects:  39% (465/1192)
remote: Counting objects:  40% (477/1192)
remote: Counting objects:  41% (489/1192)
remote: Counting objects:  42% (501/1192)
remote: Counting objects:  43% (513/1192)
remote: Counting objects:  44% (525/1192)
remote: Counting objects:  45% (537/1192)
remote: Counting objects:  46% (549/1192)
remote: Counting objects:  47% (561/1192)
remote: Counting objects:  48% (573/1192)
remote: Counting objects:  49% (585/1192)
remote: Counting objects:  50% (596/1192)
remote: Counting objects:  51% (608/1192)
remote: Counting objects:  52% (620/1192)
remote: Counting objects:  53% (632/1192)
remote: Counting objects:  54% (644/1192)
remote: Counting objects:  55% (656/1192)
remote: Counting objects:  56% (668/1192)
remote: Counting objects:  57% (680/1192)
remote: Counting objects:  58% (692/1192)
remote: Counting objects:  59% (704/1192)
remote: Counting objects:  60% (716/1192)
remote: Counting objects:  61% (728/1192)
remote: Counting objects:  62% (740/1192)
remote: Counting objects:  63% (751/1192)
remote: Counting objects:  64% (763/1192)
remote: Counting objects:  65% (775/1192)
remote: Counting objects:  66% (787/1192)
remote: Counting objects:  67% (799/1192)
remote: Counting objects:  68% (811/1192)
remote: Counting objects:  69% (823/1192)
remote: Counting objects:  70% (835/1192)
remote: Counting objects:  71% (847/1192)
remote: Counting objects:  72% (859/1192)
remote: Counting objects:  73% (871/1192)
remote: Counting objects:  74% (883/1192)
remote: Counting objects:  75% (894/1192)
remote: Counting objects:  76% (906/1192)
remote: Counting objects:  77% (918/1192)
remote: Counting objects:  78% (930/1192)
remote: Counting objects:  79% (942/1192)
remote: Counting objects:  80% (954/1192)
remote: Counting objects:  81% (966/1192)
remote: Counting objects:  82% (978/1192)
remote: Counting objects:  83% (990/1192)
remote: Counting objects:  84% (1002/1192)
remote: Counting objects:  85% (1014/1192)
remote: Counting objects:  86% (1026/1192)
remote: Counting objects:  87% (1038/1192)
remote: Counting objects:  88% (1049/1192)
remote: Counting objects:  89% (1061/1192)
remote: Counting objects:  90% (1073/1192)
remote: Counting objects:  91% (1085/1192)
remote: Counting objects:  92% (1097/1192)
remote: Counting objects:  93% (1109/1192)
remote: Counting objects:  94% (1121/1192)
remote: Counting objects:  95% (1133/1192)
remote: Counting objects:  96% (1145/1192)
remote: Counting objects:  97% (1157/1192)
remote: Counting objects:  98% (1169/1192)
remote: Counting objects:  99% (1181/1192)
remote: Counting objects: 100% (1192/1192)
remote: Counting objects: 100% (1192/1192), done.
remote: Compressing objects:   0% (1/427)
remote: Compressing objects:   1% (5/427)
remote: Compressing objects:   2% (9/427)
remote: Compressing objects:   3% (13/427)
remote: Compressing objects:   4% (18/427)
remote: Compressing objects:   5% (22/427)
remote: Compressing objects:   6% (26/427)
remote: Compressing objects:   7% (30/427)
remote: Compressing objects:   8% (35/427)
remote: Compressing objects:   9% (39/427)
remote: Compressing objects:  10% (43/427)
remote: Compressing objects:  11% (47/427)
remote: Compressing objects:  12% (52/427)
remote: Compressing objects:  13% (56/427)
remote: Compressing objects:  14% (60/427)
remote: Compressing objects:  15% (65/427)
remote: Compressing objects:  16% (69/427)
remote: Compressing objects:  17% (73/427)
remote: Compressing objects:  18% (77/427)
remote: Compressing objects:  19% (82/427)
remote: Compressing objects:  20% (86/427)
remote: Compressing objects:  21% (90/427)
remote: Compressing objects:  22% (94/427)
remote: Compressing objects:  23% (99/427)
remote: Compressing objects:  24% (103/427)
remote: Compressing objects:  25% (107/427)
remote: Compressing objects:  26% (112/427)
remote: Compressing objects:  27% (116/427)
remote: Compressing objects:  28% (120/427)
remote: Compressing objects:  29% (124/427)
remote: Compressing objects:  30% (129/427)
remote: Compressing objects:  31% (133/427)
remote: Compressing objects:  32% (137/427)
remote: Compressing objects:  33% (141/427)
remote: Compressing objects:  34% (146/427)
remote: Compressing objects:  35% (150/427)
remote: Compressing objects:  36% (154/427)
remote: Compressing objects:  37% (158/427)
remote: Compressing objects:  38% (163/427)
remote: Compressing objects:  39% (167/427)
remote: Compressing objects:  40% (171/427)
remote: Compressing objects:  41% (176/427)
remote: Compressing objects:  42% (180/427)
remote: Compressing objects:  43% (184/427)
remote: Compressing objects:  44% (188/427)
remote: Compressing objects:  45% (193/427)
remote: Compressing objects:  46% (197/427)
remote: Compressing objects:  47% (201/427)
remote: Compressing objects:  48% (205/427)
remote: Compressing objects:  49% (210/427)
remote: Compressing objects:  50% (214/427)
remote: Compressing objects:  51% (218/427)
remote: Compressing objects:  52% (223/427)
remote: Compressing objects:  53% (227/427)
remote: Compressing objects:  54% (231/427)
remote: Compressing objects:  55% (235/427)
remote: Compressing objects:  56% (240/427)
remote: Compressing objects:  57% (244/427)
remote: Compressing objects:  58% (248/427)
remote: Compressing objects:  59% (252/427)
remote: Compressing objects:  60% (257/427)
remote: Compressing objects:  61% (261/427)
remote: Compressing objects:  62% (265/427)
remote: Compressing objects:  63% (270/427)
remote: Compressing objects:  64% (274/427)
remote: Compressing objects:  65% (278/427)
remote: Compressing objects:  66% (282/427)
remote: Compressing objects:  67% (287/427)
remote: Compressing objects:  68% (291/427)
remote: Compressing objects:  69% (295/427)
remote: Compressing objects:  70% (299/427)
remote: Compressing objects:  71% (304/427)
remote: Compressing objects:  72% (308/427)
remote: Compressing objects:  73% (312/427)
remote: Compressing objects:  74% (316/427)
remote: Compressing objects:  75% (321/427)
remote: Compressing objects:  76% (325/427)
remote: Compressing objects:  77% (329/427)
remote: Compressing objects:  78% (334/427)
remote: Compressing objects:  79% (338/427)
remote: Compressing objects:  80% (342/427)
remote: Compressing objects:  81% (346/427)
remote: Compressing objects:  82% (351/427)
remote: Compressing objects:  83% (355/427)
remote: Compressing objects:  84% (359/427)
remote: Compressing objects:  85% (363/427)
remote: Compressing objects:  86% (368/427)
remote: Compressing objects:  87% (372/427)
remote: Compressing objects:  88% (376/427)
remote: Compressing objects:  89% (381/427)
remote: Compressing objects:  90% (385/427)
remote: Compressing objects:  91% (389/427)
remote: Compressing objects:  92% (393/427)
remote: Compressing objects:  93% (398/427)
remote: Compressing objects:  94% (402/427)
remote: Compressing objects:  95% (406/427)
remote: Compressing objects:  96% (410/427)
remote: Compressing objects:  97% (415/427)
remote: Compressing objects:  98% (419/427)
remote: Compressing objects:  99% (423/427)
remote: Compressing objects: 100% (427/427)
remote: Compressing objects: 100% (427/427), done.
Receiving objects:   0% (1/1242)
Receiving objects:   1% (13/1242)
Receiving objects:   2% (25/1242)
Receiving objects:   3% (38/1242)
Receiving objects:   4% (50/1242)
Receiving objects:   5% (63/1242)
Receiving objects:   6% (75/1242)
Receiving objects:   7% (87/1242)
Receiving objects:   8% (100/1242)
Receiving objects:   9% (112/1242)
Receiving objects:  10% (125/1242)
Receiving objects:  11% (137/1242)
Receiving objects:  12% (150/1242)
Receiving objects:  13% (162/1242)
Receiving objects:  14% (174/1242)
Receiving objects:  15% (187/1242)
Receiving objects:  16% (199/1242)
Receiving objects:  17% (212/1242)
Receiving objects:  18% (224/1242)
Receiving objects:  19% (236/1242)
Receiving objects:  20% (249/1242)
Receiving objects:  21% (261/1242)
Receiving objects:  22% (274/1242)
Receiving objects:  23% (286/1242)
Receiving objects:  24% (299/1242)
Receiving objects:  25% (311/1242)
Receiving objects:  26% (323/1242)
Receiving objects:  27% (336/1242)
Receiving objects:  28% (348/1242)
Receiving objects:  29% (361/1242)
Receiving objects:  30% (373/1242)
Receiving objects:  31% (386/1242)
Receiving objects:  32% (398/1242)
Receiving objects:  33% (410/1242)
Receiving objects:  34% (423/1242)
Receiving objects:  35% (435/1242)
Receiving objects:  36% (448/1242)
Receiving objects:  37% (460/1242)
Receiving objects:  38% (472/1242)
Receiving objects:  39% (485/1242)
Receiving objects:  40% (497/1242)
Receiving objects:  41% (510/1242)
Receiving objects:  42% (522/1242)
Receiving objects:  43% (535/1242)
Receiving objects:  44% (547/1242)
Receiving objects:  45% (559/1242)
Receiving objects:  46% (572/1242)
Receiving objects:  47% (584/1242)
Receiving objects:  48% (597/1242)
Receiving objects:  49% (609/1242)
Receiving objects:  50% (621/1242)
Receiving objects:  51% (634/1242)
Receiving objects:  52% (646/1242)
Receiving objects:  53% (659/1242)
Receiving objects:  54% (671/1242)
Receiving objects:  55% (684/1242)
Receiving objects:  56% (696/1242)
Receiving objects:  57% (708/1242)
Receiving objects:  58% (721/1242)
Receiving objects:  59% (733/1242)
Receiving objects:  60% (746/1242)
Receiving objects:  61% (758/1242)
Receiving objects:  62% (771/1242)
Receiving objects:  63% (783/1242)
Receiving objects:  64% (795/1242)
Receiving objects:  65% (808/1242)
Receiving objects:  66% (820/1242)
Receiving objects:  67% (833/1242)
Receiving objects:  68% (845/1242)
Receiving objects:  69% (857/1242)
Receiving objects:  70% (870/1242)
Receiving objects:  71% (882/1242)
Receiving objects:  72% (895/1242)
Receiving objects:  73% (907/1242)
Receiving objects:  74% (920/1242)
Receiving objects:  75% (932/1242)
Receiving objects:  76% (944/1242)
Receiving objects:  77% (957/1242)
Receiving objects:  78% (969/1242)
Receiving objects:  79% (982/1242)
Receiving objects:  80% (994/1242)
Receiving objects:  81% (1007/1242)
Receiving objects:  82% (1019/1242)
remote: Total 1242 (delta 888), reused 1037 (delta 750), pack-reused 50
Receiving objects:  83% (1031/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  84% (1044/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  85% (1056/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  86% (1069/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  87% (1081/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  88% (1093/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  89% (1106/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  90% (1118/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  91% (1131/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  92% (1143/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  93% (1156/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  94% (1168/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  95% (1180/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  96% (1193/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  97% (1205/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  98% (1218/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects:  99% (1230/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects: 100% (1242/1242), 771.75 KiB | 1.28 MiB/s
Receiving objects: 100% (1242/1242), 1012.90 KiB | 1.66 MiB/s, done.
Resolving deltas:   0% (0/907)
Resolving deltas:   1% (10/907)
Resolving deltas:   2% (19/907)
Resolving deltas:   3% (28/907)
Resolving deltas:   4% (37/907)
Resolving deltas:   5% (46/907)
Resolving deltas:   6% (55/907)
Resolving deltas:   7% (64/907)
Resolving deltas:   8% (73/907)
Resolving deltas:   9% (82/907)
Resolving deltas:  10% (91/907)
Resolving deltas:  11% (100/907)
Resolving deltas:  12% (109/907)
Resolving deltas:  13% (118/907)
Resolving deltas:  14% (127/907)
Resolving deltas:  15% (137/907)
Resolving deltas:  16% (146/907)
Resolving deltas:  17% (155/907)
Resolving deltas:  18% (164/907)
Resolving deltas:  19% (173/907)
Resolving deltas:  20% (182/907)
Resolving deltas:  21% (191/907)
Resolving deltas:  22% (200/907)
Resolving deltas:  23% (209/907)
Resolving deltas:  24% (218/907)
Resolving deltas:  25% (227/907)
Resolving deltas:  26% (236/907)
Resolving deltas:  27% (245/907)
Resolving deltas:  28% (254/907)
Resolving deltas:  29% (264/907)
Resolving deltas:  30% (273/907)
Resolving deltas:  31% (282/907)
Resolving deltas:  32% (291/907)
Resolving deltas:  33% (300/907)
Resolving deltas:  34% (309/907)
Resolving deltas:  35% (318/907)
Resolving deltas:  36% (327/907)
Resolving deltas:  37% (336/907)
Resolving deltas:  38% (345/907)
Resolving deltas:  39% (354/907)
Resolving deltas:  40% (363/907)
Resolving deltas:  41% (372/907)
Resolving deltas:  42% (381/907)
Resolving deltas:  43% (391/907)
Resolving deltas:  44% (400/907)
Resolving deltas:  45% (409/907)
Resolving deltas:  46% (418/907)
Resolving deltas:  47% (427/907)
Resolving deltas:  48% (436/907)
Resolving deltas:  49% (445/907)
Resolving deltas:  50% (454/907)
Resolving deltas:  51% (463/907)
Resolving deltas:  52% (472/907)
Resolving deltas:  53% (481/907)
Resolving deltas:  54% (490/907)
Resolving deltas:  55% (499/907)
Resolving deltas:  56% (508/907)
Resolving deltas:  57% (517/907)
Resolving deltas:  58% (527/907)
Resolving deltas:  59% (536/907)
Resolving deltas:  60% (545/907)
Resolving deltas:  61% (554/907)
Resolving deltas:  62% (563/907)
Resolving deltas:  63% (572/907)
Resolving deltas:  64% (581/907)
Resolving deltas:  65% (590/907)
Resolving deltas:  66% (599/907)
Resolving deltas:  67% (608/907)
Resolving deltas:  68% (617/907)
Resolving deltas:  69% (626/907)
Resolving deltas:  70% (635/907)
Resolving deltas:  71% (644/907)
Resolving deltas:  72% (654/907)
Resolving deltas:  73% (663/907)
Resolving deltas:  74% (672/907)
Resolving deltas:  75% (681/907)
Resolving deltas:  76% (690/907)
Resolving deltas:  77% (699/907)
Resolving deltas:  78% (708/907)
Resolving deltas:  79% (717/907)
Resolving deltas:  80% (726/907)
Resolving deltas:  81% (735/907)
Resolving deltas:  82% (744/907)
Resolving deltas:  83% (753/907)
Resolving deltas:  84% (762/907)
Resolving deltas:  85% (771/907)
Resolving deltas:  86% (781/907)
Resolving deltas:  87% (790/907)
Resolving deltas:  88% (799/907)
Resolving deltas:  89% (808/907)
Resolving deltas:  90% (817/907)
Resolving deltas:  91% (826/907)
Resolving deltas:  92% (835/907)
Resolving deltas:  93% (844/907)
Resolving deltas:  94% (853/907)
Resolving deltas:  95% (862/907)
Resolving deltas:  96% (871/907)
Resolving deltas:  97% (880/907)
Resolving deltas:  98% (889/907)
Resolving deltas:  99% (898/907)
Resolving deltas: 100% (907/907)
Resolving deltas: 100% (907/907), done.
====================================================
== DEPLOY -> sitef-02
====================================================
PLAY [Deploy/Rollback por máquina] *********************************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Resolver inputs principais] **********************************************
ok: [localhost]
TASK [Resolver filestore_base_dir (deploy/rollback)] ***************************
ok: [localhost]
TASK [Resolver status_dir] *****************************************************
ok: [localhost]
TASK [Resolver Nexus (base_url, user, password)] *******************************
ok: [localhost]
TASK [Mostrar variáveis de entrada e resolvidas (deploy/rollback)] *************
ok: [localhost] => {
    "msg": [
        "deployment_ref_resolved      = DEV000002",
        "machine_name_resolved       = sitef-02",
        "deploy_action_resolved      = deploy",
        "repo_root_resolved          = /tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved         = /tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../status/DEV000002",
        "filestore_env_resolved      = dev",
        "filestore_base_dir_resolved = dev/DEV000002",
        "nexus_base_url_resolved     = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev",
        "nexus_user_resolved         = ***"
    ]
}
TASK [Garantir diretório de status da TAG] *************************************
changed: [localhost]
TASK [Validar machine_name] ****************************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Incluir deploy por máquina] **********************************************
included: /tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir stage] **************************************************
ok: [localhost]
TASK [Deploy | Resolver run_id (para dedupe de append)] ************************
ok: [localhost]
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 2133865, 'dev': 64775, 'nlink': 1, 'atime': 1766766070.1208565, 'mtime': 1766766070.1118565, 'ctime': 1766766070.1118565, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2894458153', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
TASK [Deploy | Falhar se arquivo da máquina não existir] ***********************
skipping: [localhost]
TASK [Deploy | Carregar config da máquina sitef-02] ****************************
ok: [localhost]
TASK [Deploy | Validar package definido] ***************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Deploy | Carregar config do pacote sitef-core-0.0.1-0] *******************
ok: [localhost]
TASK [Deploy | Definir usuário alvo padrão] ************************************
ok: [localhost]
TASK [Deploy | Definir ssh_common_args padrão] *********************************
ok: [localhost]
TASK [Deploy | Aplicar ProxyJump via bastion (quando necessário)] **************
skipping: [localhost]
TASK [Deploy | Resolver host alvo (host/ip)] ***********************************
ok: [localhost]
TASK [Deploy | Falhar se host/ip não informado] ********************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Deploy | Registrar host dinâmico] ****************************************
changed: [localhost]
TASK [Deploy | Montar env final] ***********************************************
ok: [localhost]
TASK [Deploy | Definir deployment_ref_lower] ***********************************
ok: [localhost]
TASK [Deploy | Definir arquivos de status/log local] ***************************
ok: [localhost]
TASK [Deploy | Garantir diretório de status da máquina] ************************
changed: [localhost]
TASK [Deploy | Verificar se status.json já existe (para append)] ***************
ok: [localhost]
TASK [Deploy | Ler status.json existente (se existir)] *************************
skipping: [localhost]
TASK [Deploy | Parse do status existente (ou base vazio)] **********************
ok: [localhost]
TASK [Deploy | Resolver last_upload_run_id do stage (para dedupe)] *************
ok: [localhost]
TASK [Deploy | Calcular should_append_log] *************************************
ok: [localhost]
TASK [Deploy | Atualizar status (deploy:queued) com append em history] *********
changed: [localhost]
TASK [Deploy | Verificar init_deploy.sh no host] *******************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Falhar se init_deploy.sh não existir] ***************************
skipping: [localhost]
TASK [Deploy | Zerar init_deploy.log antes de executar (evitar acumulo)] *******
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Executar init_deploy.sh no host (com tee)] **********************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Ler deploy.txt do host (se existir)] ****************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Ler init_deploy.log do host (se existir)] ***********************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Mostrar caminhos do log (para visualizar no Harness)] ***********
ok: [localhost] => {
    "msg": [
        "LOG REMOTO (init_deploy.log): /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log",
        "LOG FILESTORE (.log):         dev/DEV000002/dev000002-sitef-02-dev-deploy.log",
        "STATUS FILESTORE (.json):     dev/DEV000002/dev000002-sitef-02-dev-deploy.json"
    ]
}
TASK [Deploy | Mostrar últimas linhas do init_deploy.log no output da pipeline (sempre)] ***
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | DEBUG deploy tail (Harness)] ************************************
ok: [localhost] => {
    "deploy_tail.stdout_lines": [
        "===== tail -n 200 /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log =====",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 04:21:20 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 04:21:20 PM UTC 2025"
    ]
}
TASK [Deploy | Montar conteúdo do log do DEPLOY] *******************************
ok: [localhost]
TASK [Deploy | Ler status.json atual (antes do append final)] ******************
ok: [localhost]
TASK [Deploy | Parse do status atual] ******************************************
ok: [localhost]
TASK [Deploy | Atualizar status final (append em history)] *********************
changed: [localhost]
TASK [Deploy | Garantir arquivo de log cumulativo] *****************************
changed: [localhost]
TASK [Deploy | Append do bloco de log do deploy no log cumulativo (dedupe por run_id)] ***
changed: [localhost]
TASK [Deploy | Carregar conteúdo completo do log cumulativo (para upload)] *****
ok: [localhost]
TASK [Deploy | Ler status.json atual (antes de salvar last_upload)] ************
ok: [localhost]
TASK [Deploy | Parse do status (antes de salvar last_upload)] ******************
ok: [localhost]
TASK [Deploy | Atualizar last_upload.&lt;stage>.run_id] ***************************
changed: [localhost]
TASK [Deploy | Upload JSON + LOG para Harness File Store] **********************
included: /tmp/tmp.tO7pF4XvNQ/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Definir stage_label] *******************************************
ok: [localhost]
TASK [Harness | Definir machine_stage_name] ************************************
ok: [localhost]
TASK [Harness | Resolver token (vars > env)] ***********************************
ok: [localhost]
TASK [Harness | Falhar se token vazio] *****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Montar tags base] **********************************************
ok: [localhost]
TASK [Harness | Adicionar extra_tags como tag=...] *****************************
skipping: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Identifiers (RAW)] *********************************************
ok: [localhost]
TASK [Harness | Identifiers (FINAL a partir do RAW)] ***************************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST)] ************************************
ok: [localhost]
TASK [Harness | Ler body ENV] **************************************************
ok: [localhost]
TASK [Harness | Marcar ENV duplicate?] *****************************************
ok: [localhost]
TASK [Harness | Falhar ENV se não OK] ******************************************
skipping: [localhost]
TASK [Harness | Garantir folder GIT_TAG (POST)] ********************************
ok: [localhost]
TASK [Harness | Ler body GIT_TAG] **********************************************
ok: [localhost]
TASK [Harness | Marcar GIT_TAG duplicate?] *************************************
ok: [localhost]
TASK [Harness | Falhar GIT_TAG se não OK] **************************************
skipping: [localhost]
TASK [Harness | Garantir folder LEAF (POST)] ***********************************
changed: [localhost]
TASK [Harness | Ler body LEAF] *************************************************
ok: [localhost]
TASK [Harness | Marcar LEAF duplicate?] ****************************************
ok: [localhost]
TASK [Harness | Falhar LEAF se não OK] *****************************************
skipping: [localhost]
TASK [Harness | Definir identifiers do STATUS] *********************************
ok: [localhost]
TASK [Harness | Limitar identifier STATUS a 128] *******************************
ok: [localhost]
TASK [Harness | CREATE status.json] ********************************************
changed: [localhost]
TASK [Harness | Ler body status CREATE] ****************************************
ok: [localhost]
TASK [Harness | UPDATE status.json (se já existe)] *****************************
skipping: [localhost]
TASK [Harness | Falhar status se create e update não deram certo] **************
skipping: [localhost]
TASK [Harness | Definir identifiers do LOG] ************************************
ok: [localhost]
TASK [Harness | Limitar identifier LOG a 128] **********************************
ok: [localhost]
TASK [Harness | Criar arquivo temporário do log no controller] *****************
changed: [localhost]
TASK [Harness | CREATE pipeline.log] *******************************************
changed: [localhost]
TASK [Harness | Ler body log CREATE] *******************************************
ok: [localhost]
TASK [Harness | UPDATE pipeline.log (se já existe)] ****************************
skipping: [localhost]
TASK [Harness | Falhar LOG se create e update não deram certo] *****************
skipping: [localhost]
TASK [Harness | Limpar arquivo temporário do log] ******************************
ok: [localhost]
TASK [Deploy | Falhar pipeline se init_deploy.sh retornou erro] ****************
skipping: [localhost]
TASK [Incluir rollback por máquina] ********************************************
skipping: [localhost]
PLAY RECAP *********************************************************************
localhost                  : ok=84   changed=14   unreachable=0    failed=0    skipped=14   rescued=0    ignored=0   
== Pipeline finalizada com sucesso ==
Command finished with status SUCCESS

## Log de como esta no harness file store.

---- pipeline deploy ----
deployment_ref=DEV000002
machine=sitef-02
host=100.99.57.128
stage=deploy
run_id=2025-12-26T16:21:12Z
rc=0
ts=2025-12-26T16:21:12Z

---- stdout (ansible) ----
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Fri Dec 26 04:21:20 PM UTC 2025
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
Fri Dec 26 04:21:20 PM UTC 2025

---- stderr (ansible) ----


---- init_deploy.log (tail) ----
+ set -euo pipefail
+ echo '== Início init_deploy.sh =='
== Início init_deploy.sh ==
+ date
Fri Dec 26 04:21:20 PM UTC 2025
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
Fri Dec 26 04:21:20 PM UTC 2025

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
