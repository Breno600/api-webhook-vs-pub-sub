log atual

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


segue log da pipeline de pre deploy que foi com sucesso
Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export GIT_TAG=DEV000000002
Clonando repo em /tmp/tmp.rybDSUe1kz...
Cloning into '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 843, done.
remote: Counting objects:   0% (1/793)
remote: Counting objects:   1% (8/793)
remote: Counting objects:   2% (16/793)
remote: Counting objects:   3% (24/793)
remote: Counting objects:   4% (32/793)
remote: Counting objects:   5% (40/793)
remote: Counting objects:   6% (48/793)
remote: Counting objects:   7% (56/793)
remote: Counting objects:   8% (64/793)
remote: Counting objects:   9% (72/793)
remote: Counting objects:  10% (80/793)
remote: Counting objects:  11% (88/793)
remote: Counting objects:  12% (96/793)
remote: Counting objects:  13% (104/793)
remote: Counting objects:  14% (112/793)
remote: Counting objects:  15% (119/793)
remote: Counting objects:  16% (127/793)
remote: Counting objects:  17% (135/793)
remote: Counting objects:  18% (143/793)
remote: Counting objects:  19% (151/793)
remote: Counting objects:  20% (159/793)
remote: Counting objects:  21% (167/793)
remote: Counting objects:  22% (175/793)
remote: Counting objects:  23% (183/793)
remote: Counting objects:  24% (191/793)
remote: Counting objects:  25% (199/793)
remote: Counting objects:  26% (207/793)
remote: Counting objects:  27% (215/793)
remote: Counting objects:  28% (223/793)
remote: Counting objects:  29% (230/793)
remote: Counting objects:  30% (238/793)
remote: Counting objects:  31% (246/793)
remote: Counting objects:  32% (254/793)
remote: Counting objects:  33% (262/793)
remote: Counting objects:  34% (270/793)
remote: Counting objects:  35% (278/793)
remote: Counting objects:  36% (286/793)
remote: Counting objects:  37% (294/793)
remote: Counting objects:  38% (302/793)
remote: Counting objects:  39% (310/793)
remote: Counting objects:  40% (318/793)
remote: Counting objects:  41% (326/793)
remote: Counting objects:  42% (334/793)
remote: Counting objects:  43% (341/793)
remote: Counting objects:  44% (349/793)
remote: Counting objects:  45% (357/793)
remote: Counting objects:  46% (365/793)
remote: Counting objects:  47% (373/793)
remote: Counting objects:  48% (381/793)
remote: Counting objects:  49% (389/793)
remote: Counting objects:  50% (397/793)
remote: Counting objects:  51% (405/793)
remote: Counting objects:  52% (413/793)
remote: Counting objects:  53% (421/793)
remote: Counting objects:  54% (429/793)
remote: Counting objects:  55% (437/793)
remote: Counting objects:  56% (445/793)
remote: Counting objects:  57% (453/793)
remote: Counting objects:  58% (460/793)
remote: Counting objects:  59% (468/793)
remote: Counting objects:  60% (476/793)
remote: Counting objects:  61% (484/793)
remote: Counting objects:  62% (492/793)
remote: Counting objects:  63% (500/793)
remote: Counting objects:  64% (508/793)
remote: Counting objects:  65% (516/793)
remote: Counting objects:  66% (524/793)
remote: Counting objects:  67% (532/793)
remote: Counting objects:  68% (540/793)
remote: Counting objects:  69% (548/793)
remote: Counting objects:  70% (556/793)
remote: Counting objects:  71% (564/793)
remote: Counting objects:  72% (571/793)
remote: Counting objects:  73% (579/793)
remote: Counting objects:  74% (587/793)
remote: Counting objects:  75% (595/793)
remote: Counting objects:  76% (603/793)
remote: Counting objects:  77% (611/793)
remote: Counting objects:  78% (619/793)
remote: Counting objects:  79% (627/793)
remote: Counting objects:  80% (635/793)
remote: Counting objects:  81% (643/793)
remote: Counting objects:  82% (651/793)
remote: Counting objects:  83% (659/793)
remote: Counting objects:  84% (667/793)
remote: Counting objects:  85% (675/793)
remote: Counting objects:  86% (682/793)
remote: Counting objects:  87% (690/793)
remote: Counting objects:  88% (698/793)
remote: Counting objects:  89% (706/793)
remote: Counting objects:  90% (714/793)
remote: Counting objects:  91% (722/793)
remote: Counting objects:  92% (730/793)
remote: Counting objects:  93% (738/793)
remote: Counting objects:  94% (746/793)
remote: Counting objects:  95% (754/793)
remote: Counting objects:  96% (762/793)
remote: Counting objects:  97% (770/793)
remote: Counting objects:  98% (778/793)
remote: Counting objects:  99% (786/793)
remote: Counting objects: 100% (793/793)
remote: Counting objects: 100% (793/793), done.
remote: Compressing objects:   0% (1/778)
remote: Compressing objects:   1% (8/778)
remote: Compressing objects:   2% (16/778)
remote: Compressing objects:   3% (24/778)
remote: Compressing objects:   4% (32/778)
remote: Compressing objects:   5% (39/778)
remote: Compressing objects:   6% (47/778)
remote: Compressing objects:   7% (55/778)
remote: Compressing objects:   8% (63/778)
remote: Compressing objects:   9% (71/778)
remote: Compressing objects:  10% (78/778)
remote: Compressing objects:  11% (86/778)
remote: Compressing objects:  12% (94/778)
remote: Compressing objects:  13% (102/778)
remote: Compressing objects:  14% (109/778)
remote: Compressing objects:  15% (117/778)
remote: Compressing objects:  16% (125/778)
remote: Compressing objects:  17% (133/778)
remote: Compressing objects:  18% (141/778)
remote: Compressing objects:  19% (148/778)
remote: Compressing objects:  20% (156/778)
remote: Compressing objects:  21% (164/778)
remote: Compressing objects:  22% (172/778)
remote: Compressing objects:  23% (179/778)
remote: Compressing objects:  24% (187/778)
remote: Compressing objects:  25% (195/778)
remote: Compressing objects:  26% (203/778)
remote: Compressing objects:  27% (211/778)
remote: Compressing objects:  28% (218/778)
remote: Compressing objects:  29% (226/778)
remote: Compressing objects:  30% (234/778)
remote: Compressing objects:  31% (242/778)
remote: Compressing objects:  32% (249/778)
remote: Compressing objects:  33% (257/778)
remote: Compressing objects:  34% (265/778)
remote: Compressing objects:  35% (273/778)
remote: Compressing objects:  36% (281/778)
remote: Compressing objects:  37% (288/778)
remote: Compressing objects:  38% (296/778)
remote: Compressing objects:  39% (304/778)
remote: Compressing objects:  40% (312/778)
remote: Compressing objects:  41% (319/778)
remote: Compressing objects:  42% (327/778)
remote: Compressing objects:  43% (335/778)
remote: Compressing objects:  44% (343/778)
remote: Compressing objects:  45% (351/778)
remote: Compressing objects:  46% (358/778)
remote: Compressing objects:  47% (366/778)
remote: Compressing objects:  48% (374/778)
remote: Compressing objects:  49% (382/778)
remote: Compressing objects:  50% (389/778)
remote: Compressing objects:  51% (397/778)
remote: Compressing objects:  52% (405/778)
remote: Compressing objects:  53% (413/778)
remote: Compressing objects:  54% (421/778)
remote: Compressing objects:  55% (428/778)
remote: Compressing objects:  56% (436/778)
remote: Compressing objects:  57% (444/778)
remote: Compressing objects:  58% (452/778)
remote: Compressing objects:  59% (460/778)
remote: Compressing objects:  60% (467/778)
remote: Compressing objects:  61% (475/778)
remote: Compressing objects:  62% (483/778)
remote: Compressing objects:  63% (491/778)
remote: Compressing objects:  64% (498/778)
remote: Compressing objects:  65% (506/778)
remote: Compressing objects:  66% (514/778)
remote: Compressing objects:  67% (522/778)
remote: Compressing objects:  68% (530/778)
remote: Compressing objects:  69% (537/778)
remote: Compressing objects:  70% (545/778)
remote: Compressing objects:  71% (553/778)
remote: Compressing objects:  72% (561/778)
remote: Compressing objects:  73% (568/778)
remote: Compressing objects:  74% (576/778)
remote: Compressing objects:  75% (584/778)
remote: Compressing objects:  76% (592/778)
remote: Compressing objects:  77% (600/778)
remote: Compressing objects:  78% (607/778)
remote: Compressing objects:  79% (615/778)
remote: Compressing objects:  80% (623/778)
remote: Compressing objects:  81% (631/778)
remote: Compressing objects:  82% (638/778)
remote: Compressing objects:  83% (646/778)
remote: Compressing objects:  84% (654/778)
remote: Compressing objects:  85% (662/778)
remote: Compressing objects:  86% (670/778)
remote: Compressing objects:  87% (677/778)
remote: Compressing objects:  88% (685/778)
remote: Compressing objects:  89% (693/778)
remote: Compressing objects:  90% (701/778)
remote: Compressing objects:  91% (708/778)
remote: Compressing objects:  92% (716/778)
remote: Compressing objects:  93% (724/778)
remote: Compressing objects:  94% (732/778)
remote: Compressing objects:  95% (740/778)
remote: Compressing objects:  96% (747/778)
remote: Compressing objects:  97% (755/778)
remote: Compressing objects:  98% (763/778)
remote: Compressing objects:  99% (771/778)
remote: Compressing objects: 100% (778/778)
remote: Compressing objects: 100% (778/778), done.
Receiving objects:   0% (1/843)
Receiving objects:   1% (9/843)
Receiving objects:   2% (17/843)
Receiving objects:   3% (26/843)
Receiving objects:   4% (34/843)
Receiving objects:   5% (43/843)
Receiving objects:   6% (51/843)
Receiving objects:   7% (60/843)
Receiving objects:   8% (68/843)
Receiving objects:   9% (76/843)
Receiving objects:  10% (85/843)
Receiving objects:  11% (93/843)
Receiving objects:  12% (102/843)
Receiving objects:  13% (110/843)
Receiving objects:  14% (119/843)
Receiving objects:  15% (127/843)
Receiving objects:  16% (135/843)
Receiving objects:  17% (144/843)
Receiving objects:  18% (152/843)
Receiving objects:  19% (161/843)
Receiving objects:  20% (169/843)
Receiving objects:  21% (178/843)
Receiving objects:  22% (186/843)
Receiving objects:  23% (194/843)
Receiving objects:  24% (203/843)
Receiving objects:  25% (211/843)
Receiving objects:  26% (220/843)
Receiving objects:  27% (228/843)
Receiving objects:  28% (237/843)
Receiving objects:  29% (245/843)
Receiving objects:  30% (253/843)
Receiving objects:  31% (262/843)
Receiving objects:  32% (270/843)
Receiving objects:  33% (279/843)
Receiving objects:  34% (287/843)
Receiving objects:  35% (296/843)
Receiving objects:  36% (304/843)
Receiving objects:  37% (312/843)
Receiving objects:  38% (321/843)
Receiving objects:  39% (329/843)
Receiving objects:  40% (338/843)
Receiving objects:  41% (346/843)
Receiving objects:  42% (355/843)
Receiving objects:  43% (363/843)
Receiving objects:  44% (371/843)
Receiving objects:  45% (380/843)
Receiving objects:  46% (388/843)
Receiving objects:  47% (397/843)
Receiving objects:  48% (405/843)
Receiving objects:  49% (414/843)
Receiving objects:  50% (422/843)
Receiving objects:  51% (430/843)
Receiving objects:  52% (439/843)
Receiving objects:  53% (447/843)
Receiving objects:  54% (456/843)
Receiving objects:  55% (464/843)
Receiving objects:  56% (473/843)
Receiving objects:  57% (481/843)
Receiving objects:  58% (489/843)
Receiving objects:  59% (498/843)
Receiving objects:  60% (506/843)
Receiving objects:  61% (515/843)
Receiving objects:  62% (523/843)
Receiving objects:  63% (532/843)
Receiving objects:  64% (540/843)
Receiving objects:  65% (548/843)
Receiving objects:  66% (557/843)
Receiving objects:  67% (565/843)
Receiving objects:  68% (574/843)
Receiving objects:  69% (582/843)
Receiving objects:  70% (591/843)
Receiving objects:  71% (599/843)
Receiving objects:  72% (607/843)
Receiving objects:  73% (616/843)
Receiving objects:  74% (624/843)
Receiving objects:  75% (633/843)
Receiving objects:  76% (641/843)
Receiving objects:  77% (650/843)
Receiving objects:  78% (658/843)
Receiving objects:  79% (666/843)
Receiving objects:  80% (675/843)
Receiving objects:  81% (683/843)
Receiving objects:  82% (692/843)
Receiving objects:  83% (700/843)
Receiving objects:  84% (709/843)
remote: Total 843 (delta 529), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (717/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  86% (725/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  87% (734/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  88% (742/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  89% (751/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  90% (759/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  91% (768/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  92% (776/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  93% (784/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  94% (793/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  95% (801/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  96% (810/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  97% (818/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  98% (827/843), 707.77 KiB | 1.34 MiB/s
Receiving objects:  99% (835/843), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (843/843), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (843/843), 924.29 KiB | 1.75 MiB/s, done.
Resolving deltas:   0% (0/548)
Resolving deltas:   1% (6/548)
Resolving deltas:   2% (11/548)
Resolving deltas:   3% (17/548)
Resolving deltas:   4% (22/548)
Resolving deltas:   5% (28/548)
Resolving deltas:   6% (33/548)
Resolving deltas:   7% (39/548)
Resolving deltas:   8% (44/548)
Resolving deltas:   9% (50/548)
Resolving deltas:  10% (55/548)
Resolving deltas:  11% (61/548)
Resolving deltas:  12% (66/548)
Resolving deltas:  13% (72/548)
Resolving deltas:  14% (78/548)
Resolving deltas:  15% (83/548)
Resolving deltas:  16% (88/548)
Resolving deltas:  17% (94/548)
Resolving deltas:  18% (99/548)
Resolving deltas:  19% (105/548)
Resolving deltas:  20% (110/548)
Resolving deltas:  21% (116/548)
Resolving deltas:  22% (121/548)
Resolving deltas:  23% (127/548)
Resolving deltas:  24% (132/548)
Resolving deltas:  25% (137/548)
Resolving deltas:  26% (143/548)
Resolving deltas:  27% (149/548)
Resolving deltas:  28% (154/548)
Resolving deltas:  29% (159/548)
Resolving deltas:  30% (165/548)
Resolving deltas:  31% (170/548)
Resolving deltas:  32% (176/548)
Resolving deltas:  33% (181/548)
Resolving deltas:  34% (187/548)
Resolving deltas:  35% (192/548)
Resolving deltas:  36% (198/548)
Resolving deltas:  37% (203/548)
Resolving deltas:  38% (209/548)
Resolving deltas:  39% (214/548)
Resolving deltas:  40% (220/548)
Resolving deltas:  41% (225/548)
Resolving deltas:  42% (231/548)
Resolving deltas:  43% (236/548)
Resolving deltas:  44% (242/548)
Resolving deltas:  45% (247/548)
Resolving deltas:  46% (253/548)
Resolving deltas:  47% (258/548)
Resolving deltas:  48% (264/548)
Resolving deltas:  49% (269/548)
Resolving deltas:  50% (274/548)
Resolving deltas:  51% (280/548)
Resolving deltas:  52% (285/548)
Resolving deltas:  53% (291/548)
Resolving deltas:  54% (296/548)
Resolving deltas:  55% (302/548)
Resolving deltas:  56% (307/548)
Resolving deltas:  57% (313/548)
Resolving deltas:  58% (318/548)
Resolving deltas:  59% (324/548)
Resolving deltas:  60% (329/548)
Resolving deltas:  61% (335/548)
Resolving deltas:  62% (340/548)
Resolving deltas:  63% (346/548)
Resolving deltas:  64% (351/548)
Resolving deltas:  65% (357/548)
Resolving deltas:  66% (362/548)
Resolving deltas:  67% (368/548)
Resolving deltas:  68% (373/548)
Resolving deltas:  69% (379/548)
Resolving deltas:  70% (384/548)
Resolving deltas:  71% (390/548)
Resolving deltas:  72% (395/548)
Resolving deltas:  73% (401/548)
Resolving deltas:  74% (406/548)
Resolving deltas:  75% (411/548)
Resolving deltas:  76% (418/548)
Resolving deltas:  77% (422/548)
Resolving deltas:  78% (428/548)
Resolving deltas:  79% (433/548)
Resolving deltas:  80% (439/548)
Resolving deltas:  81% (444/548)
Resolving deltas:  82% (450/548)
Resolving deltas:  83% (455/548)
Resolving deltas:  84% (461/548)
Resolving deltas:  85% (466/548)
Resolving deltas:  86% (472/548)
Resolving deltas:  87% (477/548)
Resolving deltas:  88% (483/548)
Resolving deltas:  89% (488/548)
Resolving deltas:  90% (494/548)
Resolving deltas:  91% (499/548)
Resolving deltas:  92% (505/548)
Resolving deltas:  93% (510/548)
Resolving deltas:  94% (516/548)
Resolving deltas:  95% (521/548)
Resolving deltas:  96% (527/548)
Resolving deltas:  97% (532/548)
Resolving deltas:  98% (538/548)
Resolving deltas:  99% (543/548)
Resolving deltas: 100% (548/548)
Resolving deltas: 100% (548/548), done.
== PIPELINE PREDEPLOY ==
TAG   : DEV000000002
EXEC  : execution/machine_list_dev.yml
BRANCH: develop-testes
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/localhost as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with auto plugin: no root 'plugin'
key found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml' is not a valid YAML inventory
plugin config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with ini plugin: Invalid host
pattern '---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with auto plugin: no root 'plugin'
key found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml' is not a valid YAML inventory
plugin config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with ini plugin:
/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml:5: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with ini plugin:
/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml:5: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml as an inventory source
[WARNING]: Unable to parse /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-
sitef/ansible as an inventory source
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [Predeploy a partir do arquivo de execução] *******************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Resolver filestore_env e filestore_base_dir sem recursão] ****************
ok: [localhost]
TASK [Mostrar variáveis de entrada e resolvidas] *******************************
ok: [localhost] => {
    "msg": [
        "execution_file_name          = execution/machine_list_dev.yml",
        "deployment_ref               = DEV000000002",
        "repo_root_resolved           = /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../status/DEV000000002",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000000002",
        "nexus_base_url               = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    ]
}
TASK [Criar diretório de status da TAG] ****************************************
changed: [localhost]
TASK [Carregar arquivo de execução] ********************************************
ok: [localhost]
TASK [Falhar se não tiver máquinas no arquivo de execução] *********************
skipping: [localhost]
TASK [Executar pré-deploy por máquina] *****************************************
included: /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/predeploy_per_machine.yml for localhost => (item=sitef-02)
TASK [Resolver nome da máquina atual] ******************************************
ok: [localhost]
TASK [Definir paths base do repositório] ***************************************
ok: [localhost]
TASK [Definir execution_dir do repositório] ************************************
ok: [localhost]
TASK [Definir machines_dir, packages_dir e scripts_dir do repositório] *********
ok: [localhost]
TASK [Definir candidatos de arquivo da máquina] ********************************
ok: [localhost]
TASK [Verificar candidatos de arquivo da máquina] ******************************
ok: [localhost] => (item=/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Selecionar machine_file existente] ***************************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 6344281, 'dev': 64781, 'nlink': 1, 'atime': 1765825245.3139253, 'mtime': 1765825245.299925, 'ctime': 1765825245.299925, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '4280017944', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
TASK [Falhar se arquivo da máquina não existir em nenhum caminho esperado] *****
skipping: [localhost]
TASK [Carregar config da máquina sitef-02] *************************************
ok: [localhost]
TASK [Validar package definido] ************************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Carregar config do pacote sitef-core-0.0.1-0] ****************************
ok: [localhost]
TASK [Validar components do pacote] ********************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Calcular env_vars mescladas] *********************************************
ok: [localhost]
TASK [Definir usuário alvo padrão] *********************************************
ok: [localhost]
TASK [Definir ssh_common_args padrão] ******************************************
ok: [localhost]
TASK [Aplicar ProxyJump via bastion (quando necessário)] ***********************
skipping: [localhost]
TASK [Registrar host dinâmico para sitef-02] ***********************************
changed: [localhost]
TASK [Mostrar hostname/IP da máquina alvo (debug)] *****************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Debug target_ident] ******************************************************
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
TASK [Definir caminhos de status e deploy] *************************************
ok: [localhost]
TASK [Calcular caminhos de status/log] *****************************************
ok: [localhost]
TASK [Criar diretório de status da máquina] ************************************
changed: [localhost]
TASK [Escrever status inicial (pre-deploy:queued)] *****************************
changed: [localhost]
TASK [Garantir base do pipeline no host] ***************************************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/components)
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts)
ok: [localhost -> sitef-02(100.99.57.128)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package)
TASK [Limpar pasta de scripts do pacote] ***************************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Recriar pasta de scripts do pacote] **************************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Copiar package.yml para o host] ******************************************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Garantir diretórios dos componentes no host] *****************************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=packages/linux/sitef-core-0.0.1-0.x86_64.rpm)
TASK [Baixar componentes do Nexus] *********************************************
ok: [localhost -> sitef-02(100.99.57.128)] => (item=packages/linux/sitef-core-0.0.1-0.x86_64.rpm)
TASK [Copiar scripts do pacote para o host] ************************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Listar conteúdo de /deploy/scripts (debug)] ******************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Mostrar conteúdo de scripts (debug)] *************************************
ok: [localhost] => {
    "scripts_ls.stdout_lines": [
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts:",
        "total 16",
        "drwxr-xr-x. 3 root root 105 Dec 15 19:00 .",
        "drwxr-xr-x. 4 root root  39 Dec 15 11:37 ..",
        "-rw-r-----. 1 root root 570 Dec 15 18:58 init_parallel.log",
        "-rwxr-xr-x. 1 root root  95 Dec 15 11:37 init_parallel.sh",
        "-rwxr-xr-x. 1 root root 162 Dec 15 11:37 init.sh",
        "drwxr-xr-x. 2 root root  36 Dec 15 19:00 package",
        "-rw-r-----. 1 root root  39 Dec 15 18:58 parallel.txt",
        "",
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package:",
        "total 4",
        "drwxr-xr-x. 2 root root  36 Dec 15 19:00 .",
        "drwxr-xr-x. 3 root root 105 Dec 15 19:00 ..",
        "-rw-r--r--. 1 root root  88 Dec 15 19:00 sitef-core-0.0.1-0.yml"
    ]
}
TASK [Executar init_parallel.sh no host (com log + stdout)] ********************
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Mostrar últimas linhas do log do predeploy (sempre)] *********************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [DEBUG predeploy tail] ****************************************************
ok: [localhost] => {
    "predeploy_tail.stdout_lines": [
        "===== tail -n 300 /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_parallel.log =====",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'",
        "+ echo 'Executando backup...'",
        "+ echo 'Backup finalizado'"
    ]
}
TASK [Atualizar status após execução do init_parallel.sh] **********************
changed: [localhost]
TASK [Upload File Store (predeploy) - SEMPRE] **********************************
included: /tmp/tmp.rybDSUe1kz/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Identifiers SAFE (HARNESS RULE: [A-Za-z][A-Za-z0-9_]{0,127})] ***
ok: [localhost]
TASK [Harness | Resolver token (vars > env)] ***********************************
ok: [localhost]
TASK [Harness | Falhar se token não está no ambiente] **************************
skipping: [localhost]
TASK [Harness | Montar tags base] **********************************************
ok: [localhost]
TASK [Harness | Adicionar extra_tags como tag=...] *****************************
ok: [localhost] => (item=dev000000002:deploy:pending)
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST) com debug] **************************
changed: [localhost]
TASK [Harness | DEBUG ENV (se falhar)] *****************************************
skipping: [localhost]
TASK [Harness | Falhar ENV (com body)] *****************************************
skipping: [localhost]
TASK [Harness | Garantir folder DEPLOYMENT_REF (POST) com debug] ***************
changed: [localhost]
TASK [Harness | DEBUG REF (se falhar)] *****************************************
skipping: [localhost]
TASK [Harness | Falhar REF (com body)] *****************************************
skipping: [localhost]
TASK [Harness | Definir nome/identifier do STATUS JSON] ************************
ok: [localhost]
TASK [Harness | CREATE status (retry se 500)] **********************************
changed: [localhost]

