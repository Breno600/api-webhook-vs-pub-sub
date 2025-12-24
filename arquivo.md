Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=deploy
export GIT_TAG=DEV000003
export STRATEGY=deploy
== DEPLOY PIPELINE ==
BRANCH            : develop-testes
STRATEGY          : deploy
GIT_TAG           : DEV000003
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000003
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.8JjiC1Pw36...
Cloning into '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 938, done.
remote: Counting objects:   0% (1/888)
remote: Counting objects:   1% (9/888)
remote: Counting objects:   2% (18/888)
remote: Counting objects:   3% (27/888)
remote: Counting objects:   4% (36/888)
remote: Counting objects:   5% (45/888)
remote: Counting objects:   6% (54/888)
remote: Counting objects:   7% (63/888)
remote: Counting objects:   8% (72/888)
remote: Counting objects:   9% (80/888)
remote: Counting objects:  10% (89/888)
remote: Counting objects:  11% (98/888)
remote: Counting objects:  12% (107/888)
remote: Counting objects:  13% (116/888)
remote: Counting objects:  14% (125/888)
remote: Counting objects:  15% (134/888)
remote: Counting objects:  16% (143/888)
remote: Counting objects:  17% (151/888)
remote: Counting objects:  18% (160/888)
remote: Counting objects:  19% (169/888)
remote: Counting objects:  20% (178/888)
remote: Counting objects:  21% (187/888)
remote: Counting objects:  22% (196/888)
remote: Counting objects:  23% (205/888)
remote: Counting objects:  24% (214/888)
remote: Counting objects:  25% (222/888)
remote: Counting objects:  26% (231/888)
remote: Counting objects:  27% (240/888)
remote: Counting objects:  28% (249/888)
remote: Counting objects:  29% (258/888)
remote: Counting objects:  30% (267/888)
remote: Counting objects:  31% (276/888)
remote: Counting objects:  32% (285/888)
remote: Counting objects:  33% (294/888)
remote: Counting objects:  34% (302/888)
remote: Counting objects:  35% (311/888)
remote: Counting objects:  36% (320/888)
remote: Counting objects:  37% (329/888)
remote: Counting objects:  38% (338/888)
remote: Counting objects:  39% (347/888)
remote: Counting objects:  40% (356/888)
remote: Counting objects:  41% (365/888)
remote: Counting objects:  42% (373/888)
remote: Counting objects:  43% (382/888)
remote: Counting objects:  44% (391/888)
remote: Counting objects:  45% (400/888)
remote: Counting objects:  46% (409/888)
remote: Counting objects:  47% (418/888)
remote: Counting objects:  48% (427/888)
remote: Counting objects:  49% (436/888)
remote: Counting objects:  50% (444/888)
remote: Counting objects:  51% (453/888)
remote: Counting objects:  52% (462/888)
remote: Counting objects:  53% (471/888)
remote: Counting objects:  54% (480/888)
remote: Counting objects:  55% (489/888)
remote: Counting objects:  56% (498/888)
remote: Counting objects:  57% (507/888)
remote: Counting objects:  58% (516/888)
remote: Counting objects:  59% (524/888)
remote: Counting objects:  60% (533/888)
remote: Counting objects:  61% (542/888)
remote: Counting objects:  62% (551/888)
remote: Counting objects:  63% (560/888)
remote: Counting objects:  64% (569/888)
remote: Counting objects:  65% (578/888)
remote: Counting objects:  66% (587/888)
remote: Counting objects:  67% (595/888)
remote: Counting objects:  68% (604/888)
remote: Counting objects:  69% (613/888)
remote: Counting objects:  70% (622/888)
remote: Counting objects:  71% (631/888)
remote: Counting objects:  72% (640/888)
remote: Counting objects:  73% (649/888)
remote: Counting objects:  74% (658/888)
remote: Counting objects:  75% (666/888)
remote: Counting objects:  76% (675/888)
remote: Counting objects:  77% (684/888)
remote: Counting objects:  78% (693/888)
remote: Counting objects:  79% (702/888)
remote: Counting objects:  80% (711/888)
remote: Counting objects:  81% (720/888)
remote: Counting objects:  82% (729/888)
remote: Counting objects:  83% (738/888)
remote: Counting objects:  84% (746/888)
remote: Counting objects:  85% (755/888)
remote: Counting objects:  86% (764/888)
remote: Counting objects:  87% (773/888)
remote: Counting objects:  88% (782/888)
remote: Counting objects:  89% (791/888)
remote: Counting objects:  90% (800/888)
remote: Counting objects:  91% (809/888)
remote: Counting objects:  92% (817/888)
remote: Counting objects:  93% (826/888)
remote: Counting objects:  94% (835/888)
remote: Counting objects:  95% (844/888)
remote: Counting objects:  96% (853/888)
remote: Counting objects:  97% (862/888)
remote: Counting objects:  98% (871/888)
remote: Counting objects:  99% (880/888)
remote: Counting objects: 100% (888/888)
remote: Counting objects: 100% (888/888), done.
remote: Compressing objects:   0% (1/873)
remote: Compressing objects:   1% (9/873)
remote: Compressing objects:   2% (18/873)
remote: Compressing objects:   3% (27/873)
remote: Compressing objects:   4% (35/873)
remote: Compressing objects:   5% (44/873)
remote: Compressing objects:   6% (53/873)
remote: Compressing objects:   7% (62/873)
remote: Compressing objects:   8% (70/873)
remote: Compressing objects:   9% (79/873)
remote: Compressing objects:  10% (88/873)
remote: Compressing objects:  11% (97/873)
remote: Compressing objects:  12% (105/873)
remote: Compressing objects:  13% (114/873)
remote: Compressing objects:  14% (123/873)
remote: Compressing objects:  15% (131/873)
remote: Compressing objects:  16% (140/873)
remote: Compressing objects:  17% (149/873)
remote: Compressing objects:  18% (158/873)
remote: Compressing objects:  19% (166/873)
remote: Compressing objects:  20% (175/873)
remote: Compressing objects:  21% (184/873)
remote: Compressing objects:  22% (193/873)
remote: Compressing objects:  23% (201/873)
remote: Compressing objects:  24% (210/873)
remote: Compressing objects:  25% (219/873)
remote: Compressing objects:  26% (227/873)
remote: Compressing objects:  27% (236/873)
remote: Compressing objects:  28% (245/873)
remote: Compressing objects:  29% (254/873)
remote: Compressing objects:  30% (262/873)
remote: Compressing objects:  31% (271/873)
remote: Compressing objects:  32% (280/873)
remote: Compressing objects:  33% (289/873)
remote: Compressing objects:  34% (297/873)
remote: Compressing objects:  35% (306/873)
remote: Compressing objects:  36% (315/873)
remote: Compressing objects:  37% (324/873)
remote: Compressing objects:  38% (332/873)
remote: Compressing objects:  39% (341/873)
remote: Compressing objects:  40% (350/873)
remote: Compressing objects:  41% (358/873)
remote: Compressing objects:  42% (367/873)
remote: Compressing objects:  43% (376/873)
remote: Compressing objects:  44% (385/873)
remote: Compressing objects:  45% (393/873)
remote: Compressing objects:  46% (402/873)
remote: Compressing objects:  47% (411/873)
remote: Compressing objects:  48% (420/873)
remote: Compressing objects:  49% (428/873)
remote: Compressing objects:  50% (437/873)
remote: Compressing objects:  51% (446/873)
remote: Compressing objects:  52% (454/873)
remote: Compressing objects:  53% (463/873)
remote: Compressing objects:  54% (472/873)
remote: Compressing objects:  55% (481/873)
remote: Compressing objects:  56% (489/873)
remote: Compressing objects:  57% (498/873)
remote: Compressing objects:  58% (507/873)
remote: Compressing objects:  59% (516/873)
remote: Compressing objects:  60% (524/873)
remote: Compressing objects:  61% (533/873)
remote: Compressing objects:  62% (542/873)
remote: Compressing objects:  63% (550/873)
remote: Compressing objects:  64% (559/873)
remote: Compressing objects:  65% (568/873)
remote: Compressing objects:  66% (577/873)
remote: Compressing objects:  67% (585/873)
remote: Compressing objects:  68% (594/873)
remote: Compressing objects:  69% (603/873)
remote: Compressing objects:  70% (612/873)
remote: Compressing objects:  71% (620/873)
remote: Compressing objects:  72% (629/873)
remote: Compressing objects:  73% (638/873)
remote: Compressing objects:  74% (647/873)
remote: Compressing objects:  75% (655/873)
remote: Compressing objects:  76% (664/873)
remote: Compressing objects:  77% (673/873)
remote: Compressing objects:  78% (681/873)
remote: Compressing objects:  79% (690/873)
remote: Compressing objects:  80% (699/873)
remote: Compressing objects:  81% (708/873)
remote: Compressing objects:  82% (716/873)
remote: Compressing objects:  83% (725/873)
remote: Compressing objects:  84% (734/873)
remote: Compressing objects:  85% (743/873)
remote: Compressing objects:  86% (751/873)
remote: Compressing objects:  87% (760/873)
remote: Compressing objects:  88% (769/873)
remote: Compressing objects:  89% (777/873)
remote: Compressing objects:  90% (786/873)
remote: Compressing objects:  91% (795/873)
remote: Compressing objects:  92% (804/873)
remote: Compressing objects:  93% (812/873)
remote: Compressing objects:  94% (821/873)
remote: Compressing objects:  95% (830/873)
remote: Compressing objects:  96% (839/873)
remote: Compressing objects:  97% (847/873)
remote: Compressing objects:  98% (856/873)
remote: Compressing objects:  99% (865/873)
remote: Compressing objects: 100% (873/873)
remote: Compressing objects: 100% (873/873), done.
Receiving objects:   0% (1/938)
Receiving objects:   1% (10/938)
Receiving objects:   2% (19/938)
Receiving objects:   3% (29/938)
Receiving objects:   4% (38/938)
Receiving objects:   5% (47/938)
Receiving objects:   6% (57/938)
Receiving objects:   7% (66/938)
Receiving objects:   8% (76/938)
Receiving objects:   9% (85/938)
Receiving objects:  10% (94/938)
Receiving objects:  11% (104/938)
Receiving objects:  12% (113/938)
Receiving objects:  13% (122/938)
Receiving objects:  14% (132/938)
Receiving objects:  15% (141/938)
Receiving objects:  16% (151/938)
Receiving objects:  17% (160/938)
Receiving objects:  18% (169/938)
Receiving objects:  19% (179/938)
Receiving objects:  20% (188/938)
Receiving objects:  21% (197/938)
Receiving objects:  22% (207/938)
Receiving objects:  23% (216/938)
Receiving objects:  24% (226/938)
Receiving objects:  25% (235/938)
Receiving objects:  26% (244/938)
Receiving objects:  27% (254/938)
Receiving objects:  28% (263/938)
Receiving objects:  29% (273/938)
Receiving objects:  30% (282/938)
Receiving objects:  31% (291/938)
Receiving objects:  32% (301/938)
Receiving objects:  33% (310/938)
Receiving objects:  34% (319/938)
Receiving objects:  35% (329/938)
Receiving objects:  36% (338/938)
Receiving objects:  37% (348/938)
Receiving objects:  38% (357/938)
Receiving objects:  39% (366/938)
Receiving objects:  40% (376/938)
Receiving objects:  41% (385/938)
Receiving objects:  42% (394/938)
Receiving objects:  43% (404/938)
Receiving objects:  44% (413/938)
Receiving objects:  45% (423/938)
Receiving objects:  46% (432/938)
Receiving objects:  47% (441/938)
Receiving objects:  48% (451/938)
Receiving objects:  49% (460/938)
Receiving objects:  50% (469/938)
Receiving objects:  51% (479/938)
Receiving objects:  52% (488/938)
Receiving objects:  53% (498/938)
Receiving objects:  54% (507/938)
Receiving objects:  55% (516/938)
Receiving objects:  56% (526/938)
Receiving objects:  57% (535/938)
Receiving objects:  58% (545/938)
Receiving objects:  59% (554/938)
Receiving objects:  60% (563/938)
Receiving objects:  61% (573/938)
Receiving objects:  62% (582/938)
Receiving objects:  63% (591/938)
Receiving objects:  64% (601/938)
Receiving objects:  65% (610/938)
Receiving objects:  66% (620/938)
Receiving objects:  67% (629/938)
Receiving objects:  68% (638/938)
Receiving objects:  69% (648/938)
Receiving objects:  70% (657/938)
Receiving objects:  71% (666/938)
Receiving objects:  72% (676/938)
Receiving objects:  73% (685/938)
Receiving objects:  74% (695/938)
Receiving objects:  75% (704/938)
Receiving objects:  76% (713/938)
Receiving objects:  77% (723/938)
Receiving objects:  78% (732/938)
Receiving objects:  79% (742/938)
Receiving objects:  80% (751/938)
Receiving objects:  81% (760/938)
Receiving objects:  82% (770/938)
Receiving objects:  83% (779/938)
Receiving objects:  84% (788/938)
remote: Total 938 (delta 603), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (798/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  86% (807/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  87% (817/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  88% (826/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  89% (835/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  90% (845/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  91% (854/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  92% (863/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  93% (873/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  94% (882/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  95% (892/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  96% (901/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  97% (910/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  98% (920/938), 707.77 KiB | 1.35 MiB/s
Receiving objects:  99% (929/938), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (938/938), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (938/938), 937.56 KiB | 1.70 MiB/s, done.
Resolving deltas:   0% (0/622)
Resolving deltas:   1% (7/622)
Resolving deltas:   2% (13/622)
Resolving deltas:   3% (19/622)
Resolving deltas:   4% (25/622)
Resolving deltas:   5% (32/622)
Resolving deltas:   6% (38/622)
Resolving deltas:   7% (44/622)
Resolving deltas:   8% (50/622)
Resolving deltas:   9% (56/622)
Resolving deltas:  10% (63/622)
Resolving deltas:  11% (69/622)
Resolving deltas:  12% (75/622)
Resolving deltas:  13% (81/622)
Resolving deltas:  14% (88/622)
Resolving deltas:  15% (94/622)
Resolving deltas:  16% (100/622)
Resolving deltas:  17% (106/622)
Resolving deltas:  18% (112/622)
Resolving deltas:  19% (119/622)
Resolving deltas:  20% (125/622)
Resolving deltas:  21% (131/622)
Resolving deltas:  22% (137/622)
Resolving deltas:  23% (144/622)
Resolving deltas:  24% (150/622)
Resolving deltas:  25% (156/622)
Resolving deltas:  26% (162/622)
Resolving deltas:  27% (168/622)
Resolving deltas:  28% (175/622)
Resolving deltas:  29% (181/622)
Resolving deltas:  30% (187/622)
Resolving deltas:  31% (193/622)
Resolving deltas:  32% (200/622)
Resolving deltas:  33% (206/622)
Resolving deltas:  34% (212/622)
Resolving deltas:  35% (219/622)
Resolving deltas:  36% (224/622)
Resolving deltas:  37% (231/622)
Resolving deltas:  38% (237/622)
Resolving deltas:  39% (243/622)
Resolving deltas:  40% (249/622)
Resolving deltas:  41% (256/622)
Resolving deltas:  42% (262/622)
Resolving deltas:  43% (268/622)
Resolving deltas:  44% (274/622)
Resolving deltas:  45% (280/622)
Resolving deltas:  46% (287/622)
Resolving deltas:  47% (293/622)
Resolving deltas:  48% (299/622)
Resolving deltas:  49% (305/622)
Resolving deltas:  50% (312/622)
Resolving deltas:  51% (318/622)
Resolving deltas:  52% (324/622)
Resolving deltas:  53% (330/622)
Resolving deltas:  54% (336/622)
Resolving deltas:  55% (343/622)
Resolving deltas:  56% (349/622)
Resolving deltas:  57% (355/622)
Resolving deltas:  58% (361/622)
Resolving deltas:  59% (367/622)
Resolving deltas:  60% (374/622)
Resolving deltas:  61% (380/622)
Resolving deltas:  62% (386/622)
Resolving deltas:  63% (392/622)
Resolving deltas:  64% (399/622)
Resolving deltas:  65% (405/622)
Resolving deltas:  66% (411/622)
Resolving deltas:  67% (417/622)
Resolving deltas:  68% (423/622)
Resolving deltas:  69% (430/622)
Resolving deltas:  70% (436/622)
Resolving deltas:  71% (442/622)
Resolving deltas:  72% (448/622)
Resolving deltas:  73% (455/622)
Resolving deltas:  74% (461/622)
Resolving deltas:  75% (467/622)
Resolving deltas:  76% (473/622)
Resolving deltas:  77% (479/622)
Resolving deltas:  78% (486/622)
Resolving deltas:  79% (492/622)
Resolving deltas:  80% (498/622)
Resolving deltas:  81% (504/622)
Resolving deltas:  82% (511/622)
Resolving deltas:  83% (517/622)
Resolving deltas:  84% (523/622)
Resolving deltas:  85% (529/622)
Resolving deltas:  86% (535/622)
Resolving deltas:  87% (542/622)
Resolving deltas:  88% (548/622)
Resolving deltas:  89% (554/622)
Resolving deltas:  90% (560/622)
Resolving deltas:  91% (567/622)
Resolving deltas:  92% (573/622)
Resolving deltas:  93% (579/622)
Resolving deltas:  94% (585/622)
Resolving deltas:  95% (591/622)
Resolving deltas:  96% (598/622)
Resolving deltas:  97% (604/622)
Resolving deltas:  98% (611/622)
Resolving deltas:  99% (616/622)
Resolving deltas: 100% (622/622)
Resolving deltas: 100% (622/622), done.
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
        "deployment_ref               = DEV000003",
        "machine_name                 = sitef-02",
        "deploy_action                = deploy",
        "deploy_action_resolved       = deploy",
        "repo_root_resolved           = /tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../status/DEV000003",
        "nexus_base_url_resolved      = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev",
        "nexus_user_resolved          = (vazio)",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000003"
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
included: /tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 6306828, 'dev': 64775, 'nlink': 1, 'atime': 1766602243.286341, 'mtime': 1766602243.286341, 'ctime': 1766602243.286341, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3326443523', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
TASK [Deploy | Append do bloco de log do deploy no log cumulativo] *************
changed: [localhost]
TASK [Deploy | Carregar conteúdo completo do log cumulativo (para upload)] *****
ok: [localhost]
TASK [Deploy | Upload JSON + LOG para Harness File Store] **********************
included: /tmp/tmp.8JjiC1Pw36/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Ler envs (MACHINE/MACHINES) e normalizar] **********************
ok: [localhost]
TASK [Harness | Resolver current_machine (força override sem recursão)] ********
ok: [localhost]
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'current_machine is defined' failed. The error was: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_mach
ine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ current_machine }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=40   changed=8    unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
Command finished with status FAILURE
