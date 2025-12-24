segue log da pipeline no harness que executa os ansible que vou mandar abaixo.
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
BRANCH            : develop-testes
STRATEGY          : deploy
GIT_TAG           : DEV000000002
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000000002
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.QIHfT6ehOL...
Cloning into '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 855, done.
remote: Counting objects:   0% (1/805)
remote: Counting objects:   1% (9/805)
remote: Counting objects:   2% (17/805)
remote: Counting objects:   3% (25/805)
remote: Counting objects:   4% (33/805)
remote: Counting objects:   5% (41/805)
remote: Counting objects:   6% (49/805)
remote: Counting objects:   7% (57/805)
remote: Counting objects:   8% (65/805)
remote: Counting objects:   9% (73/805)
remote: Counting objects:  10% (81/805)
remote: Counting objects:  11% (89/805)
remote: Counting objects:  12% (97/805)
remote: Counting objects:  13% (105/805)
remote: Counting objects:  14% (113/805)
remote: Counting objects:  15% (121/805)
remote: Counting objects:  16% (129/805)
remote: Counting objects:  17% (137/805)
remote: Counting objects:  18% (145/805)
remote: Counting objects:  19% (153/805)
remote: Counting objects:  20% (161/805)
remote: Counting objects:  21% (170/805)
remote: Counting objects:  22% (178/805)
remote: Counting objects:  23% (186/805)
remote: Counting objects:  24% (194/805)
remote: Counting objects:  25% (202/805)
remote: Counting objects:  26% (210/805)
remote: Counting objects:  27% (218/805)
remote: Counting objects:  28% (226/805)
remote: Counting objects:  29% (234/805)
remote: Counting objects:  30% (242/805)
remote: Counting objects:  31% (250/805)
remote: Counting objects:  32% (258/805)
remote: Counting objects:  33% (266/805)
remote: Counting objects:  34% (274/805)
remote: Counting objects:  35% (282/805)
remote: Counting objects:  36% (290/805)
remote: Counting objects:  37% (298/805)
remote: Counting objects:  38% (306/805)
remote: Counting objects:  39% (314/805)
remote: Counting objects:  40% (322/805)
remote: Counting objects:  41% (331/805)
remote: Counting objects:  42% (339/805)
remote: Counting objects:  43% (347/805)
remote: Counting objects:  44% (355/805)
remote: Counting objects:  45% (363/805)
remote: Counting objects:  46% (371/805)
remote: Counting objects:  47% (379/805)
remote: Counting objects:  48% (387/805)
remote: Counting objects:  49% (395/805)
remote: Counting objects:  50% (403/805)
remote: Counting objects:  51% (411/805)
remote: Counting objects:  52% (419/805)
remote: Counting objects:  53% (427/805)
remote: Counting objects:  54% (435/805)
remote: Counting objects:  55% (443/805)
remote: Counting objects:  56% (451/805)
remote: Counting objects:  57% (459/805)
remote: Counting objects:  58% (467/805)
remote: Counting objects:  59% (475/805)
remote: Counting objects:  60% (483/805)
remote: Counting objects:  61% (492/805)
remote: Counting objects:  62% (500/805)
remote: Counting objects:  63% (508/805)
remote: Counting objects:  64% (516/805)
remote: Counting objects:  65% (524/805)
remote: Counting objects:  66% (532/805)
remote: Counting objects:  67% (540/805)
remote: Counting objects:  68% (548/805)
remote: Counting objects:  69% (556/805)
remote: Counting objects:  70% (564/805)
remote: Counting objects:  71% (572/805)
remote: Counting objects:  72% (580/805)
remote: Counting objects:  73% (588/805)
remote: Counting objects:  74% (596/805)
remote: Counting objects:  75% (604/805)
remote: Counting objects:  76% (612/805)
remote: Counting objects:  77% (620/805)
remote: Counting objects:  78% (628/805)
remote: Counting objects:  79% (636/805)
remote: Counting objects:  80% (644/805)
remote: Counting objects:  81% (653/805)
remote: Counting objects:  82% (661/805)
remote: Counting objects:  83% (669/805)
remote: Counting objects:  84% (677/805)
remote: Counting objects:  85% (685/805)
remote: Counting objects:  86% (693/805)
remote: Counting objects:  87% (701/805)
remote: Counting objects:  88% (709/805)
remote: Counting objects:  89% (717/805)
remote: Counting objects:  90% (725/805)
remote: Counting objects:  91% (733/805)
remote: Counting objects:  92% (741/805)
remote: Counting objects:  93% (749/805)
remote: Counting objects:  94% (757/805)
remote: Counting objects:  95% (765/805)
remote: Counting objects:  96% (773/805)
remote: Counting objects:  97% (781/805)
remote: Counting objects:  98% (789/805)
remote: Counting objects:  99% (797/805)
remote: Counting objects: 100% (805/805)
remote: Counting objects: 100% (805/805), done.
remote: Compressing objects:   0% (1/790)
remote: Compressing objects:   1% (8/790)
remote: Compressing objects:   2% (16/790)
remote: Compressing objects:   3% (24/790)
remote: Compressing objects:   4% (32/790)
remote: Compressing objects:   5% (40/790)
remote: Compressing objects:   6% (48/790)
remote: Compressing objects:   7% (56/790)
remote: Compressing objects:   8% (64/790)
remote: Compressing objects:   9% (72/790)
remote: Compressing objects:  10% (79/790)
remote: Compressing objects:  11% (87/790)
remote: Compressing objects:  12% (95/790)
remote: Compressing objects:  13% (103/790)
remote: Compressing objects:  14% (111/790)
remote: Compressing objects:  15% (119/790)
remote: Compressing objects:  16% (127/790)
remote: Compressing objects:  17% (135/790)
remote: Compressing objects:  18% (143/790)
remote: Compressing objects:  19% (151/790)
remote: Compressing objects:  20% (158/790)
remote: Compressing objects:  21% (166/790)
remote: Compressing objects:  22% (174/790)
remote: Compressing objects:  23% (182/790)
remote: Compressing objects:  24% (190/790)
remote: Compressing objects:  25% (198/790)
remote: Compressing objects:  26% (206/790)
remote: Compressing objects:  27% (214/790)
remote: Compressing objects:  28% (222/790)
remote: Compressing objects:  29% (230/790)
remote: Compressing objects:  30% (237/790)
remote: Compressing objects:  31% (245/790)
remote: Compressing objects:  32% (253/790)
remote: Compressing objects:  33% (261/790)
remote: Compressing objects:  34% (269/790)
remote: Compressing objects:  35% (277/790)
remote: Compressing objects:  36% (285/790)
remote: Compressing objects:  37% (293/790)
remote: Compressing objects:  38% (301/790)
remote: Compressing objects:  39% (309/790)
remote: Compressing objects:  40% (316/790)
remote: Compressing objects:  41% (324/790)
remote: Compressing objects:  42% (332/790)
remote: Compressing objects:  43% (340/790)
remote: Compressing objects:  44% (348/790)
remote: Compressing objects:  45% (356/790)
remote: Compressing objects:  46% (364/790)
remote: Compressing objects:  47% (372/790)
remote: Compressing objects:  48% (380/790)
remote: Compressing objects:  49% (388/790)
remote: Compressing objects:  50% (395/790)
remote: Compressing objects:  51% (403/790)
remote: Compressing objects:  52% (411/790)
remote: Compressing objects:  53% (419/790)
remote: Compressing objects:  54% (427/790)
remote: Compressing objects:  55% (435/790)
remote: Compressing objects:  56% (443/790)
remote: Compressing objects:  57% (451/790)
remote: Compressing objects:  58% (459/790)
remote: Compressing objects:  59% (467/790)
remote: Compressing objects:  60% (474/790)
remote: Compressing objects:  61% (482/790)
remote: Compressing objects:  62% (490/790)
remote: Compressing objects:  63% (498/790)
remote: Compressing objects:  64% (506/790)
remote: Compressing objects:  65% (514/790)
remote: Compressing objects:  66% (522/790)
remote: Compressing objects:  67% (530/790)
remote: Compressing objects:  68% (538/790)
remote: Compressing objects:  69% (546/790)
remote: Compressing objects:  70% (553/790)
remote: Compressing objects:  71% (561/790)
remote: Compressing objects:  72% (569/790)
remote: Compressing objects:  73% (577/790)
remote: Compressing objects:  74% (585/790)
remote: Compressing objects:  75% (593/790)
remote: Compressing objects:  76% (601/790)
remote: Compressing objects:  77% (609/790)
remote: Compressing objects:  78% (617/790)
remote: Compressing objects:  79% (625/790)
remote: Compressing objects:  80% (632/790)
remote: Compressing objects:  81% (640/790)
remote: Compressing objects:  82% (648/790)
remote: Compressing objects:  83% (656/790)
remote: Compressing objects:  84% (664/790)
remote: Compressing objects:  85% (672/790)
remote: Compressing objects:  86% (680/790)
remote: Compressing objects:  87% (688/790)
remote: Compressing objects:  88% (696/790)
remote: Compressing objects:  89% (704/790)
remote: Compressing objects:  90% (711/790)
remote: Compressing objects:  91% (719/790)
remote: Compressing objects:  92% (727/790)
remote: Compressing objects:  93% (735/790)
remote: Compressing objects:  94% (743/790)
remote: Compressing objects:  95% (751/790)
remote: Compressing objects:  96% (759/790)
remote: Compressing objects:  97% (767/790)
remote: Compressing objects:  98% (775/790)
remote: Compressing objects:  99% (783/790)
remote: Compressing objects: 100% (790/790)
remote: Compressing objects: 100% (790/790), done.
Receiving objects:   0% (1/855)
Receiving objects:   1% (9/855)
Receiving objects:   2% (18/855)
Receiving objects:   3% (26/855)
Receiving objects:   4% (35/855)
Receiving objects:   5% (43/855)
Receiving objects:   6% (52/855)
Receiving objects:   7% (60/855)
Receiving objects:   8% (69/855)
Receiving objects:   9% (77/855)
Receiving objects:  10% (86/855)
Receiving objects:  11% (95/855)
Receiving objects:  12% (103/855)
Receiving objects:  13% (112/855)
Receiving objects:  14% (120/855)
Receiving objects:  15% (129/855)
Receiving objects:  16% (137/855)
Receiving objects:  17% (146/855)
Receiving objects:  18% (154/855)
Receiving objects:  19% (163/855)
Receiving objects:  20% (171/855)
Receiving objects:  21% (180/855)
Receiving objects:  22% (189/855)
Receiving objects:  23% (197/855)
Receiving objects:  24% (206/855)
Receiving objects:  25% (214/855)
Receiving objects:  26% (223/855)
Receiving objects:  27% (231/855)
Receiving objects:  28% (240/855)
Receiving objects:  29% (248/855)
Receiving objects:  30% (257/855)
Receiving objects:  31% (266/855)
Receiving objects:  32% (274/855)
Receiving objects:  33% (283/855)
Receiving objects:  34% (291/855)
Receiving objects:  35% (300/855)
Receiving objects:  36% (308/855)
Receiving objects:  37% (317/855)
Receiving objects:  38% (325/855)
Receiving objects:  39% (334/855)
Receiving objects:  40% (342/855)
Receiving objects:  41% (351/855)
Receiving objects:  42% (360/855)
Receiving objects:  43% (368/855)
Receiving objects:  44% (377/855)
Receiving objects:  45% (385/855)
Receiving objects:  46% (394/855)
Receiving objects:  47% (402/855)
Receiving objects:  48% (411/855)
Receiving objects:  49% (419/855)
Receiving objects:  50% (428/855)
Receiving objects:  51% (437/855)
Receiving objects:  52% (445/855)
Receiving objects:  53% (454/855)
Receiving objects:  54% (462/855)
Receiving objects:  55% (471/855)
Receiving objects:  56% (479/855)
Receiving objects:  57% (488/855)
Receiving objects:  58% (496/855)
Receiving objects:  59% (505/855)
Receiving objects:  60% (513/855)
Receiving objects:  61% (522/855)
Receiving objects:  62% (531/855)
Receiving objects:  63% (539/855)
Receiving objects:  64% (548/855)
Receiving objects:  65% (556/855)
Receiving objects:  66% (565/855)
Receiving objects:  67% (573/855)
Receiving objects:  68% (582/855)
Receiving objects:  69% (590/855)
Receiving objects:  70% (599/855)
Receiving objects:  71% (608/855)
Receiving objects:  72% (616/855)
Receiving objects:  73% (625/855)
Receiving objects:  74% (633/855)
Receiving objects:  75% (642/855)
Receiving objects:  76% (650/855)
Receiving objects:  77% (659/855)
Receiving objects:  78% (667/855)
Receiving objects:  79% (676/855)
Receiving objects:  80% (684/855)
Receiving objects:  81% (693/855)
Receiving objects:  82% (702/855)
Receiving objects:  83% (710/855)
Receiving objects:  84% (719/855)
remote: Total 855 (delta 541), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (727/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  86% (736/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  87% (744/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  88% (753/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  89% (761/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  90% (770/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  91% (779/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  92% (787/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  93% (796/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  94% (804/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  95% (813/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  96% (821/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  97% (830/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  98% (838/855), 707.77 KiB | 1.36 MiB/s
Receiving objects:  99% (847/855), 707.77 KiB | 1.36 MiB/s
Receiving objects: 100% (855/855), 707.77 KiB | 1.36 MiB/s
Receiving objects: 100% (855/855), 928.02 KiB | 1.78 MiB/s, done.
Resolving deltas:   0% (0/560)
Resolving deltas:   1% (6/560)
Resolving deltas:   2% (12/560)
Resolving deltas:   3% (17/560)
Resolving deltas:   4% (23/560)
Resolving deltas:   5% (28/560)
Resolving deltas:   6% (34/560)
Resolving deltas:   7% (40/560)
Resolving deltas:   8% (45/560)
Resolving deltas:   9% (52/560)
Resolving deltas:  10% (56/560)
Resolving deltas:  11% (62/560)
Resolving deltas:  12% (68/560)
Resolving deltas:  13% (73/560)
Resolving deltas:  14% (79/560)
Resolving deltas:  15% (84/560)
Resolving deltas:  16% (90/560)
Resolving deltas:  17% (96/560)
Resolving deltas:  18% (101/560)
Resolving deltas:  19% (107/560)
Resolving deltas:  20% (112/560)
Resolving deltas:  21% (118/560)
Resolving deltas:  22% (124/560)
Resolving deltas:  23% (129/560)
Resolving deltas:  24% (135/560)
Resolving deltas:  25% (140/560)
Resolving deltas:  26% (146/560)
Resolving deltas:  27% (152/560)
Resolving deltas:  28% (157/560)
Resolving deltas:  29% (163/560)
Resolving deltas:  30% (168/560)
Resolving deltas:  31% (174/560)
Resolving deltas:  32% (180/560)
Resolving deltas:  33% (185/560)
Resolving deltas:  34% (191/560)
Resolving deltas:  35% (196/560)
Resolving deltas:  36% (202/560)
Resolving deltas:  37% (208/560)
Resolving deltas:  38% (213/560)
Resolving deltas:  39% (219/560)
Resolving deltas:  40% (224/560)
Resolving deltas:  41% (230/560)
Resolving deltas:  42% (236/560)
Resolving deltas:  43% (241/560)
Resolving deltas:  44% (247/560)
Resolving deltas:  45% (252/560)
Resolving deltas:  46% (258/560)
Resolving deltas:  47% (264/560)
Resolving deltas:  48% (269/560)
Resolving deltas:  49% (275/560)
Resolving deltas:  50% (280/560)
Resolving deltas:  51% (286/560)
Resolving deltas:  52% (292/560)
Resolving deltas:  53% (297/560)
Resolving deltas:  54% (303/560)
Resolving deltas:  55% (308/560)
Resolving deltas:  56% (314/560)
Resolving deltas:  57% (320/560)
Resolving deltas:  58% (325/560)
Resolving deltas:  59% (331/560)
Resolving deltas:  60% (336/560)
Resolving deltas:  61% (342/560)
Resolving deltas:  62% (348/560)
Resolving deltas:  63% (353/560)
Resolving deltas:  64% (359/560)
Resolving deltas:  65% (364/560)
Resolving deltas:  66% (370/560)
Resolving deltas:  67% (376/560)
Resolving deltas:  68% (381/560)
Resolving deltas:  69% (387/560)
Resolving deltas:  70% (392/560)
Resolving deltas:  71% (398/560)
Resolving deltas:  72% (404/560)
Resolving deltas:  73% (409/560)
Resolving deltas:  74% (415/560)
Resolving deltas:  75% (420/560)
Resolving deltas:  76% (426/560)
Resolving deltas:  77% (432/560)
Resolving deltas:  78% (437/560)
Resolving deltas:  79% (444/560)
Resolving deltas:  80% (448/560)
Resolving deltas:  81% (454/560)
Resolving deltas:  82% (460/560)
Resolving deltas:  83% (465/560)
Resolving deltas:  84% (471/560)
Resolving deltas:  85% (476/560)
Resolving deltas:  86% (482/560)
Resolving deltas:  87% (488/560)
Resolving deltas:  88% (493/560)
Resolving deltas:  89% (499/560)
Resolving deltas:  90% (504/560)
Resolving deltas:  91% (510/560)
Resolving deltas:  92% (516/560)
Resolving deltas:  93% (521/560)
Resolving deltas:  94% (527/560)
Resolving deltas:  95% (532/560)
Resolving deltas:  96% (538/560)
Resolving deltas:  97% (544/560)
Resolving deltas:  98% (549/560)
Resolving deltas:  99% (555/560)
Resolving deltas: 100% (560/560)
Resolving deltas: 100% (560/560), done.
====================================================
== DEPLOY -> sitef-02
====================================================

PLAY [Deploy/Rollback por máquina] *********************************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Normalizar deploy_action (deploy/rollback)] ******************************
ok: [localhost]
TASK [Resolver filestore_env e filestore_base_dir (deploy/rollback)] ***********
ok: [localhost]
TASK [Resolver Nexus (base_url, user, password)] *******************************
ok: [localhost]
TASK [Mostrar variáveis de entrada e resolvidas (deploy/rollback)] *************
ok: [localhost] => {
    "msg": [
        "deployment_ref               = DEV000000002",
        "machine_name                 = sitef-02",
        "deploy_action                = deploy",
        "deploy_action_resolved       = deploy",
        "repo_root_resolved           = /tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../status/DEV000000002",
        "nexus_base_url_resolved      = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev",
        "nexus_user_resolved          = (vazio)",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000000002"
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
included: /tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 6352590, 'dev': 64781, 'nlink': 1, 'atime': 1765830336.9463093, 'mtime': 1765830336.9343092, 'ctime': 1765830336.9343092, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2491568688', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.QIHfT6ehOL/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
TASK [Deploy | Registrar host dinâmico] ****************************************
changed: [localhost]
TASK [Deploy | Montar env final] ***********************************************
ok: [localhost]
TASK [Deploy | Definir deployment_ref_lower (fix ansible set_fact)] ************
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
TASK [Deploy | Atualizar status (deploy:queued) com append em history] *********
changed: [localhost]
TASK [Deploy | Verificar init_deploy.sh no host] *******************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | Falhar se init_deploy.sh não existir] ***************************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "init_deploy.sh não encontrado em /opt/SoftwareExpress/sitef-pipeline/deploy/scripts. Rode o predeploy antes."}
PLAY RECAP *********************************************************************
localhost                  : ok=27   changed=4    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0   
Command finished with status FAILURE


# deploy_from_status.yml
---
# PIPELINE 2 - DEPLOY / ROLLBACK
# - Recebe machine_name (ou lista via loop externo no script do Harness)
# - Usa TAG (deployment_ref) como referência da execução
# - Para deploy:
#     - Reusa área /opt/SoftwareExpress/sitef-pipeline/deploy/scripts preparada no predeploy
#     - Executa init_deploy.sh
# - Para rollback:
#     - Prepara /opt/SoftwareExpress/sitef-pipeline/rollback
#     - Baixa componentes do Nexus
#     - Executa init_rollback.sh
# - Atualiza status em JSON por máquina
# - Faz upload de JSON + LOG para Harness File Store

- name: "Deploy/Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # TAG da execução (vem do GIT_TAG no Harness)
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    # Nome lógico da máquina (ex: sitef-01)
    machine_name: "{{ machine_name | default('') }}"

    # Ação solicitada: deploy ou rollback (vem do STRATEGY/ACTION no Harness)
    deploy_action: "{{ deploy_action | default('deploy') }}"

    # Paths do repositório
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref }}"

    # URL default do Nexus (evita recursão)
    nexus_base_url_default: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"

  tasks:
    # -------------------------------------------------------------------
    # Normalizar deploy_action (deploy/rollback)
    # -------------------------------------------------------------------
    - name: "Normalizar deploy_action (deploy/rollback)"
      ansible.builtin.set_fact:
        deploy_action_resolved: >-
          {{
            (deploy_action | string | lower | trim)
              if (deploy_action is defined and (deploy_action | string | trim) | length > 0)
              else 'deploy'
          }}

    # -------------------------------------------------------------------
    # Resolver filestore_env e filestore_base_dir sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver filestore_env e filestore_base_dir (deploy/rollback)"
      ansible.builtin.set_fact:
        filestore_env_resolved: >-
          {{
            (filestore_env | string | trim)
              if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
              else 'dev'
          }}
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir | string | trim)
              if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0)
              else (
                (
                  (filestore_env | string | trim)
                    if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
                    else 'dev'
                ) ~ '/' ~ deployment_ref
              )
          }}

    # -------------------------------------------------------------------
    # Resolver Nexus (base_url, user, password) sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver Nexus (base_url, user, password)"
      ansible.builtin.set_fact:
        nexus_base_url_resolved: >-
          {{
            (nexus_base_url | string | trim)
              if (nexus_base_url is defined and (nexus_base_url | string | trim) | length > 0)
              else nexus_base_url_default
          }}
        nexus_user_resolved: >-
          {{
            (nexus_user | string | trim)
              if (nexus_user is defined)
              else ''
          }}
        nexus_password_resolved: >-
          {{
            (nexus_password | string | trim)
              if (nexus_password is defined)
              else ''
          }}

    # -------------------------------------------------------------------
    # Debug das principais variáveis de entrada / resolvidas
    # -------------------------------------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas (deploy/rollback)"
      ansible.builtin.debug:
        msg:
          - "deployment_ref               = {{ deployment_ref }}"
          - "machine_name                 = {{ machine_name }}"
          - "deploy_action                = {{ deploy_action }}"
          - "deploy_action_resolved       = {{ deploy_action_resolved }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
          - "nexus_user_resolved          = {{ '***' if (nexus_user_resolved | length) > 0 else '(vazio)' }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"

    # -------------------------------------------------------------------
    # Garantir diretório base de status da TAG
    # -------------------------------------------------------------------
    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # -------------------------------------------------------------------
    # Validar machine_name
    # -------------------------------------------------------------------
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name is defined
          - (machine_name | string | trim) | length > 0
        fail_msg: "machine_name não informado"

    # -------------------------------------------------------------------
    # Branch de DEPLOY
    # -------------------------------------------------------------------
    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      when: deploy_action_resolved == 'deploy'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"

    # -------------------------------------------------------------------
    # Branch de ROLLBACK
    # -------------------------------------------------------------------
    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      when: deploy_action_resolved == 'rollback'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"


# deploy_per_machine.yml
---
# =====================================================================================
# DEPLOY POR MÁQUINA
# Chamado por deploy_from_status.yml (PIPELINE 2)
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Paths e defaults
# -----------------------------------------------------------------------------
- name: "Deploy | Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ (machine_name | string | trim) }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log"

- name: "Deploy | Definir diretórios principais do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 2) Resolver machine_file
# -----------------------------------------------------------------------------
- name: "Deploy | Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Deploy | Verificar candidatos"
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
# 6) Status file local + paths lógicos do filestore  (FIX: split set_fact)
# -----------------------------------------------------------------------------
- name: "Deploy | Definir deployment_ref_lower (fix ansible set_fact)"
  ansible.builtin.set_fact:
    deployment_ref_lower: "{{ deployment_ref | lower }}"

- name: "Deploy | Definir arquivos de status/log local"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"

    env_folder: "{{ filestore_env | lower }}"
    deployment_ref_folder: "{{ deployment_ref }}"

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
    log_content: "{{ log_content_to_upload }}"
    env_folder: "{{ env_folder }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}
    extra_tags: []

# -----------------------------------------------------------------------------
# 11) Falhar pipeline se deploy retornou erro (depois do upload)
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: (deploy_result.rc | default(1)) != 0


## Segue como esta script no harness

#!/usr/bin/env bash
set -euo pipefail

# =========================================================
# DEFAULTS / INPUTS DO HARNESS
# =========================================================
BRANCH="${BRANCH:-develop-testes}"
STRATEGY="${STRATEGY:-deploy}"        # deploy | rollback
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"

# >>> CORREÇÃO DO ERRO (unbound variable)
FILESTORE_ENV="${FILESTORE_ENV:-dev}"
FILESTORE_BASE_DIR="${FILESTORE_BASE_DIR:-${FILESTORE_ENV}/${GIT_TAG}}"

# =========================================================
# GIT / HARNESS
# =========================================================
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0}"

# =========================================================
# HEADER
# =========================================================
echo "== DEPLOY PIPELINE =="
echo "BRANCH            : ${BRANCH}"
echo "STRATEGY          : ${STRATEGY}"
echo "GIT_TAG           : ${GIT_TAG}"
echo "MACHINES          : ${MACHINES}"
echo "FILESTORE_ENV     : ${FILESTORE_ENV}"
echo "FILESTORE_BASEDIR : ${FILESTORE_BASE_DIR}"
echo

# =========================================================
# VALIDAÇÕES
# =========================================================
if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

# =========================================================
# NORMALIZA LISTA DE MÁQUINAS
# - aceita .yml/.yaml
# - aceita espaço ou vírgula
# =========================================================
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

# =========================================================
# CLONE DO REPO
# =========================================================
WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

# =========================================================
# LOOP POR MÁQUINA
# =========================================================
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
    -e "filestore_base_dir=${FILESTORE_BASE_DIR}" \
    -e "harness_x_api_key=${HARNESS_X_API_KEY}"

done

echo
echo "== Pipeline finalizada com sucesso =="