TASK [Harness | UPDATE status (se create retornou 409) (retry se 500)] *********
skipping: [localhost]
TASK [Harness | DEBUG body STATUS (quando falhar)] *****************************
skipping: [localhost]
TASK [Harness | Falhar STATUS (com correlationId/body)] ************************
skipping: [localhost]
TASK [Harness | Definir nome/identifier do LOG] ********************************
ok: [localhost]
TASK [Harness | Criar arquivo temporário do log no controller] *****************
changed: [localhost]
TASK [Harness | CREATE log (retry se 500)] *************************************
changed: [localhost]
TASK [Harness | UPDATE log (se create retornou 409) (retry se 500)] ************
skipping: [localhost]
TASK [Harness | DEBUG body LOG (quando falhar)] ********************************
skipping: [localhost]
TASK [Harness | Falhar LOG (com correlationId/body)] ***************************
skipping: [localhost]
TASK [Harness | Limpar arquivo temporário do log] ******************************
ok: [localhost]
TASK [Falhar se init_parallel.sh retornou erro (DEPOIS do upload)] *************
skipping: [localhost]
PLAY RECAP *********************************************************************
localhost                  : ok=57   changed=14   unreachable=0    failed=0    skipped=15   rescued=0    ignored=0   
Command finished with status SUCCESS

