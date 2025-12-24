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
Clonando repo em /tmp/tmp.jaOFZAKrWM...
Cloning into '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 934, done.
remote: Counting objects:   0% (1/884)
remote: Counting objects:   1% (9/884)
remote: Counting objects:   2% (18/884)
remote: Counting objects:   3% (27/884)
remote: Counting objects:   4% (36/884)
remote: Counting objects:   5% (45/884)
remote: Counting objects:   6% (54/884)
remote: Counting objects:   7% (62/884)
remote: Counting objects:   8% (71/884)
remote: Counting objects:   9% (80/884)
remote: Counting objects:  10% (89/884)
remote: Counting objects:  11% (98/884)
remote: Counting objects:  12% (107/884)
remote: Counting objects:  13% (115/884)
remote: Counting objects:  14% (124/884)
remote: Counting objects:  15% (133/884)
remote: Counting objects:  16% (142/884)
remote: Counting objects:  17% (151/884)
remote: Counting objects:  18% (160/884)
remote: Counting objects:  19% (168/884)
remote: Counting objects:  20% (177/884)
remote: Counting objects:  21% (186/884)
remote: Counting objects:  22% (195/884)
remote: Counting objects:  23% (204/884)
remote: Counting objects:  24% (213/884)
remote: Counting objects:  25% (221/884)
remote: Counting objects:  26% (230/884)
remote: Counting objects:  27% (239/884)
remote: Counting objects:  28% (248/884)
remote: Counting objects:  29% (257/884)
remote: Counting objects:  30% (266/884)
remote: Counting objects:  31% (275/884)
remote: Counting objects:  32% (283/884)
remote: Counting objects:  33% (292/884)
remote: Counting objects:  34% (301/884)
remote: Counting objects:  35% (310/884)
remote: Counting objects:  36% (319/884)
remote: Counting objects:  37% (328/884)
remote: Counting objects:  38% (336/884)
remote: Counting objects:  39% (345/884)
remote: Counting objects:  40% (354/884)
remote: Counting objects:  41% (363/884)
remote: Counting objects:  42% (372/884)
remote: Counting objects:  43% (381/884)
remote: Counting objects:  44% (389/884)
remote: Counting objects:  45% (398/884)
remote: Counting objects:  46% (407/884)
remote: Counting objects:  47% (416/884)
remote: Counting objects:  48% (425/884)
remote: Counting objects:  49% (434/884)
remote: Counting objects:  50% (442/884)
remote: Counting objects:  51% (451/884)
remote: Counting objects:  52% (460/884)
remote: Counting objects:  53% (469/884)
remote: Counting objects:  54% (478/884)
remote: Counting objects:  55% (487/884)
remote: Counting objects:  56% (496/884)
remote: Counting objects:  57% (504/884)
remote: Counting objects:  58% (513/884)
remote: Counting objects:  59% (522/884)
remote: Counting objects:  60% (531/884)
remote: Counting objects:  61% (540/884)
remote: Counting objects:  62% (549/884)
remote: Counting objects:  63% (557/884)
remote: Counting objects:  64% (566/884)
remote: Counting objects:  65% (575/884)
remote: Counting objects:  66% (584/884)
remote: Counting objects:  67% (593/884)
remote: Counting objects:  68% (602/884)
remote: Counting objects:  69% (610/884)
remote: Counting objects:  70% (619/884)
remote: Counting objects:  71% (628/884)
remote: Counting objects:  72% (637/884)
remote: Counting objects:  73% (646/884)
remote: Counting objects:  74% (655/884)
remote: Counting objects:  75% (663/884)
remote: Counting objects:  76% (672/884)
remote: Counting objects:  77% (681/884)
remote: Counting objects:  78% (690/884)
remote: Counting objects:  79% (699/884)
remote: Counting objects:  80% (708/884)
remote: Counting objects:  81% (717/884)
remote: Counting objects:  82% (725/884)
remote: Counting objects:  83% (734/884)
remote: Counting objects:  84% (743/884)
remote: Counting objects:  85% (752/884)
remote: Counting objects:  86% (761/884)
remote: Counting objects:  87% (770/884)
remote: Counting objects:  88% (778/884)
remote: Counting objects:  89% (787/884)
remote: Counting objects:  90% (796/884)
remote: Counting objects:  91% (805/884)
remote: Counting objects:  92% (814/884)
remote: Counting objects:  93% (823/884)
remote: Counting objects:  94% (831/884)
remote: Counting objects:  95% (840/884)
remote: Counting objects:  96% (849/884)
remote: Counting objects:  97% (858/884)
remote: Counting objects:  98% (867/884)
remote: Counting objects:  99% (876/884)
remote: Counting objects: 100% (884/884)
remote: Counting objects: 100% (884/884), done.
remote: Compressing objects:   0% (1/869)
remote: Compressing objects:   1% (9/869)
remote: Compressing objects:   2% (18/869)
remote: Compressing objects:   3% (27/869)
remote: Compressing objects:   4% (35/869)
remote: Compressing objects:   5% (44/869)
remote: Compressing objects:   6% (53/869)
remote: Compressing objects:   7% (61/869)
remote: Compressing objects:   8% (70/869)
remote: Compressing objects:   9% (79/869)
remote: Compressing objects:  10% (87/869)
remote: Compressing objects:  11% (96/869)
remote: Compressing objects:  12% (105/869)
remote: Compressing objects:  13% (113/869)
remote: Compressing objects:  14% (122/869)
remote: Compressing objects:  15% (131/869)
remote: Compressing objects:  16% (140/869)
remote: Compressing objects:  17% (148/869)
remote: Compressing objects:  18% (157/869)
remote: Compressing objects:  19% (166/869)
remote: Compressing objects:  20% (174/869)
remote: Compressing objects:  21% (183/869)
remote: Compressing objects:  22% (192/869)
remote: Compressing objects:  23% (200/869)
remote: Compressing objects:  24% (209/869)
remote: Compressing objects:  25% (218/869)
remote: Compressing objects:  26% (226/869)
remote: Compressing objects:  27% (235/869)
remote: Compressing objects:  28% (244/869)
remote: Compressing objects:  29% (253/869)
remote: Compressing objects:  30% (261/869)
remote: Compressing objects:  31% (270/869)
remote: Compressing objects:  32% (279/869)
remote: Compressing objects:  33% (287/869)
remote: Compressing objects:  34% (296/869)
remote: Compressing objects:  35% (305/869)
remote: Compressing objects:  36% (313/869)
remote: Compressing objects:  37% (322/869)
remote: Compressing objects:  38% (331/869)
remote: Compressing objects:  39% (339/869)
remote: Compressing objects:  40% (348/869)
remote: Compressing objects:  41% (357/869)
remote: Compressing objects:  42% (365/869)
remote: Compressing objects:  43% (374/869)
remote: Compressing objects:  44% (383/869)
remote: Compressing objects:  45% (392/869)
remote: Compressing objects:  46% (400/869)
remote: Compressing objects:  47% (409/869)
remote: Compressing objects:  48% (418/869)
remote: Compressing objects:  49% (426/869)
remote: Compressing objects:  50% (435/869)
remote: Compressing objects:  51% (444/869)
remote: Compressing objects:  52% (452/869)
remote: Compressing objects:  53% (461/869)
remote: Compressing objects:  54% (470/869)
remote: Compressing objects:  55% (478/869)
remote: Compressing objects:  56% (487/869)
remote: Compressing objects:  57% (496/869)
remote: Compressing objects:  58% (505/869)
remote: Compressing objects:  59% (513/869)
remote: Compressing objects:  60% (522/869)
remote: Compressing objects:  61% (531/869)
remote: Compressing objects:  62% (539/869)
remote: Compressing objects:  63% (548/869)
remote: Compressing objects:  64% (557/869)
remote: Compressing objects:  65% (565/869)
remote: Compressing objects:  66% (574/869)
remote: Compressing objects:  67% (583/869)
remote: Compressing objects:  68% (591/869)
remote: Compressing objects:  69% (600/869)
remote: Compressing objects:  70% (609/869)
remote: Compressing objects:  71% (617/869)
remote: Compressing objects:  72% (626/869)
remote: Compressing objects:  73% (635/869)
remote: Compressing objects:  74% (644/869)
remote: Compressing objects:  75% (652/869)
remote: Compressing objects:  76% (661/869)
remote: Compressing objects:  77% (670/869)
remote: Compressing objects:  78% (678/869)
remote: Compressing objects:  79% (687/869)
remote: Compressing objects:  80% (696/869)
remote: Compressing objects:  81% (704/869)
remote: Compressing objects:  82% (713/869)
remote: Compressing objects:  83% (722/869)
remote: Compressing objects:  84% (730/869)
remote: Compressing objects:  85% (739/869)
remote: Compressing objects:  86% (748/869)
remote: Compressing objects:  87% (757/869)
remote: Compressing objects:  88% (765/869)
remote: Compressing objects:  89% (774/869)
remote: Compressing objects:  90% (783/869)
remote: Compressing objects:  91% (791/869)
remote: Compressing objects:  92% (800/869)
remote: Compressing objects:  93% (809/869)
remote: Compressing objects:  94% (817/869)
remote: Compressing objects:  95% (826/869)
remote: Compressing objects:  96% (835/869)
remote: Compressing objects:  97% (843/869)
remote: Compressing objects:  98% (852/869)
remote: Compressing objects:  99% (861/869)
remote: Compressing objects: 100% (869/869)
remote: Compressing objects: 100% (869/869), done.
Receiving objects:   0% (1/934)
Receiving objects:   1% (10/934)
Receiving objects:   2% (19/934)
Receiving objects:   3% (29/934)
Receiving objects:   4% (38/934)
Receiving objects:   5% (47/934)
Receiving objects:   6% (57/934)
Receiving objects:   7% (66/934)
Receiving objects:   8% (75/934)
Receiving objects:   9% (85/934)
Receiving objects:  10% (94/934)
Receiving objects:  11% (103/934)
Receiving objects:  12% (113/934)
Receiving objects:  13% (122/934)
Receiving objects:  14% (131/934)
Receiving objects:  15% (141/934)
Receiving objects:  16% (150/934)
Receiving objects:  17% (159/934)
Receiving objects:  18% (169/934)
Receiving objects:  19% (178/934)
Receiving objects:  20% (187/934)
Receiving objects:  21% (197/934)
Receiving objects:  22% (206/934)
Receiving objects:  23% (215/934)
Receiving objects:  24% (225/934)
Receiving objects:  25% (234/934)
Receiving objects:  26% (243/934)
Receiving objects:  27% (253/934)
Receiving objects:  28% (262/934)
Receiving objects:  29% (271/934)
Receiving objects:  30% (281/934)
Receiving objects:  31% (290/934)
Receiving objects:  32% (299/934)
Receiving objects:  33% (309/934)
Receiving objects:  34% (318/934)
Receiving objects:  35% (327/934)
Receiving objects:  36% (337/934)
Receiving objects:  37% (346/934)
Receiving objects:  38% (355/934)
Receiving objects:  39% (365/934)
Receiving objects:  40% (374/934)
Receiving objects:  41% (383/934)
Receiving objects:  42% (393/934)
Receiving objects:  43% (402/934)
Receiving objects:  44% (411/934)
Receiving objects:  45% (421/934)
Receiving objects:  46% (430/934)
Receiving objects:  47% (439/934)
Receiving objects:  48% (449/934)
Receiving objects:  49% (458/934)
Receiving objects:  50% (467/934)
Receiving objects:  51% (477/934)
Receiving objects:  52% (486/934)
Receiving objects:  53% (496/934)
Receiving objects:  54% (505/934)
Receiving objects:  55% (514/934)
Receiving objects:  56% (524/934)
Receiving objects:  57% (533/934)
Receiving objects:  58% (542/934)
Receiving objects:  59% (552/934)
Receiving objects:  60% (561/934)
Receiving objects:  61% (570/934)
Receiving objects:  62% (580/934)
Receiving objects:  63% (589/934)
Receiving objects:  64% (598/934)
Receiving objects:  65% (608/934)
Receiving objects:  66% (617/934)
Receiving objects:  67% (626/934)
Receiving objects:  68% (636/934)
Receiving objects:  69% (645/934)
Receiving objects:  70% (654/934)
Receiving objects:  71% (664/934)
Receiving objects:  72% (673/934)
Receiving objects:  73% (682/934)
Receiving objects:  74% (692/934)
Receiving objects:  75% (701/934)
Receiving objects:  76% (710/934)
Receiving objects:  77% (720/934)
Receiving objects:  78% (729/934)
Receiving objects:  79% (738/934)
Receiving objects:  80% (748/934)
Receiving objects:  81% (757/934)
Receiving objects:  82% (766/934)
Receiving objects:  83% (776/934)
Receiving objects:  84% (785/934)
Receiving objects:  85% (794/934)
remote: Total 934 (delta 600), reused 0 (delta 0), pack-reused 50
Receiving objects:  86% (804/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  87% (813/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  88% (822/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  89% (832/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  90% (841/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  91% (850/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  92% (860/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  93% (869/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  94% (878/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  95% (888/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  96% (897/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  97% (906/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  98% (916/934), 835.73 KiB | 1.59 MiB/s
Receiving objects:  99% (925/934), 835.73 KiB | 1.59 MiB/s
Receiving objects: 100% (934/934), 835.73 KiB | 1.59 MiB/s
Receiving objects: 100% (934/934), 937.08 KiB | 1.78 MiB/s, done.
Resolving deltas:   0% (0/619)
Resolving deltas:   1% (7/619)
Resolving deltas:   2% (13/619)
Resolving deltas:   3% (19/619)
Resolving deltas:   4% (25/619)
Resolving deltas:   5% (31/619)
Resolving deltas:   6% (38/619)
Resolving deltas:   7% (44/619)
Resolving deltas:   8% (50/619)
Resolving deltas:   9% (56/619)
Resolving deltas:  10% (62/619)
Resolving deltas:  11% (69/619)
Resolving deltas:  12% (75/619)
Resolving deltas:  13% (81/619)
Resolving deltas:  14% (87/619)
Resolving deltas:  15% (93/619)
Resolving deltas:  16% (100/619)
Resolving deltas:  17% (106/619)
Resolving deltas:  18% (112/619)
Resolving deltas:  19% (118/619)
Resolving deltas:  20% (124/619)
Resolving deltas:  21% (130/619)
Resolving deltas:  22% (137/619)
Resolving deltas:  23% (143/619)
Resolving deltas:  24% (149/619)
Resolving deltas:  25% (155/619)
Resolving deltas:  26% (161/619)
Resolving deltas:  27% (168/619)
Resolving deltas:  28% (174/619)
Resolving deltas:  29% (180/619)
Resolving deltas:  30% (186/619)
Resolving deltas:  31% (192/619)
Resolving deltas:  32% (199/619)
Resolving deltas:  33% (205/619)
Resolving deltas:  34% (211/619)
Resolving deltas:  35% (217/619)
Resolving deltas:  36% (223/619)
Resolving deltas:  37% (230/619)
Resolving deltas:  38% (236/619)
Resolving deltas:  39% (242/619)
Resolving deltas:  40% (248/619)
Resolving deltas:  41% (254/619)
Resolving deltas:  42% (260/619)
Resolving deltas:  43% (267/619)
Resolving deltas:  44% (273/619)
Resolving deltas:  45% (279/619)
Resolving deltas:  46% (285/619)
Resolving deltas:  47% (291/619)
Resolving deltas:  48% (298/619)
Resolving deltas:  49% (304/619)
Resolving deltas:  50% (310/619)
Resolving deltas:  51% (316/619)
Resolving deltas:  52% (322/619)
Resolving deltas:  53% (329/619)
Resolving deltas:  54% (335/619)
Resolving deltas:  55% (341/619)
Resolving deltas:  56% (347/619)
Resolving deltas:  57% (353/619)
Resolving deltas:  58% (360/619)
Resolving deltas:  59% (366/619)
Resolving deltas:  60% (372/619)
Resolving deltas:  61% (378/619)
Resolving deltas:  62% (384/619)
Resolving deltas:  63% (390/619)
Resolving deltas:  64% (397/619)
Resolving deltas:  65% (403/619)
Resolving deltas:  66% (409/619)
Resolving deltas:  67% (415/619)
Resolving deltas:  68% (421/619)
Resolving deltas:  69% (428/619)
Resolving deltas:  70% (434/619)
Resolving deltas:  71% (440/619)
Resolving deltas:  72% (446/619)
Resolving deltas:  73% (452/619)
Resolving deltas:  74% (459/619)
Resolving deltas:  75% (465/619)
Resolving deltas:  76% (471/619)
Resolving deltas:  77% (477/619)
Resolving deltas:  78% (483/619)
Resolving deltas:  79% (490/619)
Resolving deltas:  80% (496/619)
Resolving deltas:  81% (502/619)
Resolving deltas:  82% (508/619)
Resolving deltas:  83% (514/619)
Resolving deltas:  84% (520/619)
Resolving deltas:  85% (527/619)
Resolving deltas:  86% (533/619)
Resolving deltas:  87% (539/619)
Resolving deltas:  88% (545/619)
Resolving deltas:  89% (551/619)
Resolving deltas:  90% (558/619)
Resolving deltas:  91% (564/619)
Resolving deltas:  92% (570/619)
Resolving deltas:  93% (576/619)
Resolving deltas:  94% (582/619)
Resolving deltas:  95% (589/619)
Resolving deltas:  96% (595/619)
Resolving deltas:  97% (601/619)
Resolving deltas:  98% (607/619)
Resolving deltas:  99% (613/619)
Resolving deltas: 100% (619/619)
Resolving deltas: 100% (619/619), done.
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
        "repo_root_resolved           = /tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../status/DEV000003",
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
included: /tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 15432, 'dev': 64775, 'nlink': 1, 'atime': 1766601951.6133235, 'mtime': 1766601951.582323, 'ctime': 1766601951.582323, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3952454763', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
included: /tmp/tmp.jaOFZAKrWM/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Ler envs (MACHINE/MACHINES) e normalizar] **********************
ok: [localhost]
TASK [Harness | Resolver current_machine de forma segura (evita recursão)] *****
ok: [localhost]
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'current_machine is defined' failed. The error was: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ current_machine }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=40   changed=8    unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
Command finished with status FAILURE
