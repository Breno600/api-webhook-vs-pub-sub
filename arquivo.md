Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=deploy
export GIT_TAG=DEV000001
export STRATEGY=deploy
== DEPLOY PIPELINE ==
BRANCH            : develop-testes
STRATEGY          : deploy
GIT_TAG           : DEV000001
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000001
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.n0R3FhPABQ...
Cloning into '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1195, done.
remote: Counting objects:   0% (1/1145)
remote: Counting objects:   1% (12/1145)
remote: Counting objects:   2% (23/1145)
remote: Counting objects:   3% (35/1145)
remote: Counting objects:   4% (46/1145)
remote: Counting objects:   5% (58/1145)
remote: Counting objects:   6% (69/1145)
remote: Counting objects:   7% (81/1145)
remote: Counting objects:   8% (92/1145)
remote: Counting objects:   9% (104/1145)
remote: Counting objects:  10% (115/1145)
remote: Counting objects:  11% (126/1145)
remote: Counting objects:  12% (138/1145)
remote: Counting objects:  13% (149/1145)
remote: Counting objects:  14% (161/1145)
remote: Counting objects:  15% (172/1145)
remote: Counting objects:  16% (184/1145)
remote: Counting objects:  17% (195/1145)
remote: Counting objects:  18% (207/1145)
remote: Counting objects:  19% (218/1145)
remote: Counting objects:  20% (229/1145)
remote: Counting objects:  21% (241/1145)
remote: Counting objects:  22% (252/1145)
remote: Counting objects:  23% (264/1145)
remote: Counting objects:  24% (275/1145)
remote: Counting objects:  25% (287/1145)
remote: Counting objects:  26% (298/1145)
remote: Counting objects:  27% (310/1145)
remote: Counting objects:  28% (321/1145)
remote: Counting objects:  29% (333/1145)
remote: Counting objects:  30% (344/1145)
remote: Counting objects:  31% (355/1145)
remote: Counting objects:  32% (367/1145)
remote: Counting objects:  33% (378/1145)
remote: Counting objects:  34% (390/1145)
remote: Counting objects:  35% (401/1145)
remote: Counting objects:  36% (413/1145)
remote: Counting objects:  37% (424/1145)
remote: Counting objects:  38% (436/1145)
remote: Counting objects:  39% (447/1145)
remote: Counting objects:  40% (458/1145)
remote: Counting objects:  41% (470/1145)
remote: Counting objects:  42% (481/1145)
remote: Counting objects:  43% (493/1145)
remote: Counting objects:  44% (504/1145)
remote: Counting objects:  45% (516/1145)
remote: Counting objects:  46% (527/1145)
remote: Counting objects:  47% (539/1145)
remote: Counting objects:  48% (550/1145)
remote: Counting objects:  49% (562/1145)
remote: Counting objects:  50% (573/1145)
remote: Counting objects:  51% (584/1145)
remote: Counting objects:  52% (596/1145)
remote: Counting objects:  53% (607/1145)
remote: Counting objects:  54% (619/1145)
remote: Counting objects:  55% (630/1145)
remote: Counting objects:  56% (642/1145)
remote: Counting objects:  57% (653/1145)
remote: Counting objects:  58% (665/1145)
remote: Counting objects:  59% (676/1145)
remote: Counting objects:  60% (687/1145)
remote: Counting objects:  61% (699/1145)
remote: Counting objects:  62% (710/1145)
remote: Counting objects:  63% (722/1145)
remote: Counting objects:  64% (733/1145)
remote: Counting objects:  65% (745/1145)
remote: Counting objects:  66% (756/1145)
remote: Counting objects:  67% (768/1145)
remote: Counting objects:  68% (779/1145)
remote: Counting objects:  69% (791/1145)
remote: Counting objects:  70% (802/1145)
remote: Counting objects:  71% (813/1145)
remote: Counting objects:  72% (825/1145)
remote: Counting objects:  73% (836/1145)
remote: Counting objects:  74% (848/1145)
remote: Counting objects:  75% (859/1145)
remote: Counting objects:  76% (871/1145)
remote: Counting objects:  77% (882/1145)
remote: Counting objects:  78% (894/1145)
remote: Counting objects:  79% (905/1145)
remote: Counting objects:  80% (916/1145)
remote: Counting objects:  81% (928/1145)
remote: Counting objects:  82% (939/1145)
remote: Counting objects:  83% (951/1145)
remote: Counting objects:  84% (962/1145)
remote: Counting objects:  85% (974/1145)
remote: Counting objects:  86% (985/1145)
remote: Counting objects:  87% (997/1145)
remote: Counting objects:  88% (1008/1145)
remote: Counting objects:  89% (1020/1145)
remote: Counting objects:  90% (1031/1145)
remote: Counting objects:  91% (1042/1145)
remote: Counting objects:  92% (1054/1145)
remote: Counting objects:  93% (1065/1145)
remote: Counting objects:  94% (1077/1145)
remote: Counting objects:  95% (1088/1145)
remote: Counting objects:  96% (1100/1145)
remote: Counting objects:  97% (1111/1145)
remote: Counting objects:  98% (1123/1145)
remote: Counting objects:  99% (1134/1145)
remote: Counting objects: 100% (1145/1145)
remote: Counting objects: 100% (1145/1145), done.
remote: Compressing objects:   0% (1/381)
remote: Compressing objects:   1% (4/381)
remote: Compressing objects:   2% (8/381)
remote: Compressing objects:   3% (12/381)
remote: Compressing objects:   4% (16/381)
remote: Compressing objects:   5% (20/381)
remote: Compressing objects:   6% (23/381)
remote: Compressing objects:   7% (27/381)
remote: Compressing objects:   8% (31/381)
remote: Compressing objects:   9% (35/381)
remote: Compressing objects:  10% (39/381)
remote: Compressing objects:  11% (42/381)
remote: Compressing objects:  12% (46/381)
remote: Compressing objects:  13% (50/381)
remote: Compressing objects:  14% (54/381)
remote: Compressing objects:  15% (58/381)
remote: Compressing objects:  16% (61/381)
remote: Compressing objects:  17% (65/381)
remote: Compressing objects:  18% (69/381)
remote: Compressing objects:  19% (73/381)
remote: Compressing objects:  20% (77/381)
remote: Compressing objects:  21% (81/381)
remote: Compressing objects:  22% (84/381)
remote: Compressing objects:  23% (88/381)
remote: Compressing objects:  24% (92/381)
remote: Compressing objects:  25% (96/381)
remote: Compressing objects:  26% (100/381)
remote: Compressing objects:  27% (103/381)
remote: Compressing objects:  28% (107/381)
remote: Compressing objects:  29% (111/381)
remote: Compressing objects:  30% (115/381)
remote: Compressing objects:  31% (119/381)
remote: Compressing objects:  32% (122/381)
remote: Compressing objects:  33% (126/381)
remote: Compressing objects:  34% (130/381)
remote: Compressing objects:  35% (134/381)
remote: Compressing objects:  36% (138/381)
remote: Compressing objects:  37% (141/381)
remote: Compressing objects:  38% (145/381)
remote: Compressing objects:  39% (149/381)
remote: Compressing objects:  40% (153/381)
remote: Compressing objects:  41% (157/381)
remote: Compressing objects:  42% (161/381)
remote: Compressing objects:  43% (164/381)
remote: Compressing objects:  44% (168/381)
remote: Compressing objects:  45% (172/381)
remote: Compressing objects:  46% (176/381)
remote: Compressing objects:  47% (180/381)
remote: Compressing objects:  48% (183/381)
remote: Compressing objects:  49% (187/381)
remote: Compressing objects:  50% (191/381)
remote: Compressing objects:  51% (195/381)
remote: Compressing objects:  52% (199/381)
remote: Compressing objects:  53% (202/381)
remote: Compressing objects:  54% (206/381)
remote: Compressing objects:  55% (210/381)
remote: Compressing objects:  56% (214/381)
remote: Compressing objects:  57% (218/381)
remote: Compressing objects:  58% (221/381)
remote: Compressing objects:  59% (225/381)
remote: Compressing objects:  60% (229/381)
remote: Compressing objects:  61% (233/381)
remote: Compressing objects:  62% (237/381)
remote: Compressing objects:  63% (241/381)
remote: Compressing objects:  64% (244/381)
remote: Compressing objects:  65% (248/381)
remote: Compressing objects:  66% (252/381)
remote: Compressing objects:  67% (256/381)
remote: Compressing objects:  68% (260/381)
remote: Compressing objects:  69% (263/381)
remote: Compressing objects:  70% (267/381)
remote: Compressing objects:  71% (271/381)
remote: Compressing objects:  72% (275/381)
remote: Compressing objects:  73% (279/381)
remote: Compressing objects:  74% (282/381)
remote: Compressing objects:  75% (286/381)
remote: Compressing objects:  76% (290/381)
remote: Compressing objects:  77% (294/381)
remote: Compressing objects:  78% (298/381)
remote: Compressing objects:  79% (301/381)
remote: Compressing objects:  80% (305/381)
remote: Compressing objects:  81% (309/381)
remote: Compressing objects:  82% (313/381)
remote: Compressing objects:  83% (317/381)
remote: Compressing objects:  84% (321/381)
remote: Compressing objects:  85% (324/381)
remote: Compressing objects:  86% (328/381)
remote: Compressing objects:  87% (332/381)
remote: Compressing objects:  88% (336/381)
remote: Compressing objects:  89% (340/381)
remote: Compressing objects:  90% (343/381)
remote: Compressing objects:  91% (347/381)
remote: Compressing objects:  92% (351/381)
remote: Compressing objects:  93% (355/381)
remote: Compressing objects:  94% (359/381)
remote: Compressing objects:  95% (362/381)
remote: Compressing objects:  96% (366/381)
remote: Compressing objects:  97% (370/381)
remote: Compressing objects:  98% (374/381)
remote: Compressing objects:  99% (378/381)
remote: Compressing objects: 100% (381/381)
remote: Compressing objects: 100% (381/381), done.
Receiving objects:   0% (1/1195)
Receiving objects:   1% (12/1195)
Receiving objects:   2% (24/1195)
Receiving objects:   3% (36/1195)
Receiving objects:   4% (48/1195)
Receiving objects:   5% (60/1195)
Receiving objects:   6% (72/1195)
Receiving objects:   7% (84/1195)
Receiving objects:   8% (96/1195)
Receiving objects:   9% (108/1195)
Receiving objects:  10% (120/1195)
Receiving objects:  11% (132/1195)
Receiving objects:  12% (144/1195)
Receiving objects:  13% (156/1195)
Receiving objects:  14% (168/1195)
Receiving objects:  15% (180/1195)
Receiving objects:  16% (192/1195)
Receiving objects:  17% (204/1195)
Receiving objects:  18% (216/1195)
Receiving objects:  19% (228/1195)
Receiving objects:  20% (239/1195)
Receiving objects:  21% (251/1195)
Receiving objects:  22% (263/1195)
Receiving objects:  23% (275/1195)
Receiving objects:  24% (287/1195)
Receiving objects:  25% (299/1195)
Receiving objects:  26% (311/1195)
Receiving objects:  27% (323/1195)
Receiving objects:  28% (335/1195)
Receiving objects:  29% (347/1195)
Receiving objects:  30% (359/1195)
Receiving objects:  31% (371/1195)
Receiving objects:  32% (383/1195)
Receiving objects:  33% (395/1195)
Receiving objects:  34% (407/1195)
Receiving objects:  35% (419/1195)
Receiving objects:  36% (431/1195)
Receiving objects:  37% (443/1195)
Receiving objects:  38% (455/1195)
Receiving objects:  39% (467/1195)
Receiving objects:  40% (478/1195)
Receiving objects:  41% (490/1195)
Receiving objects:  42% (502/1195)
Receiving objects:  43% (514/1195)
Receiving objects:  44% (526/1195)
Receiving objects:  45% (538/1195)
Receiving objects:  46% (550/1195)
Receiving objects:  47% (562/1195)
Receiving objects:  48% (574/1195)
Receiving objects:  49% (586/1195)
Receiving objects:  50% (598/1195)
Receiving objects:  51% (610/1195)
Receiving objects:  52% (622/1195)
Receiving objects:  53% (634/1195)
Receiving objects:  54% (646/1195)
Receiving objects:  55% (658/1195)
Receiving objects:  56% (670/1195)
Receiving objects:  57% (682/1195)
Receiving objects:  58% (694/1195)
Receiving objects:  59% (706/1195)
Receiving objects:  60% (717/1195)
Receiving objects:  61% (729/1195)
Receiving objects:  62% (741/1195)
Receiving objects:  63% (753/1195)
Receiving objects:  64% (765/1195)
Receiving objects:  65% (777/1195)
Receiving objects:  66% (789/1195)
Receiving objects:  67% (801/1195)
Receiving objects:  68% (813/1195)
Receiving objects:  69% (825/1195)
Receiving objects:  70% (837/1195)
Receiving objects:  71% (849/1195)
Receiving objects:  72% (861/1195)
Receiving objects:  73% (873/1195)
Receiving objects:  74% (885/1195)
Receiving objects:  75% (897/1195)
Receiving objects:  76% (909/1195)
Receiving objects:  77% (921/1195)
Receiving objects:  78% (933/1195)
Receiving objects:  79% (945/1195)
Receiving objects:  80% (956/1195)
Receiving objects:  81% (968/1195)
Receiving objects:  82% (980/1195)
Receiving objects:  83% (992/1195)
Receiving objects:  84% (1004/1195)
Receiving objects:  85% (1016/1195)
Receiving objects:  86% (1028/1195)
remote: Total 1195 (delta 854), reused 1036 (delta 749), pack-reused 50
Receiving objects:  87% (1040/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  88% (1052/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  89% (1064/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  90% (1076/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  91% (1088/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  92% (1100/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  93% (1112/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  94% (1124/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  95% (1136/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  96% (1148/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  97% (1160/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  98% (1172/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects:  99% (1184/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects: 100% (1195/1195), 827.75 KiB | 1.60 MiB/s
Receiving objects: 100% (1195/1195), 1000.26 KiB | 1.92 MiB/s, done.
Resolving deltas:   0% (0/873)
Resolving deltas:   1% (9/873)
Resolving deltas:   2% (18/873)
Resolving deltas:   3% (27/873)
Resolving deltas:   4% (35/873)
Resolving deltas:   5% (44/873)
Resolving deltas:   6% (53/873)
Resolving deltas:   7% (62/873)
Resolving deltas:   8% (70/873)
Resolving deltas:   9% (79/873)
Resolving deltas:  10% (88/873)
Resolving deltas:  11% (97/873)
Resolving deltas:  12% (105/873)
Resolving deltas:  13% (114/873)
Resolving deltas:  14% (123/873)
Resolving deltas:  15% (131/873)
Resolving deltas:  16% (140/873)
Resolving deltas:  17% (149/873)
Resolving deltas:  18% (158/873)
Resolving deltas:  19% (166/873)
Resolving deltas:  20% (175/873)
Resolving deltas:  21% (184/873)
Resolving deltas:  22% (193/873)
Resolving deltas:  23% (201/873)
Resolving deltas:  24% (210/873)
Resolving deltas:  25% (219/873)
Resolving deltas:  26% (227/873)
Resolving deltas:  27% (236/873)
Resolving deltas:  28% (245/873)
Resolving deltas:  29% (254/873)
Resolving deltas:  30% (262/873)
Resolving deltas:  31% (271/873)
Resolving deltas:  32% (280/873)
Resolving deltas:  33% (289/873)
Resolving deltas:  34% (297/873)
Resolving deltas:  35% (306/873)
Resolving deltas:  36% (315/873)
Resolving deltas:  37% (324/873)
Resolving deltas:  38% (332/873)
Resolving deltas:  39% (341/873)
Resolving deltas:  40% (350/873)
Resolving deltas:  41% (358/873)
Resolving deltas:  42% (367/873)
Resolving deltas:  43% (376/873)
Resolving deltas:  44% (385/873)
Resolving deltas:  45% (393/873)
Resolving deltas:  46% (402/873)
Resolving deltas:  47% (411/873)
Resolving deltas:  48% (420/873)
Resolving deltas:  49% (428/873)
Resolving deltas:  50% (437/873)
Resolving deltas:  51% (446/873)
Resolving deltas:  52% (454/873)
Resolving deltas:  53% (463/873)
Resolving deltas:  54% (472/873)
Resolving deltas:  55% (481/873)
Resolving deltas:  56% (489/873)
Resolving deltas:  57% (498/873)
Resolving deltas:  58% (507/873)
Resolving deltas:  59% (516/873)
Resolving deltas:  60% (524/873)
Resolving deltas:  61% (533/873)
Resolving deltas:  62% (542/873)
Resolving deltas:  63% (550/873)
Resolving deltas:  64% (559/873)
Resolving deltas:  65% (568/873)
Resolving deltas:  66% (577/873)
Resolving deltas:  67% (585/873)
Resolving deltas:  68% (594/873)
Resolving deltas:  69% (603/873)
Resolving deltas:  70% (612/873)
Resolving deltas:  71% (620/873)
Resolving deltas:  72% (629/873)
Resolving deltas:  73% (638/873)
Resolving deltas:  74% (647/873)
Resolving deltas:  75% (655/873)
Resolving deltas:  76% (664/873)
Resolving deltas:  77% (673/873)
Resolving deltas:  78% (681/873)
Resolving deltas:  79% (690/873)
Resolving deltas:  80% (699/873)
Resolving deltas:  81% (708/873)
Resolving deltas:  82% (716/873)
Resolving deltas:  83% (725/873)
Resolving deltas:  84% (734/873)
Resolving deltas:  85% (743/873)
Resolving deltas:  86% (751/873)
Resolving deltas:  87% (760/873)
Resolving deltas:  88% (769/873)
Resolving deltas:  89% (777/873)
Resolving deltas:  90% (786/873)
Resolving deltas:  91% (795/873)
Resolving deltas:  92% (804/873)
Resolving deltas:  93% (812/873)
Resolving deltas:  94% (821/873)
Resolving deltas:  95% (830/873)
Resolving deltas:  96% (839/873)
Resolving deltas:  97% (847/873)
Resolving deltas:  98% (856/873)
Resolving deltas:  99% (865/873)
Resolving deltas: 100% (873/873)
Resolving deltas: 100% (873/873), done.
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
        "deployment_ref_resolved      = DEV000001",
        "machine_name_resolved       = sitef-02",
        "deploy_action_resolved      = deploy",
        "repo_root_resolved          = /tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved         = /tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../status/DEV000001",
        "filestore_env_resolved      = dev",
        "filestore_base_dir_resolved = dev/DEV000001",
        "nexus_base_url_resolved     = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev",
        "nexus_user_resolved         = (vazio)"
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
included: /tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
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
ok: [localhost] => (item=/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 2102514, 'dev': 64775, 'nlink': 1, 'atime': 1766749997.4510767, 'mtime': 1766749997.4500766, 'ctime': 1766749997.4500766, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2945187207', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
TASK [Deploy | Executar init_deploy.sh no host (com tee)] **********************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Ler deploy.txt do host (se existir)] ****************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Ler init_deploy.log do host (se existir)] ***********************
ok: [localhost -> sitef-02(100.99.57.128)]
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
included: /tmp/tmp.n0R3FhPABQ/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'status_tag_value is defined' failed. The error was: An unhandled exception occurred while templating '{{ (deployment_ref | lower) ~ ':' ~ stage_name ~ ':' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.Ansi
bleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ stage_name }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ stage_name }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=48   changed=9    unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
Command finished with status FAILURE


#### segue como esta meu harnessansible completo que esta funcionando 100% para o pre deploy, entao entendo que deveria funcionar igual para deploy e rollback

---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina) - SIMPLES / SEM LOOKUP
#
# Estrutura:
#   dev/$GIT_TAG/$MACHINE-(PRE-DEPLOY|DEPLOY|ROLLBACK)
#
# Requer:
#   current_machine, deployment_ref, filestore_env, machine_status_file, log_content, status_tag_value
# Opcional:
#   stage_name (predeploy|deploy|rollback)  (default: predeploy)
#   extra_tags []
# Token:
#   harness_api_key_override (var) OU env: HARNESS_API_KEY / HARNESS_PAT / HARNESS_TOKEN
# =====================================================================================

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - current_machine is defined
      - deployment_ref is defined
      - filestore_env is defined
      - machine_status_file is defined
      - log_content is defined
      - status_tag_value is defined
    fail_msg: "Faltam vars obrigatórias para harness_filestore_upload.yml"

- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: >-
      {{
        (harness_account_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ACCOUNT_ID') | default('', true) | string | trim), true)
          | default('fgDto6qoTT6ctfZS9eWbEw', true)
      }}
    harness_org_id_resolved: >-
      {{
        (harness_org_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ORG_ID') | default('', true) | string | trim), true)
          | default('Fiserv', true)
      }}
    harness_project_id_resolved: >-
      {{
        (harness_project_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_PROJECT_ID') | default('', true) | string | trim), true)
          | default('sitef', true)
      }}
    harness_filestore_root_identifier_resolved: >-
      {{
        (harness_filestore_root_identifier_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_FILESTORE_ROOT_IDENTIFIER') | default('', true) | string | trim), true)
          | default('Root', true)
      }}

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"

# ✅ FIX: stage_label em uma task, machine_stage_name em outra
- name: "Harness | Definir stage_label"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"

- name: "Harness | Definir machine_stage_name"
  ansible.builtin.set_fact:
    machine_stage_name: "{{ (current_machine | string | trim) ~ '-' ~ stage_label }}"

# -----------------------------------------------------------------------------
# TOKEN (sem recursão)
# -----------------------------------------------------------------------------
- name: "Harness | Resolver token (vars > env)"
  no_log: true
  ansible.builtin.set_fact:
    harness_api_key_resolved: >-
      {{
        (harness_api_key_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_API_KEY') | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_PAT')     | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_TOKEN')   | default('', true) | string | trim), true)
      }}

- name: "Harness | Falhar se token vazio"
  ansible.builtin.assert:
    that:
      - (harness_api_key_resolved | default('') | length) > 10
    fail_msg: >-
      Harness token não encontrado. Defina harness_api_key_override OU exporte
      HARNESS_API_KEY (ou HARNESS_PAT / HARNESS_TOKEN) no step ANTES do ansible.

- name: "Harness | Definir http_codes aceitos"
  ansible.builtin.set_fact:
    hfs_ok_folder_codes: ["200","201","202","409"]
    hfs_ok_file_codes:   ["200","201","202"]
    hfs_retry_opts: "--connect-timeout 10 --max-time 60 --retry 2 --retry-delay 1 --retry-connrefused"

# -----------------------------------------------------------------------------
# Tags
# -----------------------------------------------------------------------------
- name: "Harness | Montar tags base"
  ansible.builtin.set_fact:
    harness_tags:
      - { key: "env",        value: "{{ env_lower }}" }
      - { key: "deployment", value: "{{ deployment_ref_lower }}" }
      - { key: "machine",    value: "{{ current_machine | lower }}" }
      - { key: "stage",      value: "{{ stage_label }}" }
      - { key: "tag",        value: "{{ status_tag_value }}" }

- name: "Harness | Adicionar extra_tags como tag=..."
  ansible.builtin.set_fact:
    harness_tags: "{{ harness_tags + [ {'key':'tag','value': (item | string | trim)} ] }}"
  loop: "{{ extra_tags | default([]) }}"
  when: (item | string | trim | length) > 0

- name: "Harness | Render tags JSON"
  ansible.builtin.set_fact:
    harness_tags_json: "{{ harness_tags | to_json }}"

# -----------------------------------------------------------------------------
# Identifiers SIMPLES: usar identifier = name (isso evita lookup e bate com o que já existe)
# Regras Harness: [A-Za-z][A-Za-z0-9_]{0,127}
# -----------------------------------------------------------------------------
# -----------------------------------------------------------------------------
# Identifiers SIMPLES: usar identifier = name (isso evita lookup e bate com o que já existe)
# Regras Harness: [A-Za-z][A-Za-z0-9_]{0,127}
# -----------------------------------------------------------------------------

- name: "Harness | Identifiers (RAW)"
  ansible.builtin.set_fact:
    env_identifier_final: "{{ env_lower }}"

    deployment_ref_identifier_raw: "{{ deployment_ref_folder | regex_replace('[^A-Za-z0-9_]', '_') }}"

    leaf_identifier_raw: "{{ (machine_stage_name | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Identifiers (FINAL a partir do RAW)"
  ansible.builtin.set_fact:
    deployment_ref_identifier_final: >-
      {{
        deployment_ref_identifier_raw
        if (deployment_ref_identifier_raw | regex_search('^[A-Za-z]'))
        else ('d_' ~ deployment_ref_identifier_raw)
      }}

    leaf_identifier_raw2: >-
      {{
        leaf_identifier_raw
        if (leaf_identifier_raw | regex_search('^[A-Za-z]'))
        else ('p_' ~ leaf_identifier_raw)
      }}

- name: "Harness | Limitar leaf_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_raw2[0:110] ~ '_' ~ (leaf_identifier_raw2 | hash('sha1'))[0:16])
        if (leaf_identifier_raw2 | length) > 128 else leaf_identifier_raw2
      }}


- name: "Harness | Limitar leaf_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_raw2[0:110] ~ '_' ~ (leaf_identifier_raw2 | hash('sha1'))[0:16])
        if (leaf_identifier_raw2 | length) > 128 else leaf_identifier_raw2
      }}

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (dev) embaixo do Root
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier_final }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_env_create_http
  failed_when: false
  changed_when: hfs_env_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body ENV"
  ansible.builtin.shell: "cat /tmp/hfs_env_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_env_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar ENV duplicate?"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ hfs_env_create_http.stdout | default('') | string | trim }}"
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_env_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar ENV se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_http_code }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when:
    - (hfs_env_http_code not in hfs_ok_folder_codes)
    - not (hfs_env_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 2) Garantir folder GIT_TAG embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder GIT_TAG (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier_final }}" \
      -F "identifier={{ deployment_ref_identifier_final }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_ref_create_http
  failed_when: false
  changed_when: hfs_ref_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body GIT_TAG"
  ansible.builtin.shell: "cat /tmp/hfs_ref_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar GIT_TAG duplicate?"
  ansible.builtin.set_fact:
    hfs_ref_http_code: "{{ hfs_ref_create_http.stdout | default('') | string | trim }}"
    hfs_ref_is_duplicate: >-
      {{
        (hfs_ref_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_ref_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar GIT_TAG se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder GIT_TAG no Harness.
      http_code={{ hfs_ref_http_code }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when:
    - (hfs_ref_http_code not in hfs_ok_folder_codes)
    - not (hfs_ref_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (MACHINE-STAGE) embaixo do GIT_TAG
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_leaf_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ machine_stage_name }}-{{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ deployment_ref_identifier_final }}" \
      -F "identifier={{ leaf_identifier_final }}_{{ deployment_ref_folder }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_leaf_create_http
  failed_when: false
  changed_when: hfs_leaf_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body LEAF"
  ansible.builtin.shell: "cat /tmp/hfs_leaf_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_leaf_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar LEAF duplicate?"
  ansible.builtin.set_fact:
    hfs_leaf_http_code: "{{ hfs_leaf_create_http.stdout | default('') | string | trim }}"
    hfs_leaf_is_duplicate: >-
      {{
        (hfs_leaf_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_leaf_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar LEAF se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF no Harness.
      http_code={{ hfs_leaf_http_code }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when:
    - (hfs_leaf_http_code not in hfs_ok_folder_codes)
    - not (hfs_leaf_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 4) Upload/Upsert STATUS JSON (POST -> se 409 ou DUPLICATE faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir identifiers do STATUS"
  ansible.builtin.set_fact:
    hfs_status_name: "status-{{ deployment_ref_folder }}.json"
    hfs_status_parent_identifier: "{{ leaf_identifier_final }}_{{ deployment_ref_folder }}"
    hfs_status_identifier_raw: "{{ ('status_' ~ deployment_ref_identifier_final ~ '_' ~ leaf_identifier_final) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Limitar identifier STATUS a 128"
  ansible.builtin.set_fact:
    hfs_status_identifier: >-
      {{
        (hfs_status_identifier_raw[0:110] ~ '_' ~ (hfs_status_identifier_raw | hash('sha1'))[0:16])
        if (hfs_status_identifier_raw | length) > 128 else hfs_status_identifier_raw
      }}


- name: "Harness | CREATE status.json"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_create_http
  failed_when: false
  changed_when: (hfs_status_create_http.stdout | trim) is match('^2..$')

- name: "Harness | Ler body status CREATE"
  ansible.builtin.shell: "cat /tmp/hfs_status_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_status_create_body
  changed_when: false
  failed_when: false

- name: "Harness | UPDATE status.json (se já existe)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_update_http
  when: >
    (hfs_status_create_http.stdout | default('') | trim) in ['409','400']
    or ((hfs_status_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
  failed_when: false
  changed_when: (hfs_status_update_http.stdout | default('') | trim) is match('^2..$')

- name: "Harness | Falhar status se create e update não deram certo"
  ansible.builtin.fail:
    msg: |
      Falha ao enviar status.json
      create_http={{ hfs_status_create_http.stdout | default('') | trim }}
      create_body={{ hfs_status_create_body.stdout | default('') }}
      update_http={{ hfs_status_update_http.stdout | default('') | trim }}
  when: >
    not ((hfs_status_create_http.stdout | default('') | trim) is match('^2..$'))
    and not ((hfs_status_update_http.stdout | default('') | trim) is match('^2..$'))

# -----------------------------------------------------------------------------
# 5) Upload/Upsert LOG (POST -> se 409 ou DUPLICATE faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir identifiers do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "pipeline-{{ deployment_ref_folder }}.log"
    hfs_log_parent_identifier: "{{ leaf_identifier_final }}_{{ deployment_ref_folder }}"
    hfs_log_identifier_raw: "{{ ('log_' ~ deployment_ref_identifier_final ~ '_' ~ leaf_identifier_final) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Limitar identifier LOG a 128"
  ansible.builtin.set_fact:
    hfs_log_identifier: >-
      {{
        (hfs_log_identifier_raw[0:110] ~ '_' ~ (hfs_log_identifier_raw | hash('sha1'))[0:16])
        if (hfs_log_identifier_raw | length) > 128 else hfs_log_identifier_raw
      }}

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | CREATE pipeline.log"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_create_http
      failed_when: false
      changed_when: (hfs_log_create_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | Ler body log CREATE"
      ansible.builtin.shell: "cat /tmp/hfs_log_create.out 2>/dev/null || true"
      args:
        executable: /bin/bash
      register: hfs_log_create_body
      changed_when: false
      failed_when: false

    - name: "Harness | UPDATE pipeline.log (se já existe)"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_update_http
      when: >
        (hfs_log_create_http.stdout | default('') | trim) in ['409','400']
        or ((hfs_log_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      failed_when: false
      changed_when: (hfs_log_update_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | Falhar LOG se create e update não deram certo"
      ansible.builtin.fail:
        msg: |
          Falha ao enviar pipeline.log
          create_http={{ hfs_log_create_http.stdout | default('') | trim }}
          create_body={{ hfs_log_create_body.stdout | default('') }}
          update_http={{ hfs_log_update_http.stdout | default('') | trim }}
      when: >
        not ((hfs_log_create_http.stdout | default('') | trim) is match('^2..$'))
        and not ((hfs_log_update_http.stdout | default('') | trim) is match('^2..$'))

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        state: absent
      changed_when: false
      failed_when: false
