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
Clonando repo em /tmp/tmp.0Y4rLbQuLV...
Cloning into '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 851, done.
remote: Counting objects:   0% (1/801)
remote: Counting objects:   1% (9/801)
remote: Counting objects:   2% (17/801)
remote: Counting objects:   3% (25/801)
remote: Counting objects:   4% (33/801)
remote: Counting objects:   5% (41/801)
remote: Counting objects:   6% (49/801)
remote: Counting objects:   7% (57/801)
remote: Counting objects:   8% (65/801)
remote: Counting objects:   9% (73/801)
remote: Counting objects:  10% (81/801)
remote: Counting objects:  11% (89/801)
remote: Counting objects:  12% (97/801)
remote: Counting objects:  13% (105/801)
remote: Counting objects:  14% (113/801)
remote: Counting objects:  15% (121/801)
remote: Counting objects:  16% (129/801)
remote: Counting objects:  17% (137/801)
remote: Counting objects:  18% (145/801)
remote: Counting objects:  19% (153/801)
remote: Counting objects:  20% (161/801)
remote: Counting objects:  21% (169/801)
remote: Counting objects:  22% (177/801)
remote: Counting objects:  23% (185/801)
remote: Counting objects:  24% (193/801)
remote: Counting objects:  25% (201/801)
remote: Counting objects:  26% (209/801)
remote: Counting objects:  27% (217/801)
remote: Counting objects:  28% (225/801)
remote: Counting objects:  29% (233/801)
remote: Counting objects:  30% (241/801)
remote: Counting objects:  31% (249/801)
remote: Counting objects:  32% (257/801)
remote: Counting objects:  33% (265/801)
remote: Counting objects:  34% (273/801)
remote: Counting objects:  35% (281/801)
remote: Counting objects:  36% (289/801)
remote: Counting objects:  37% (297/801)
remote: Counting objects:  38% (305/801)
remote: Counting objects:  39% (313/801)
remote: Counting objects:  40% (321/801)
remote: Counting objects:  41% (329/801)
remote: Counting objects:  42% (337/801)
remote: Counting objects:  43% (345/801)
remote: Counting objects:  44% (353/801)
remote: Counting objects:  45% (361/801)
remote: Counting objects:  46% (369/801)
remote: Counting objects:  47% (377/801)
remote: Counting objects:  48% (385/801)
remote: Counting objects:  49% (393/801)
remote: Counting objects:  50% (401/801)
remote: Counting objects:  51% (409/801)
remote: Counting objects:  52% (417/801)
remote: Counting objects:  53% (425/801)
remote: Counting objects:  54% (433/801)
remote: Counting objects:  55% (441/801)
remote: Counting objects:  56% (449/801)
remote: Counting objects:  57% (457/801)
remote: Counting objects:  58% (465/801)
remote: Counting objects:  59% (473/801)
remote: Counting objects:  60% (481/801)
remote: Counting objects:  61% (489/801)
remote: Counting objects:  62% (497/801)
remote: Counting objects:  63% (505/801)
remote: Counting objects:  64% (513/801)
remote: Counting objects:  65% (521/801)
remote: Counting objects:  66% (529/801)
remote: Counting objects:  67% (537/801)
remote: Counting objects:  68% (545/801)
remote: Counting objects:  69% (553/801)
remote: Counting objects:  70% (561/801)
remote: Counting objects:  71% (569/801)
remote: Counting objects:  72% (577/801)
remote: Counting objects:  73% (585/801)
remote: Counting objects:  74% (593/801)
remote: Counting objects:  75% (601/801)
remote: Counting objects:  76% (609/801)
remote: Counting objects:  77% (617/801)
remote: Counting objects:  78% (625/801)
remote: Counting objects:  79% (633/801)
remote: Counting objects:  80% (641/801)
remote: Counting objects:  81% (649/801)
remote: Counting objects:  82% (657/801)
remote: Counting objects:  83% (665/801)
remote: Counting objects:  84% (673/801)
remote: Counting objects:  85% (681/801)
remote: Counting objects:  86% (689/801)
remote: Counting objects:  87% (697/801)
remote: Counting objects:  88% (705/801)
remote: Counting objects:  89% (713/801)
remote: Counting objects:  90% (721/801)
remote: Counting objects:  91% (729/801)
remote: Counting objects:  92% (737/801)
remote: Counting objects:  93% (745/801)
remote: Counting objects:  94% (753/801)
remote: Counting objects:  95% (761/801)
remote: Counting objects:  96% (769/801)
remote: Counting objects:  97% (777/801)
remote: Counting objects:  98% (785/801)
remote: Counting objects:  99% (793/801)
remote: Counting objects: 100% (801/801)
remote: Counting objects: 100% (801/801), done.
remote: Compressing objects:   0% (1/786)
remote: Compressing objects:   1% (8/786)
remote: Compressing objects:   2% (16/786)
remote: Compressing objects:   3% (24/786)
remote: Compressing objects:   4% (32/786)
remote: Compressing objects:   5% (40/786)
remote: Compressing objects:   6% (48/786)
remote: Compressing objects:   7% (56/786)
remote: Compressing objects:   8% (63/786)
remote: Compressing objects:   9% (71/786)
remote: Compressing objects:  10% (79/786)
remote: Compressing objects:  11% (87/786)
remote: Compressing objects:  12% (95/786)
remote: Compressing objects:  13% (103/786)
remote: Compressing objects:  14% (111/786)
remote: Compressing objects:  15% (118/786)
remote: Compressing objects:  16% (126/786)
remote: Compressing objects:  17% (134/786)
remote: Compressing objects:  18% (142/786)
remote: Compressing objects:  19% (150/786)
remote: Compressing objects:  20% (158/786)
remote: Compressing objects:  21% (166/786)
remote: Compressing objects:  22% (173/786)
remote: Compressing objects:  23% (181/786)
remote: Compressing objects:  24% (189/786)
remote: Compressing objects:  25% (197/786)
remote: Compressing objects:  26% (205/786)
remote: Compressing objects:  27% (213/786)
remote: Compressing objects:  28% (221/786)
remote: Compressing objects:  29% (228/786)
remote: Compressing objects:  30% (236/786)
remote: Compressing objects:  31% (244/786)
remote: Compressing objects:  32% (252/786)
remote: Compressing objects:  33% (260/786)
remote: Compressing objects:  34% (268/786)
remote: Compressing objects:  35% (276/786)
remote: Compressing objects:  36% (283/786)
remote: Compressing objects:  37% (291/786)
remote: Compressing objects:  38% (299/786)
remote: Compressing objects:  39% (307/786)
remote: Compressing objects:  40% (315/786)
remote: Compressing objects:  41% (323/786)
remote: Compressing objects:  42% (331/786)
remote: Compressing objects:  43% (338/786)
remote: Compressing objects:  44% (346/786)
remote: Compressing objects:  45% (354/786)
remote: Compressing objects:  46% (362/786)
remote: Compressing objects:  47% (370/786)
remote: Compressing objects:  48% (378/786)
remote: Compressing objects:  49% (386/786)
remote: Compressing objects:  50% (393/786)
remote: Compressing objects:  51% (401/786)
remote: Compressing objects:  52% (409/786)
remote: Compressing objects:  53% (417/786)
remote: Compressing objects:  54% (425/786)
remote: Compressing objects:  55% (433/786)
remote: Compressing objects:  56% (441/786)
remote: Compressing objects:  57% (449/786)
remote: Compressing objects:  58% (456/786)
remote: Compressing objects:  59% (464/786)
remote: Compressing objects:  60% (472/786)
remote: Compressing objects:  61% (480/786)
remote: Compressing objects:  62% (488/786)
remote: Compressing objects:  63% (496/786)
remote: Compressing objects:  64% (504/786)
remote: Compressing objects:  65% (511/786)
remote: Compressing objects:  66% (519/786)
remote: Compressing objects:  67% (527/786)
remote: Compressing objects:  68% (535/786)
remote: Compressing objects:  69% (543/786)
remote: Compressing objects:  70% (551/786)
remote: Compressing objects:  71% (559/786)
remote: Compressing objects:  72% (566/786)
remote: Compressing objects:  73% (574/786)
remote: Compressing objects:  74% (582/786)
remote: Compressing objects:  75% (590/786)
remote: Compressing objects:  76% (598/786)
remote: Compressing objects:  77% (606/786)
remote: Compressing objects:  78% (614/786)
remote: Compressing objects:  79% (621/786)
remote: Compressing objects:  80% (629/786)
remote: Compressing objects:  81% (637/786)
remote: Compressing objects:  82% (645/786)
remote: Compressing objects:  83% (653/786)
remote: Compressing objects:  84% (661/786)
remote: Compressing objects:  85% (669/786)
remote: Compressing objects:  86% (676/786)
remote: Compressing objects:  87% (684/786)
remote: Compressing objects:  88% (692/786)
remote: Compressing objects:  89% (700/786)
remote: Compressing objects:  90% (708/786)
remote: Compressing objects:  91% (716/786)
remote: Compressing objects:  92% (724/786)
remote: Compressing objects:  93% (731/786)
remote: Compressing objects:  94% (739/786)
remote: Compressing objects:  95% (747/786)
remote: Compressing objects:  96% (755/786)
remote: Compressing objects:  97% (763/786)
remote: Compressing objects:  98% (771/786)
remote: Compressing objects:  99% (779/786)
remote: Compressing objects: 100% (786/786)
remote: Compressing objects: 100% (786/786), done.
Receiving objects:   0% (1/851)
Receiving objects:   1% (9/851)
Receiving objects:   2% (18/851)
Receiving objects:   3% (26/851)
Receiving objects:   4% (35/851)
Receiving objects:   5% (43/851)
Receiving objects:   6% (52/851)
Receiving objects:   7% (60/851)
Receiving objects:   8% (69/851)
Receiving objects:   9% (77/851)
Receiving objects:  10% (86/851)
Receiving objects:  11% (94/851)
Receiving objects:  12% (103/851)
Receiving objects:  13% (111/851)
Receiving objects:  14% (120/851)
Receiving objects:  15% (128/851)
Receiving objects:  16% (137/851)
Receiving objects:  17% (145/851)
Receiving objects:  18% (154/851)
Receiving objects:  19% (162/851)
Receiving objects:  20% (171/851)
Receiving objects:  21% (179/851)
Receiving objects:  22% (188/851)
Receiving objects:  23% (196/851)
Receiving objects:  24% (205/851)
Receiving objects:  25% (213/851)
Receiving objects:  26% (222/851)
Receiving objects:  27% (230/851)
Receiving objects:  28% (239/851)
Receiving objects:  29% (247/851)
Receiving objects:  30% (256/851)
Receiving objects:  31% (264/851)
Receiving objects:  32% (273/851)
Receiving objects:  33% (281/851)
Receiving objects:  34% (290/851)
Receiving objects:  35% (298/851)
Receiving objects:  36% (307/851)
Receiving objects:  37% (315/851)
Receiving objects:  38% (324/851)
Receiving objects:  39% (332/851)
Receiving objects:  40% (341/851)
Receiving objects:  41% (349/851)
Receiving objects:  42% (358/851)
Receiving objects:  43% (366/851)
Receiving objects:  44% (375/851)
Receiving objects:  45% (383/851)
Receiving objects:  46% (392/851)
Receiving objects:  47% (400/851)
Receiving objects:  48% (409/851)
Receiving objects:  49% (417/851)
Receiving objects:  50% (426/851)
Receiving objects:  51% (435/851)
Receiving objects:  52% (443/851)
Receiving objects:  53% (452/851)
Receiving objects:  54% (460/851)
Receiving objects:  55% (469/851)
Receiving objects:  56% (477/851)
Receiving objects:  57% (486/851)
Receiving objects:  58% (494/851)
Receiving objects:  59% (503/851)
Receiving objects:  60% (511/851)
Receiving objects:  61% (520/851)
Receiving objects:  62% (528/851)
Receiving objects:  63% (537/851)
Receiving objects:  64% (545/851)
Receiving objects:  65% (554/851)
Receiving objects:  66% (562/851)
Receiving objects:  67% (571/851)
Receiving objects:  68% (579/851)
Receiving objects:  69% (588/851)
Receiving objects:  70% (596/851)
Receiving objects:  71% (605/851)
Receiving objects:  72% (613/851)
Receiving objects:  73% (622/851)
Receiving objects:  74% (630/851)
Receiving objects:  75% (639/851)
Receiving objects:  76% (647/851)
Receiving objects:  77% (656/851)
Receiving objects:  78% (664/851)
Receiving objects:  79% (673/851)
Receiving objects:  80% (681/851)
Receiving objects:  81% (690/851)
Receiving objects:  82% (698/851)
Receiving objects:  83% (707/851)
Receiving objects:  84% (715/851)
remote: Total 851 (delta 535), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (724/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  86% (732/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  87% (741/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  88% (749/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  89% (758/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  90% (766/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  91% (775/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  92% (783/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  93% (792/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  94% (800/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  95% (809/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  96% (817/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  97% (826/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  98% (834/851), 707.77 KiB | 1.34 MiB/s
Receiving objects:  99% (843/851), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (851/851), 707.77 KiB | 1.34 MiB/s
Receiving objects: 100% (851/851), 930.55 KiB | 1.68 MiB/s, done.
Resolving deltas:   0% (0/554)
Resolving deltas:   1% (6/554)
Resolving deltas:   2% (13/554)
Resolving deltas:   3% (17/554)
Resolving deltas:   4% (23/554)
Resolving deltas:   5% (28/554)
Resolving deltas:   6% (34/554)
Resolving deltas:   7% (39/554)
Resolving deltas:   8% (45/554)
Resolving deltas:   9% (50/554)
Resolving deltas:  10% (56/554)
Resolving deltas:  11% (61/554)
Resolving deltas:  12% (67/554)
Resolving deltas:  13% (73/554)
Resolving deltas:  14% (78/554)
Resolving deltas:  15% (84/554)
Resolving deltas:  16% (89/554)
Resolving deltas:  17% (95/554)
Resolving deltas:  18% (100/554)
Resolving deltas:  19% (106/554)
Resolving deltas:  20% (111/554)
Resolving deltas:  21% (117/554)
Resolving deltas:  22% (122/554)
Resolving deltas:  23% (128/554)
Resolving deltas:  24% (133/554)
Resolving deltas:  25% (139/554)
Resolving deltas:  26% (145/554)
Resolving deltas:  27% (150/554)
Resolving deltas:  28% (156/554)
Resolving deltas:  29% (161/554)
Resolving deltas:  30% (167/554)
Resolving deltas:  31% (172/554)
Resolving deltas:  32% (178/554)
Resolving deltas:  33% (183/554)
Resolving deltas:  34% (189/554)
Resolving deltas:  35% (194/554)
Resolving deltas:  36% (200/554)
Resolving deltas:  37% (205/554)
Resolving deltas:  38% (211/554)
Resolving deltas:  39% (217/554)
Resolving deltas:  40% (222/554)
Resolving deltas:  41% (228/554)
Resolving deltas:  42% (233/554)
Resolving deltas:  43% (239/554)
Resolving deltas:  44% (244/554)
Resolving deltas:  45% (250/554)
Resolving deltas:  46% (255/554)
Resolving deltas:  47% (261/554)
Resolving deltas:  48% (266/554)
Resolving deltas:  49% (272/554)
Resolving deltas:  50% (277/554)
Resolving deltas:  51% (283/554)
Resolving deltas:  52% (289/554)
Resolving deltas:  53% (294/554)
Resolving deltas:  54% (300/554)
Resolving deltas:  55% (305/554)
Resolving deltas:  56% (311/554)
Resolving deltas:  57% (316/554)
Resolving deltas:  58% (322/554)
Resolving deltas:  59% (327/554)
Resolving deltas:  60% (333/554)
Resolving deltas:  61% (338/554)
Resolving deltas:  62% (344/554)
Resolving deltas:  63% (350/554)
Resolving deltas:  64% (355/554)
Resolving deltas:  65% (361/554)
Resolving deltas:  66% (366/554)
Resolving deltas:  67% (372/554)
Resolving deltas:  68% (377/554)
Resolving deltas:  69% (383/554)
Resolving deltas:  70% (388/554)
Resolving deltas:  71% (394/554)
Resolving deltas:  72% (399/554)
Resolving deltas:  73% (405/554)
Resolving deltas:  74% (410/554)
Resolving deltas:  75% (416/554)
Resolving deltas:  76% (422/554)
Resolving deltas:  77% (427/554)
Resolving deltas:  78% (434/554)
Resolving deltas:  79% (438/554)
Resolving deltas:  80% (444/554)
Resolving deltas:  81% (449/554)
Resolving deltas:  82% (455/554)
Resolving deltas:  83% (460/554)
Resolving deltas:  84% (466/554)
Resolving deltas:  85% (471/554)
Resolving deltas:  86% (477/554)
Resolving deltas:  87% (482/554)
Resolving deltas:  88% (488/554)
Resolving deltas:  89% (494/554)
Resolving deltas:  90% (499/554)
Resolving deltas:  91% (505/554)
Resolving deltas:  92% (510/554)
Resolving deltas:  93% (516/554)
Resolving deltas:  94% (521/554)
Resolving deltas:  95% (527/554)
Resolving deltas:  96% (532/554)
Resolving deltas:  97% (538/554)
Resolving deltas:  98% (543/554)
Resolving deltas:  99% (549/554)
Resolving deltas: 100% (554/554)
Resolving deltas: 100% (554/554), done.
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
        "repo_root_resolved           = /tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../status/DEV000000002",
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
included: /tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
TASK [Deploy | Definir caminhos para sitef-02] *********************************
ok: [localhost]
TASK [Deploy | Definir diretórios principais do repositório] *******************
ok: [localhost]
TASK [Deploy | Definir candidatos de arquivo da máquina] ***********************
ok: [localhost]
TASK [Deploy | Verificar candidatos] *******************************************
ok: [localhost] => (item=/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 4367011, 'dev': 64781, 'nlink': 1, 'atime': 1765829563.5721304, 'mtime': 1765829563.5521302, 'ctime': 1765829563.5521302, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '2786559025', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
TASK [Deploy | Falhar se arquivo da máquina não existir] ***********************
skipping: [localhost]
TASK [Deploy | Carregar config da máquina] *************************************
ok: [localhost]
TASK [Deploy | Carregar config do pacote] **************************************
ok: [localhost]
TASK [Deploy | Registrar host] *************************************************
changed: [localhost]
TASK [Deploy | Montar env final] ***********************************************
ok: [localhost]
TASK [Deploy | Definir variáveis base] *****************************************
fatal: [localhost]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'deployment_ref_lower' is undefined. 'deployment_ref_lower' is undefined\n\nThe error appears to be in '/tmp/tmp.0Y4rLbQuLV/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml': line 95, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n# -----------------------------------------------------------------------------\n- name: \"Deploy | Definir variáveis base\"\n  ^ here\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=17   changed=2    unreachable=0    failed=1    skipped=1    rescued=0    ignored=0   
Command finished with status FAILURE
