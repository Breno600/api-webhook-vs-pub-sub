Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-02
export MACHINE=sitef-02
export ACTION=deploy
export GIT_TAG=DEV000002
export STRATEGY=deploy
== DEPLOY/ROLLBACK PIPELINE ==
BRANCH            : develop-testes
STRATEGY          : deploy
GIT_TAG           : DEV000002
MACHINES          : sitef-02
FILESTORE_ENV     : dev
FILESTORE_BASEDIR : dev/DEV000002
MACHINES_NORMALIZADAS: sitef-02 
Clonando repo em /tmp/tmp.AM8JXFfIwy...
Cloning into '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1225, done.
remote: Counting objects:   0% (1/1175)
remote: Counting objects:   1% (12/1175)
remote: Counting objects:   2% (24/1175)
remote: Counting objects:   3% (36/1175)
remote: Counting objects:   4% (47/1175)
remote: Counting objects:   5% (59/1175)
remote: Counting objects:   6% (71/1175)
remote: Counting objects:   7% (83/1175)
remote: Counting objects:   8% (94/1175)
remote: Counting objects:   9% (106/1175)
remote: Counting objects:  10% (118/1175)
remote: Counting objects:  11% (130/1175)
remote: Counting objects:  12% (141/1175)
remote: Counting objects:  13% (153/1175)
remote: Counting objects:  14% (165/1175)
remote: Counting objects:  15% (177/1175)
remote: Counting objects:  16% (188/1175)
remote: Counting objects:  17% (200/1175)
remote: Counting objects:  18% (212/1175)
remote: Counting objects:  19% (224/1175)
remote: Counting objects:  20% (235/1175)
remote: Counting objects:  21% (247/1175)
remote: Counting objects:  22% (259/1175)
remote: Counting objects:  23% (271/1175)
remote: Counting objects:  24% (282/1175)
remote: Counting objects:  25% (294/1175)
remote: Counting objects:  26% (306/1175)
remote: Counting objects:  27% (318/1175)
remote: Counting objects:  28% (329/1175)
remote: Counting objects:  29% (341/1175)
remote: Counting objects:  30% (353/1175)
remote: Counting objects:  31% (365/1175)
remote: Counting objects:  32% (376/1175)
remote: Counting objects:  33% (388/1175)
remote: Counting objects:  34% (400/1175)
remote: Counting objects:  35% (412/1175)
remote: Counting objects:  36% (423/1175)
remote: Counting objects:  37% (435/1175)
remote: Counting objects:  38% (447/1175)
remote: Counting objects:  39% (459/1175)
remote: Counting objects:  40% (470/1175)
remote: Counting objects:  41% (482/1175)
remote: Counting objects:  42% (494/1175)
remote: Counting objects:  43% (506/1175)
remote: Counting objects:  44% (517/1175)
remote: Counting objects:  45% (529/1175)
remote: Counting objects:  46% (541/1175)
remote: Counting objects:  47% (553/1175)
remote: Counting objects:  48% (564/1175)
remote: Counting objects:  49% (576/1175)
remote: Counting objects:  50% (588/1175)
remote: Counting objects:  51% (600/1175)
remote: Counting objects:  52% (611/1175)
remote: Counting objects:  53% (623/1175)
remote: Counting objects:  54% (635/1175)
remote: Counting objects:  55% (647/1175)
remote: Counting objects:  56% (658/1175)
remote: Counting objects:  57% (670/1175)
remote: Counting objects:  58% (682/1175)
remote: Counting objects:  59% (694/1175)
remote: Counting objects:  60% (705/1175)
remote: Counting objects:  61% (717/1175)
remote: Counting objects:  62% (729/1175)
remote: Counting objects:  63% (741/1175)
remote: Counting objects:  64% (752/1175)
remote: Counting objects:  65% (764/1175)
remote: Counting objects:  66% (776/1175)
remote: Counting objects:  67% (788/1175)
remote: Counting objects:  68% (799/1175)
remote: Counting objects:  69% (811/1175)
remote: Counting objects:  70% (823/1175)
remote: Counting objects:  71% (835/1175)
remote: Counting objects:  72% (846/1175)
remote: Counting objects:  73% (858/1175)
remote: Counting objects:  74% (870/1175)
remote: Counting objects:  75% (882/1175)
remote: Counting objects:  76% (893/1175)
remote: Counting objects:  77% (905/1175)
remote: Counting objects:  78% (917/1175)
remote: Counting objects:  79% (929/1175)
remote: Counting objects:  80% (940/1175)
remote: Counting objects:  81% (952/1175)
remote: Counting objects:  82% (964/1175)
remote: Counting objects:  83% (976/1175)
remote: Counting objects:  84% (987/1175)
remote: Counting objects:  85% (999/1175)
remote: Counting objects:  86% (1011/1175)
remote: Counting objects:  87% (1023/1175)
remote: Counting objects:  88% (1034/1175)
remote: Counting objects:  89% (1046/1175)
remote: Counting objects:  90% (1058/1175)
remote: Counting objects:  91% (1070/1175)
remote: Counting objects:  92% (1081/1175)
remote: Counting objects:  93% (1093/1175)
remote: Counting objects:  94% (1105/1175)
remote: Counting objects:  95% (1117/1175)
remote: Counting objects:  96% (1128/1175)
remote: Counting objects:  97% (1140/1175)
remote: Counting objects:  98% (1152/1175)
remote: Counting objects:  99% (1164/1175)
remote: Counting objects: 100% (1175/1175)
remote: Counting objects: 100% (1175/1175), done.
remote: Compressing objects:   0% (1/410)
remote: Compressing objects:   1% (5/410)
remote: Compressing objects:   2% (9/410)
remote: Compressing objects:   3% (13/410)
remote: Compressing objects:   4% (17/410)
remote: Compressing objects:   5% (21/410)
remote: Compressing objects:   6% (25/410)
remote: Compressing objects:   7% (29/410)
remote: Compressing objects:   8% (33/410)
remote: Compressing objects:   9% (37/410)
remote: Compressing objects:  10% (41/410)
remote: Compressing objects:  11% (46/410)
remote: Compressing objects:  12% (50/410)
remote: Compressing objects:  13% (54/410)
remote: Compressing objects:  14% (58/410)
remote: Compressing objects:  15% (62/410)
remote: Compressing objects:  16% (66/410)
remote: Compressing objects:  17% (70/410)
remote: Compressing objects:  18% (74/410)
remote: Compressing objects:  19% (78/410)
remote: Compressing objects:  20% (82/410)
remote: Compressing objects:  21% (87/410)
remote: Compressing objects:  22% (91/410)
remote: Compressing objects:  23% (95/410)
remote: Compressing objects:  24% (99/410)
remote: Compressing objects:  25% (103/410)
remote: Compressing objects:  26% (107/410)
remote: Compressing objects:  27% (111/410)
remote: Compressing objects:  28% (115/410)
remote: Compressing objects:  29% (119/410)
remote: Compressing objects:  30% (123/410)
remote: Compressing objects:  31% (128/410)
remote: Compressing objects:  32% (132/410)
remote: Compressing objects:  33% (136/410)
remote: Compressing objects:  34% (140/410)
remote: Compressing objects:  35% (144/410)
remote: Compressing objects:  36% (148/410)
remote: Compressing objects:  37% (152/410)
remote: Compressing objects:  38% (156/410)
remote: Compressing objects:  39% (160/410)
remote: Compressing objects:  40% (164/410)
remote: Compressing objects:  41% (169/410)
remote: Compressing objects:  42% (173/410)
remote: Compressing objects:  43% (177/410)
remote: Compressing objects:  44% (181/410)
remote: Compressing objects:  45% (185/410)
remote: Compressing objects:  46% (189/410)
remote: Compressing objects:  47% (193/410)
remote: Compressing objects:  48% (197/410)
remote: Compressing objects:  49% (201/410)
remote: Compressing objects:  50% (205/410)
remote: Compressing objects:  51% (210/410)
remote: Compressing objects:  52% (214/410)
remote: Compressing objects:  53% (218/410)
remote: Compressing objects:  54% (222/410)
remote: Compressing objects:  55% (226/410)
remote: Compressing objects:  56% (230/410)
remote: Compressing objects:  57% (234/410)
remote: Compressing objects:  58% (238/410)
remote: Compressing objects:  59% (242/410)
remote: Compressing objects:  60% (246/410)
remote: Compressing objects:  61% (251/410)
remote: Compressing objects:  62% (255/410)
remote: Compressing objects:  63% (259/410)
remote: Compressing objects:  64% (263/410)
remote: Compressing objects:  65% (267/410)
remote: Compressing objects:  66% (271/410)
remote: Compressing objects:  67% (275/410)
remote: Compressing objects:  68% (279/410)
remote: Compressing objects:  69% (283/410)
remote: Compressing objects:  70% (287/410)
remote: Compressing objects:  71% (292/410)
remote: Compressing objects:  72% (296/410)
remote: Compressing objects:  73% (300/410)
remote: Compressing objects:  74% (304/410)
remote: Compressing objects:  75% (308/410)
remote: Compressing objects:  76% (312/410)
remote: Compressing objects:  77% (316/410)
remote: Compressing objects:  78% (320/410)
remote: Compressing objects:  79% (324/410)
remote: Compressing objects:  80% (328/410)
remote: Compressing objects:  81% (333/410)
remote: Compressing objects:  82% (337/410)
remote: Compressing objects:  83% (341/410)
remote: Compressing objects:  84% (345/410)
remote: Compressing objects:  85% (349/410)
remote: Compressing objects:  86% (353/410)
remote: Compressing objects:  87% (357/410)
remote: Compressing objects:  88% (361/410)
remote: Compressing objects:  89% (365/410)
remote: Compressing objects:  90% (369/410)
remote: Compressing objects:  91% (374/410)
remote: Compressing objects:  92% (378/410)
remote: Compressing objects:  93% (382/410)
remote: Compressing objects:  94% (386/410)
remote: Compressing objects:  95% (390/410)
remote: Compressing objects:  96% (394/410)
remote: Compressing objects:  97% (398/410)
remote: Compressing objects:  98% (402/410)
remote: Compressing objects:  99% (406/410)
remote: Compressing objects: 100% (410/410)
remote: Compressing objects: 100% (410/410), done.
Receiving objects:   0% (1/1225)
Receiving objects:   1% (13/1225)
Receiving objects:   2% (25/1225)
Receiving objects:   3% (37/1225)
Receiving objects:   4% (49/1225)
Receiving objects:   5% (62/1225)
Receiving objects:   6% (74/1225)
Receiving objects:   7% (86/1225)
Receiving objects:   8% (98/1225)
Receiving objects:   9% (111/1225)
Receiving objects:  10% (123/1225)
Receiving objects:  11% (135/1225)
Receiving objects:  12% (147/1225)
Receiving objects:  13% (160/1225)
Receiving objects:  14% (172/1225)
Receiving objects:  15% (184/1225)
Receiving objects:  16% (196/1225)
Receiving objects:  17% (209/1225)
Receiving objects:  18% (221/1225)
Receiving objects:  19% (233/1225)
Receiving objects:  20% (245/1225)
Receiving objects:  21% (258/1225)
Receiving objects:  22% (270/1225)
Receiving objects:  23% (282/1225)
Receiving objects:  24% (294/1225)
Receiving objects:  25% (307/1225)
Receiving objects:  26% (319/1225)
Receiving objects:  27% (331/1225)
Receiving objects:  28% (343/1225)
Receiving objects:  29% (356/1225)
Receiving objects:  30% (368/1225)
Receiving objects:  31% (380/1225)
Receiving objects:  32% (392/1225)
Receiving objects:  33% (405/1225)
Receiving objects:  34% (417/1225)
Receiving objects:  35% (429/1225)
Receiving objects:  36% (441/1225)
Receiving objects:  37% (454/1225)
Receiving objects:  38% (466/1225)
Receiving objects:  39% (478/1225)
Receiving objects:  40% (490/1225)
Receiving objects:  41% (503/1225)
Receiving objects:  42% (515/1225)
Receiving objects:  43% (527/1225)
Receiving objects:  44% (539/1225)
Receiving objects:  45% (552/1225)
Receiving objects:  46% (564/1225)
Receiving objects:  47% (576/1225)
Receiving objects:  48% (588/1225)
Receiving objects:  49% (601/1225)
Receiving objects:  50% (613/1225)
Receiving objects:  51% (625/1225)
Receiving objects:  52% (637/1225)
Receiving objects:  53% (650/1225)
Receiving objects:  54% (662/1225)
Receiving objects:  55% (674/1225)
Receiving objects:  56% (686/1225)
Receiving objects:  57% (699/1225)
Receiving objects:  58% (711/1225)
Receiving objects:  59% (723/1225)
Receiving objects:  60% (735/1225)
Receiving objects:  61% (748/1225)
Receiving objects:  62% (760/1225)
Receiving objects:  63% (772/1225)
Receiving objects:  64% (784/1225)
Receiving objects:  65% (797/1225)
Receiving objects:  66% (809/1225)
Receiving objects:  67% (821/1225)
Receiving objects:  68% (833/1225)
Receiving objects:  69% (846/1225)
Receiving objects:  70% (858/1225)
Receiving objects:  71% (870/1225)
Receiving objects:  72% (882/1225)
Receiving objects:  73% (895/1225)
Receiving objects:  74% (907/1225)
Receiving objects:  75% (919/1225)
Receiving objects:  76% (931/1225)
Receiving objects:  77% (944/1225)
Receiving objects:  78% (956/1225)
Receiving objects:  79% (968/1225)
Receiving objects:  80% (980/1225)
Receiving objects:  81% (993/1225)
Receiving objects:  82% (1005/1225)
remote: Total 1225 (delta 880), reused 1037 (delta 750), pack-reused 50
Receiving objects:  83% (1017/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  84% (1029/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  85% (1042/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  86% (1054/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  87% (1066/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  88% (1078/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  89% (1091/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  90% (1103/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  91% (1115/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  92% (1127/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  93% (1140/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  94% (1152/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  95% (1164/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  96% (1176/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  97% (1189/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  98% (1201/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects:  99% (1213/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects: 100% (1225/1225), 899.71 KiB | 1.52 MiB/s
Receiving objects: 100% (1225/1225), 1007.29 KiB | 1.70 MiB/s, done.
Resolving deltas:   0% (0/899)
Resolving deltas:   1% (9/899)
Resolving deltas:   2% (18/899)
Resolving deltas:   3% (27/899)
Resolving deltas:   4% (36/899)
Resolving deltas:   5% (45/899)
Resolving deltas:   6% (54/899)
Resolving deltas:   7% (63/899)
Resolving deltas:   8% (72/899)
Resolving deltas:   9% (81/899)
Resolving deltas:  10% (90/899)
Resolving deltas:  11% (99/899)
Resolving deltas:  12% (108/899)
Resolving deltas:  13% (117/899)
Resolving deltas:  14% (126/899)
Resolving deltas:  15% (135/899)
Resolving deltas:  16% (144/899)
Resolving deltas:  17% (153/899)
Resolving deltas:  18% (162/899)
Resolving deltas:  19% (171/899)
Resolving deltas:  20% (180/899)
Resolving deltas:  21% (189/899)
Resolving deltas:  22% (198/899)
Resolving deltas:  23% (207/899)
Resolving deltas:  24% (216/899)
Resolving deltas:  25% (225/899)
Resolving deltas:  26% (234/899)
Resolving deltas:  27% (243/899)
Resolving deltas:  28% (252/899)
Resolving deltas:  29% (261/899)
Resolving deltas:  30% (270/899)
Resolving deltas:  31% (279/899)
Resolving deltas:  32% (288/899)
Resolving deltas:  33% (297/899)
Resolving deltas:  34% (306/899)
Resolving deltas:  35% (315/899)
Resolving deltas:  36% (324/899)
Resolving deltas:  37% (333/899)
Resolving deltas:  38% (342/899)
Resolving deltas:  39% (351/899)
Resolving deltas:  40% (360/899)
Resolving deltas:  41% (369/899)
Resolving deltas:  42% (378/899)
Resolving deltas:  43% (387/899)
Resolving deltas:  44% (396/899)
Resolving deltas:  45% (405/899)
Resolving deltas:  46% (414/899)
Resolving deltas:  47% (423/899)
Resolving deltas:  48% (432/899)
Resolving deltas:  49% (441/899)
Resolving deltas:  50% (450/899)
Resolving deltas:  51% (459/899)
Resolving deltas:  52% (468/899)
Resolving deltas:  53% (477/899)
Resolving deltas:  54% (486/899)
Resolving deltas:  55% (495/899)
Resolving deltas:  56% (504/899)
Resolving deltas:  57% (513/899)
Resolving deltas:  58% (522/899)
Resolving deltas:  59% (531/899)
Resolving deltas:  60% (540/899)
Resolving deltas:  61% (549/899)
Resolving deltas:  62% (558/899)
Resolving deltas:  63% (567/899)
Resolving deltas:  64% (576/899)
Resolving deltas:  65% (585/899)
Resolving deltas:  66% (594/899)
Resolving deltas:  67% (603/899)
Resolving deltas:  68% (612/899)
Resolving deltas:  69% (621/899)
Resolving deltas:  70% (630/899)
Resolving deltas:  71% (639/899)
Resolving deltas:  72% (648/899)
Resolving deltas:  73% (657/899)
Resolving deltas:  74% (666/899)
Resolving deltas:  75% (675/899)
Resolving deltas:  76% (684/899)
Resolving deltas:  77% (693/899)
Resolving deltas:  78% (702/899)
Resolving deltas:  79% (711/899)
Resolving deltas:  80% (720/899)
Resolving deltas:  81% (729/899)
Resolving deltas:  82% (738/899)
Resolving deltas:  83% (747/899)
Resolving deltas:  84% (756/899)
Resolving deltas:  85% (765/899)
Resolving deltas:  86% (774/899)
Resolving deltas:  87% (783/899)
Resolving deltas:  88% (792/899)
Resolving deltas:  89% (801/899)
Resolving deltas:  90% (810/899)
Resolving deltas:  91% (819/899)
Resolving deltas:  92% (828/899)
Resolving deltas:  93% (837/899)
Resolving deltas:  94% (846/899)
Resolving deltas:  95% (855/899)
Resolving deltas:  96% (864/899)
Resolving deltas:  97% (873/899)
Resolving deltas:  98% (882/899)
Resolving deltas:  99% (891/899)
Resolving deltas: 100% (899/899)
Resolving deltas: 100% (899/899), done.
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
        "deployment_ref_resolved      = DEV000002",
        "machine_name_resolved       = sitef-02",
        "deploy_action_resolved      = deploy",
        "repo_root_resolved          = /tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved         = /tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../status/DEV000002",
        "filestore_env_resolved      = dev",
        "filestore_base_dir_resolved = dev/DEV000002",
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
included: /tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost
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
ok: [localhost] => (item=/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Deploy | Selecionar machine_file existente] ******************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 4367006, 'dev': 64775, 'nlink': 1, 'atime': 1766753395.4770734, 'mtime': 1766753395.4600732, 'ctime': 1766753395.4600732, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '105561897', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
TASK [Deploy | Mostrar caminhos do log (para visualizar no Harness)] ***********
ok: [localhost] => {
    "msg": [
        "LOG REMOTO (init_deploy.log): /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log",
        "LOG FILESTORE (.log):         dev/DEV000002/dev000002-sitef-02-dev-deploy.log",
        "STATUS FILESTORE (.json):     dev/DEV000002/dev000002-sitef-02-dev-deploy.json"
    ]
}
TASK [Deploy | Mostrar últimas linhas do init_deploy.log no output da pipeline (sempre)] ***
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Deploy | DEBUG deploy tail (Harness)] ************************************
ok: [localhost] => {
    "deploy_tail.stdout_lines": [
        "===== tail -n 200 /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log =====",
        "This system has release set to 9 and it receives updates only for this release.",
        "",
        "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    ",
        "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.4 kB/s | 4.5 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.6 kB/s | 4.1 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.1 kB/s | 4.5 kB     00:01    ",
        "EPEL9_X86                                       1.4 kB/s | 2.3 kB     00:01    ",
        "Package python3.12-3.12.12-1.el9_7.x86_64 is already installed.",
        "Package python3.12-pip-23.2.1-5.el9.noarch is already installed.",
        "Dependencies resolved.",
        "Nothing to do.",
        "Complete!",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ mkdir -p /opt/SoftwareExpress/sitef-setup",
        "+ python3.12 -m venv /opt/SoftwareExpress/sitef-setup/venv",
        "+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate",
        "++ deactivate nondestructive",
        "++ '[' -n '' ']'",
        "++ '[' -n '' ']'",
        "++ hash -r",
        "++ '[' -n '' ']'",
        "++ unset VIRTUAL_ENV",
        "++ unset VIRTUAL_ENV_PROMPT",
        "++ '[' '!' nondestructive = nondestructive ']'",
        "++ case \"$(uname)\" in",
        "+++ uname",
        "++ export VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv",
        "++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv",
        "++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin",
        "++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin",
        "++ export PATH",
        "++ VIRTUAL_ENV_PROMPT='(venv) '",
        "++ export VIRTUAL_ENV_PROMPT",
        "++ '[' -n '' ']'",
        "++ '[' -z '' ']'",
        "++ _OLD_VIRTUAL_PS1=",
        "++ PS1='((venv) ) '",
        "++ export PS1",
        "++ hash -r",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ pip3.12 install --no-index --no-input pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl",
        "WARNING: Requirement 'pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl' looks like a filename, but the file does not exist",
        "Processing ./pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl",
        "ERROR: Could not install packages due to an OSError: [Errno 2] No such file or directory: '/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/pyyaml-6.0.3-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl'",
        "",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 06:46:00 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 06:46:00 PM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 06:50:51 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 06:50:51 PM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 07:00:31 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Wed Dec 24 07:00:31 PM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:45:28 AM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:45:28 AM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:53:26 AM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:53:26 AM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:58:35 AM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 11:58:35 AM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:08:25 PM UTC 2025",
        "+ echo 'Instalacao d
e dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:08:25 PM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:41:40 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:41:40 PM UTC 2025",
        "+ set -euo pipefail",
        "+ echo '== Início init_deploy.sh =='",
        "== Início init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:50:06 PM UTC 2025",
        "+ echo 'Instalacao de dependencias do sistema...'",
        "Instalacao de dependencias do sistema...",
        "+ echo 'Instalacao de dependencias do sistema finalizada.'",
        "Instalacao de dependencias do sistema finalizada.",
        "+ echo 'Instalacao de dependencias do python...'",
        "Instalacao de dependencias do python...",
        "+ echo 'Instalacao de dependencias do python finalizada.'",
        "Instalacao de dependencias do python finalizada.",
        "+ echo '== Fim init_deploy.sh =='",
        "== Fim init_deploy.sh ==",
        "+ date",
        "Fri Dec 26 12:50:06 PM UTC 2025"
    ]
}
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
included: /tmp/tmp.AM8JXFfIwy/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Definir stage_label] *******************************************
ok: [localhost]
TASK [Harness | Definir machine_stage_name] ************************************
ok: [localhost]
TASK [Harness | Resolver token (vars > env)] ***********************************
ok: [localhost]
TASK [Harness | Falhar se token vazio] *****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Montar tags base] **********************************************
ok: [localhost]
TASK [Harness | Adicionar extra_tags como tag=...] *****************************
skipping: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Identifiers (RAW)] *********************************************
ok: [localhost]
TASK [Harness | Identifiers (FINAL a partir do RAW)] ***************************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST)] ************************************
ok: [localhost]
TASK [Harness | Ler body ENV] **************************************************
ok: [localhost]
TASK [Harness | Marcar ENV duplicate?] *****************************************
ok: [localhost]
TASK [Harness | Falhar ENV se não OK] ******************************************
skipping: [localhost]
TASK [Harness | Garantir folder GIT_TAG (POST)] ********************************
ok: [localhost]
TASK [Harness | Ler body GIT_TAG] **********************************************
ok: [localhost]
TASK [Harness | Marcar GIT_TAG duplicate?] *************************************
ok: [localhost]
TASK [Harness | Falhar GIT_TAG se não OK] **************************************
skipping: [localhost]
TASK [Harness | Garantir folder LEAF (POST)] ***********************************
changed: [localhost]
TASK [Harness | Ler body LEAF] *************************************************
ok: [localhost]
TASK [Harness | Marcar LEAF duplicate?] ****************************************
ok: [localhost]
TASK [Harness | Falhar LEAF se não OK] *****************************************
skipping: [localhost]
TASK [Harness | Definir identifiers do STATUS] *********************************
ok: [localhost]
TASK [Harness | Limitar identifier STATUS a 128] *******************************
ok: [localhost]
TASK [Harness | CREATE status.json] ********************************************
changed: [localhost]
TASK [Harness | Ler body status CREATE] ****************************************
ok: [localhost]
TASK [Harness | UPDATE status.json (se já existe)] *****************************
skipping: [localhost]
TASK [Harness | Falhar status se create e update não deram certo] **************
skipping: [localhost]
TASK [Harness | Definir identifiers do LOG] ************************************
ok: [localhost]
TASK [Harness | Limitar identifier LOG a 128] **********************************
ok: [localhost]
TASK [Harness | Criar arquivo temporário do log no controller] *****************
changed: [localhost]
TASK [Harness | CREATE pipeline.log] *******************************************
changed: [localhost]
TASK [Harness | Ler body log CREATE] *******************************************
ok: [localhost]
TASK [Harness | UPDATE pipeline.log (se já existe)] ****************************
skipping: [localhost]
TASK [Harness | Falhar LOG se create e update não deram certo] *****************
skipping: [localhost]
TASK [Harness | Limpar arquivo temporário do log] ******************************
ok: [localhost]
TASK [Deploy | Falhar pipeline se init_deploy.sh retornou erro] ****************
skipping: [localhost]
TASK [Incluir rollback por máquina] ********************************************
skipping: [localhost]
PLAY RECAP *********************************************************************
localhost                  : ok=83   changed=13   unreachable=0    failed=0    skipped=14   rescued=0    ignored=0   
== Pipeline finalizada com sucesso ==
Command finished with status SUCCESS
