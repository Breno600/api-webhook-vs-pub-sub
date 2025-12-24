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
Clonando repo em /tmp/tmp.5DXAmyZGWO...
Cloning into '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 923, done.
remote: Counting objects:   0% (1/873)
remote: Counting objects:   1% (9/873)
remote: Counting objects:   2% (18/873)
remote: Counting objects:   3% (27/873)
remote: Counting objects:   4% (35/873)
remote: Counting objects:   5% (44/873)
remote: Counting objects:   6% (53/873)
remote: Counting objects:   7% (62/873)
remote: Counting objects:   8% (70/873)
remote: Counting objects:   9% (79/873)
remote: Counting objects:  10% (88/873)
remote: Counting objects:  11% (97/873)
remote: Counting objects:  12% (105/873)
remote: Counting objects:  13% (114/873)
remote: Counting objects:  14% (123/873)
remote: Counting objects:  15% (131/873)
remote: Counting objects:  16% (140/873)
remote: Counting objects:  17% (149/873)
remote: Counting objects:  18% (158/873)
remote: Counting objects:  19% (166/873)
remote: Counting objects:  20% (175/873)
remote: Counting objects:  21% (184/873)
remote: Counting objects:  22% (193/873)
remote: Counting objects:  23% (201/873)
remote: Counting objects:  24% (210/873)
remote: Counting objects:  25% (219/873)
remote: Counting objects:  26% (227/873)
remote: Counting objects:  27% (236/873)
remote: Counting objects:  28% (245/873)
remote: Counting objects:  29% (254/873)
remote: Counting objects:  30% (262/873)
remote: Counting objects:  31% (271/873)
remote: Counting objects:  32% (280/873)
remote: Counting objects:  33% (289/873)
remote: Counting objects:  34% (297/873)
remote: Counting objects:  35% (306/873)
remote: Counting objects:  36% (315/873)
remote: Counting objects:  37% (324/873)
remote: Counting objects:  38% (332/873)
remote: Counting objects:  39% (341/873)
remote: Counting objects:  40% (350/873)
remote: Counting objects:  41% (358/873)
remote: Counting objects:  42% (367/873)
remote: Counting objects:  43% (376/873)
remote: Counting objects:  44% (385/873)
remote: Counting objects:  45% (393/873)
remote: Counting objects:  46% (402/873)
remote: Counting objects:  47% (411/873)
remote: Counting objects:  48% (420/873)
remote: Counting objects:  49% (428/873)
remote: Counting objects:  50% (437/873)
remote: Counting objects:  51% (446/873)
remote: Counting objects:  52% (454/873)
remote: Counting objects:  53% (463/873)
remote: Counting objects:  54% (472/873)
remote: Counting objects:  55% (481/873)
remote: Counting objects:  56% (489/873)
remote: Counting objects:  57% (498/873)
remote: Counting objects:  58% (507/873)
remote: Counting objects:  59% (516/873)
remote: Counting objects:  60% (524/873)
remote: Counting objects:  61% (533/873)
remote: Counting objects:  62% (542/873)
remote: Counting objects:  63% (550/873)
remote: Counting objects:  64% (559/873)
remote: Counting objects:  65% (568/873)
remote: Counting objects:  66% (577/873)
remote: Counting objects:  67% (585/873)
remote: Counting objects:  68% (594/873)
remote: Counting objects:  69% (603/873)
remote: Counting objects:  70% (612/873)
remote: Counting objects:  71% (620/873)
remote: Counting objects:  72% (629/873)
remote: Counting objects:  73% (638/873)
remote: Counting objects:  74% (647/873)
remote: Counting objects:  75% (655/873)
remote: Counting objects:  76% (664/873)
remote: Counting objects:  77% (673/873)
remote: Counting objects:  78% (681/873)
remote: Counting objects:  79% (690/873)
remote: Counting objects:  80% (699/873)
remote: Counting objects:  81% (708/873)
remote: Counting objects:  82% (716/873)
remote: Counting objects:  83% (725/873)
remote: Counting objects:  84% (734/873)
remote: Counting objects:  85% (743/873)
remote: Counting objects:  86% (751/873)
remote: Counting objects:  87% (760/873)
remote: Counting objects:  88% (769/873)
remote: Counting objects:  89% (777/873)
remote: Counting objects:  90% (786/873)
remote: Counting objects:  91% (795/873)
remote: Counting objects:  92% (804/873)
remote: Counting objects:  93% (812/873)
remote: Counting objects:  94% (821/873)
remote: Counting objects:  95% (830/873)
remote: Counting objects:  96% (839/873)
remote: Counting objects:  97% (847/873)
remote: Counting objects:  98% (856/873)
remote: Counting objects:  99% (865/873)
remote: Counting objects: 100% (873/873)
remote: Counting objects: 100% (873/873), done.
remote: Compressing objects:   0% (1/858)
remote: Compressing objects:   1% (9/858)
remote: Compressing objects:   2% (18/858)
remote: Compressing objects:   3% (26/858)
remote: Compressing objects:   4% (35/858)
remote: Compressing objects:   5% (43/858)
remote: Compressing objects:   6% (52/858)
remote: Compressing objects:   7% (61/858)
remote: Compressing objects:   8% (69/858)
remote: Compressing objects:   9% (78/858)
remote: Compressing objects:  10% (86/858)
remote: Compressing objects:  11% (95/858)
remote: Compressing objects:  12% (103/858)
remote: Compressing objects:  13% (112/858)
remote: Compressing objects:  14% (121/858)
remote: Compressing objects:  15% (129/858)
remote: Compressing objects:  16% (138/858)
remote: Compressing objects:  17% (146/858)
remote: Compressing objects:  18% (155/858)
remote: Compressing objects:  19% (164/858)
remote: Compressing objects:  20% (172/858)
remote: Compressing objects:  21% (181/858)
remote: Compressing objects:  22% (189/858)
remote: Compressing objects:  23% (198/858)
remote: Compressing objects:  24% (206/858)
remote: Compressing objects:  25% (215/858)
remote: Compressing objects:  26% (224/858)
remote: Compressing objects:  27% (232/858)
remote: Compressing objects:  28% (241/858)
remote: Compressing objects:  29% (249/858)
remote: Compressing objects:  30% (258/858)
remote: Compressing objects:  31% (266/858)
remote: Compressing objects:  32% (275/858)
remote: Compressing objects:  33% (284/858)
remote: Compressing objects:  34% (292/858)
remote: Compressing objects:  35% (301/858)
remote: Compressing objects:  36% (309/858)
remote: Compressing objects:  37% (318/858)
remote: Compressing objects:  38% (327/858)
remote: Compressing objects:  39% (335/858)
remote: Compressing objects:  40% (344/858)
remote: Compressing objects:  41% (352/858)
remote: Compressing objects:  42% (361/858)
remote: Compressing objects:  43% (369/858)
remote: Compressing objects:  44% (378/858)
remote: Compressing objects:  45% (387/858)
remote: Compressing objects:  46% (395/858)
remote: Compressing objects:  47% (404/858)
remote: Compressing objects:  48% (412/858)
remote: Compressing objects:  49% (421/858)
remote: Compressing objects:  50% (429/858)
remote: Compressing objects:  51% (438/858)
remote: Compressing objects:  52% (447/858)
remote: Compressing objects:  53% (455/858)
remote: Compressing objects:  54% (464/858)
remote: Compressing objects:  55% (472/858)
remote: Compressing objects:  56% (481/858)
remote: Compressing objects:  57% (490/858)
remote: Compressing objects:  58% (498/858)
remote: Compressing objects:  59% (507/858)
remote: Compressing objects:  60% (515/858)
remote: Compressing objects:  61% (524/858)
remote: Compressing objects:  62% (532/858)
remote: Compressing objects:  63% (541/858)
remote: Compressing objects:  64% (550/858)
remote: Compressing objects:  65% (558/858)
remote: Compressing objects:  66% (567/858)
remote: Compressing objects:  67% (575/858)
remote: Compressing objects:  68% (584/858)
remote: Compressing objects:  69% (593/858)
remote: Compressing objects:  70% (601/858)
remote: Compressing objects:  71% (610/858)
remote: Compressing objects:  72% (618/858)
remote: Compressing objects:  73% (627/858)
remote: Compressing objects:  74% (635/858)
remote: Compressing objects:  75% (644/858)
remote: Compressing objects:  76% (653/858)
remote: Compressing objects:  77% (661/858)
remote: Compressing objects:  78% (670/858)
remote: Compressing objects:  79% (678/858)
remote: Compressing objects:  80% (687/858)
remote: Compressing objects:  81% (695/858)
remote: Compressing objects:  82% (704/858)
remote: Compressing objects:  83% (713/858)
remote: Compressing objects:  84% (721/858)
remote: Compressing objects:  85% (730/858)
remote: Compressing objects:  86% (738/858)
remote: Compressing objects:  87% (747/858)
remote: Compressing objects:  88% (756/858)
remote: Compressing objects:  89% (764/858)
remote: Compressing objects:  90% (773/858)
remote: Compressing objects:  91% (781/858)
remote: Compressing objects:  92% (790/858)
remote: Compressing objects:  93% (798/858)
remote: Compressing objects:  94% (807/858)
remote: Compressing objects:  95% (816/858)
remote: Compressing objects:  96% (824/858)
remote: Compressing objects:  97% (833/858)
remote: Compressing objects:  98% (841/858)
remote: Compressing objects:  99% (850/858)
remote: Compressing objects: 100% (858/858)
remote: Compressing objects: 100% (858/858), done.
Receiving objects:   0% (1/923)
Receiving objects:   1% (10/923)
Receiving objects:   2% (19/923)
Receiving objects:   3% (28/923)
Receiving objects:   4% (37/923)
Receiving objects:   5% (47/923)
Receiving objects:   6% (56/923)
Receiving objects:   7% (65/923)
Receiving objects:   8% (74/923)
Receiving objects:   9% (84/923)
Receiving objects:  10% (93/923)
Receiving objects:  11% (102/923)
Receiving objects:  12% (111/923)
Receiving objects:  13% (120/923)
Receiving objects:  14% (130/923)
Receiving objects:  15% (139/923)
Receiving objects:  16% (148/923)
Receiving objects:  17% (157/923)
Receiving objects:  18% (167/923)
Receiving objects:  19% (176/923)
Receiving objects:  20% (185/923)
Receiving objects:  21% (194/923)
Receiving objects:  22% (204/923)
Receiving objects:  23% (213/923)
Receiving objects:  24% (222/923)
Receiving objects:  25% (231/923)
Receiving objects:  26% (240/923)
Receiving objects:  27% (250/923)
Receiving objects:  28% (259/923)
Receiving objects:  29% (268/923)
Receiving objects:  30% (277/923)
Receiving objects:  31% (287/923)
Receiving objects:  32% (296/923)
Receiving objects:  33% (305/923)
Receiving objects:  34% (314/923)
Receiving objects:  35% (324/923)
Receiving objects:  36% (333/923)
Receiving objects:  37% (342/923)
Receiving objects:  38% (351/923)
Receiving objects:  39% (360/923)
Receiving objects:  40% (370/923)
Receiving objects:  41% (379/923)
Receiving objects:  42% (388/923)
Receiving objects:  43% (397/923)
Receiving objects:  44% (407/923)
Receiving objects:  45% (416/923)
Receiving objects:  46% (425/923)
Receiving objects:  47% (434/923)
Receiving objects:  48% (444/923)
Receiving objects:  49% (453/923)
Receiving objects:  50% (462/923)
Receiving objects:  51% (471/923)
Receiving objects:  52% (480/923)
Receiving objects:  53% (490/923)
Receiving objects:  54% (499/923)
Receiving objects:  55% (508/923)
Receiving objects:  56% (517/923)
Receiving objects:  57% (527/923)
Receiving objects:  58% (536/923)
Receiving objects:  59% (545/923)
Receiving objects:  60% (554/923)
Receiving objects:  61% (564/923)
Receiving objects:  62% (573/923)
Receiving objects:  63% (582/923)
Receiving objects:  64% (591/923)
Receiving objects:  65% (600/923)
Receiving objects:  66% (610/923)
Receiving objects:  67% (619/923)
Receiving objects:  68% (628/923)
Receiving objects:  69% (637/923)
Receiving objects:  70% (647/923)
Receiving objects:  71% (656/923)
Receiving objects:  72% (665/923)
Receiving objects:  73% (674/923)
Receiving objects:  74% (684/923)
Receiving objects:  75% (693/923)
Receiving objects:  76% (702/923)
Receiving objects:  77% (711/923)
Receiving objects:  78% (720/923)
Receiving objects:  79% (730/923)
Receiving objects:  80% (739/923)
Receiving objects:  81% (748/923)
Receiving objects:  82% (757/923)
Receiving objects:  83% (767/923)
Receiving objects:  84% (776/923)
remote: Total 923 (delta 591), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (785/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  86% (794/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  87% (804/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  88% (813/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  89% (822/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  90% (831/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  91% (840/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  92% (850/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  93% (859/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  94% (868/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  95% (877/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  96% (887/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  97% (896/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  98% (905/923), 707.77 KiB | 1.34 MiB/s
Receiving objects:  99% (914/923), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (923/923), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (923/923), 936.04 KiB | 1.78 MiB/s, done.
Resolving deltas:   0% (0/610)
Resolving deltas:   1% (7/610)
Resolving deltas:   2% (13/610)
Resolving deltas:   3% (19/610)
Resolving deltas:   4% (25/610)
Resolving deltas:   5% (31/610)
Resolving deltas:   6% (37/610)
Resolving deltas:   7% (43/610)
Resolving deltas:   8% (49/610)
Resolving deltas:   9% (55/610)
Resolving deltas:  10% (61/610)
Resolving deltas:  11% (68/610)
Resolving deltas:  12% (74/610)
Resolving deltas:  13% (80/610)
Resolving deltas:  14% (86/610)
Resolving deltas:  15% (92/610)
Resolving deltas:  16% (98/610)
Resolving deltas:  17% (104/610)
Resolving deltas:  18% (110/610)
Resolving deltas:  19% (116/610)
Resolving deltas:  20% (122/610)
Resolving deltas:  21% (129/610)
Resolving deltas:  22% (135/610)
Resolving deltas:  23% (141/610)
Resolving deltas:  24% (147/610)
Resolving deltas:  25% (153/610)
Resolving deltas:  26% (159/610)
Resolving deltas:  27% (165/610)
Resolving deltas:  28% (171/610)
Resolving deltas:  29% (177/610)
Resolving deltas:  30% (183/610)
Resolving deltas:  31% (190/610)
Resolving deltas:  32% (196/610)
Resolving deltas:  33% (202/610)
Resolving deltas:  34% (208/610)
Resolving deltas:  35% (214/610)
Resolving deltas:  36% (220/610)
Resolving deltas:  37% (226/610)
Resolving deltas:  38% (232/610)
Resolving deltas:  39% (238/610)
Resolving deltas:  40% (244/610)
Resolving deltas:  41% (251/610)
Resolving deltas:  42% (257/610)
Resolving deltas:  43% (263/610)
Resolving deltas:  44% (269/610)
Resolving deltas:  45% (275/610)
Resolving deltas:  46% (281/610)
Resolving deltas:  47% (287/610)
Resolving deltas:  48% (293/610)
Resolving deltas:  49% (299/610)
Resolving deltas:  50% (305/610)
Resolving deltas:  51% (312/610)
Resolving deltas:  52% (318/610)
Resolving deltas:  53% (324/610)
Resolving deltas:  54% (331/610)
Resolving deltas:  55% (336/610)
Resolving deltas:  56% (342/610)
Resolving deltas:  57% (348/610)
Resolving deltas:  58% (354/610)
Resolving deltas:  59% (360/610)
Resolving deltas:  60% (366/610)
Resolving deltas:  61% (373/610)
Resolving deltas:  62% (379/610)
Resolving deltas:  63% (385/610)
Resolving deltas:  64% (391/610)
Resolving deltas:  65% (397/610)
Resolving deltas:  66% (403/610)
Resolving deltas:  67% (409/610)
Resolving deltas:  68% (415/610)
Resolving deltas:  69% (421/610)
Resolving deltas:  70% (427/610)
Resolving deltas:  71% (434/610)
Resolving deltas:  72% (440/610)
Resolving deltas:  73% (446/610)
Resolving deltas:  74% (452/610)
Resolving deltas:  75% (458/610)
Resolving deltas:  76% (464/610)
Resolving deltas:  77% (470/610)
Resolving deltas:  78% (476/610)
Resolving deltas:  79% (482/610)
Resolving deltas:  80% (488/610)
Resolving deltas:  81% (496/610)
Resolving deltas:  82% (501/610)
Resolving deltas:  83% (507/610)
Resolving deltas:  84% (513/610)
Resolving deltas:  85% (519/610)
Resolving deltas:  86% (525/610)
Resolving deltas:  87% (531/610)
Resolving deltas:  88% (537/610)
Resolving deltas:  89% (543/610)
Resolving deltas:  90% (549/610)
Resolving deltas:  91% (556/610)
Resolving deltas:  92% (562/610)
Resolving deltas:  93% (568/610)
Resolving deltas:  94% (574/610)
Resolving deltas:  95% (580/610)
Resolving deltas:  96% (586/610)
Resolving deltas:  97% (592/610)
Resolving deltas:  98% (598/610)
Resolving deltas:  99% (604/610)
Resolving deltas: 100% (610/610)
Resolving deltas: 100% (610/610), done.
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
        "repo_root_resolved           = /tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../status/DEV000000002",
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
included: /tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 6304346, 'dev': 64775, 'nlink': 1, 'atime': 1766601116.5976925, 'mtime': 1766601116.5976925, 'ctime': 1766601116.5976925, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2229128425', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
fatal: [localhost -> sitef-02(100.99.57.128)]: FAILED! => {"changed": true, "cmd": "set -o pipefail\ncd \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\"\n/usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&amp;1 | tee -a \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log\"\nexit ${PIPESTATUS[0]}\n", "delta": "0:00:13.575377", "end": "2025-12-24 18:32:18.547314", "msg": "non-zero return code", "rc": 1, "start": "2025-12-24 18:32:04.971937", "stderr": "", "stderr_lines": [], "stdout": "+ set -euo pipefail\n+++ dirname ./init_deploy.sh\n++ cd .\n++ pwd\n+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\n+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ exec\n++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ echo '== Início init_deploy.sh =='\n== Início init_deploy.sh ==\n+ date\nWed Dec 24 06:32:04 PM UTC 2025\n+ set -x\n+ echo 'Instalacao de dependencias do sistema...'\nInstalacao de dependencias do sistema...\n+ dnf install -y --nodocs python3.12 python3.12-pip\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    \nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 3.4 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 3.1 kB/s | 4.5 kB     00:01    \nEPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    \nPackage python3.12-3.12.12-1.el9_7.x86_64 is already installed.\nPackage python3.12-pip-23.2.1-5.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\n+ echo 'Instalacao de dependencias do sistema finalizada.'\nInstalacao de dependencias do sistema finalizada.\n+ mkdir -p /opt/SoftwareExpress/sitef-setup\n+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv\n+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate\n++ deactivate nondestructive\n++ '[' -n '' ']'\n++ '[' -n '' ']'\n++ hash -r\n++ '[' -n '' ']'\n++ unset VIRTUAL_ENV\n++ unset VIRTUAL_ENV_PROMPT\n++ '[' '!' nondestructive = nondestructive ']'\n++ case \"$(uname)\" in\n+++ uname\n++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv\n++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv\n++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin\n++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin\n++ export PATH\n++ VIRTUAL_ENV_PROMPT='(venv) '\n++ export VIRTUAL_ENV_PROMPT\n++ '[' -n '' ']'\n++ '[' -z '' ']'\n++ _OLD_VIRTUAL_PS1=\n++ PS1='((venv) ) '\n++ export PS1\n++ hash -r\n+ echo 'Instalacao de dependencias do python...'\nInstalacao de dependencias do python...\n+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl\nWARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist\nProcessing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl\nERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'", "stdout_lines": ["+ set -euo pipefail", "+++ dirname ./init_deploy.sh", "++ cd .", "++ pwd", "+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts", "+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ exec", "++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ echo '== Início init_deploy.sh =='", "== Início init_deploy.sh ==", "+ date", "Wed Dec 24 06:32:04 PM UTC 2025", "+ set -x", "+ echo 'Instalacao de dependencias do sistema...'", "Instalacao de dependencias do sistema...", "+ dnf install -y --nodocs python3.12 python3.12-pip", "Updating Subscription Management repositories.", "", "This system has release set to 9 and it receives updates only for this release.", "", "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    ", "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.4 kB/s | 4.5 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.1 kB/s | 4.5 kB     00:01    ", "EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    ", "Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.", "Package python3.12-pip-23.2.1-5.el9.noarch is already installed.", "Dependencies resolved.", "Nothing to do.", "Complete!", "+ echo 'Instalacao de dependencias do sistema finalizada.'", "Instalacao de dependencias do sistema finalizada.", "+ mkdir -p /opt/SoftwareExpress/sitef-setup", "+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv", "+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate", "++ deactivate nondestructive", "++ '[' -n '' ']'", "++ '[' -n '' ']'", "++ hash -r", "++ '[' -n '' ']'", "++ unset VIRTUAL_ENV", "++ unset VIRTUAL_ENV_PROMPT", "++ '[' '!' nondestructive = nondestructive ']'", "++ case \"$(uname)\" in", "+++ uname", "++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv", "++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv", "++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin", "++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin", "++ export PATH", "++ VIRTUAL_ENV_PROMPT='(venv) '", "++ export VIRTUAL_ENV_PROMPT", "++ '[' -n '' ']'", "++ '[' -z '' ']'", "++ _OLD_VIRTUAL_PS1=", "++ PS1='((venv) ) '", "++ export PS1", "++ hash -r", "+ echo 'Instalacao de dependencias do python...'", "Instalacao de dependencias do python...", "+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl", "WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist", "Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl", "ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'"]}
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
included: /tmp/tmp.5DXAmyZGWO/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Resolver current_machine de forma segura (evita recursão)] *****
fatal: [localhost]: FAILED! => {"msg": "template error while templating string: expected token ')', got 'else'. String: {{\n  (lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim)\n    if (lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim | length) > 0\n    else\n  ((machine_name | default('') | string | trim)\n    if (machine_name is defined and (machine_name | string | trim | length) > 0\n    else\n  (lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim).split(',')[0] | trim\n    if (lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim | length) > 0\n    else\n  'localhost')\n}}. expected token ')', got 'else'"}
PLAY RECAP *********************************************************************
localhost                  : ok=38   changed=8    unreachable=0    failed=1    skipped=4    rescued=0    ignored=1   
Command finished with status FAILURE
