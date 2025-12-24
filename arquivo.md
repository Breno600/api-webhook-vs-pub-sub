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
Clonando repo em /tmp/tmp.oNunlG8ZSC...
Cloning into '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 919, done.
remote: Counting objects:   0% (1/869)
remote: Counting objects:   1% (9/869)
remote: Counting objects:   2% (18/869)
remote: Counting objects:   3% (27/869)
remote: Counting objects:   4% (35/869)
remote: Counting objects:   5% (44/869)
remote: Counting objects:   6% (53/869)
remote: Counting objects:   7% (61/869)
remote: Counting objects:   8% (70/869)
remote: Counting objects:   9% (79/869)
remote: Counting objects:  10% (87/869)
remote: Counting objects:  11% (96/869)
remote: Counting objects:  12% (105/869)
remote: Counting objects:  13% (113/869)
remote: Counting objects:  14% (122/869)
remote: Counting objects:  15% (131/869)
remote: Counting objects:  16% (140/869)
remote: Counting objects:  17% (148/869)
remote: Counting objects:  18% (157/869)
remote: Counting objects:  19% (166/869)
remote: Counting objects:  20% (174/869)
remote: Counting objects:  21% (183/869)
remote: Counting objects:  22% (192/869)
remote: Counting objects:  23% (200/869)
remote: Counting objects:  24% (209/869)
remote: Counting objects:  25% (218/869)
remote: Counting objects:  26% (226/869)
remote: Counting objects:  27% (235/869)
remote: Counting objects:  28% (244/869)
remote: Counting objects:  29% (253/869)
remote: Counting objects:  30% (261/869)
remote: Counting objects:  31% (270/869)
remote: Counting objects:  32% (279/869)
remote: Counting objects:  33% (287/869)
remote: Counting objects:  34% (296/869)
remote: Counting objects:  35% (305/869)
remote: Counting objects:  36% (313/869)
remote: Counting objects:  37% (322/869)
remote: Counting objects:  38% (331/869)
remote: Counting objects:  39% (339/869)
remote: Counting objects:  40% (348/869)
remote: Counting objects:  41% (357/869)
remote: Counting objects:  42% (365/869)
remote: Counting objects:  43% (374/869)
remote: Counting objects:  44% (383/869)
remote: Counting objects:  45% (392/869)
remote: Counting objects:  46% (400/869)
remote: Counting objects:  47% (409/869)
remote: Counting objects:  48% (418/869)
remote: Counting objects:  49% (426/869)
remote: Counting objects:  50% (435/869)
remote: Counting objects:  51% (444/869)
remote: Counting objects:  52% (452/869)
remote: Counting objects:  53% (461/869)
remote: Counting objects:  54% (470/869)
remote: Counting objects:  55% (478/869)
remote: Counting objects:  56% (487/869)
remote: Counting objects:  57% (496/869)
remote: Counting objects:  58% (505/869)
remote: Counting objects:  59% (513/869)
remote: Counting objects:  60% (522/869)
remote: Counting objects:  61% (531/869)
remote: Counting objects:  62% (539/869)
remote: Counting objects:  63% (548/869)
remote: Counting objects:  64% (557/869)
remote: Counting objects:  65% (565/869)
remote: Counting objects:  66% (574/869)
remote: Counting objects:  67% (583/869)
remote: Counting objects:  68% (591/869)
remote: Counting objects:  69% (600/869)
remote: Counting objects:  70% (609/869)
remote: Counting objects:  71% (617/869)
remote: Counting objects:  72% (626/869)
remote: Counting objects:  73% (635/869)
remote: Counting objects:  74% (644/869)
remote: Counting objects:  75% (652/869)
remote: Counting objects:  76% (661/869)
remote: Counting objects:  77% (670/869)
remote: Counting objects:  78% (678/869)
remote: Counting objects:  79% (687/869)
remote: Counting objects:  80% (696/869)
remote: Counting objects:  81% (704/869)
remote: Counting objects:  82% (713/869)
remote: Counting objects:  83% (722/869)
remote: Counting objects:  84% (730/869)
remote: Counting objects:  85% (739/869)
remote: Counting objects:  86% (748/869)
remote: Counting objects:  87% (757/869)
remote: Counting objects:  88% (765/869)
remote: Counting objects:  89% (774/869)
remote: Counting objects:  90% (783/869)
remote: Counting objects:  91% (791/869)
remote: Counting objects:  92% (800/869)
remote: Counting objects:  93% (809/869)
remote: Counting objects:  94% (817/869)
remote: Counting objects:  95% (826/869)
remote: Counting objects:  96% (835/869)
remote: Counting objects:  97% (843/869)
remote: Counting objects:  98% (852/869)
remote: Counting objects:  99% (861/869)
remote: Counting objects: 100% (869/869)
remote: Counting objects: 100% (869/869), done.
remote: Compressing objects:   0% (1/854)
remote: Compressing objects:   1% (9/854)
remote: Compressing objects:   2% (18/854)
remote: Compressing objects:   3% (26/854)
remote: Compressing objects:   4% (35/854)
remote: Compressing objects:   5% (43/854)
remote: Compressing objects:   6% (52/854)
remote: Compressing objects:   7% (60/854)
remote: Compressing objects:   8% (69/854)
remote: Compressing objects:   9% (77/854)
remote: Compressing objects:  10% (86/854)
remote: Compressing objects:  11% (94/854)
remote: Compressing objects:  12% (103/854)
remote: Compressing objects:  13% (112/854)
remote: Compressing objects:  14% (120/854)
remote: Compressing objects:  15% (129/854)
remote: Compressing objects:  16% (137/854)
remote: Compressing objects:  17% (146/854)
remote: Compressing objects:  18% (154/854)
remote: Compressing objects:  19% (163/854)
remote: Compressing objects:  20% (171/854)
remote: Compressing objects:  21% (180/854)
remote: Compressing objects:  22% (188/854)
remote: Compressing objects:  23% (197/854)
remote: Compressing objects:  24% (205/854)
remote: Compressing objects:  25% (214/854)
remote: Compressing objects:  26% (223/854)
remote: Compressing objects:  27% (231/854)
remote: Compressing objects:  28% (240/854)
remote: Compressing objects:  29% (248/854)
remote: Compressing objects:  30% (257/854)
remote: Compressing objects:  31% (265/854)
remote: Compressing objects:  32% (274/854)
remote: Compressing objects:  33% (282/854)
remote: Compressing objects:  34% (291/854)
remote: Compressing objects:  35% (299/854)
remote: Compressing objects:  36% (308/854)
remote: Compressing objects:  37% (316/854)
remote: Compressing objects:  38% (325/854)
remote: Compressing objects:  39% (334/854)
remote: Compressing objects:  40% (342/854)
remote: Compressing objects:  41% (351/854)
remote: Compressing objects:  42% (359/854)
remote: Compressing objects:  43% (368/854)
remote: Compressing objects:  44% (376/854)
remote: Compressing objects:  45% (385/854)
remote: Compressing objects:  46% (393/854)
remote: Compressing objects:  47% (402/854)
remote: Compressing objects:  48% (410/854)
remote: Compressing objects:  49% (419/854)
remote: Compressing objects:  50% (427/854)
remote: Compressing objects:  51% (436/854)
remote: Compressing objects:  52% (445/854)
remote: Compressing objects:  53% (453/854)
remote: Compressing objects:  54% (462/854)
remote: Compressing objects:  55% (470/854)
remote: Compressing objects:  56% (479/854)
remote: Compressing objects:  57% (487/854)
remote: Compressing objects:  58% (496/854)
remote: Compressing objects:  59% (504/854)
remote: Compressing objects:  60% (513/854)
remote: Compressing objects:  61% (521/854)
remote: Compressing objects:  62% (530/854)
remote: Compressing objects:  63% (539/854)
remote: Compressing objects:  64% (547/854)
remote: Compressing objects:  65% (556/854)
remote: Compressing objects:  66% (564/854)
remote: Compressing objects:  67% (573/854)
remote: Compressing objects:  68% (581/854)
remote: Compressing objects:  69% (590/854)
remote: Compressing objects:  70% (598/854)
remote: Compressing objects:  71% (607/854)
remote: Compressing objects:  72% (615/854)
remote: Compressing objects:  73% (624/854)
remote: Compressing objects:  74% (632/854)
remote: Compressing objects:  75% (641/854)
remote: Compressing objects:  76% (650/854)
remote: Compressing objects:  77% (658/854)
remote: Compressing objects:  78% (667/854)
remote: Compressing objects:  79% (675/854)
remote: Compressing objects:  80% (684/854)
remote: Compressing objects:  81% (692/854)
remote: Compressing objects:  82% (701/854)
remote: Compressing objects:  83% (709/854)
remote: Compressing objects:  84% (718/854)
remote: Compressing objects:  85% (726/854)
remote: Compressing objects:  86% (735/854)
remote: Compressing objects:  87% (743/854)
remote: Compressing objects:  88% (752/854)
remote: Compressing objects:  89% (761/854)
remote: Compressing objects:  90% (769/854)
remote: Compressing objects:  91% (778/854)
remote: Compressing objects:  92% (786/854)
remote: Compressing objects:  93% (795/854)
remote: Compressing objects:  94% (803/854)
remote: Compressing objects:  95% (812/854)
remote: Compressing objects:  96% (820/854)
remote: Compressing objects:  97% (829/854)
remote: Compressing objects:  98% (837/854)
remote: Compressing objects:  99% (846/854)
remote: Compressing objects: 100% (854/854)
remote: Compressing objects: 100% (854/854), done.
Receiving objects:   0% (1/919)
Receiving objects:   1% (10/919)
Receiving objects:   2% (19/919)
Receiving objects:   3% (28/919)
Receiving objects:   4% (37/919)
Receiving objects:   5% (46/919)
Receiving objects:   6% (56/919)
Receiving objects:   7% (65/919)
Receiving objects:   8% (74/919)
Receiving objects:   9% (83/919)
Receiving objects:  10% (92/919)
Receiving objects:  11% (102/919)
Receiving objects:  12% (111/919)
Receiving objects:  13% (120/919)
Receiving objects:  14% (129/919)
Receiving objects:  15% (138/919)
Receiving objects:  16% (148/919)
Receiving objects:  17% (157/919)
Receiving objects:  18% (166/919)
Receiving objects:  19% (175/919)
Receiving objects:  20% (184/919)
Receiving objects:  21% (193/919)
Receiving objects:  22% (203/919)
Receiving objects:  23% (212/919)
Receiving objects:  24% (221/919)
Receiving objects:  25% (230/919)
Receiving objects:  26% (239/919)
Receiving objects:  27% (249/919)
Receiving objects:  28% (258/919)
Receiving objects:  29% (267/919)
Receiving objects:  30% (276/919)
Receiving objects:  31% (285/919)
Receiving objects:  32% (295/919)
Receiving objects:  33% (304/919)
Receiving objects:  34% (313/919)
Receiving objects:  35% (322/919)
Receiving objects:  36% (331/919)
Receiving objects:  37% (341/919)
Receiving objects:  38% (350/919)
Receiving objects:  39% (359/919)
Receiving objects:  40% (368/919)
Receiving objects:  41% (377/919)
Receiving objects:  42% (386/919)
Receiving objects:  43% (396/919)
Receiving objects:  44% (405/919)
Receiving objects:  45% (414/919)
Receiving objects:  46% (423/919)
Receiving objects:  47% (432/919)
Receiving objects:  48% (442/919)
Receiving objects:  49% (451/919)
Receiving objects:  50% (460/919)
Receiving objects:  51% (469/919)
Receiving objects:  52% (478/919)
Receiving objects:  53% (488/919)
Receiving objects:  54% (497/919)
Receiving objects:  55% (506/919)
Receiving objects:  56% (515/919)
Receiving objects:  57% (524/919)
Receiving objects:  58% (534/919)
Receiving objects:  59% (543/919)
Receiving objects:  60% (552/919)
Receiving objects:  61% (561/919)
Receiving objects:  62% (570/919)
Receiving objects:  63% (579/919)
Receiving objects:  64% (589/919)
Receiving objects:  65% (598/919)
Receiving objects:  66% (607/919)
Receiving objects:  67% (616/919)
Receiving objects:  68% (625/919)
Receiving objects:  69% (635/919)
Receiving objects:  70% (644/919)
Receiving objects:  71% (653/919)
Receiving objects:  72% (662/919)
Receiving objects:  73% (671/919)
Receiving objects:  74% (681/919)
Receiving objects:  75% (690/919)
Receiving objects:  76% (699/919)
Receiving objects:  77% (708/919)
Receiving objects:  78% (717/919)
Receiving objects:  79% (727/919)
Receiving objects:  80% (736/919)
Receiving objects:  81% (745/919)
Receiving objects:  82% (754/919)
Receiving objects:  83% (763/919)
Receiving objects:  84% (772/919)
remote: Total 919 (delta 588), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (782/919)
Receiving objects:  86% (791/919)
Receiving objects:  87% (800/919)
Receiving objects:  88% (809/919)
Receiving objects:  89% (818/919)
Receiving objects:  90% (828/919)
Receiving objects:  91% (837/919)
Receiving objects:  92% (846/919)
Receiving objects:  93% (855/919)
Receiving objects:  94% (864/919)
Receiving objects:  95% (874/919)
Receiving objects:  96% (883/919)
Receiving objects:  97% (892/919)
Receiving objects:  98% (901/919)
Receiving objects:  99% (910/919)
Receiving objects: 100% (919/919)
Receiving objects: 100% (919/919), 935.18 KiB | 2.29 MiB/s, done.
Resolving deltas:   0% (0/607)
Resolving deltas:   1% (7/607)
Resolving deltas:   2% (13/607)
Resolving deltas:   3% (19/607)
Resolving deltas:   4% (25/607)
Resolving deltas:   5% (31/607)
Resolving deltas:   6% (37/607)
Resolving deltas:   7% (43/607)
Resolving deltas:   8% (49/607)
Resolving deltas:   9% (55/607)
Resolving deltas:  10% (61/607)
Resolving deltas:  11% (67/607)
Resolving deltas:  12% (73/607)
Resolving deltas:  13% (79/607)
Resolving deltas:  14% (85/607)
Resolving deltas:  15% (92/607)
Resolving deltas:  16% (98/607)
Resolving deltas:  17% (104/607)
Resolving deltas:  18% (110/607)
Resolving deltas:  19% (116/607)
Resolving deltas:  20% (122/607)
Resolving deltas:  21% (128/607)
Resolving deltas:  22% (134/607)
Resolving deltas:  23% (140/607)
Resolving deltas:  24% (146/607)
Resolving deltas:  25% (152/607)
Resolving deltas:  26% (158/607)
Resolving deltas:  27% (164/607)
Resolving deltas:  28% (170/607)
Resolving deltas:  29% (177/607)
Resolving deltas:  30% (183/607)
Resolving deltas:  31% (190/607)
Resolving deltas:  32% (195/607)
Resolving deltas:  33% (201/607)
Resolving deltas:  34% (207/607)
Resolving deltas:  35% (213/607)
Resolving deltas:  36% (219/607)
Resolving deltas:  37% (225/607)
Resolving deltas:  38% (231/607)
Resolving deltas:  39% (237/607)
Resolving deltas:  40% (243/607)
Resolving deltas:  41% (249/607)
Resolving deltas:  42% (255/607)
Resolving deltas:  43% (262/607)
Resolving deltas:  44% (268/607)
Resolving deltas:  45% (274/607)
Resolving deltas:  46% (280/607)
Resolving deltas:  47% (286/607)
Resolving deltas:  48% (292/607)
Resolving deltas:  49% (298/607)
Resolving deltas:  50% (304/607)
Resolving deltas:  51% (310/607)
Resolving deltas:  52% (316/607)
Resolving deltas:  53% (322/607)
Resolving deltas:  54% (328/607)
Resolving deltas:  55% (334/607)
Resolving deltas:  56% (340/607)
Resolving deltas:  57% (346/607)
Resolving deltas:  58% (353/607)
Resolving deltas:  59% (359/607)
Resolving deltas:  60% (365/607)
Resolving deltas:  61% (371/607)
Resolving deltas:  62% (377/607)
Resolving deltas:  63% (383/607)
Resolving deltas:  64% (389/607)
Resolving deltas:  65% (395/607)
Resolving deltas:  66% (401/607)
Resolving deltas:  67% (407/607)
Resolving deltas:  68% (413/607)
Resolving deltas:  69% (419/607)
Resolving deltas:  70% (425/607)
Resolving deltas:  71% (431/607)
Resolving deltas:  72% (438/607)
Resolving deltas:  73% (444/607)
Resolving deltas:  74% (450/607)
Resolving deltas:  75% (456/607)
Resolving deltas:  76% (462/607)
Resolving deltas:  77% (468/607)
Resolving deltas:  78% (474/607)
Resolving deltas:  79% (480/607)
Resolving deltas:  80% (486/607)
Resolving deltas:  81% (493/607)
Resolving deltas:  82% (498/607)
Resolving deltas:  83% (504/607)
Resolving deltas:  84% (510/607)
Resolving deltas:  85% (516/607)
Resolving deltas:  86% (523/607)
Resolving deltas:  87% (529/607)
Resolving deltas:  88% (535/607)
Resolving deltas:  89% (541/607)
Resolving deltas:  90% (547/607)
Resolving deltas:  91% (553/607)
Resolving deltas:  92% (559/607)
Resolving deltas:  93% (565/607)
Resolving deltas:  94% (571/607)
Resolving deltas:  95% (577/607)
Resolving deltas:  96% (583/607)
Resolving deltas:  97% (589/607)
Resolving deltas:  98% (595/607)
Resolving deltas:  99% (601/607)
Resolving deltas: 100% (607/607)
Resolving deltas: 100% (607/607), done.
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
        "repo_root_resolved           = /tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../status/DEV000000002",
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
included: /tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 8773, 'dev': 64775, 'nlink': 1, 'atime': 1766600831.6137578, 'mtime': 1766600831.6017578, 'ctime': 1766600831.6017578, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '432398042', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
skipping: [localhost]
TASK [Deploy | Executar init_deploy.sh no host (com tee)] **********************
fatal: [localhost -> sitef-02(100.99.57.128)]: FAILED! => {"changed": true, "cmd": "set -o pipefail\ncd \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\"\n/usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&amp;1 | tee -a \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log\"\nexit ${PIPESTATUS[0]}\n", "delta": "0:00:13.693629", "end": "2025-12-24 18:27:33.876690", "msg": "non-zero return code", "rc": 1, "start": "2025-12-24 18:27:20.183061", "stderr": "", "stderr_lines": [], "stdout": "+ set -euo pipefail\n+++ dirname ./init_deploy.sh\n++ cd .\n++ pwd\n+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\n+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ exec\n++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ echo '== Início init_deploy.sh =='\n== Início init_deploy.sh ==\n+ date\nWed Dec 24 06:27:20 PM UTC 2025\n+ set -x\n+ echo 'Instalacao de dependencias do sistema...'\nInstalacao de dependencias do sistema...\n+ dnf install -y --nodocs python3.12 python3.12-pip\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    \nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 3.5 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 3.5 kB/s | 4.5 kB     00:01    \nEPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    \nPackage python3.12-3.12.12-1.el9_7.x86_64 is already installed.\nPackage python3.12-pip-23.2.1-5.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\n+ echo 'Instalacao de dependencias do sistema finalizada.'\nInstalacao de dependencias do sistema finalizada.\n+ mkdir -p /opt/SoftwareExpress/sitef-setup\n+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv\n+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate\n++ deactivate nondestructive\n++ '[' -n '' ']'\n++ '[' -n '' ']'\n++ hash -r\n++ '[' -n '' ']'\n++ unset VIRTUAL_ENV\n++ unset VIRTUAL_ENV_PROMPT\n++ '[' '!' nondestructive = nondestructive ']'\n++ case \"$(uname)\" in\n+++ uname\n++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv\n++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv\n++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin\n++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin\n++ export PATH\n++ VIRTUAL_ENV_PROMPT='(venv) '\n++ export VIRTUAL_ENV_PROMPT\n++ '[' -n '' ']'\n++ '[' -z '' ']'\n++ _OLD_VIRTUAL_PS1=\n++ PS1='((venv) ) '\n++ export PS1\n++ hash -r\n+ echo 'Instalacao de dependencias do python...'\nInstalacao de dependencias do python...\n+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl\nWARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist\nProcessing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl\nERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'", "stdout_lines": ["+ set -euo pipefail", "+++ dirname ./init_deploy.sh", "++ cd .", "++ pwd", "+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts", "+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ exec", "++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ echo '== Início init_deploy.sh =='", "== Início init_deploy.sh ==", "+ date", "Wed Dec 24 06:27:20 PM UTC 2025", "+ set -x", "+ echo 'Instalacao de dependencias do sistema...'", "Instalacao de dependencias do sistema...", "+ dnf install -y --nodocs python3.12 python3.12-pip", "Updating Subscription Management repositories.", "", "This system has release set to 9 and it receives updates only for this release.", "", "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.3 kB/s | 3.8 kB     00:01    ", "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.5 kB/s | 4.5 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.5 kB/s | 4.5 kB     00:01    ", "EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    ", "Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.", "Package python3.12-pip-23.2.1-5.el9.noarch is already installed.", "Dependencies resolved.", "Nothing to do.", "Complete!", "+ echo 'Instalacao de dependencias do sistema finalizada.'", "Instalacao de dependencias do sistema finalizada.", "+ mkdir -p /opt/SoftwareExpress/sitef-setup", "+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv", "+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate", "++ deactivate nondestructive", "++ '[' -n '' ']'", "++ '[' -n '' ']'", "++ hash -r", "++ '[' -n '' ']'", "++ unset VIRTUAL_ENV", "++ unset VIRTUAL_ENV_PROMPT", "++ '[' '!' nondestructive = nondestructive ']'", "++ case \"$(uname)\" in", "+++ uname", "++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv", "++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv", "++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin", "++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin", "++ export PATH", "++ VIRTUAL_ENV_PROMPT='(venv) '", "++ export VIRTUAL_ENV_PROMPT", "++ '[' -n '' ']'", "++ '[' -z '' ']'", "++ _OLD_VIRTUAL_PS1=", "++ PS1='((venv) ) '", "++ export PS1", "++ hash -r", "+ echo 'Instalacao de dependencias do python...'", "Instalacao de dependencias do python...", "+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl", "WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist", "Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl", "ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'"]}
...ignoring
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
TASK [Deploy | Append do bloco de log do deploy no log cumulativo] *************
changed: [localhost]
TASK [Deploy | Carregar conteúdo completo do log cumulativo (para upload)] *****
ok: [localhost]
TASK [Deploy | Upload JSON + LOG para Harness File Store] **********************
included: /tmp/tmp.oNunlG8ZSC/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'current_machine is defined' failed. The error was: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.An
sibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ current_machine }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=38   changed=8    unreachable=0    failed=1    skipped=4    rescued=0    ignored=1   
Command finished with status FAILURE
