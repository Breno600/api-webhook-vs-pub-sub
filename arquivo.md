Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export MACHINES=sitef-01
export MACHINE=sitef-01
export ACTION=deploy
export GIT_TAG=DEV000000007
export STRATEGY=deploy
== DEPLOY PIPELINE ==
BRANCH   : develop
STRATEGY : deploy
GIT_TAG  : DEV000000007
MACHINES : sitef-01
MACHINES_NORMALIZADAS: sitef-01 
Clonando repo em /tmp/tmp.d86UGJXQ5L...
Cloning into '/tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 500, done.
remote: Counting objects:   0% (1/450)
remote: Counting objects:   1% (5/450)
remote: Counting objects:   2% (9/450)
remote: Counting objects:   3% (14/450)
remote: Counting objects:   4% (18/450)
remote: Counting objects:   5% (23/450)
remote: Counting objects:   6% (27/450)
remote: Counting objects:   7% (32/450)
remote: Counting objects:   8% (36/450)
remote: Counting objects:   9% (41/450)
remote: Counting objects:  10% (45/450)
remote: Counting objects:  11% (50/450)
remote: Counting objects:  12% (54/450)
remote: Counting objects:  13% (59/450)
remote: Counting objects:  14% (63/450)
remote: Counting objects:  15% (68/450)
remote: Counting objects:  16% (72/450)
remote: Counting objects:  17% (77/450)
remote: Counting objects:  18% (81/450)
remote: Counting objects:  19% (86/450)
remote: Counting objects:  20% (90/450)
remote: Counting objects:  21% (95/450)
remote: Counting objects:  22% (99/450)
remote: Counting objects:  23% (104/450)
remote: Counting objects:  24% (108/450)
remote: Counting objects:  25% (113/450)
remote: Counting objects:  26% (117/450)
remote: Counting objects:  27% (122/450)
remote: Counting objects:  28% (126/450)
remote: Counting objects:  29% (131/450)
remote: Counting objects:  30% (135/450)
remote: Counting objects:  31% (140/450)
remote: Counting objects:  32% (144/450)
remote: Counting objects:  33% (149/450)
remote: Counting objects:  34% (153/450)
remote: Counting objects:  35% (158/450)
remote: Counting objects:  36% (162/450)
remote: Counting objects:  37% (167/450)
remote: Counting objects:  38% (171/450)
remote: Counting objects:  39% (176/450)
remote: Counting objects:  40% (180/450)
remote: Counting objects:  41% (185/450)
remote: Counting objects:  42% (189/450)
remote: Counting objects:  43% (194/450)
remote: Counting objects:  44% (198/450)
remote: Counting objects:  45% (203/450)
remote: Counting objects:  46% (207/450)
remote: Counting objects:  47% (212/450)
remote: Counting objects:  48% (216/450)
remote: Counting objects:  49% (221/450)
remote: Counting objects:  50% (225/450)
remote: Counting objects:  51% (230/450)
remote: Counting objects:  52% (234/450)
remote: Counting objects:  53% (239/450)
remote: Counting objects:  54% (243/450)
remote: Counting objects:  55% (248/450)
remote: Counting objects:  56% (252/450)
remote: Counting objects:  57% (257/450)
remote: Counting objects:  58% (261/450)
remote: Counting objects:  59% (266/450)
remote: Counting objects:  60% (270/450)
remote: Counting objects:  61% (275/450)
remote: Counting objects:  62% (279/450)
remote: Counting objects:  63% (284/450)
remote: Counting objects:  64% (288/450)
remote: Counting objects:  65% (293/450)
remote: Counting objects:  66% (297/450)
remote: Counting objects:  67% (302/450)
remote: Counting objects:  68% (306/450)
remote: Counting objects:  69% (311/450)
remote: Counting objects:  70% (315/450)
remote: Counting objects:  71% (320/450)
remote: Counting objects:  72% (324/450)
remote: Counting objects:  73% (329/450)
remote: Counting objects:  74% (333/450)
remote: Counting objects:  75% (338/450)
remote: Counting objects:  76% (342/450)
remote: Counting objects:  77% (347/450)
remote: Counting objects:  78% (351/450)
remote: Counting objects:  79% (356/450)
remote: Counting objects:  80% (360/450)
remote: Counting objects:  81% (365/450)
remote: Counting objects:  82% (369/450)
remote: Counting objects:  83% (374/450)
remote: Counting objects:  84% (378/450)
remote: Counting objects:  85% (383/450)
remote: Counting objects:  86% (387/450)
remote: Counting objects:  87% (392/450)
remote: Counting objects:  88% (396/450)
remote: Counting objects:  89% (401/450)
remote: Counting objects:  90% (405/450)
remote: Counting objects:  91% (410/450)
remote: Counting objects:  92% (414/450)
remote: Counting objects:  93% (419/450)
remote: Counting objects:  94% (423/450)
remote: Counting objects:  95% (428/450)
remote: Counting objects:  96% (432/450)
remote: Counting objects:  97% (437/450)
remote: Counting objects:  98% (441/450)
remote: Counting objects:  99% (446/450)
remote: Counting objects: 100% (450/450)
remote: Counting objects: 100% (450/450), done.
remote: Compressing objects:   0% (1/437)
remote: Compressing objects:   1% (5/437)
remote: Compressing objects:   2% (9/437)
remote: Compressing objects:   3% (14/437)
remote: Compressing objects:   4% (18/437)
remote: Compressing objects:   5% (22/437)
remote: Compressing objects:   6% (27/437)
remote: Compressing objects:   7% (31/437)
remote: Compressing objects:   8% (35/437)
remote: Compressing objects:   9% (40/437)
remote: Compressing objects:  10% (44/437)
remote: Compressing objects:  11% (49/437)
remote: Compressing objects:  12% (53/437)
remote: Compressing objects:  13% (57/437)
remote: Compressing objects:  14% (62/437)
remote: Compressing objects:  15% (66/437)
remote: Compressing objects:  16% (70/437)
remote: Compressing objects:  17% (75/437)
remote: Compressing objects:  18% (79/437)
remote: Compressing objects:  19% (84/437)
remote: Compressing objects:  20% (88/437)
remote: Compressing objects:  21% (92/437)
remote: Compressing objects:  22% (97/437)
remote: Compressing objects:  23% (101/437)
remote: Compressing objects:  24% (105/437)
remote: Compressing objects:  25% (110/437)
remote: Compressing objects:  26% (114/437)
remote: Compressing objects:  27% (118/437)
remote: Compressing objects:  28% (123/437)
remote: Compressing objects:  29% (127/437)
remote: Compressing objects:  30% (132/437)
remote: Compressing objects:  31% (136/437)
remote: Compressing objects:  32% (140/437)
remote: Compressing objects:  33% (145/437)
remote: Compressing objects:  34% (149/437)
remote: Compressing objects:  35% (153/437)
remote: Compressing objects:  36% (158/437)
remote: Compressing objects:  37% (162/437)
remote: Compressing objects:  38% (167/437)
remote: Compressing objects:  39% (171/437)
remote: Compressing objects:  40% (175/437)
remote: Compressing objects:  41% (180/437)
remote: Compressing objects:  42% (184/437)
remote: Compressing objects:  43% (188/437)
remote: Compressing objects:  44% (193/437)
remote: Compressing objects:  45% (197/437)
remote: Compressing objects:  46% (202/437)
remote: Compressing objects:  47% (206/437)
remote: Compressing objects:  48% (210/437)
remote: Compressing objects:  49% (215/437)
remote: Compressing objects:  50% (219/437)
remote: Compressing objects:  51% (223/437)
remote: Compressing objects:  52% (228/437)
remote: Compressing objects:  53% (232/437)
remote: Compressing objects:  54% (236/437)
remote: Compressing objects:  55% (241/437)
remote: Compressing objects:  56% (245/437)
remote: Compressing objects:  57% (250/437)
remote: Compressing objects:  58% (254/437)
remote: Compressing objects:  59% (258/437)
remote: Compressing objects:  60% (263/437)
remote: Compressing objects:  61% (267/437)
remote: Compressing objects:  62% (271/437)
remote: Compressing objects:  63% (276/437)
remote: Compressing objects:  64% (280/437)
remote: Compressing objects:  65% (285/437)
remote: Compressing objects:  66% (289/437)
remote: Compressing objects:  67% (293/437)
remote: Compressing objects:  68% (298/437)
remote: Compressing objects:  69% (302/437)
remote: Compressing objects:  70% (306/437)
remote: Compressing objects:  71% (311/437)
remote: Compressing objects:  72% (315/437)
remote: Compressing objects:  73% (320/437)
remote: Compressing objects:  74% (324/437)
remote: Compressing objects:  75% (328/437)
remote: Compressing objects:  76% (333/437)
remote: Compressing objects:  77% (337/437)
remote: Compressing objects:  78% (341/437)
remote: Compressing objects:  79% (346/437)
remote: Compressing objects:  80% (350/437)
remote: Compressing objects:  81% (354/437)
remote: Compressing objects:  82% (359/437)
remote: Compressing objects:  83% (363/437)
remote: Compressing objects:  84% (368/437)
remote: Compressing objects:  85% (372/437)
remote: Compressing objects:  86% (376/437)
remote: Compressing objects:  87% (381/437)
remote: Compressing objects:  88% (385/437)
remote: Compressing objects:  89% (389/437)
remote: Compressing objects:  90% (394/437)
remote: Compressing objects:  91% (398/437)
remote: Compressing objects:  92% (403/437)
remote: Compressing objects:  93% (407/437)
remote: Compressing objects:  94% (411/437)
remote: Compressing objects:  95% (416/437)
remote: Compressing objects:  96% (420/437)
remote: Compressing objects:  97% (424/437)
remote: Compressing objects:  98% (429/437)
remote: Compressing objects:  99% (433/437)
remote: Compressing objects: 100% (437/437)
remote: Compressing objects: 100% (437/437), done.
remote: Total 500 (delta 256), reused 0 (delta 0), pack-reused 50
Receiving objects:   0% (1/500)
Receiving objects:   1% (5/500)
Receiving objects:   2% (10/500)
Receiving objects:   3% (15/500)
Receiving objects:   4% (20/500)
Receiving objects:   5% (25/500)
Receiving objects:   6% (30/500)
Receiving objects:   7% (35/500)
Receiving objects:   8% (40/500)
Receiving objects:   9% (45/500)
Receiving objects:  10% (50/500)
Receiving objects:  11% (55/500)
Receiving objects:  12% (60/500)
Receiving objects:  13% (65/500)
Receiving objects:  14% (70/500)
Receiving objects:  15% (75/500)
Receiving objects:  16% (80/500)
Receiving objects:  17% (85/500)
Receiving objects:  18% (90/500)
Receiving objects:  19% (95/500)
Receiving objects:  20% (100/500)
Receiving objects:  21% (105/500)
Receiving objects:  22% (110/500)
Receiving objects:  23% (115/500)
Receiving objects:  24% (120/500)
Receiving objects:  25% (125/500)
Receiving objects:  26% (130/500)
Receiving objects:  27% (135/500)
Receiving objects:  28% (140/500)
Receiving objects:  29% (145/500)
Receiving objects:  30% (150/500)
Receiving objects:  31% (155/500)
Receiving objects:  32% (160/500)
Receiving objects:  33% (165/500)
Receiving objects:  34% (170/500)
Receiving objects:  35% (175/500)
Receiving objects:  36% (180/500)
Receiving objects:  37% (185/500)
Receiving objects:  38% (190/500)
Receiving objects:  39% (195/500)
Receiving objects:  40% (200/500)
Receiving objects:  41% (205/500)
Receiving objects:  42% (210/500)
Receiving objects:  43% (215/500)
Receiving objects:  44% (220/500)
Receiving objects:  45% (225/500)
Receiving objects:  46% (230/500)
Receiving objects:  47% (235/500)
Receiving objects:  48% (240/500)
Receiving objects:  49% (245/500)
Receiving objects:  50% (250/500)
Receiving objects:  51% (255/500)
Receiving objects:  52% (260/500)
Receiving objects:  53% (265/500)
Receiving objects:  54% (270/500)
Receiving objects:  55% (275/500)
Receiving objects:  56% (280/500)
Receiving objects:  57% (285/500)
Receiving objects:  58% (290/500)
Receiving objects:  59% (295/500)
Receiving objects:  60% (300/500)
Receiving objects:  61% (305/500)
Receiving objects:  62% (310/500)
Receiving objects:  63% (315/500)
Receiving objects:  64% (320/500)
Receiving objects:  65% (325/500)
Receiving objects:  66% (330/500)
Receiving objects:  67% (335/500)
Receiving objects:  68% (340/500)
Receiving objects:  69% (345/500)
Receiving objects:  70% (350/500)
Receiving objects:  71% (355/500)
Receiving objects:  72% (360/500)
Receiving objects:  73% (365/500)
Receiving objects:  74% (370/500)
Receiving objects:  75% (375/500)
Receiving objects:  76% (380/500)
Receiving objects:  77% (385/500)
Receiving objects:  78% (390/500)
Receiving objects:  79% (395/500)
Receiving objects:  80% (400/500)
Receiving objects:  81% (405/500)
Receiving objects:  82% (410/500)
Receiving objects:  83% (415/500)
Receiving objects:  84% (420/500)
Receiving objects:  85% (425/500)
Receiving objects:  86% (430/500)
Receiving objects:  87% (435/500)
Receiving objects:  88% (440/500)
Receiving objects:  89% (445/500)
Receiving objects:  90% (450/500)
Receiving objects:  91% (455/500)
Receiving objects:  92% (460/500)
Receiving objects:  93% (465/500)
Receiving objects:  94% (470/500)
Receiving objects:  95% (475/500)
Receiving objects:  96% (480/500)
Receiving objects:  97% (485/500)
Receiving objects:  98% (490/500)
Receiving objects:  99% (495/500)
Receiving objects: 100% (500/500)
Receiving objects: 100% (500/500), 80.57 KiB | 1.05 MiB/s, done.
Resolving deltas:   0% (0/275)
Resolving deltas:   1% (3/275)
Resolving deltas:   2% (6/275)
Resolving deltas:   3% (9/275)
Resolving deltas:   4% (11/275)
Resolving deltas:   5% (14/275)
Resolving deltas:   6% (17/275)
Resolving deltas:   7% (20/275)
Resolving deltas:   8% (22/275)
Resolving deltas:   9% (25/275)
Resolving deltas:  10% (28/275)
Resolving deltas:  11% (31/275)
Resolving deltas:  12% (33/275)
Resolving deltas:  13% (36/275)
Resolving deltas:  14% (39/275)
Resolving deltas:  15% (42/275)
Resolving deltas:  16% (44/275)
Resolving deltas:  17% (47/275)
Resolving deltas:  18% (50/275)
Resolving deltas:  19% (53/275)
Resolving deltas:  20% (55/275)
Resolving deltas:  21% (58/275)
Resolving deltas:  22% (61/275)
Resolving deltas:  23% (64/275)
Resolving deltas:  24% (66/275)
Resolving deltas:  25% (69/275)
Resolving deltas:  26% (72/275)
Resolving deltas:  27% (75/275)
Resolving deltas:  28% (77/275)
Resolving deltas:  29% (80/275)
Resolving deltas:  30% (83/275)
Resolving deltas:  31% (86/275)
Resolving deltas:  32% (88/275)
Resolving deltas:  33% (91/275)
Resolving deltas:  34% (94/275)
Resolving deltas:  35% (97/275)
Resolving deltas:  36% (99/275)
Resolving deltas:  37% (102/275)
Resolving deltas:  38% (105/275)
Resolving deltas:  39% (108/275)
Resolving deltas:  40% (110/275)
Resolving deltas:  41% (113/275)
Resolving deltas:  42% (117/275)
Resolving deltas:  43% (119/275)
Resolving deltas:  44% (121/275)
Resolving deltas:  45% (124/275)
Resolving deltas:  46% (127/275)
Resolving deltas:  47% (130/275)
Resolving deltas:  48% (132/275)
Resolving deltas:  49% (135/275)
Resolving deltas:  50% (138/275)
Resolving deltas:  51% (141/275)
Resolving deltas:  52% (143/275)
Resolving deltas:  53% (146/275)
Resolving deltas:  54% (149/275)
Resolving deltas:  55% (152/275)
Resolving deltas:  56% (154/275)
Resolving deltas:  57% (157/275)
Resolving deltas:  58% (160/275)
Resolving deltas:  59% (163/275)
Resolving deltas:  60% (165/275)
Resolving deltas:  61% (168/275)
Resolving deltas:  62% (171/275)
Resolving deltas:  63% (174/275)
Resolving deltas:  64% (176/275)
Resolving deltas:  65% (179/275)
Resolving deltas:  66% (183/275)
Resolving deltas:  67% (185/275)
Resolving deltas:  68% (187/275)
Resolving deltas:  69% (190/275)
Resolving deltas:  70% (193/275)
Resolving deltas:  71% (196/275)
Resolving deltas:  72% (198/275)
Resolving deltas:  73% (201/275)
Resolving deltas:  74% (204/275)
Resolving deltas:  75% (207/275)
Resolving deltas:  76% (209/275)
Resolving deltas:  77% (212/275)
Resolving deltas:  78% (215/275)
Resolving deltas:  79% (218/275)
Resolving deltas:  80% (220/275)
Resolving deltas:  81% (224/275)
Resolving deltas:  82% (226/275)
Resolving deltas:  83% (229/275)
Resolving deltas:  84% (231/275)
Resolving deltas:  85% (234/275)
Resolving deltas:  86% (237/275)
Resolving deltas:  87% (240/275)
Resolving deltas:  88% (242/275)
Resolving deltas:  89% (245/275)
Resolving deltas:  90% (248/275)
Resolving deltas:  91% (251/275)
Resolving deltas:  92% (253/275)
Resolving deltas:  93% (256/275)
Resolving deltas:  94% (259/275)
Resolving deltas:  95% (262/275)
Resolving deltas:  96% (264/275)
Resolving deltas:  97% (267/275)
Resolving deltas:  98% (270/275)
Resolving deltas:  99% (273/275)
Resolving deltas: 100% (275/275)
Resolving deltas: 100% (275/275), done.
====================================================
== DEPLOY -> sitef-01
====================================================
PLAY [Deploy a partir do status do predeploy] **********************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Resolver defaults de pacote (sem recursão)] ******************************
ok: [localhost]
TASK [Mostrar variáveis de entrada] ********************************************
ok: [localhost] => {
    "msg": [
        "deployment_ref      = DEV000000007",
        "repo_root           = /tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/ansible/..",
        "status_dir          = /tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/ansible/../status/DEV000000007",
        "package_name_eff    = sitef-core",
        "package_version_eff = 0.0.2-0"
    ]
}
TASK [Garantir que o diretório de status exista] *******************************
ok: [localhost]
TASK [Falhar se status_dir não existir] ****************************************
skipping: [localhost]
TASK [Listar arquivos de predeploy da TAG] *************************************
ok: [localhost]
TASK [Falhar se não houver predeploy-*.json] ***********************************
skipping: [localhost]
TASK [Carregar JSONs de predeploy] *********************************************
ok: [localhost] => (item={'path': '/tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 6295489, 'dev': 64775, 'nlink': 1, 'atime': 1765220878.9081852, 'mtime': 1765220878.8951852, 'ctime': 1765220878.8951852, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False})
TASK [Montar lista de predeploys parseados] ************************************
ok: [localhost] => (item={'content': 'eyJtYWNoaW5lIjogInNpdGVmLTAxIiwgImhvc3QiOiAiMTAwLjk5LjQxLjU4IiwgInBhY2thZ2UiOiAic2l0ZWYtY29yZS0wLjAuMi0wIiwgInJvbGxiYWNrIjogIiIsICJzdGF0dXMiOiAic3VjY2VzcyIsICJkZXBsb3ltZW50X3JlZiI6ICJERVYwMDAwMDAwMDciLCAidGltZXN0YW1wIjogIjIwMjUtMTItMDhUMTg6MTU6NTJaIiwgInN0ZG91dCI6ICIrIGVjaG8gJz09IEluXHUwMGVkY2lvIGluaXRfcGFyYWxsZWwuc2ggPT0nXG49PSBJblx1MDBlZGNpbyBpbml0X3BhcmFsbGVsLnNoID09XG4rIGRhdGVcbk1vbiBEZWMgIDggMDY6MTY6MTMgUE0gVVRDIDIwMjVcbisgc2V0IC14XG4rIGVjaG8gJ0luc3RhbGFjYW8gZGUgZGVwZW5kZW5jaWFzIGRvIHNpc3RlbWEuLi4nXG5JbnN0YWxhY2FvIGRlIGRlcGVuZGVuY2lhcyBkbyBzaXN0ZW1hLi4uXG4rIGRuZiBpbnN0YWxsIC15IC0tbm9kb2NzIHB5dGhvbjMgcHl0aG9uMy1waXBcblVwZGF0aW5nIFN1YnNjcmlwdGlvbiBNYW5hZ2VtZW50IHJlcG9zaXRvcmllcy5cblxuVGhpcyBzeXN0ZW0gaGFzIHJlbGVhc2Ugc2V0IHRvIDkgYW5kIGl0IHJlY2VpdmVzIHVwZGF0ZXMgb25seSBmb3IgdGhpcyByZWxlYXNlLlxuXG5SZWQgSGF0IENvZGVSZWFkeSBMaW51eCBCdWlsZGVyIGZvciBSSEVMIDkgeDg2XyAyLjgga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQXBwU3RyZSA0LjEga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IFNhdGVsbGl0ZSBDbGllbnQgNiBmb3IgUkhFTCA5IHg4Nl82NCAoUiAzLjQga0IvcyB8IDMuOCBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQmFzZU9TICAzLjcga0IvcyB8IDQuMSBrQiAgICAgMDA6MDEgICAgXG5FUEVMOV9YODYgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAxLjYga0IvcyB8IDIuMyBrQiAgICAgMDA6MDEgICAgXG5QYWNrYWdlIHB5dGhvbjMtMy45LjIzLTIuZWw5Lng4Nl82NCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cblBhY2thZ2UgcHl0aG9uMy1waXAtMjEuMy4xLTEuZWw5Lm5vYXJjaCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cbkRlcGVuZGVuY2llcyByZXNvbHZlZC5cbk5vdGhpbmcgdG8gZG8uXG5Db21wbGV0ZSFcbisgcHl0aG9uMyAtbSB2ZW52IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisgc291cmNlIC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnYvYmluL2FjdGl2YXRlXG4rKyBkZWFjdGl2YXRlIG5vbmRlc3RydWN0aXZlXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKysgaGFzaCAtclxuKysgJ1snIC1uICcnICddJ1xuKysgdW5zZXQgVklSVFVBTF9FTlZcbisrICdbJyAnIScgbm9uZGVzdHJ1Y3RpdmUgPSBub25kZXN0cnVjdGl2ZSAnXSdcbisrIFZJUlRVQUxfRU5WPS9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisrIGV4cG9ydCBWSVJUVUFMX0VOVlxuKysgX09MRF9WSVJUVUFMX1BBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIFBBVEg9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvdmVudi9iaW46L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIGV4cG9ydCBQQVRIXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLXogJycgJ10nXG4rKyBfT0xEX1ZJUlRVQUxfUFMxPVxuKysgUFMxPScodmVudikgJ1xuKysgZXhwb3J0IFBTMVxuKysgJ1snIC1uIC9iaW4vYmFzaCAtbyAtbiAnJyAnXSdcbisrIGhhc2ggLXJcbisgZWNobyAnSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uJ1xuSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uXG4rIHB5dGhvbjMgLXUgc2NyaXB0X3ByZXBhcmUucHlcbkV4ZWN1dGFuZG8gYmFja3VwLi4uXG5GYXplbmRvIGxldmFudGFtZW50byBkZSBhcnF1aXZvcy9wYXN0YXNcblBhdGggZW5jb250cmFkbzogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvY29uZmlnXG5QcmVwYXJhY2FvIGRvIGJhY2t1cDogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvYmFja3Vwcy9iYWNrdXAtYmVmb3JlLWRlZmF1bHQudGFyLmd6XG5BcGFnYW5kbyBiYWNrdXA6IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL2JhY2t1cHMvYmFja3VwLWJlZm9yZS1kZWZhdWx0LnRhci5nelxuTWV0YWRhZG9zOiB7J3BhY2thZ2UnOiB7J2JhY2t1cCc6IHsnYmVmb3JlX2luc3RhbGxfdmVyc2lvbic6ICdkZWZhdWx0JywgJ2V4ZWN1dGVkX2F0JzogJzIwMjUtMTItMDggMTg6MTY6MjQnfX19XG5CYWNrdXAgZmluYWxpemFkby5cbisgZWNobyAnc2NyaXB0X3ByZXBhcmUucHkgZmluYWxpemFkbyBjb20gcmM9MCdcbnNjcmlwdF9wcmVwYXJlLnB5IGZpbmFsaXphZG8gY29tIHJjPTBcbisgZGVhY3RpdmF0ZVxuKyAnWycgLW4gL3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW4gJ10nXG4rIFBBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisgZXhwb3J0IFBBVEhcbisgdW5zZXQgX09MRF9WSVJUVUFMX1BBVEhcbisgJ1snIC1uICcnICddJ1xuKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKyBoYXNoIC1yXG4rICdbJyAtbiAnJyAnXSdcbisgdW5zZXQgVklSVFVBTF9FTlZcbisgJ1snICchJyAnJyA9IG5vbmRlc3RydWN0aXZlICddJ1xuKyB1bnNldCAtZiBkZWFjdGl2YXRlXG4rIHNldCAreFxuPT0gRmltIGluaXRfcGFyYWxsZWwuc2ggPT1cbk1vbiBEZWMgIDggMDY6MTY6MjQgUE0gVVRDIDIwMjUiLCAic3RkZXJyIjogIisgc2V0IC1ldW8gcGlwZWZhaWxcbisrKyBkaXJuYW1lIC4vaW5pdF9wYXJhbGxlbC5zaFxuKysgY2QgLlxuKysgcHdkXG4rIFNDUklQVF9ESVI9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjJcbisgTE9HX0ZJTEU9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjIvcGFyYWxsZWwudHh0XG4rIGV4ZWNcbisrIHRlZSAtYSAvb3B0L1NvZnR3YXJlRXhwcmVzcy9zaXRlZi9zY3JpcHRzL2RlcGxveS1zaXRlZi0wLjAuMi9wYXJhbGxlbC50eHQifQ==', 'source': '/tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'encoding': 'base64', 'invocation': {'module_args': {'src': '/tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json'}}, 'failed': False, 'changed': False, 'item': {'path': '/tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 6295489, 'dev': 64775, 'nlink': 1, 'atime': 1765220878.9081852, 'mtime': 1765220878.8951852, 'ctime': 1765220878.8951852, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False}, 'ansible_loop_var': 'item'})
TASK [Inicializar lista de máquinas para deploy] *******************************
ok: [localhost]
TASK [Montar lista de máquinas com predeploy success (machine ou machine_name)] ***
ok: [localhost] => (item={'machine': 'sitef-01', 'host': '100.99.41.58', 'package': 'sitef-core-0.0.2-0', 'rollback': '', 'status': 'success', 'deployment_ref': 'DEV000000007', 'timestamp': '2025-12-08T18:15:52Z', 'stdout': "+ echo '== Início init_parallel.sh =='\n== Início init_parallel.sh ==\n+ date\nMon Dec  8 06:16:13 PM UTC 2025\n+ set -x\n+ echo 'Instalacao de dependencias do sistema...'\nInstalacao de dependencias do sistema...\n+ dnf install -y --nodocs python3 python3-pip\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 2.8 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 4.1 kB/s | 4.5 kB     00:01    \nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 3.4 kB/s | 3.8 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  3.7 kB/s | 4.1 kB     00:01    \nEPEL9_X86                                       1.6 kB/s | 2.3 kB     00:01    \nPackage python3-3.9.23-2.el9.x86_64 is already installed.\nPackage python3-pip-21.3.1-1.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\n+ python3 -m venv /opt/SoftwareExpress/sitef-setup/venv\n+ source /opt/SoftwareExpress/sitef-setup/venv/bin/activate\n++ deactivate nondestructive\n++ '[' -n '' ']'\n++ '[' -n '' ']'\n++ '[' -n /bin/bash -o -n '' ']'\n++ hash -r\n++ '[' -n '' ']'\n++ unset VIRTUAL_ENV\n++ '[' '!' nondestructive = nondestructive ']'\n++ VIRTUAL_ENV=/opt/SoftwareExpress/sitef-setup/venv\n++ export VIRTUAL_ENV\n++ _OLD_VIRTUAL_PATH=/sbin:/bin:/usr/sbin:/usr/bin\n++ PATH=/opt/SoftwareExpress/sitef-setup/venv/bin:/sbin:/bin:/usr/sbin:/usr/bin\n++ export PATH\n++ '[' -n '' ']'\n++ '[' -z '' ']'\n++ _OLD_VIRTUAL_PS1=\n++ PS1='(venv) '\n++ export PS1\n++ '[' -n /bin/bash -o -n '' ']'\n++ hash -r\n+ echo 'Instalacao de dependencias do python...'\nInstalacao de dependencias do python...\n+ python3 -u script_prepare.py\nExecutando backup...\nFazendo levantamento de arquivos/pastas\nPath encontrado: /opt/SoftwareExpress/sitef/config\nPreparacao do backup: /opt/SoftwareExpress/sitef-setup/backups/backup-before-default.tar.gz\nApagando backup: /opt/SoftwareExpress/sitef-setup/backups/backup-before-default.tar.gz\nMetadados: {'package': {'backup': {'before_install_version': 'default', 'executed_at': '2025-12-08 18:16:24'}}}\nBackup finalizado.\n+ echo 'script_prepare.py finalizado com rc=0'\nscript_prepare.py finalizado com rc=0\n+ deactivate\n+ '[' -n /sbin:/bin:/usr/sbin:/usr/bin ']'\n+ PATH=/sbin:/bin:/usr/sbin:/usr/bin\n+ export PATH\n+ unset _OLD_VIRTUAL_PATH\n+ '[' -n '' ']'\n+ '[' -n /bin/bash -o -n '' ']'\n+ hash -r\n+ '[' -n '' ']'\n+ unset VIRTUAL_ENV\n+ '[' '!' '' = nondestructive ']'\n+ unset -f deactivate\n+ set +x\n== Fim init_parallel.sh ==\nMon Dec  8 06:16:24 PM UTC 2025", 'stderr': '+ set -euo pipefail\n+++ dirname ./init_parallel.sh\n++ cd .\n++ pwd\n+ SCRIPT_DIR=/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2\n+ LOG_FILE=/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/parallel.txt\n+ exec\n++ tee -a /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/parallel.txt'})
TASK [Falhar se nenhuma máquina estiver apta para deploy] **********************
skipping: [localhost]
TASK [Exibir máquinas aptas para deploy] ***************************************
ok: [localhost] => {
    "machines_to_deploy": [
        "sitef-01"
    ]
}
TASK [Executar deploy por máquina] *********************************************
included: /tmp/tmp.d86UGJXQ5L/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost => (item=sitef-01)
TASK [Normalizar variável de máquina] ******************************************
ok: [localhost]
TASK [Falhar se machine_name não foi informado] ********************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Definir caminhos para sitef-01] ******************************************
ok: [localhost]
TASK [Validar arquivo da máquina sitef-01] *************************************
ok: [localhost]
TASK [Falhar se arquivo da máquina não existir] ********************************
skipping: [localhost]
TASK [Carregar config da máquina sitef-01] *************************************
ok: [localhost]
TASK [Validar package definido para sitef-01] **********************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Carregar config do pacote sitef-core-0.0.2-0] ****************************
ok: [localhost]
TASK [Validar script definido no pacote sitef-core-0.0.2-0] ********************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Definir usuário alvo padrão para sitef-01] *******************************
ok: [localhost]
TASK [Definir ssh_common_args padrão da máquina (se existir)] ******************
ok: [localhost]
TASK [Aplicar ProxyJump via bastion (quando necessário)] ***********************
skipping: [localhost]
TASK [Registrar host dinâmico para sitef-01] ***********************************
changed: [localhost]
TASK [Criar status inicial (queued) para sitef-01] *****************************
changed: [localhost]
TASK [Verificar se status de predeploy existe] *********************************
ok: [localhost]
TASK [Avisar se predeploy não foi encontrado (não bloqueante)] *****************
skipping: [localhost]
TASK [Garantir diretórios do deploy no host 100.99.41.58] **********************
ok: [localhost -> sitef-01(100.99.41.58)] => (item=/opt/SoftwareExpress/sitef)
ok: [localhost -> sitef-01(100.99.41.58)] => (item=/opt/SoftwareExpress/sitef/package/linux)
ok: [localhost -> sitef-01(100.99.41.58)] => (item=/opt/SoftwareExpress/sitef/scripts)
ok: [localhost -> sitef-01(100.99.41.58)] => (item=/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2)
TASK [Executar script de deploy (init.sh) em sitef-01] *************************
fatal: [localhost -> sitef-01(100.99.41.58)]: FAILED! => {"changed": true, "cmd": "set -e\ncd \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2\"\n./init.sh\n", "delta": "0:00:11.874909", "end": "2025-12-08 19:08:21.979141", "msg": "non-zero return code", "rc": 1, "start": "2025-12-08 19:08:10.104232", "stderr": "Traceback (most recent call last):\n  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/script_deploy.py\", line 14, in &lt;module>\n    if not sitef_packages.install():\n  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/sitef_packages.py\", line 19, in install\n    for name in os.listdir(self._package_paths):\nFileNotFoundError: [Errno 2] No such file or directory: 'packages/linux'", "stderr_lines": ["Traceback (most recent call last):", "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/script_deploy.py\", line 14, in &lt;module>", "    if not sitef_packages.install():", "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/sitef_packages.py\", line 19, in install", "    for name in os.listdir(self._package_paths):", "FileNotFoundError: [Errno 2] No such file or directory: 'packages/linux'"], "stdout": "Instalacao de dependencias do sistema...\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 3.1 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 3.9 kB/s | 4.5 kB     00:01    \nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 3.0 kB/s | 3.8 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    \nEPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    \nPackage python3-3.9.23-2.el9.x86_64 is already installed.\nPackage python3-pip-21.3.1-1.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\nInstalacao de dependencias finalizada.\nInstalacao de dependencias do python...\nInstalacao de dependencias finalizada.\nVerificando presenca do servico sitef...\nScript finalizou com erro.", "stdout_lines": ["Instalacao de dependencias do sistema...", "Updating Subscription Management repositories.", "", "This system has release set to 9 and it receives updates only for this release.", "", "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.1 kB/s | 4.5 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.9 kB/s | 4.5 kB     00:01    ", "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 3.0 kB/s | 3.8 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    ", "EPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    ", "Package python3-3.9.23-2.el9.x86_64 is already installed.", "Package python3-pip-21.3.1-1.el9.noarch is already installed.", "Dependencies resolved.", "Nothing to do.", "Complete!", "Instalacao de dependencias finalizada.", "Instalacao de dependencias do python...", "Instalacao de dependencias finalizada.", "Verificando presenca do servico sitef...", "Script finalizou com erro."]}
...ignoring
TASK [DEBUG - stdout do init.sh] ***********************************************
ok: [localhost] => {
    "deploy_result.stdout_lines": [
        "Instalacao de dependencias do sistema...",
        "Updating Subscription Management repositories.",
        "",
        "This system has release set to 9 and it receives updates only for this release.",
        "",
        "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 3.1 kB/s | 4.5 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.9 kB/s | 4.5 kB     00:01    ",
        "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 3.0 kB/s | 3.8 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  2.5 kB/s | 4.1 kB     00:01    ",
        "EPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    ",
        "Package python3-3.9.23-2.el9.x86_64 is already installed.",
        "Package python3-pip-21.3.1-1.el9.noarch is already installed.",
        "Dependencies resolved.",
        "Nothing to do.",
        "Complete!",
        "Instalacao de dependencias finalizada.",
        "Instalacao de dependencias do python...",
        "Instalacao de dependencias finalizada.",
        "Verificando presenca do servico sitef...",
        "Script finalizou com erro."
    ]
}
TASK [DEBUG - stderr do init.sh] ***********************************************
ok: [localhost] => {
    "deploy_result.stderr_lines": [
        "Traceback (most recent call last):",
        "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/script_deploy.py\", line 14, in &lt;module>",
        "    if not sitef_packages.install():",
        "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/sitef_packages.py\", line 19, in install",
        "    for name in os.listdir(self._package_paths):",
        "FileNotFoundError: [Errno 2] No such file or directory: 'packages/linux'"
    ]
}
TASK [Ler deploy.txt do host alvo] *********************************************
ok: [localhost -> sitef-01(100.99.41.58)]
TASK [DEBUG - conteúdo do deploy.txt] ******************************************
ok: [localhost] => {
    "deploy_log.stdout_lines": [
        "deploy.txt nao existe"
    ]
}
TASK [Atualizar status para success de sitef-01] *******************************
skipping: [localhost]
TASK [Atualizar status para failed de sitef-01] ********************************
changed: [localhost]
TASK [Falhar pipeline se init.sh retornou erro] ********************************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Deploy falhou em sitef-01 (host 100.99.41.58). Verifique deploy.txt e status JSON."}
PLAY RECAP *********************************************************************
localhost                  : ok=31   changed=4    unreachable=0    failed=1    skipped=7    rescued=0    ignored=1   
Command finished with status FAILURE
