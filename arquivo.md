Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=rollback
export GIT_TAG=DEV000001
export STRATEGY=rollback
== DEPLOY/ROLLBACK PIPELINE ==
BRANCH            : develop-testes
STRATEGY          : rollback
GIT_TAG           : DEV000001
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000001
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.MeLaFVIsZs...
Cloning into '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1213, done.
remote: Counting objects:   0% (1/1163)
remote: Counting objects:   1% (12/1163)
remote: Counting objects:   2% (24/1163)
remote: Counting objects:   3% (35/1163)
remote: Counting objects:   4% (47/1163)
remote: Counting objects:   5% (59/1163)
remote: Counting objects:   6% (70/1163)
remote: Counting objects:   7% (82/1163)
remote: Counting objects:   8% (94/1163)
remote: Counting objects:   9% (105/1163)
remote: Counting objects:  10% (117/1163)
remote: Counting objects:  11% (128/1163)
remote: Counting objects:  12% (140/1163)
remote: Counting objects:  13% (152/1163)
remote: Counting objects:  14% (163/1163)
remote: Counting objects:  15% (175/1163)
remote: Counting objects:  16% (187/1163)
remote: Counting objects:  17% (198/1163)
remote: Counting objects:  18% (210/1163)
remote: Counting objects:  19% (221/1163)
remote: Counting objects:  20% (233/1163)
remote: Counting objects:  21% (245/1163)
remote: Counting objects:  22% (256/1163)
remote: Counting objects:  23% (268/1163)
remote: Counting objects:  24% (280/1163)
remote: Counting objects:  25% (291/1163)
remote: Counting objects:  26% (303/1163)
remote: Counting objects:  27% (315/1163)
remote: Counting objects:  28% (326/1163)
remote: Counting objects:  29% (338/1163)
remote: Counting objects:  30% (349/1163)
remote: Counting objects:  31% (361/1163)
remote: Counting objects:  32% (373/1163)
remote: Counting objects:  33% (384/1163)
remote: Counting objects:  34% (396/1163)
remote: Counting objects:  35% (408/1163)
remote: Counting objects:  36% (419/1163)
remote: Counting objects:  37% (431/1163)
remote: Counting objects:  38% (442/1163)
remote: Counting objects:  39% (454/1163)
remote: Counting objects:  40% (466/1163)
remote: Counting objects:  41% (477/1163)
remote: Counting objects:  42% (489/1163)
remote: Counting objects:  43% (501/1163)
remote: Counting objects:  44% (512/1163)
remote: Counting objects:  45% (524/1163)
remote: Counting objects:  46% (535/1163)
remote: Counting objects:  47% (547/1163)
remote: Counting objects:  48% (559/1163)
remote: Counting objects:  49% (570/1163)
remote: Counting objects:  50% (582/1163)
remote: Counting objects:  51% (594/1163)
remote: Counting objects:  52% (605/1163)
remote: Counting objects:  53% (617/1163)
remote: Counting objects:  54% (629/1163)
remote: Counting objects:  55% (640/1163)
remote: Counting objects:  56% (652/1163)
remote: Counting objects:  57% (663/1163)
remote: Counting objects:  58% (675/1163)
remote: Counting objects:  59% (687/1163)
remote: Counting objects:  60% (698/1163)
remote: Counting objects:  61% (710/1163)
remote: Counting objects:  62% (722/1163)
remote: Counting objects:  63% (733/1163)
remote: Counting objects:  64% (745/1163)
remote: Counting objects:  65% (756/1163)
remote: Counting objects:  66% (768/1163)
remote: Counting objects:  67% (780/1163)
remote: Counting objects:  68% (791/1163)
remote: Counting objects:  69% (803/1163)
remote: Counting objects:  70% (815/1163)
remote: Counting objects:  71% (826/1163)
remote: Counting objects:  72% (838/1163)
remote: Counting objects:  73% (849/1163)
remote: Counting objects:  74% (861/1163)
remote: Counting objects:  75% (873/1163)
remote: Counting objects:  76% (884/1163)
remote: Counting objects:  77% (896/1163)
remote: Counting objects:  78% (908/1163)
remote: Counting objects:  79% (919/1163)
remote: Counting objects:  80% (931/1163)
remote: Counting objects:  81% (943/1163)
remote: Counting objects:  82% (954/1163)
remote: Counting objects:  83% (966/1163)
remote: Counting objects:  84% (977/1163)
remote: Counting objects:  85% (989/1163)
remote: Counting objects:  86% (1001/1163)
remote: Counting objects:  87% (1012/1163)
remote: Counting objects:  88% (1024/1163)
remote: Counting objects:  89% (1036/1163)
remote: Counting objects:  90% (1047/1163)
remote: Counting objects:  91% (1059/1163)
remote: Counting objects:  92% (1070/1163)
remote: Counting objects:  93% (1082/1163)
remote: Counting objects:  94% (1094/1163)
remote: Counting objects:  95% (1105/1163)
remote: Counting objects:  96% (1117/1163)
remote: Counting objects:  97% (1129/1163)
remote: Counting objects:  98% (1140/1163)
remote: Counting objects:  99% (1152/1163)
remote: Counting objects: 100% (1163/1163)
remote: Counting objects: 100% (1163/1163), done.
remote: Compressing objects:   0% (1/399)
remote: Compressing objects:   1% (4/399)
remote: Compressing objects:   2% (8/399)
remote: Compressing objects:   3% (12/399)
remote: Compressing objects:   4% (16/399)
remote: Compressing objects:   5% (20/399)
remote: Compressing objects:   6% (24/399)
remote: Compressing objects:   7% (28/399)
remote: Compressing objects:   8% (32/399)
remote: Compressing objects:   9% (36/399)
remote: Compressing objects:  10% (40/399)
remote: Compressing objects:  11% (44/399)
remote: Compressing objects:  12% (48/399)
remote: Compressing objects:  13% (52/399)
remote: Compressing objects:  14% (56/399)
remote: Compressing objects:  15% (60/399)
remote: Compressing objects:  16% (64/399)
remote: Compressing objects:  17% (68/399)
remote: Compressing objects:  18% (72/399)
remote: Compressing objects:  19% (76/399)
remote: Compressing objects:  20% (80/399)
remote: Compressing objects:  21% (84/399)
remote: Compressing objects:  22% (88/399)
remote: Compressing objects:  23% (92/399)
remote: Compressing objects:  24% (96/399)
remote: Compressing objects:  25% (100/399)
remote: Compressing objects:  26% (104/399)
remote: Compressing objects:  27% (108/399)
remote: Compressing objects:  28% (112/399)
remote: Compressing objects:  29% (116/399)
remote: Compressing objects:  30% (120/399)
remote: Compressing objects:  31% (124/399)
remote: Compressing objects:  32% (128/399)
remote: Compressing objects:  33% (132/399)
remote: Compressing objects:  34% (136/399)
remote: Compressing objects:  35% (140/399)
remote: Compressing objects:  36% (144/399)
remote: Compressing objects:  37% (148/399)
remote: Compressing objects:  38% (152/399)
remote: Compressing objects:  39% (156/399)
remote: Compressing objects:  40% (160/399)
remote: Compressing objects:  41% (164/399)
remote: Compressing objects:  42% (168/399)
remote: Compressing objects:  43% (172/399)
remote: Compressing objects:  44% (176/399)
remote: Compressing objects:  45% (180/399)
remote: Compressing objects:  46% (184/399)
remote: Compressing objects:  47% (188/399)
remote: Compressing objects:  48% (192/399)
remote: Compressing objects:  49% (196/399)
remote: Compressing objects:  50% (200/399)
remote: Compressing objects:  51% (204/399)
remote: Compressing objects:  52% (208/399)
remote: Compressing objects:  53% (212/399)
remote: Compressing objects:  54% (216/399)
remote: Compressing objects:  55% (220/399)
remote: Compressing objects:  56% (224/399)
remote: Compressing objects:  57% (228/399)
remote: Compressing objects:  58% (232/399)
remote: Compressing objects:  59% (236/399)
remote: Compressing objects:  60% (240/399)
remote: Compressing objects:  61% (244/399)
remote: Compressing objects:  62% (248/399)
remote: Compressing objects:  63% (252/399)
remote: Compressing objects:  64% (256/399)
remote: Compressing objects:  65% (260/399)
remote: Compressing objects:  66% (264/399)
remote: Compressing objects:  67% (268/399)
remote: Compressing objects:  68% (272/399)
remote: Compressing objects:  69% (276/399)
remote: Compressing objects:  70% (280/399)
remote: Compressing objects:  71% (284/399)
remote: Compressing objects:  72% (288/399)
remote: Compressing objects:  73% (292/399)
remote: Compressing objects:  74% (296/399)
remote: Compressing objects:  75% (300/399)
remote: Compressing objects:  76% (304/399)
remote: Compressing objects:  77% (308/399)
remote: Compressing objects:  78% (312/399)
remote: Compressing objects:  79% (316/399)
remote: Compressing objects:  80% (320/399)
remote: Compressing objects:  81% (324/399)
remote: Compressing objects:  82% (328/399)
remote: Compressing objects:  83% (332/399)
remote: Compressing objects:  84% (336/399)
remote: Compressing objects:  85% (340/399)
remote: Compressing objects:  86% (344/399)
remote: Compressing objects:  87% (348/399)
remote: Compressing objects:  88% (352/399)
remote: Compressing objects:  89% (356/399)
remote: Compressing objects:  90% (360/399)
remote: Compressing objects:  91% (364/399)
remote: Compressing objects:  92% (368/399)
remote: Compressing objects:  93% (372/399)
remote: Compressing objects:  94% (376/399)
remote: Compressing objects:  95% (380/399)
remote: Compressing objects:  96% (384/399)
remote: Compressing objects:  97% (388/399)
remote: Compressing objects:  98% (392/399)
remote: Compressing objects:  99% (396/399)
remote: Compressing objects: 100% (399/399)
remote: Compressing objects: 100% (399/399), done.
Receiving objects:   0% (1/1213)
Receiving objects:   1% (13/1213)
Receiving objects:   2% (25/1213)
Receiving objects:   3% (37/1213)
Receiving objects:   4% (49/1213)
Receiving objects:   5% (61/1213)
Receiving objects:   6% (73/1213)
Receiving objects:   7% (85/1213)
Receiving objects:   8% (98/1213)
Receiving objects:   9% (110/1213)
Receiving objects:  10% (122/1213)
Receiving objects:  11% (134/1213)
Receiving objects:  12% (146/1213)
Receiving objects:  13% (158/1213)
Receiving objects:  14% (170/1213)
Receiving objects:  15% (182/1213)
Receiving objects:  16% (195/1213)
Receiving objects:  17% (207/1213)
Receiving objects:  18% (219/1213)
Receiving objects:  19% (231/1213)
Receiving objects:  20% (243/1213)
Receiving objects:  21% (255/1213)
Receiving objects:  22% (267/1213)
Receiving objects:  23% (279/1213)
Receiving objects:  24% (292/1213)
Receiving objects:  25% (304/1213)
Receiving objects:  26% (316/1213)
Receiving objects:  27% (328/1213)
Receiving objects:  28% (340/1213)
Receiving objects:  29% (352/1213)
Receiving objects:  30% (364/1213)
Receiving objects:  31% (377/1213)
Receiving objects:  32% (389/1213)
Receiving objects:  33% (401/1213)
Receiving objects:  34% (413/1213)
Receiving objects:  35% (425/1213)
Receiving objects:  36% (437/1213)
Receiving objects:  37% (449/1213)
Receiving objects:  38% (461/1213)
Receiving objects:  39% (474/1213)
Receiving objects:  40% (486/1213)
Receiving objects:  41% (498/1213)
Receiving objects:  42% (510/1213)
Receiving objects:  43% (522/1213)
Receiving objects:  44% (534/1213)
Receiving objects:  45% (546/1213)
Receiving objects:  46% (558/1213)
Receiving objects:  47% (571/1213)
Receiving objects:  48% (583/1213)
Receiving objects:  49% (595/1213)
Receiving objects:  50% (607/1213)
Receiving objects:  51% (619/1213)
Receiving objects:  52% (631/1213)
Receiving objects:  53% (643/1213)
Receiving objects:  54% (656/1213)
Receiving objects:  55% (668/1213)
Receiving objects:  56% (680/1213)
Receiving objects:  57% (692/1213)
Receiving objects:  58% (704/1213)
Receiving objects:  59% (716/1213)
Receiving objects:  60% (728/1213)
Receiving objects:  61% (740/1213)
Receiving objects:  62% (753/1213)
Receiving objects:  63% (765/1213)
Receiving objects:  64% (777/1213)
Receiving objects:  65% (789/1213)
Receiving objects:  66% (801/1213)
Receiving objects:  67% (813/1213)
Receiving objects:  68% (825/1213)
Receiving objects:  69% (837/1213)
Receiving objects:  70% (850/1213)
Receiving objects:  71% (862/1213)
Receiving objects:  72% (874/1213)
Receiving objects:  73% (886/1213)
Receiving objects:  74% (898/1213)
Receiving objects:  75% (910/1213)
Receiving objects:  76% (922/1213)
Receiving objects:  77% (935/1213)
Receiving objects:  78% (947/1213)
Receiving objects:  79% (959/1213)
Receiving objects:  80% (971/1213)
Receiving objects:  81% (983/1213)
Receiving objects:  82% (995/1213)
Receiving objects:  83% (1007/1213)
Receiving objects:  84% (1019/1213)
Receiving objects:  85% (1032/1213)
remote: Total 1213 (delta 869), reused 1034 (delta 749), pack-reused 50
Receiving objects:  86% (1044/1213)
Receiving objects:  87% (1056/1213)
Receiving objects:  88% (1068/1213)
Receiving objects:  89% (1080/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  90% (1092/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  91% (1104/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  92% (1116/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  93% (1129/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  94% (1141/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  95% (1153/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  96% (1165/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  97% (1177/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  98% (1189/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects:  99% (1201/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects: 100% (1213/1213), 947.73 KiB | 1.83 MiB/s
Receiving objects: 100% (1213/1213), 1005.21 KiB | 1.95 MiB/s, done.
Resolving deltas:   0% (0/888)
Resolving deltas:   1% (9/888)
Resolving deltas:   2% (18/888)
Resolving deltas:   3% (27/888)
Resolving deltas:   4% (36/888)
Resolving deltas:   5% (45/888)
Resolving deltas:   6% (54/888)
Resolving deltas:   7% (63/888)
Resolving deltas:   8% (72/888)
Resolving deltas:   9% (80/888)
Resolving deltas:  10% (89/888)
Resolving deltas:  11% (98/888)
Resolving deltas:  12% (107/888)
Resolving deltas:  13% (116/888)
Resolving deltas:  14% (125/888)
Resolving deltas:  15% (134/888)
Resolving deltas:  16% (143/888)
Resolving deltas:  17% (151/888)
Resolving deltas:  18% (160/888)
Resolving deltas:  19% (169/888)
Resolving deltas:  20% (178/888)
Resolving deltas:  21% (187/888)
Resolving deltas:  22% (196/888)
Resolving deltas:  23% (205/888)
Resolving deltas:  24% (214/888)
Resolving deltas:  25% (222/888)
Resolving deltas:  26% (231/888)
Resolving deltas:  27% (240/888)
Resolving deltas:  28% (249/888)
Resolving deltas:  29% (258/888)
Resolving deltas:  30% (267/888)
Resolving deltas:  31% (276/888)
Resolving deltas:  32% (285/888)
Resolving deltas:  33% (294/888)
Resolving deltas:  34% (302/888)
Resolving deltas:  35% (311/888)
Resolving deltas:  36% (320/888)
Resolving deltas:  37% (329/888)
Resolving deltas:  38% (338/888)
Resolving deltas:  39% (347/888)
Resolving deltas:  40% (356/888)
Resolving deltas:  41% (365/888)
Resolving deltas:  42% (373/888)
Resolving deltas:  43% (382/888)
Resolving deltas:  44% (391/888)
Resolving deltas:  45% (400/888)
Resolving deltas:  46% (409/888)
Resolving deltas:  47% (418/888)
Resolving deltas:  48% (427/888)
Resolving deltas:  49% (436/888)
Resolving deltas:  50% (444/888)
Resolving deltas:  51% (453/888)
Resolving deltas:  52% (462/888)
Resolving deltas:  53% (471/888)
Resolving deltas:  54% (480/888)
Resolving deltas:  55% (489/888)
Resolving deltas:  56% (498/888)
Resolving deltas:  57% (507/888)
Resolving deltas:  58% (516/888)
Resolving deltas:  59% (524/888)
Resolving deltas:  60% (533/888)
Resolving deltas:  61% (542/888)
Resolving deltas:  62% (551/888)
Resolving deltas:  63% (560/888)
Resolving deltas:  64% (569/888)
Resolving deltas:  65% (578/888)
Resolving deltas:  66% (587/888)
Resolving deltas:  67% (595/888)
Resolving deltas:  68% (604/888)
Resolving deltas:  69% (613/888)
Resolving deltas:  70% (622/888)
Resolving deltas:  71% (631/888)
Resolving deltas:  72% (640/888)
Resolving deltas:  73% (649/888)
Resolving deltas:  74% (658/888)
Resolving deltas:  75% (666/888)
Resolving deltas:  76% (675/888)
Resolving deltas:  77% (684/888)
Resolving deltas:  78% (693/888)
Resolving deltas:  79% (702/888)
Resolving deltas:  80% (711/888)
Resolving deltas:  81% (720/888)
Resolving deltas:  82% (729/888)
Resolving deltas:  83% (738/888)
Resolving deltas:  84% (746/888)
Resolving deltas:  85% (755/888)
Resolving deltas:  86% (764/888)
Resolving deltas:  87% (773/888)
Resolving deltas:  88% (782/888)
Resolving deltas:  89% (791/888)
Resolving deltas:  90% (800/888)
Resolving deltas:  91% (809/888)
Resolving deltas:  92% (817/888)
Resolving deltas:  93% (826/888)
Resolving deltas:  94% (835/888)
Resolving deltas:  95% (844/888)
Resolving deltas:  96% (853/888)
Resolving deltas:  97% (862/888)
Resolving deltas:  98% (871/888)
Resolving deltas:  99% (880/888)
Resolving deltas: 100% (888/888)
Resolving deltas: 100% (888/888), done.
====================================================
== ROLLBACK -> sitef-02
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
        "deploy_action_resolved      = rollback",
        "repo_root_resolved          = /tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved         = /tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../status/DEV000001",
        "filestore_env_resolved      = dev",
        "filestore_base_dir_resolved = dev/DEV000001",
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
skipping: [localhost]
TASK [Incluir rollback por máquina] ********************************************
included: /tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/rollback_per_machine.yml for localhost
TASK [Resolver nome da máquina atual (rollback)] *******************************
ok: [localhost]
TASK [Definir paths base do repositório (rollback)] ****************************
ok: [localhost]
TASK [Definir execution_dir, machines_dir, packages_dir e scripts_dir (rollback)] ***
ok: [localhost]
TASK [Definir candidatos de arquivo da máquina (rollback)] *********************
ok: [localhost]
TASK [Verificar candidatos de arquivo da máquina (rollback)] *******************
ok: [localhost] => (item=/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Selecionar machine_file existente (rollback)] ****************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 2133840, 'dev': 64775, 'nlink': 1, 'atime': 1766751849.5816813, 'mtime': 1766751849.573681, 'ctime': 1766751849.573681, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '211909380', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
TASK [Falhar se arquivo da máquina não existir em nenhum caminho esperado (rollback)] ***
skipping: [localhost]
TASK [Carregar config da máquina sitef-02 (rollback)] **************************
ok: [localhost]
TASK [Validar rollback definido na máquina] ************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Carregar config do pacote de rollback sitef-core-0.0.2-0-rollback] *******
ok: [localhost]
TASK [Validar components e script do pacote de rollback] ***********************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Calcular env_vars mescladas (rollback)] **********************************
ok: [localhost]
TASK [Definir usuário alvo padrão (rollback)] **********************************
ok: [localhost]
TASK [Definir ssh_common_args padrão (rollback)] *******************************
ok: [localhost]
TASK [Aplicar ProxyJump via bastion (quando necessário) (rollback)] ************
skipping: [localhost]
TASK [Registrar host dinâmico para sitef-02 (rollback)] ************************
changed: [localhost]
TASK [Mostrar hostname/IP da máquina alvo (debug rollback)] ********************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Debug target_ident (rollback)] *******************************************
ok: [localhost] => {
    "msg": [
        "current_machine = sitef-02",
        "machine_cfg.host = 100.99.57.128",
        "machine_cfg.ip   = ",
        "hostname/ip alvo:",
        [
            "100.99.57.128 ",
            "ip-100-99-57-128"
        ]
    ]
}
TASK [Definir caminhos de status e rollback] ***********************************
ok: [localhost]
TASK [Calcular caminhos de status/log (rollback)] ******************************
ok: [localhost]
TASK [Criar diretório de status da máquina (rollback)] *************************
changed: [localhost]
TASK [Escrever status inicial (rollback:queued)] *******************************
changed: [localhost]
TASK [Garantir base de rollback no host] ***************************************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/rollback/components)
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/rollback/scripts)
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package)
TASK [Limpar pasta de scripts do pacote de rollback] ***************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Recriar pasta de scripts do pacote de rollback] **************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Copiar package.yml de rollback para o host] ******************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Garantir diretórios dos componentes de rollback no host] *****************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=packages/linux/sitef-core-0.0.1-0.x86_64.rpm)
TASK [Baixar componentes de rollback do Nexus] *********************************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=packages/linux/sitef-core-0.0.1-0.x86_64.rpm)
TASK [Copiar scripts de rollback para o host] **********************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Listar conteúdo de /rollback/scripts (debug)] ****************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Mostrar conteúdo de scripts de rollback (debug)] *************************
ok: [localhost] => {
    "scripts_ls_rb.stdout_lines": [
        "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts:",
        "total 840",
        "drwxr-xr-x. 3 root root   4096 Dec 26 12:24 .",
        "drwxr-xr-x. 4 root root     39 Dec 26 12:19 ..",
        "-rwxr-xr-x. 1 root root   5475 Dec 26 12:20 backup.py",
        "-rwxr-xr-x. 1 root root   1147 Dec 26 12:20 init_deploy.sh",
        "-rwxr-xr-x. 1 root root    844 Dec 26 12:20 init_parallel.sh",
        "-rw-r-----. 1 root root    461 Dec 26 12:20 init_rollback.log",
        "-rwxr-xr-x. 1 root root    259 Dec 26 12:20 init_rollback.sh",
        "drwxr-xr-x. 2 root root     45 Dec 26 12:24 package",
        "-rwxr-xr-x. 1 root root 807870 Dec 26 12:20 pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl",
        "-rwxr-xr-x. 1 root root   1698 Dec 26 12:20 script_deploy.py",
        "-rwxr-xr-x. 1 root root    258 Dec 26 12:20 script_prepare.py",
        "-rwxr-xr-x. 1 root root   1163 Dec 26 12:20 script_rollback.py",
        "-rwxr-xr-x. 1 root root   2605 Dec 26 12:20 sitef_packages.py",
        "-rwxr-xr-x. 1 root root   3571 Dec 26 12:20 sitef.py",
        "",
        "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package:",
        "total 8",
        "drwxr-xr-x. 2 root root   45 Dec 26 12:24 .",
        "drwxr-xr-x. 3 root root 4096 Dec 26 12:24 ..",
        "-rw-r--r--. 1 root root  139 Dec 26 12:24 sitef-core-0.0.2-0-rollback.yml"
    ]
}
TASK [Executar init_rollback.sh no host (com log + stdout)] ********************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Mostrar últimas linhas do log do rollback (sempre)] **********************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [DEBUG rollback tail] *****************************************************
ok: [localhost] => {
    "rollback_tail.stdout_lines": [
        "===== tail -n 300 /opt/SoftwareExpress/sitef-pipeline/rollback/scripts/init_rollback.log =====",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias finalizada.'",
        "Instalacao de dependencias finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo 'Instalacao de dependencias finalizada.'",
        "Instalacao de dependencias finalizada.",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias finalizada.'",
        "Instalacao de dependencias finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo 'Instalacao de dependencias finalizada.'",
        "Instalacao de dependencias finalizada."
    ]
}
TASK [Atualizar status após execução do init_rollback.sh] **********************
changed: [localhost]
TASK [Montar conteúdo do log do ROLLBACK] **************************************
ok: [localhost]
TASK [Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE] ***
included: /tmp/tmp.MeLaFVIsZs/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'machine_status_file is defined' failed. The error was: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ machine_status_file }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ machine_status_file }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=44   changed=9    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0   
Command finished with status FAILURE
