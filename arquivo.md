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
Clonando repo em /tmp/tmp.sprwQpVVqN...
Cloning into '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 915, done.
remote: Counting objects:   0% (1/865)
remote: Counting objects:   1% (9/865)
remote: Counting objects:   2% (18/865)
remote: Counting objects:   3% (26/865)
remote: Counting objects:   4% (35/865)
remote: Counting objects:   5% (44/865)
remote: Counting objects:   6% (52/865)
remote: Counting objects:   7% (61/865)
remote: Counting objects:   8% (70/865)
remote: Counting objects:   9% (78/865)
remote: Counting objects:  10% (87/865)
remote: Counting objects:  11% (96/865)
remote: Counting objects:  12% (104/865)
remote: Counting objects:  13% (113/865)
remote: Counting objects:  14% (122/865)
remote: Counting objects:  15% (130/865)
remote: Counting objects:  16% (139/865)
remote: Counting objects:  17% (148/865)
remote: Counting objects:  18% (156/865)
remote: Counting objects:  19% (165/865)
remote: Counting objects:  20% (173/865)
remote: Counting objects:  21% (182/865)
remote: Counting objects:  22% (191/865)
remote: Counting objects:  23% (199/865)
remote: Counting objects:  24% (208/865)
remote: Counting objects:  25% (217/865)
remote: Counting objects:  26% (225/865)
remote: Counting objects:  27% (234/865)
remote: Counting objects:  28% (243/865)
remote: Counting objects:  29% (251/865)
remote: Counting objects:  30% (260/865)
remote: Counting objects:  31% (269/865)
remote: Counting objects:  32% (277/865)
remote: Counting objects:  33% (286/865)
remote: Counting objects:  34% (295/865)
remote: Counting objects:  35% (303/865)
remote: Counting objects:  36% (312/865)
remote: Counting objects:  37% (321/865)
remote: Counting objects:  38% (329/865)
remote: Counting objects:  39% (338/865)
remote: Counting objects:  40% (346/865)
remote: Counting objects:  41% (355/865)
remote: Counting objects:  42% (364/865)
remote: Counting objects:  43% (372/865)
remote: Counting objects:  44% (381/865)
remote: Counting objects:  45% (390/865)
remote: Counting objects:  46% (398/865)
remote: Counting objects:  47% (407/865)
remote: Counting objects:  48% (416/865)
remote: Counting objects:  49% (424/865)
remote: Counting objects:  50% (433/865)
remote: Counting objects:  51% (442/865)
remote: Counting objects:  52% (450/865)
remote: Counting objects:  53% (459/865)
remote: Counting objects:  54% (468/865)
remote: Counting objects:  55% (476/865)
remote: Counting objects:  56% (485/865)
remote: Counting objects:  57% (494/865)
remote: Counting objects:  58% (502/865)
remote: Counting objects:  59% (511/865)
remote: Counting objects:  60% (519/865)
remote: Counting objects:  61% (528/865)
remote: Counting objects:  62% (537/865)
remote: Counting objects:  63% (545/865)
remote: Counting objects:  64% (554/865)
remote: Counting objects:  65% (563/865)
remote: Counting objects:  66% (571/865)
remote: Counting objects:  67% (580/865)
remote: Counting objects:  68% (589/865)
remote: Counting objects:  69% (597/865)
remote: Counting objects:  70% (606/865)
remote: Counting objects:  71% (615/865)
remote: Counting objects:  72% (623/865)
remote: Counting objects:  73% (632/865)
remote: Counting objects:  74% (641/865)
remote: Counting objects:  75% (649/865)
remote: Counting objects:  76% (658/865)
remote: Counting objects:  77% (667/865)
remote: Counting objects:  78% (675/865)
remote: Counting objects:  79% (684/865)
remote: Counting objects:  80% (692/865)
remote: Counting objects:  81% (701/865)
remote: Counting objects:  82% (710/865)
remote: Counting objects:  83% (718/865)
remote: Counting objects:  84% (727/865)
remote: Counting objects:  85% (736/865)
remote: Counting objects:  86% (744/865)
remote: Counting objects:  87% (753/865)
remote: Counting objects:  88% (762/865)
remote: Counting objects:  89% (770/865)
remote: Counting objects:  90% (779/865)
remote: Counting objects:  91% (788/865)
remote: Counting objects:  92% (796/865)
remote: Counting objects:  93% (805/865)
remote: Counting objects:  94% (814/865)
remote: Counting objects:  95% (822/865)
remote: Counting objects:  96% (831/865)
remote: Counting objects:  97% (840/865)
remote: Counting objects:  98% (848/865)
remote: Counting objects:  99% (857/865)
remote: Counting objects: 100% (865/865)
remote: Counting objects: 100% (865/865), done.
remote: Compressing objects:   0% (1/850)
remote: Compressing objects:   1% (9/850)
remote: Compressing objects:   2% (17/850)
remote: Compressing objects:   3% (26/850)
remote: Compressing objects:   4% (34/850)
remote: Compressing objects:   5% (43/850)
remote: Compressing objects:   6% (51/850)
remote: Compressing objects:   7% (60/850)
remote: Compressing objects:   8% (68/850)
remote: Compressing objects:   9% (77/850)
remote: Compressing objects:  10% (85/850)
remote: Compressing objects:  11% (94/850)
remote: Compressing objects:  12% (102/850)
remote: Compressing objects:  13% (111/850)
remote: Compressing objects:  14% (119/850)
remote: Compressing objects:  15% (128/850)
remote: Compressing objects:  16% (136/850)
remote: Compressing objects:  17% (145/850)
remote: Compressing objects:  18% (153/850)
remote: Compressing objects:  19% (162/850)
remote: Compressing objects:  20% (170/850)
remote: Compressing objects:  21% (179/850)
remote: Compressing objects:  22% (187/850)
remote: Compressing objects:  23% (196/850)
remote: Compressing objects:  24% (204/850)
remote: Compressing objects:  25% (213/850)
remote: Compressing objects:  26% (221/850)
remote: Compressing objects:  27% (230/850)
remote: Compressing objects:  28% (238/850)
remote: Compressing objects:  29% (247/850)
remote: Compressing objects:  30% (255/850)
remote: Compressing objects:  31% (264/850)
remote: Compressing objects:  32% (272/850)
remote: Compressing objects:  33% (281/850)
remote: Compressing objects:  34% (289/850)
remote: Compressing objects:  35% (298/850)
remote: Compressing objects:  36% (306/850)
remote: Compressing objects:  37% (315/850)
remote: Compressing objects:  38% (323/850)
remote: Compressing objects:  39% (332/850)
remote: Compressing objects:  40% (340/850)
remote: Compressing objects:  41% (349/850)
remote: Compressing objects:  42% (357/850)
remote: Compressing objects:  43% (366/850)
remote: Compressing objects:  44% (374/850)
remote: Compressing objects:  45% (383/850)
remote: Compressing objects:  46% (391/850)
remote: Compressing objects:  47% (400/850)
remote: Compressing objects:  48% (408/850)
remote: Compressing objects:  49% (417/850)
remote: Compressing objects:  50% (425/850)
remote: Compressing objects:  51% (434/850)
remote: Compressing objects:  52% (442/850)
remote: Compressing objects:  53% (451/850)
remote: Compressing objects:  54% (459/850)
remote: Compressing objects:  55% (468/850)
remote: Compressing objects:  56% (476/850)
remote: Compressing objects:  57% (485/850)
remote: Compressing objects:  58% (493/850)
remote: Compressing objects:  59% (502/850)
remote: Compressing objects:  60% (510/850)
remote: Compressing objects:  61% (519/850)
remote: Compressing objects:  62% (527/850)
remote: Compressing objects:  63% (536/850)
remote: Compressing objects:  64% (544/850)
remote: Compressing objects:  65% (553/850)
remote: Compressing objects:  66% (561/850)
remote: Compressing objects:  67% (570/850)
remote: Compressing objects:  68% (578/850)
remote: Compressing objects:  69% (587/850)
remote: Compressing objects:  70% (595/850)
remote: Compressing objects:  71% (604/850)
remote: Compressing objects:  72% (612/850)
remote: Compressing objects:  73% (621/850)
remote: Compressing objects:  74% (629/850)
remote: Compressing objects:  75% (638/850)
remote: Compressing objects:  76% (646/850)
remote: Compressing objects:  77% (655/850)
remote: Compressing objects:  78% (663/850)
remote: Compressing objects:  79% (672/850)
remote: Compressing objects:  80% (680/850)
remote: Compressing objects:  81% (689/850)
remote: Compressing objects:  82% (697/850)
remote: Compressing objects:  83% (706/850)
remote: Compressing objects:  84% (714/850)
remote: Compressing objects:  85% (723/850)
remote: Compressing objects:  86% (731/850)
remote: Compressing objects:  87% (740/850)
remote: Compressing objects:  88% (748/850)
remote: Compressing objects:  89% (757/850)
remote: Compressing objects:  90% (765/850)
remote: Compressing objects:  91% (774/850)
remote: Compressing objects:  92% (782/850)
remote: Compressing objects:  93% (791/850)
remote: Compressing objects:  94% (799/850)
remote: Compressing objects:  95% (808/850)
remote: Compressing objects:  96% (816/850)
remote: Compressing objects:  97% (825/850)
remote: Compressing objects:  98% (833/850)
remote: Compressing objects:  99% (842/850)
remote: Compressing objects: 100% (850/850)
remote: Compressing objects: 100% (850/850), done.
Receiving objects:   0% (1/915)
Receiving objects:   1% (10/915)
Receiving objects:   2% (19/915)
Receiving objects:   3% (28/915)
Receiving objects:   4% (37/915)
Receiving objects:   5% (46/915)
Receiving objects:   6% (55/915)
Receiving objects:   7% (65/915)
Receiving objects:   8% (74/915)
Receiving objects:   9% (83/915)
Receiving objects:  10% (92/915)
Receiving objects:  11% (101/915)
Receiving objects:  12% (110/915)
Receiving objects:  13% (119/915)
Receiving objects:  14% (129/915)
Receiving objects:  15% (138/915)
Receiving objects:  16% (147/915)
Receiving objects:  17% (156/915)
Receiving objects:  18% (165/915)
Receiving objects:  19% (174/915)
Receiving objects:  20% (183/915)
Receiving objects:  21% (193/915)
Receiving objects:  22% (202/915)
Receiving objects:  23% (211/915)
Receiving objects:  24% (220/915)
Receiving objects:  25% (229/915)
Receiving objects:  26% (238/915)
Receiving objects:  27% (248/915)
Receiving objects:  28% (257/915)
Receiving objects:  29% (266/915)
Receiving objects:  30% (275/915)
Receiving objects:  31% (284/915)
Receiving objects:  32% (293/915)
Receiving objects:  33% (302/915)
Receiving objects:  34% (312/915)
Receiving objects:  35% (321/915)
Receiving objects:  36% (330/915)
Receiving objects:  37% (339/915)
Receiving objects:  38% (348/915)
Receiving objects:  39% (357/915)
Receiving objects:  40% (366/915)
Receiving objects:  41% (376/915)
Receiving objects:  42% (385/915)
Receiving objects:  43% (394/915)
Receiving objects:  44% (403/915)
Receiving objects:  45% (412/915)
Receiving objects:  46% (421/915)
Receiving objects:  47% (431/915)
Receiving objects:  48% (440/915)
Receiving objects:  49% (449/915)
Receiving objects:  50% (458/915)
Receiving objects:  51% (467/915)
Receiving objects:  52% (476/915)
Receiving objects:  53% (485/915)
Receiving objects:  54% (495/915)
Receiving objects:  55% (504/915)
Receiving objects:  56% (513/915)
Receiving objects:  57% (522/915)
Receiving objects:  58% (531/915)
Receiving objects:  59% (540/915)
Receiving objects:  60% (549/915)
Receiving objects:  61% (559/915)
Receiving objects:  62% (568/915)
Receiving objects:  63% (577/915)
Receiving objects:  64% (586/915)
Receiving objects:  65% (595/915)
Receiving objects:  66% (604/915)
Receiving objects:  67% (614/915)
Receiving objects:  68% (623/915)
Receiving objects:  69% (632/915)
Receiving objects:  70% (641/915)
Receiving objects:  71% (650/915)
Receiving objects:  72% (659/915)
Receiving objects:  73% (668/915)
Receiving objects:  74% (678/915)
Receiving objects:  75% (687/915)
Receiving objects:  76% (696/915)
Receiving objects:  77% (705/915)
Receiving objects:  78% (714/915)
Receiving objects:  79% (723/915)
Receiving objects:  80% (732/915)
Receiving objects:  81% (742/915)
Receiving objects:  82% (751/915)
Receiving objects:  83% (760/915)
Receiving objects:  84% (769/915)
remote: Total 915 (delta 585), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (778/915)
Receiving objects:  86% (787/915)
Receiving objects:  87% (797/915)
Receiving objects:  88% (806/915)
Receiving objects:  89% (815/915)
Receiving objects:  90% (824/915)
Receiving objects:  91% (833/915)
Receiving objects:  92% (842/915)
Receiving objects:  93% (851/915)
Receiving objects:  94% (861/915)
Receiving objects:  95% (870/915)
Receiving objects:  96% (879/915)
Receiving objects:  97% (888/915)
Receiving objects:  98% (897/915)
Receiving objects:  99% (906/915)
Receiving objects: 100% (915/915)
Receiving objects: 100% (915/915), 934.37 KiB | 1.83 MiB/s, done.
Resolving deltas:   0% (0/604)
Resolving deltas:   1% (7/604)
Resolving deltas:   2% (13/604)
Resolving deltas:   3% (19/604)
Resolving deltas:   4% (25/604)
Resolving deltas:   5% (31/604)
Resolving deltas:   6% (37/604)
Resolving deltas:   7% (43/604)
Resolving deltas:   8% (49/604)
Resolving deltas:   9% (55/604)
Resolving deltas:  10% (61/604)
Resolving deltas:  11% (67/604)
Resolving deltas:  12% (73/604)
Resolving deltas:  13% (79/604)
Resolving deltas:  14% (85/604)
Resolving deltas:  15% (91/604)
Resolving deltas:  16% (97/604)
Resolving deltas:  17% (103/604)
Resolving deltas:  18% (109/604)
Resolving deltas:  19% (115/604)
Resolving deltas:  20% (121/604)
Resolving deltas:  21% (127/604)
Resolving deltas:  22% (133/604)
Resolving deltas:  23% (139/604)
Resolving deltas:  24% (145/604)
Resolving deltas:  25% (151/604)
Resolving deltas:  26% (158/604)
Resolving deltas:  27% (164/604)
Resolving deltas:  28% (170/604)
Resolving deltas:  29% (176/604)
Resolving deltas:  30% (182/604)
Resolving deltas:  31% (188/604)
Resolving deltas:  32% (194/604)
Resolving deltas:  33% (200/604)
Resolving deltas:  34% (206/604)
Resolving deltas:  35% (212/604)
Resolving deltas:  36% (218/604)
Resolving deltas:  37% (224/604)
Resolving deltas:  38% (230/604)
Resolving deltas:  39% (236/604)
Resolving deltas:  40% (242/604)
Resolving deltas:  41% (248/604)
Resolving deltas:  42% (254/604)
Resolving deltas:  43% (260/604)
Resolving deltas:  44% (266/604)
Resolving deltas:  45% (272/604)
Resolving deltas:  46% (278/604)
Resolving deltas:  47% (284/604)
Resolving deltas:  48% (290/604)
Resolving deltas:  49% (296/604)
Resolving deltas:  50% (302/604)
Resolving deltas:  51% (309/604)
Resolving deltas:  52% (316/604)
Resolving deltas:  53% (321/604)
Resolving deltas:  54% (327/604)
Resolving deltas:  55% (333/604)
Resolving deltas:  56% (339/604)
Resolving deltas:  57% (345/604)
Resolving deltas:  58% (352/604)
Resolving deltas:  59% (357/604)
Resolving deltas:  60% (363/604)
Resolving deltas:  61% (369/604)
Resolving deltas:  62% (375/604)
Resolving deltas:  63% (381/604)
Resolving deltas:  64% (387/604)
Resolving deltas:  65% (393/604)
Resolving deltas:  66% (399/604)
Resolving deltas:  67% (405/604)
Resolving deltas:  68% (411/604)
Resolving deltas:  69% (417/604)
Resolving deltas:  70% (423/604)
Resolving deltas:  71% (429/604)
Resolving deltas:  72% (435/604)
Resolving deltas:  73% (441/604)
Resolving deltas:  74% (447/604)
Resolving deltas:  75% (453/604)
Resolving deltas:  76% (460/604)
Resolving deltas:  77% (466/604)
Resolving deltas:  78% (472/604)
Resolving deltas:  79% (478/604)
Resolving deltas:  80% (484/604)
Resolving deltas:  81% (490/604)
Resolving deltas:  82% (496/604)
Resolving deltas:  83% (502/604)
Resolving deltas:  84% (508/604)
Resolving deltas:  85% (514/604)
Resolving deltas:  86% (520/604)
Resolving deltas:  87% (526/604)
Resolving deltas:  88% (532/604)
Resolving deltas:  89% (538/604)
Resolving deltas:  90% (544/604)
Resolving deltas:  91% (550/604)
Resolving deltas:  92% (556/604)
Resolving deltas:  93% (562/604)
Resolving deltas:  94% (568/604)
Resolving deltas:  95% (574/604)
Resolving deltas:  96% (580/604)
Resolving deltas:  97% (586/604)
Resolving deltas:  98% (592/604)
Resolving deltas:  99% (598/604)
Resolving deltas: 100% (604/604)
Resolving deltas: 100% (604/604), done.
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
        "deployment_ref               = DEV000001",
        "machine_name                 = sitef-02",
        "deploy_action                = deploy",
        "deploy_action_resolved       = deploy",
        "repo_root_resolved           = /tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../status/DEV000001",
        "nexus_base_url_resolved      = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev",
        "nexus_user_resolved          = (vazio)",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000001"
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
included: /tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 4341148, 'dev': 64775, 'nlink': 1, 'atime': 1766599633.963521, 'mtime': 1766599633.963521, 'ctime': 1766599633.963521, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2329176614', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
fatal: [localhost -> sitef-02(100.99.57.128)]: FAILED! => {"changed": true, "cmd": "set -o pipefail\ncd \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\"\n/usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&amp;1 | tee -a \"/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log\"\nexit ${PIPESTATUS[0]}\n", "delta": "0:00:09.961133", "end": "2025-12-24 18:07:32.532875", "msg": "non-zero return code", "rc": 1, "start": "2025-12-24 18:07:22.571742", "stderr": "", "stderr_lines": [], "stdout": "+ set -euo pipefail\n+++ dirname ./init_deploy.sh\n++ cd .\n++ pwd\n+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts\n+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ exec\n++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt\n+ echo '== Início init_deploy.sh =='\n== Início init_deploy.sh ==\n+ date\nWed Dec 24 06:07:22 PM UTC 2025\n+ set -x\n+ echo 'Instalacao de dependencias do sistema...'\nInstalacao de dependencias do sistema...\n+ dnf install -y --nodocs python3.12 python3.12-pip\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    \nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  3.5 kB/s | 4.1 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 3.2 kB/s | 4.5 kB     00:01    \nEPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    \nPackage python3.12-3.12.12-1.el9_7.x86_64 is already installed.\nPackage python3.12-pip-23.2.1-5.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\n+ echo 'Instalacao de dependencias do sistema finalizada.'\nInstalacao de dependencias do sistema finalizada.\n+ set +x\n./init_deploy.sh: line 22: RC: unbound variable", "stdout_lines": ["+ set -euo pipefail", "+++ dirname ./init_deploy.sh", "++ cd .", "++ pwd", "+ SCRIPT_DIR=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts", "+ LOG_FILE=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ exec", "++ tee -a /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/deploy.txt", "+ echo '== Início init_deploy.sh =='", "== Início init_deploy.sh ==", "+ date", "Wed Dec 24 06:07:22 PM UTC 2025", "+ set -x", "+ echo 'Instalacao de dependencias do sistema...'", "Instalacao de dependencias do sistema...", "+ dnf install -y --nodocs python3.12 python3.12-pip", "Updating Subscription Management repositories.", "", "This system has release set to 9 and it receives updates only for this release.", "", "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    ", "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.9 kB/s | 4.5 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  3.5 kB/s | 4.1 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.2 kB/s | 4.5 kB     00:01    ", "EPEL9_X86                                       2.1 kB/s | 2.3 kB     00:01    ", "Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.", "Package python3.12-pip-23.2.1-5.el9.noarch is already installed.", "Dependencies resolved.", "Nothing to do.", "Complete!", "+ echo 'Instalacao de dependencias do sistema finalizada.'", "Instalacao de dependencias do sistema finalizada.", "+ set +x", "./init_deploy.sh: line 22: RC: unbound variable"]}
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
included: /tmp/tmp.sprwQpVVqN/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {"msg": "The conditional check 'current_machine is defined' failed. The error was: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ current_machine }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ current_machine }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=38   changed=8    unreachable=0    failed=1    skipped=4    rescued=0    ignored=1   
Command finished with status FAILURE
