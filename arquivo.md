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
Clonando repo em /tmp/tmp.varHeB5orO...
Cloning into '/tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 505, done.
remote: Counting objects:   0% (1/455)
remote: Counting objects:   1% (5/455)
remote: Counting objects:   2% (10/455)
remote: Counting objects:   3% (14/455)
remote: Counting objects:   4% (19/455)
remote: Counting objects:   5% (23/455)
remote: Counting objects:   6% (28/455)
remote: Counting objects:   7% (32/455)
remote: Counting objects:   8% (37/455)
remote: Counting objects:   9% (41/455)
remote: Counting objects:  10% (46/455)
remote: Counting objects:  11% (51/455)
remote: Counting objects:  12% (55/455)
remote: Counting objects:  13% (60/455)
remote: Counting objects:  14% (64/455)
remote: Counting objects:  15% (69/455)
remote: Counting objects:  16% (73/455)
remote: Counting objects:  17% (78/455)
remote: Counting objects:  18% (82/455)
remote: Counting objects:  19% (87/455)
remote: Counting objects:  20% (91/455)
remote: Counting objects:  21% (96/455)
remote: Counting objects:  22% (101/455)
remote: Counting objects:  23% (105/455)
remote: Counting objects:  24% (110/455)
remote: Counting objects:  25% (114/455)
remote: Counting objects:  26% (119/455)
remote: Counting objects:  27% (123/455)
remote: Counting objects:  28% (128/455)
remote: Counting objects:  29% (132/455)
remote: Counting objects:  30% (137/455)
remote: Counting objects:  31% (142/455)
remote: Counting objects:  32% (146/455)
remote: Counting objects:  33% (151/455)
remote: Counting objects:  34% (155/455)
remote: Counting objects:  35% (160/455)
remote: Counting objects:  36% (164/455)
remote: Counting objects:  37% (169/455)
remote: Counting objects:  38% (173/455)
remote: Counting objects:  39% (178/455)
remote: Counting objects:  40% (182/455)
remote: Counting objects:  41% (187/455)
remote: Counting objects:  42% (192/455)
remote: Counting objects:  43% (196/455)
remote: Counting objects:  44% (201/455)
remote: Counting objects:  45% (205/455)
remote: Counting objects:  46% (210/455)
remote: Counting objects:  47% (214/455)
remote: Counting objects:  48% (219/455)
remote: Counting objects:  49% (223/455)
remote: Counting objects:  50% (228/455)
remote: Counting objects:  51% (233/455)
remote: Counting objects:  52% (237/455)
remote: Counting objects:  53% (242/455)
remote: Counting objects:  54% (246/455)
remote: Counting objects:  55% (251/455)
remote: Counting objects:  56% (255/455)
remote: Counting objects:  57% (260/455)
remote: Counting objects:  58% (264/455)
remote: Counting objects:  59% (269/455)
remote: Counting objects:  60% (273/455)
remote: Counting objects:  61% (278/455)
remote: Counting objects:  62% (283/455)
remote: Counting objects:  63% (287/455)
remote: Counting objects:  64% (292/455)
remote: Counting objects:  65% (296/455)
remote: Counting objects:  66% (301/455)
remote: Counting objects:  67% (305/455)
remote: Counting objects:  68% (310/455)
remote: Counting objects:  69% (314/455)
remote: Counting objects:  70% (319/455)
remote: Counting objects:  71% (324/455)
remote: Counting objects:  72% (328/455)
remote: Counting objects:  73% (333/455)
remote: Counting objects:  74% (337/455)
remote: Counting objects:  75% (342/455)
remote: Counting objects:  76% (346/455)
remote: Counting objects:  77% (351/455)
remote: Counting objects:  78% (355/455)
remote: Counting objects:  79% (360/455)
remote: Counting objects:  80% (364/455)
remote: Counting objects:  81% (369/455)
remote: Counting objects:  82% (374/455)
remote: Counting objects:  83% (378/455)
remote: Counting objects:  84% (383/455)
remote: Counting objects:  85% (387/455)
remote: Counting objects:  86% (392/455)
remote: Counting objects:  87% (396/455)
remote: Counting objects:  88% (401/455)
remote: Counting objects:  89% (405/455)
remote: Counting objects:  90% (410/455)
remote: Counting objects:  91% (415/455)
remote: Counting objects:  92% (419/455)
remote: Counting objects:  93% (424/455)
remote: Counting objects:  94% (428/455)
remote: Counting objects:  95% (433/455)
remote: Counting objects:  96% (437/455)
remote: Counting objects:  97% (442/455)
remote: Counting objects:  98% (446/455)
remote: Counting objects:  99% (451/455)
remote: Counting objects: 100% (455/455)
remote: Counting objects: 100% (455/455), done.
remote: Compressing objects:   0% (1/442)
remote: Compressing objects:   1% (5/442)
remote: Compressing objects:   2% (9/442)
remote: Compressing objects:   3% (14/442)
remote: Compressing objects:   4% (18/442)
remote: Compressing objects:   5% (23/442)
remote: Compressing objects:   6% (27/442)
remote: Compressing objects:   7% (31/442)
remote: Compressing objects:   8% (36/442)
remote: Compressing objects:   9% (40/442)
remote: Compressing objects:  10% (45/442)
remote: Compressing objects:  11% (49/442)
remote: Compressing objects:  12% (54/442)
remote: Compressing objects:  13% (58/442)
remote: Compressing objects:  14% (62/442)
remote: Compressing objects:  15% (67/442)
remote: Compressing objects:  16% (71/442)
remote: Compressing objects:  17% (76/442)
remote: Compressing objects:  18% (80/442)
remote: Compressing objects:  19% (84/442)
remote: Compressing objects:  20% (89/442)
remote: Compressing objects:  21% (93/442)
remote: Compressing objects:  22% (98/442)
remote: Compressing objects:  23% (102/442)
remote: Compressing objects:  24% (107/442)
remote: Compressing objects:  25% (111/442)
remote: Compressing objects:  26% (115/442)
remote: Compressing objects:  27% (120/442)
remote: Compressing objects:  28% (124/442)
remote: Compressing objects:  29% (129/442)
remote: Compressing objects:  30% (133/442)
remote: Compressing objects:  31% (138/442)
remote: Compressing objects:  32% (142/442)
remote: Compressing objects:  33% (146/442)
remote: Compressing objects:  34% (151/442)
remote: Compressing objects:  35% (155/442)
remote: Compressing objects:  36% (160/442)
remote: Compressing objects:  37% (164/442)
remote: Compressing objects:  38% (168/442)
remote: Compressing objects:  39% (173/442)
remote: Compressing objects:  40% (177/442)
remote: Compressing objects:  41% (182/442)
remote: Compressing objects:  42% (186/442)
remote: Compressing objects:  43% (191/442)
remote: Compressing objects:  44% (195/442)
remote: Compressing objects:  45% (199/442)
remote: Compressing objects:  46% (204/442)
remote: Compressing objects:  47% (208/442)
remote: Compressing objects:  48% (213/442)
remote: Compressing objects:  49% (217/442)
remote: Compressing objects:  50% (221/442)
remote: Compressing objects:  51% (226/442)
remote: Compressing objects:  52% (230/442)
remote: Compressing objects:  53% (235/442)
remote: Compressing objects:  54% (239/442)
remote: Compressing objects:  55% (244/442)
remote: Compressing objects:  56% (248/442)
remote: Compressing objects:  57% (252/442)
remote: Compressing objects:  58% (257/442)
remote: Compressing objects:  59% (261/442)
remote: Compressing objects:  60% (266/442)
remote: Compressing objects:  61% (270/442)
remote: Compressing objects:  62% (275/442)
remote: Compressing objects:  63% (279/442)
remote: Compressing objects:  64% (283/442)
remote: Compressing objects:  65% (288/442)
remote: Compressing objects:  66% (292/442)
remote: Compressing objects:  67% (297/442)
remote: Compressing objects:  68% (301/442)
remote: Compressing objects:  69% (305/442)
remote: Compressing objects:  70% (310/442)
remote: Compressing objects:  71% (314/442)
remote: Compressing objects:  72% (319/442)
remote: Compressing objects:  73% (323/442)
remote: Compressing objects:  74% (328/442)
remote: Compressing objects:  75% (332/442)
remote: Compressing objects:  76% (336/442)
remote: Compressing objects:  77% (341/442)
remote: Compressing objects:  78% (345/442)
remote: Compressing objects:  79% (350/442)
remote: Compressing objects:  80% (354/442)
remote: Compressing objects:  81% (359/442)
remote: Compressing objects:  82% (363/442)
remote: Compressing objects:  83% (367/442)
remote: Compressing objects:  84% (372/442)
remote: Compressing objects:  85% (376/442)
remote: Compressing objects:  86% (381/442)
remote: Compressing objects:  87% (385/442)
remote: Compressing objects:  88% (389/442)
remote: Compressing objects:  89% (394/442)
remote: Compressing objects:  90% (398/442)
remote: Compressing objects:  91% (403/442)
remote: Compressing objects:  92% (407/442)
remote: Compressing objects:  93% (412/442)
remote: Compressing objects:  94% (416/442)
remote: Compressing objects:  95% (420/442)
remote: Compressing objects:  96% (425/442)
remote: Compressing objects:  97% (429/442)
remote: Compressing objects:  98% (434/442)
remote: Compressing objects:  99% (438/442)
remote: Compressing objects: 100% (442/442)
remote: Compressing objects: 100% (442/442), done.
remote: Total 505 (delta 262), reused 0 (delta 0), pack-reused 50
Receiving objects:   0% (1/505)
Receiving objects:   1% (6/505)
Receiving objects:   2% (11/505)
Receiving objects:   3% (16/505)
Receiving objects:   4% (21/505)
Receiving objects:   5% (26/505)
Receiving objects:   6% (31/505)
Receiving objects:   7% (36/505)
Receiving objects:   8% (41/505)
Receiving objects:   9% (46/505)
Receiving objects:  10% (51/505)
Receiving objects:  11% (56/505)
Receiving objects:  12% (61/505)
Receiving objects:  13% (66/505)
Receiving objects:  14% (71/505)
Receiving objects:  15% (76/505)
Receiving objects:  16% (81/505)
Receiving objects:  17% (86/505)
Receiving objects:  18% (91/505)
Receiving objects:  19% (96/505)
Receiving objects:  20% (101/505)
Receiving objects:  21% (107/505)
Receiving objects:  22% (112/505)
Receiving objects:  23% (117/505)
Receiving objects:  24% (122/505)
Receiving objects:  25% (127/505)
Receiving objects:  26% (132/505)
Receiving objects:  27% (137/505)
Receiving objects:  28% (142/505)
Receiving objects:  29% (147/505)
Receiving objects:  30% (152/505)
Receiving objects:  31% (157/505)
Receiving objects:  32% (162/505)
Receiving objects:  33% (167/505)
Receiving objects:  34% (172/505)
Receiving objects:  35% (177/505)
Receiving objects:  36% (182/505)
Receiving objects:  37% (187/505)
Receiving objects:  38% (192/505)
Receiving objects:  39% (197/505)
Receiving objects:  40% (202/505)
Receiving objects:  41% (208/505)
Receiving objects:  42% (213/505)
Receiving objects:  43% (218/505)
Receiving objects:  44% (223/505)
Receiving objects:  45% (228/505)
Receiving objects:  46% (233/505)
Receiving objects:  47% (238/505)
Receiving objects:  48% (243/505)
Receiving objects:  49% (248/505)
Receiving objects:  50% (253/505)
Receiving objects:  51% (258/505)
Receiving objects:  52% (263/505)
Receiving objects:  53% (268/505)
Receiving objects:  54% (273/505)
Receiving objects:  55% (278/505)
Receiving objects:  56% (283/505)
Receiving objects:  57% (288/505)
Receiving objects:  58% (293/505)
Receiving objects:  59% (298/505)
Receiving objects:  60% (303/505)
Receiving objects:  61% (309/505)
Receiving objects:  62% (314/505)
Receiving objects:  63% (319/505)
Receiving objects:  64% (324/505)
Receiving objects:  65% (329/505)
Receiving objects:  66% (334/505)
Receiving objects:  67% (339/505)
Receiving objects:  68% (344/505)
Receiving objects:  69% (349/505)
Receiving objects:  70% (354/505)
Receiving objects:  71% (359/505)
Receiving objects:  72% (364/505)
Receiving objects:  73% (369/505)
Receiving objects:  74% (374/505)
Receiving objects:  75% (379/505)
Receiving objects:  76% (384/505)
Receiving objects:  77% (389/505)
Receiving objects:  78% (394/505)
Receiving objects:  79% (399/505)
Receiving objects:  80% (404/505)
Receiving objects:  81% (410/505)
Receiving objects:  82% (415/505)
Receiving objects:  83% (420/505)
Receiving objects:  84% (425/505)
Receiving objects:  85% (430/505)
Receiving objects:  86% (435/505)
Receiving objects:  87% (440/505)
Receiving objects:  88% (445/505)
Receiving objects:  89% (450/505)
Receiving objects:  90% (455/505)
Receiving objects:  91% (460/505)
Receiving objects:  92% (465/505)
Receiving objects:  93% (470/505)
Receiving objects:  94% (475/505)
Receiving objects:  95% (480/505)
Receiving objects:  96% (485/505)
Receiving objects:  97% (490/505)
Receiving objects:  98% (495/505)
Receiving objects:  99% (500/505)
Receiving objects: 100% (505/505)
Receiving objects: 100% (505/505), 78.55 KiB | 19.64 MiB/s, done.
Resolving deltas:   0% (0/281)
Resolving deltas:   1% (3/281)
Resolving deltas:   2% (6/281)
Resolving deltas:   3% (9/281)
Resolving deltas:   4% (12/281)
Resolving deltas:   5% (15/281)
Resolving deltas:   6% (17/281)
Resolving deltas:   7% (20/281)
Resolving deltas:   8% (23/281)
Resolving deltas:   9% (26/281)
Resolving deltas:  10% (29/281)
Resolving deltas:  11% (31/281)
Resolving deltas:  12% (34/281)
Resolving deltas:  13% (37/281)
Resolving deltas:  14% (40/281)
Resolving deltas:  15% (43/281)
Resolving deltas:  16% (45/281)
Resolving deltas:  17% (48/281)
Resolving deltas:  18% (51/281)
Resolving deltas:  19% (54/281)
Resolving deltas:  20% (57/281)
Resolving deltas:  21% (61/281)
Resolving deltas:  22% (62/281)
Resolving deltas:  23% (65/281)
Resolving deltas:  24% (68/281)
Resolving deltas:  25% (71/281)
Resolving deltas:  26% (74/281)
Resolving deltas:  27% (76/281)
Resolving deltas:  28% (79/281)
Resolving deltas:  29% (82/281)
Resolving deltas:  30% (85/281)
Resolving deltas:  31% (88/281)
Resolving deltas:  32% (90/281)
Resolving deltas:  33% (93/281)
Resolving deltas:  34% (96/281)
Resolving deltas:  35% (99/281)
Resolving deltas:  36% (102/281)
Resolving deltas:  37% (104/281)
Resolving deltas:  38% (107/281)
Resolving deltas:  39% (110/281)
Resolving deltas:  40% (113/281)
Resolving deltas:  41% (116/281)
Resolving deltas:  42% (120/281)
Resolving deltas:  43% (121/281)
Resolving deltas:  44% (124/281)
Resolving deltas:  45% (127/281)
Resolving deltas:  46% (130/281)
Resolving deltas:  47% (133/281)
Resolving deltas:  48% (135/281)
Resolving deltas:  49% (138/281)
Resolving deltas:  50% (141/281)
Resolving deltas:  51% (144/281)
Resolving deltas:  52% (147/281)
Resolving deltas:  53% (149/281)
Resolving deltas:  54% (153/281)
Resolving deltas:  55% (155/281)
Resolving deltas:  56% (158/281)
Resolving deltas:  57% (161/281)
Resolving deltas:  58% (163/281)
Resolving deltas:  59% (166/281)
Resolving deltas:  60% (169/281)
Resolving deltas:  61% (172/281)
Resolving deltas:  62% (175/281)
Resolving deltas:  63% (178/281)
Resolving deltas:  64% (180/281)
Resolving deltas:  65% (183/281)
Resolving deltas:  66% (186/281)
Resolving deltas:  67% (189/281)
Resolving deltas:  68% (192/281)
Resolving deltas:  69% (194/281)
Resolving deltas:  70% (197/281)
Resolving deltas:  71% (200/281)
Resolving deltas:  72% (203/281)
Resolving deltas:  73% (206/281)
Resolving deltas:  74% (208/281)
Resolving deltas:  75% (211/281)
Resolving deltas:  76% (214/281)
Resolving deltas:  77% (217/281)
Resolving deltas:  78% (220/281)
Resolving deltas:  79% (222/281)
Resolving deltas:  80% (225/281)
Resolving deltas:  81% (228/281)
Resolving deltas:  82% (231/281)
Resolving deltas:  83% (234/281)
Resolving deltas:  84% (237/281)
Resolving deltas:  85% (239/281)
Resolving deltas:  86% (242/281)
Resolving deltas:  87% (245/281)
Resolving deltas:  88% (248/281)
Resolving deltas:  89% (251/281)
Resolving deltas:  90% (253/281)
Resolving deltas:  91% (256/281)
Resolving deltas:  92% (259/281)
Resolving deltas:  93% (262/281)
Resolving deltas:  94% (265/281)
Resolving deltas:  95% (267/281)
Resolving deltas:  96% (270/281)
Resolving deltas:  97% (273/281)
Resolving deltas:  98% (276/281)
Resolving deltas:  99% (279/281)
Resolving deltas: 100% (281/281)
Resolving deltas: 100% (281/281), done.
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
        "repo_root           = /tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/ansible/..",
        "status_dir          = /tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/ansible/../status/DEV000000007",
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
ok: [localhost] => (item={'path': '/tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 4328812, 'dev': 64775, 'nlink': 1, 'atime': 1765221462.844574, 'mtime': 1765221462.8375738, 'ctime': 1765221462.8375738, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False})
TASK [Montar lista de predeploys parseados] ************************************
ok: [localhost] => (item={'content': 'eyJtYWNoaW5lIjogInNpdGVmLTAxIiwgImhvc3QiOiAiMTAwLjk5LjQxLjU4IiwgInBhY2thZ2UiOiAic2l0ZWYtY29yZS0wLjAuMi0wIiwgInJvbGxiYWNrIjogIiIsICJzdGF0dXMiOiAic3VjY2VzcyIsICJkZXBsb3ltZW50X3JlZiI6ICJERVYwMDAwMDAwMDciLCAidGltZXN0YW1wIjogIjIwMjUtMTItMDhUMTg6MTU6NTJaIiwgInN0ZG91dCI6ICIrIGVjaG8gJz09IEluXHUwMGVkY2lvIGluaXRfcGFyYWxsZWwuc2ggPT0nXG49PSBJblx1MDBlZGNpbyBpbml0X3BhcmFsbGVsLnNoID09XG4rIGRhdGVcbk1vbiBEZWMgIDggMDY6MTY6MTMgUE0gVVRDIDIwMjVcbisgc2V0IC14XG4rIGVjaG8gJ0luc3RhbGFjYW8gZGUgZGVwZW5kZW5jaWFzIGRvIHNpc3RlbWEuLi4nXG5JbnN0YWxhY2FvIGRlIGRlcGVuZGVuY2lhcyBkbyBzaXN0ZW1hLi4uXG4rIGRuZiBpbnN0YWxsIC15IC0tbm9kb2NzIHB5dGhvbjMgcHl0aG9uMy1waXBcblVwZGF0aW5nIFN1YnNjcmlwdGlvbiBNYW5hZ2VtZW50IHJlcG9zaXRvcmllcy5cblxuVGhpcyBzeXN0ZW0gaGFzIHJlbGVhc2Ugc2V0IHRvIDkgYW5kIGl0IHJlY2VpdmVzIHVwZGF0ZXMgb25seSBmb3IgdGhpcyByZWxlYXNlLlxuXG5SZWQgSGF0IENvZGVSZWFkeSBMaW51eCBCdWlsZGVyIGZvciBSSEVMIDkgeDg2XyAyLjgga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQXBwU3RyZSA0LjEga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IFNhdGVsbGl0ZSBDbGllbnQgNiBmb3IgUkhFTCA5IHg4Nl82NCAoUiAzLjQga0IvcyB8IDMuOCBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQmFzZU9TICAzLjcga0IvcyB8IDQuMSBrQiAgICAgMDA6MDEgICAgXG5FUEVMOV9YODYgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAxLjYga0IvcyB8IDIuMyBrQiAgICAgMDA6MDEgICAgXG5QYWNrYWdlIHB5dGhvbjMtMy45LjIzLTIuZWw5Lng4Nl82NCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cblBhY2thZ2UgcHl0aG9uMy1waXAtMjEuMy4xLTEuZWw5Lm5vYXJjaCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cbkRlcGVuZGVuY2llcyByZXNvbHZlZC5cbk5vdGhpbmcgdG8gZG8uXG5Db21wbGV0ZSFcbisgcHl0aG9uMyAtbSB2ZW52IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisgc291cmNlIC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnYvYmluL2FjdGl2YXRlXG4rKyBkZWFjdGl2YXRlIG5vbmRlc3RydWN0aXZlXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKysgaGFzaCAtclxuKysgJ1snIC1uICcnICddJ1xuKysgdW5zZXQgVklSVFVBTF9FTlZcbisrICdbJyAnIScgbm9uZGVzdHJ1Y3RpdmUgPSBub25kZXN0cnVjdGl2ZSAnXSdcbisrIFZJUlRVQUxfRU5WPS9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisrIGV4cG9ydCBWSVJUVUFMX0VOVlxuKysgX09MRF9WSVJUVUFMX1BBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIFBBVEg9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvdmVudi9iaW46L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIGV4cG9ydCBQQVRIXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLXogJycgJ10nXG4rKyBfT0xEX1ZJUlRVQUxfUFMxPVxuKysgUFMxPScodmVudikgJ1xuKysgZXhwb3J0IFBTMVxuKysgJ1snIC1uIC9iaW4vYmFzaCAtbyAtbiAnJyAnXSdcbisrIGhhc2ggLXJcbisgZWNobyAnSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uJ1xuSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uXG4rIHB5dGhvbjMgLXUgc2NyaXB0X3ByZXBhcmUucHlcbkV4ZWN1dGFuZG8gYmFja3VwLi4uXG5GYXplbmRvIGxldmFudGFtZW50byBkZSBhcnF1aXZvcy9wYXN0YXNcblBhdGggZW5jb250cmFkbzogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvY29uZmlnXG5QcmVwYXJhY2FvIGRvIGJhY2t1cDogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvYmFja3Vwcy9iYWNrdXAtYmVmb3JlLWRlZmF1bHQudGFyLmd6XG5BcGFnYW5kbyBiYWNrdXA6IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL2JhY2t1cHMvYmFja3VwLWJlZm9yZS1kZWZhdWx0LnRhci5nelxuTWV0YWRhZG9zOiB7J3BhY2thZ2UnOiB7J2JhY2t1cCc6IHsnYmVmb3JlX2luc3RhbGxfdmVyc2lvbic6ICdkZWZhdWx0JywgJ2V4ZWN1dGVkX2F0JzogJzIwMjUtMTItMDggMTg6MTY6MjQnfX19XG5CYWNrdXAgZmluYWxpemFkby5cbisgZWNobyAnc2NyaXB0X3ByZXBhcmUucHkgZmluYWxpemFkbyBjb20gcmM9MCdcbnNjcmlwdF9wcmVwYXJlLnB5IGZpbmFsaXphZG8gY29tIHJjPTBcbisgZGVhY3RpdmF0ZVxuKyAnWycgLW4gL3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW4gJ10nXG4rIFBBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisgZXhwb3J0IFBBVEhcbisgdW5zZXQgX09MRF9WSVJUVUFMX1BBVEhcbisgJ1snIC1uICcnICddJ1xuKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKyBoYXNoIC1yXG4rICdbJyAtbiAnJyAnXSdcbisgdW5zZXQgVklSVFVBTF9FTlZcbisgJ1snICchJyAnJyA9IG5vbmRlc3RydWN0aXZlICddJ1xuKyB1bnNldCAtZiBkZWFjdGl2YXRlXG4rIHNldCAreFxuPT0gRmltIGluaXRfcGFyYWxsZWwuc2ggPT1cbk1vbiBEZWMgIDggMDY6MTY6MjQgUE0gVVRDIDIwMjUiLCAic3RkZXJyIjogIisgc2V0IC1ldW8gcGlwZWZhaWxcbisrKyBkaXJuYW1lIC4vaW5pdF9wYXJhbGxlbC5zaFxuKysgY2QgLlxuKysgcHdkXG4rIFNDUklQVF9ESVI9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjJcbisgTE9HX0ZJTEU9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjIvcGFyYWxsZWwudHh0XG4rIGV4ZWNcbisrIHRlZSAtYSAvb3B0L1NvZnR3YXJlRXhwcmVzcy9zaXRlZi9zY3JpcHRzL2RlcGxveS1zaXRlZi0wLjAuMi9wYXJhbGxlbC50eHQifQ==', 'source': '/tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'encoding': 'base64', 'invocation': {'module_args': {'src': '/tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json'}}, 'failed': False, 'changed': False, 'item': {'path': '/tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 4328812, 'dev': 64775, 'nlink': 1, 'atime': 1765221462.844574, 'mtime': 1765221462.8375738, 'ctime': 1765221462.8375738, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False}, 'ansible_loop_var': 'item'})
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
included: /tmp/tmp.varHeB5orO/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost => (item=sitef-01)
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
fatal: [localhost -> sitef-01(100.99.41.58)]: FAILED! => {"changed": true, "cmd": "set -e\ncd \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2\"\n./init.sh\n", "delta": "0:00:11.878240", "end": "2025-12-08 19:18:04.502406", "msg": "non-zero return code", "rc": 1, "start": "2025-12-08 19:17:52.624166", "stderr": "Traceback (most recent call last):\n  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/script_deploy.py\", line 14, in &lt;module>\n    if not sitef_packages.install():\n  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/sitef_packages.py\", line 19, in install\n    for name in os.listdir(self._package_paths):\nFileNotFoundError: [Errno 2] No such file or directory: 'packages/linux'", "stderr_lines": ["Traceback (most recent call last):", "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/script_deploy.py\", line 14, in &lt;module>", "    if not sitef_packages.install():", "  File \"/opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/sitef_packages.py\", line 19, in install", "    for name in os.listdir(self._package_paths):", "FileNotFoundError: [Errno 2] No such file or directory: 'packages/linux'"], "stdout": "Instalacao de dependencias do sistema...\nUpdating Subscription Management repositories.\n\nThis system has release set to 9 and it receives updates only for this release.\n\nRed Hat CodeReady Linux Builder for RHEL 9 x86_ 2.8 kB/s | 4.5 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - AppStre 3.6 kB/s | 4.5 kB     00:01    \nRed Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    \nRed Hat Enterprise Linux 9 for x86_64 - BaseOS  3.2 kB/s | 4.1 kB     00:01    \nEPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    \nPackage python3-3.9.23-2.el9.x86_64 is already installed.\nPackage python3-pip-21.3.1-1.el9.noarch is already installed.\nDependencies resolved.\nNothing to do.\nComplete!\nInstalacao de dependencias finalizada.\nInstalacao de dependencias do python...\nInstalacao de dependencias finalizada.\nVerificando presenca do servico sitef...\nScript finalizou com erro.", "stdout_lines": ["Instalacao de dependencias do sistema...", "Updating Subscription Management repositories.", "", "This system has release set to 9 and it receives updates only for this release.", "", "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 2.8 kB/s | 4.5 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.6 kB/s | 4.5 kB     00:01    ", "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    ", "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  3.2 kB/s | 4.1 kB     00:01    ", "EPEL9_X86                                       1.8 kB/s | 2.3 kB     00:01    ", "Package python3-3.9.23-2.el9.x86_64 is already installed.", "Package python3-pip-21.3.1-1.el9.noarch is already installed.", "Dependencies resolved.", "Nothing to do.", "Complete!", "Instalacao de dependencias finalizada.", "Instalacao de dependencias do python...", "Instalacao de dependencias finalizada.", "Verificando presenca do servico sitef...", "Script finalizou com erro."]}
...ignoring
TASK [DEBUG - stdout do init.sh] ***********************************************
ok: [localhost] => {
    "deploy_result.stdout_lines": [
        "Instalacao de dependencias do sistema...",
        "Updating Subscription Management repositories.",
        "",
        "This system has release set to 9 and it receives updates only for this release.",
        "",
        "Red Hat CodeReady Linux Builder for RHEL 9 x86_ 2.8 kB/s | 4.5 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - AppStre 3.6 kB/s | 4.5 kB     00:01    ",
        "Red Hat Satellite Client 6 for RHEL 9 x86_64 (R 2.6 kB/s | 3.8 kB     00:01    ",
        "Red Hat Enterprise Linux 9 for x86_64 - BaseOS  3.2 kB/s | 4.1 kB     00:01    ",
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



#!/bin/bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
LOG_FILE="${SCRIPT_DIR}/deploy.txt"

# Tudo que sair em stdout/stderr vai pro console e pro arquivo
exec > >(tee -a "$LOG_FILE") 2>&1

echo "== Início init.sh =="
date

set -x

echo "Instalacao de dependencias do sistema..."
dnf install -y --nodocs python3 python3-pip
echo "Instalacao de dependencias do sistema finalizada."

# Garante diretório de setup
mkdir -p /opt/SoftwareExpress/sitef-setup

# Venv padrão do fluxo
python3 -m venv /opt/SoftwareExpress/sitef-setup/venv
source /opt/SoftwareExpress/sitef-setup/venv/bin/activate

echo "Instalacao de dependencias do python..."
# Se um dia precisar:
# pip3 install <dependencia>
echo "Instalacao de dependencias do python finalizada."

# Execução do deploy real
python3 -u script_deploy.py
RC=$?

echo "script_deploy.py finalizado com rc=${RC}"

deactivate

set +x

if [ $RC -ne 0 ]; then
  echo "Script finalizou com erro."
  exit 1
fi

echo "== Fim init.sh =="
date

import os
import sys
import backup
import sitef as sitef_module
import sitef_packages as sitef_packages_module

sitef = sitef_module.sitef()

# Caminho correto onde seus pacotes realmente ficam no host
# Ex esperado: /opt/SoftwareExpress/sitef/package/linux
pkg_dir = os.path.join(sitef.root_path(), "package", "linux")

# Instancia sitef_packages de forma compatível com versões antigas e novas
try:
    # Se você adicionar suporte a esse parâmetro na classe, vai usar aqui
    sitef_packages = sitef_packages_module.sitef_packages(package_paths=pkg_dir)
except TypeError:
    # Fallback para versão antiga da classe
    sitef_packages = sitef_packages_module.sitef_packages()
    # Ajuste direto no atributo interno, se existir
    if hasattr(sitef_packages, "_package_paths"):
        sitef_packages._package_paths = pkg_dir

print("== Início script_deploy.py ==")
print(f"Root path        : {sitef.root_path()}")
print(f"Setup path       : {sitef.setup_path()}")
print(f"Config path      : {sitef.config_path()}")
print(f"Package dir (fs) : {pkg_dir}")

print("Verificando presenca do servico sitef...")
if sitef.service_exists():
    print("Serviço sitef existe.")
    print("Parando servico sitef...")
    if not sitef.service_stop():
        print("Falha ao parar servico sitef.")
        sys.exit(1)
else:
    print("Serviço sitef não encontrado. Seguindo instalação mesmo assim.")

# (Opcional) caso você queira garantir backup também no deploy:
# print("Executando backup pre-install...")
# if not backup.backup().prepare():
#     print("Falha na operacao de backup.")
#     sys.exit(1)
# print("Backup finalizado.")

print("Executando instalacao de pacotes...")
if not sitef_packages.install():
    print("Falha na operacao de instalacao.")
    sys.exit(1)

installed = sitef_packages.get_installed()
print(f"Pacotes instalados ({len(installed)}): {installed}")

for pack in installed:
    if pack.startswith("aplicpatchprods"):
        print("Executando atualizacao do prods...")
        if not sitef.run_aplicpatchprods():
            print("Falha na execucao do aplicpatchprods.")
            sys.exit(1)

print("Validando serviço pós-instalação...")
if sitef.service_exists():
    print("Iniciando servico sitef...")
    if not sitef.service_start():
        print("Falha ao iniciar servico sitef.")
        sys.exit(1)
else:
    print("Serviço sitef ainda não existe após instalação (ok dependendo do pacote).")

print("== Fim script_deploy.py ==")

from sys import platform
import subprocess
import json
import os

class sitef:
    """
    Classe para acesso a atributos e tratamentos relacionados ao SiTef.
    """

    def __init__(self):
        if platform in ["linux", "linux2"]:
            self._root_path = '/opt/SoftwareExpress/sitef'
            self._setup_path = '/opt/SoftwareExpress/sitef-setup'
        else:
            self._root_path = '/sitef'
            self._setup_path = '/sitef-setup'

        self._metadata = None
        self._metadata_path = f'{self._setup_path}/metadata.json'

    def root_path(self):
        return self._root_path

    def config_path(self):
        return f'{self._root_path}/config'

    def bin_path(self):
        if platform in ["linux", "linux2"]:
            return f'{self._root_path}/bin'
        else:
            return f'{self._root_path}/aplic.win'

    def setup_path(self):
        return self._setup_path

    def load_metadata(self):
        self._metadata = {}

        if os.path.exists(self._metadata_path):
            with open(self._metadata_path, 'r', encoding='utf-8') as f:
                self._metadata = json.load(f)

        return self._metadata

    def write_metadata(self, metadata):
        self._metadata = metadata

        os.makedirs(self._setup_path, exist_ok=True)

        with open(self._metadata_path, 'w', encoding='utf-8') as f:
            json.dump(metadata, f, ensure_ascii=False, indent=2)

        return self._metadata

    def service_exists(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'list-unit-files', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha verificacao de servico SiTef: {e}')
                return False
        return False

    def service_start(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'start', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha start de servico SiTef: {e}')
                return False
        return False

    def service_stop(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'stop', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha stop de servico SiTef: {e}')
                return False
        return False

    def run_aplicpatchprods(self, create=True):
        if platform in ["linux", "linux2"]:
            try:
                binary_path = f'{self.bin_path()}/aplicpatchprods'
                if create:
                    result = subprocess.run(['sudo', '-u', 'sitefd', binary_path, 'create=1'], check=False)
                else:
                    result = subprocess.run(['sudo', '-u', 'sitefd', binary_path], check=False)
                return result.returncode == 0
            except Exception as e:
                print(f'Falha na execucao do aplicpatchprods: {e}')
                return False
        return False


from sys import platform
import subprocess
import os

class sitef_packages:

    def __init__(self):
        # Diretório real no host onde o predeploy baixou/copou os RPMs
        if platform in ["linux", "linux2"]:
            self._package_dir = "/opt/SoftwareExpress/sitef/package/linux"
        else:
            self._package_dir = "packages/windows"

        self._installed = []

    def install(self, rollback=False):
        if platform in ["linux", "linux2"]:
            if not os.path.isdir(self._package_dir):
                print(f"Diretório de pacotes não existe: {self._package_dir}")
                return False

            for name in os.listdir(self._package_dir):
                path = os.path.join(self._package_dir, name)

                if os.path.isfile(path) and name.endswith('.rpm'):
                    print(f"Instalando: {path}")
                    result = subprocess.run(['dnf', 'install', '-y', path], check=False)
                    if result.returncode != 0:
                        print(f"Falha instalando RPM: {name}")
                        return False
                    self._installed.append(name)

            return True

        return False

    def get_installed(self):
        return self._installed


from datetime import datetime
import os
import shutil
from sys import platform
import sitef
import tarfile

class backup:

    def __init__(self):
        self._sitef = sitef.sitef()

        if platform in ["linux", "linux2"]:
            self._basepath = '/opt/SoftwareExpress'
        else:
            self._basepath = '/'
        self._basepath = os.path.normpath(self._basepath)

        # Permite somente paths que ficam abaixo do basepath
        # Protege contra alterações em outros caminhos indevidos
        self._paths = [self._sitef.config_path()]
        self._paths = [p for p in self._paths if os.path.commonpath([os.path.normpath(p), self._basepath]) == self._basepath]

    def prepare(self):
        print('Fazendo levantamento de arquivos/pastas')
        for path in self._paths:
            if not os.path.exists(path):
                print(f'Path nao encontrado: {path}')
            else:
                print(f'Path encontrado: {path}')

        # A versão recebida aqui é do pacote sendo preparado para instalação
        package_version = os.getenv('PACKAGE_VERSION')
        if not package_version:
            package_version = 'default'

        backup_name = f'{self._sitef.setup_path()}/backups/backup-before-{package_version}.tar.gz'
        backup_dir = os.path.dirname(backup_name)
        print(f'Preparacao do backup: {backup_name}')

        os.makedirs(os.path.dirname(backup_name), exist_ok=True)

        # Faz o expurgo de backups anteriores, por questão de espaço em disco não mantem um histórico.
        # É esperado que ao final dos backups só esteja mantido o mais atual.
        for name in os.listdir(backup_dir):
            backup = os.path.join(backup_dir, name)
            if os.path.isfile(backup) and name.startswith('backup-'):
                print(f'Apagando backup: {backup}')
                os.remove(backup)

        #if os.path.exists(backup_name):
        #    os.remove(backup_name) # Apaga backup anterior com o mesmo nome

        with tarfile.open(backup_name, "w:gz") as tar:
            for path in self._paths:
                tar.add(path, arcname=os.path.relpath(path, self._basepath))

        metadata = self._sitef.load_metadata()

        if not ('package' in metadata) or not metadata['package']:
            metadata['package'] = {}

        metadata['package']['backup'] = {}
        metadata['package']['backup']['before_install_version'] = package_version
        metadata['package']['backup']['executed_at'] = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        metadata = self._sitef.write_metadata(metadata)

        print(f'Metadados: {metadata}')
        return True

    def restore(self):
        # Obtem qual o escopo de versão que terá o rollback.
        package_version = os.getenv('PACKAGE_VERSION')
        if not package_version:
            package_version = 'default'

        metadata = self._sitef.load_metadata()

        if ('package' in metadata) and ('backup' in metadata['package']) and ('before_install_version' in metadata['package']['backup']):
            print(f"Identificado a presenca do backup: {metadata['package']['backup']['before_install_version']}")

            if metadata['package']['backup']['before_install_version'] == package_version:
                print(f'Validacao de versao com sucesso: {package_version}')

                backup_name = f'{self._sitef.setup_path()}/backups/backup-before-{package_version}.tar.gz'
                print(f'Recuperacao do backup: {backup_name}')

                for path in self._paths:
                    # Remove path de rollback que possa já existir
                    destpath = f'{path}.rollback'
                    if os.path.exists(destpath):
                        if os.path.isdir(destpath):
                            shutil.rmtree(destpath)
                        else:
                            os.remove(destpath)
                    # Renomeia path atual para ser substituido pelo backup
                    if os.path.exists(path):
                        print(f'Renomeando: {path} -> {destpath}')
                        os.rename(path, destpath)

                with tarfile.open(backup_name, "r:gz") as tar:
                    tar.extractall(path=self._basepath, filter='fully_trusted')

                if not ('package' in metadata) or not metadata['package']:
                    metadata['package'] = {}

                metadata['package']['rollback'] = {}
                metadata['package']['rollback']['from_version'] = package_version
                metadata['package']['rollback']['executed_at'] = datetime.now().strftime('%Y-%m-%d %H:%M:%S')
                metadata = self._sitef.write_metadata(metadata)

                # Faz o expurgo dos arquivos rollback
                for path in self._paths:
                    destpath = f'{path}.rollback'
                    if os.path.exists(destpath):
                        print(f'Limpando: {destpath}')
                        if os.path.isdir(destpath):
                            shutil.rmtree(destpath)
                        else:
                            os.remove(destpath)

                print('Backup recuperado com sucesso.')
                return True
            else:
                print(f'Versao do backup diferente do esperado: {package_version}')
        else:
            print('Backup indisponivel')

        return False
