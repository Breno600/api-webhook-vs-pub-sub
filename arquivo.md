## Fui rodar o pre deploy novamente e esta dando erro no momento de jogar o json e logs no harness, realmente ja existe a pasta pois eu ja fiz outros pre deploy antes, ent se tem a pasta deveria seguir.

Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export GIT_TAG=DEV000002
Clonando repo em /tmp/tmp.1YZSOgOL7J...
Cloning into '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef'...
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
remote: Total 915 (delta 583), reused 0 (delta 0), pack-reused 50
Receiving objects:  85% (778/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  86% (787/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  87% (797/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  88% (806/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  89% (815/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  90% (824/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  91% (833/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  92% (842/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  93% (851/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  94% (861/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  95% (870/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  96% (879/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  97% (888/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  98% (897/915), 707.77 KiB | 1.35 MiB/s
Receiving objects:  99% (906/915), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (915/915), 707.77 KiB | 1.35 MiB/s
Receiving objects: 100% (915/915), 934.60 KiB | 1.78 MiB/s, done.
Resolving deltas:   0% (0/602)
Resolving deltas:   1% (7/602)
Resolving deltas:   2% (13/602)
Resolving deltas:   3% (19/602)
Resolving deltas:   4% (25/602)
Resolving deltas:   5% (31/602)
Resolving deltas:   6% (37/602)
Resolving deltas:   7% (43/602)
Resolving deltas:   8% (49/602)
Resolving deltas:   9% (55/602)
Resolving deltas:  10% (61/602)
Resolving deltas:  11% (67/602)
Resolving deltas:  12% (73/602)
Resolving deltas:  13% (79/602)
Resolving deltas:  14% (85/602)
Resolving deltas:  15% (91/602)
Resolving deltas:  16% (97/602)
Resolving deltas:  17% (103/602)
Resolving deltas:  18% (109/602)
Resolving deltas:  19% (115/602)
Resolving deltas:  20% (121/602)
Resolving deltas:  21% (127/602)
Resolving deltas:  22% (133/602)
Resolving deltas:  23% (139/602)
Resolving deltas:  24% (145/602)
Resolving deltas:  25% (151/602)
Resolving deltas:  26% (157/602)
Resolving deltas:  27% (163/602)
Resolving deltas:  28% (169/602)
Resolving deltas:  29% (175/602)
Resolving deltas:  30% (181/602)
Resolving deltas:  31% (187/602)
Resolving deltas:  32% (193/602)
Resolving deltas:  33% (199/602)
Resolving deltas:  34% (205/602)
Resolving deltas:  35% (211/602)
Resolving deltas:  36% (217/602)
Resolving deltas:  37% (223/602)
Resolving deltas:  38% (229/602)
Resolving deltas:  39% (235/602)
Resolving deltas:  40% (241/602)
Resolving deltas:  41% (247/602)
Resolving deltas:  42% (253/602)
Resolving deltas:  43% (259/602)
Resolving deltas:  44% (265/602)
Resolving deltas:  45% (271/602)
Resolving deltas:  46% (277/602)
Resolving deltas:  47% (283/602)
Resolving deltas:  48% (289/602)
Resolving deltas:  49% (295/602)
Resolving deltas:  50% (301/602)
Resolving deltas:  51% (308/602)
Resolving deltas:  52% (314/602)
Resolving deltas:  53% (320/602)
Resolving deltas:  54% (326/602)
Resolving deltas:  55% (332/602)
Resolving deltas:  56% (338/602)
Resolving deltas:  57% (344/602)
Resolving deltas:  58% (350/602)
Resolving deltas:  59% (356/602)
Resolving deltas:  60% (362/602)
Resolving deltas:  61% (368/602)
Resolving deltas:  62% (374/602)
Resolving deltas:  63% (380/602)
Resolving deltas:  64% (386/602)
Resolving deltas:  65% (392/602)
Resolving deltas:  66% (398/602)
Resolving deltas:  67% (404/602)
Resolving deltas:  68% (410/602)
Resolving deltas:  69% (416/602)
Resolving deltas:  70% (422/602)
Resolving deltas:  71% (428/602)
Resolving deltas:  72% (434/602)
Resolving deltas:  73% (440/602)
Resolving deltas:  74% (446/602)
Resolving deltas:  75% (452/602)
Resolving deltas:  76% (458/602)
Resolving deltas:  77% (464/602)
Resolving deltas:  78% (470/602)
Resolving deltas:  79% (476/602)
Resolving deltas:  80% (482/602)
Resolving deltas:  81% (488/602)
Resolving deltas:  82% (494/602)
Resolving deltas:  83% (500/602)
Resolving deltas:  84% (506/602)
Resolving deltas:  85% (512/602)
Resolving deltas:  86% (518/602)
Resolving deltas:  87% (524/602)
Resolving deltas:  88% (530/602)
Resolving deltas:  89% (536/602)
Resolving deltas:  90% (542/602)
Resolving deltas:  91% (548/602)
Resolving deltas:  92% (554/602)
Resolving deltas:  93% (560/602)
Resolving deltas:  94% (566/602)
Resolving deltas:  95% (573/602)
Resolving deltas:  96% (578/602)
Resolving deltas:  97% (584/602)
Resolving deltas:  98% (590/602)
Resolving deltas:  99% (596/602)
Resolving deltas: 100% (602/602)
Resolving deltas: 100% (602/602), done.
== PIPELINE PREDEPLOY ==
TAG   : DEV000002
EXEC  : execution/machine_list_dev.yml
BRANCH: develop-testes
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/localhost as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with auto plugin: no root 'plugin'
key found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml' is not a valid YAML inventory
plugin config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml with ini plugin: Invalid host
pattern '---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/harness_filestore_upload.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with auto plugin: no root 'plugin'
key found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml' is not a valid YAML inventory
plugin config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with ini plugin:
/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml:5: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with ini plugin:
/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml:5: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml as an inventory source
[WARNING]: Unable to parse /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-
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
        "deployment_ref               = DEV000002",
        "repo_root_resolved           = /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../status/DEV000002",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000002",
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
included: /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/predeploy_per_machine.yml for localhost => (item=sitef-02)
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
ok: [localhost] => (item=/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml)
ok: [localhost] => (item=/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml)
TASK [Selecionar machine_file existente] ***************************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../execution/sitef-02.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 248, 'inode': 6303742, 'dev': 64775, 'nlink': 1, 'atime': 1766600253.8928084, 'mtime': 1766600253.8828082, 'ctime': 1766600253.8828082, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '005e3367129d5aefc77f49fd9fe62b4e58c51cd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3798999667', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../machines/sitef-02.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-02.yml', 'ansible_loop_var': 'item'}) 
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
changed: [localhost -> sitef-02(100.99.57.128)]
TASK [Listar conteúdo de /deploy/scripts (debug)] ******************************
ok: [localhost -> sitef-02(100.99.57.128)]
TASK [Mostrar conteúdo de scripts (debug)] *************************************
ok: [localhost] => {
    "scripts_ls.stdout_lines": [
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts:",
        "total 48",
        "drwxr-xr-x. 3 root root   168 Dec 24 18:17 .",
        "drwxr-xr-x. 4 root root    39 Dec 15 11:37 ..",
        "-rw-r-----. 1 root root 12048 Dec 24 18:07 deploy.txt",
        "-rw-r-----. 1 root root 13686 Dec 24 18:14 init_deploy.log",
        "-rwxr-xr-x. 1 root root  1149 Dec 24 18:17 init_deploy.sh",
        "-rw-r-----. 1 root root   684 Dec 24 17:56 init_parallel.log",
        "-rwxr-xr-x. 1 root root    95 Dec 15 11:37 init_parallel.sh",
        "-rwxr-xr-x. 1 root root   162 Dec 15 11:37 init.sh",
        "drwxr-xr-x. 2 root root    36 Dec 24 18:17 package",
        "-rw-r-----. 1 root root    39 Dec 24 17:56 parallel.txt",
        "",
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package:",
        "total 4",
        "drwxr-xr-x. 2 root root  36 Dec 24 18:17 .",
        "drwxr-xr-x. 3 root root 168 Dec 24 18:17 ..",
        "-rw-r--r--. 1 root root  88 Dec 24 18:17 sitef-core-0.0.1-0.yml"
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
included: /tmp/tmp.1YZSOgOL7J/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
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
ok: [localhost] => (item=dev000002:deploy:pending)
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST) com debug] **************************
ok: [localhost]
TASK [Harness | DEBUG ENV (se falhar)] *****************************************
ok: [localhost]
TASK [Harness | Falhar ENV (com body)] *****************************************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Falha ao criar/garantir folder ENV no Harness.\nhttp_code=400\nbody={\"status\":\"ERROR\",\"code\":\"DUPLICATE_FIELD\",\"message\":\"Try another name, folder with name [dev] already exists in the parent folder [Root].\",\"correlationId\":\"08eaaab2-bbf2-4a25-8627-21f1831e322d\",\"detailedMessage\":null,\"responseMessages\":[{\"code\":\"DUPLICATE_FIELD\",\"level\":\"ERROR\",\"message\":\"Try another name, folder with name [dev] already exists in the parent folder [Root].\",\"exception\":null,\"failureTypes\":[],\"additionalInfo\":{}}],\"metadata\":null}\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=51   changed=10   unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
Command finished with status FAILURE


## predeploy_from_execution.yml

# =====================================================================================
# PIPELINE 1 - PREDEPLOY (a partir do arquivo de execução)
# =====================================================================================

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # -------------------------------
    # Entradas principais
    # -------------------------------
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    # -------------------------------
    # Paths do repositório
    # -------------------------------
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref }}"

    # -------------------------------
    # Nexus (ideal vir de secrets do Harness)
    # -------------------------------
    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

  tasks:
    # ---------------------------------------
    # Resolver File Store sem recursão
    # ---------------------------------------
    - name: "Resolver filestore_env e filestore_base_dir sem recursão"
      ansible.builtin.set_fact:
        filestore_env_resolved: >-
          {{
            (filestore_env | string | trim)
              if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
              else 'dev'
          }}
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir | string | trim)
              if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0)
              else (
                (
                  (filestore_env | string | trim)
                    if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
                    else 'dev'
                ) ~ '/' ~ deployment_ref
              )
          }}

    # ---------------------------------------
    # Mostrar variáveis resolvidas (debug)
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas"
      ansible.builtin.debug:
        msg:
          - "execution_file_name          = {{ execution_file_name }}"
          - "deployment_ref               = {{ deployment_ref }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url               = {{ nexus_base_url }}"

    # ---------------------------------------
    # Criar diretório base de status local
    # ---------------------------------------
    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # ---------------------------------------
    # Carregar arquivo de execução
    # ---------------------------------------
    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root_resolved }}/{{ execution_file_name }}"
        name: execution_cfg

    # ---------------------------------------
    # Validar se há máquinas
    # ---------------------------------------
    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: execution_cfg.machines is not defined or execution_cfg.machines | length == 0

    # ---------------------------------------
    # Executar PREDEPLOY por máquina
    # ---------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"

        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"


## predeploy_per_machine.yml
---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Chamado por predeploy_from_execution.yml
#
# Responsabilidades:
# - Carregar machine + package
# - Mesclar env_vars (machine + package)
# - Preparar diretórios:
#     /opt/SoftwareExpress/sitef-pipeline/deploy
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package
# - Copiar package.yml para /deploy/scripts/package/
# - Baixar components do Nexus para /deploy/<path do componente>
#   (ex: /deploy/package/linux/sitef-core-0.0.2-0.rpm)
# - Copiar scripts do pacote para /deploy/scripts
# - Executar init_parallel.sh na máquina alvo com as env_vars
# - Gravar status.json local com status + log_path
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina atual
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (machine_name | default(item) | default('')) | string | trim
      }}

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root
          | default(repo_root_resolved | default(playbook_dir ~ '/..'))
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório
# -----------------------------------------------------------------------------
- name: "Definir execution_dir do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"

- name: "Definir machines_dir, packages_dir e scripts_dir do repositório"
  ansible.builtin.set_fact:
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

# -----------------------------------------------------------------------------
# 5) Verificar quais candidatos existem
# -----------------------------------------------------------------------------
- name: "Verificar candidatos de arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

# -----------------------------------------------------------------------------
# 6) Selecionar o primeiro arquivo existente
# -----------------------------------------------------------------------------
- name: "Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

# -----------------------------------------------------------------------------
# 7) Falhar se nenhum candidato existir
# -----------------------------------------------------------------------------
- name: "Falhar se arquivo da máquina não existir em nenhum caminho esperado"
  ansible.builtin.fail:
    msg: |
      Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 8) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 9) Validar package definido
# -----------------------------------------------------------------------------
- name: "Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

# -----------------------------------------------------------------------------
# 10) Carregar config do pacote
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 11) Validações mínimas do pacote
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"

# -----------------------------------------------------------------------------
# 12) Calcular env_vars mescladas (machine + package + extras)
# -----------------------------------------------------------------------------
- name: "Calcular env_vars mescladas"
  ansible.builtin.set_fact:
    merged_env_vars: >-
      {{
        (machine_cfg.env_vars | default({}))
        | combine(package_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': machine_cfg.host | default(machine_cfg.ip | default('')),
            'DEPLOYMENT_REF': deployment_ref | default(''),
            'PACKAGE_NAME': machine_cfg.package,
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default('')
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 13) Usuário/SSH defaults
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

# -----------------------------------------------------------------------------
# 14) Registrar host dinâmico (forçando conexão SSH)
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default(machine_cfg.host | default(''))) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
    ansible_connection: ssh
  changed_when: true

# -----------------------------------------------------------------------------
# 14.1) Debug: identificar hostname/IP da máquina alvo
# -----------------------------------------------------------------------------
- name: "Mostrar hostname/IP da máquina alvo (debug)"
  ansible.builtin.shell: "hostname -I && hostname"
  delegate_to: "{{ current_machine }}"
  register: target_ident
  changed_when: false

- name: "Debug target_ident"
  ansible.builtin.debug:
    msg:
      - "current_machine = {{ current_machine }}"
      - "machine_cfg.host = {{ machine_cfg.host | default('') }}"
      - "machine_cfg.ip   = {{ machine_cfg.ip | default('') }}"
      - "hostname/ip alvo:"
      - "{{ target_ident.stdout_lines }}"

# -----------------------------------------------------------------------------
# 15) Definir caminhos de status e deploy (BASE = /opt/.../deploy)
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e deploy"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    machine_status_file: "{{ status_dir }}/{{ current_machine }}/status.json"

    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/components"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 16) Calcular caminhos de status/log "lógicos"
# -----------------------------------------------------------------------------
- name: "Calcular caminhos de status/log"
  ansible.builtin.set_fact:
    filestore_log_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log
    filestore_status_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.json

# -----------------------------------------------------------------------------
# 17) Criar diretório de status local e registrar status 'pre-deploy:queued'
# -----------------------------------------------------------------------------
- name: "Criar diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (pre-deploy:queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "pre-deploy:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 18) Garantir base do pipeline no host remoto
# -----------------------------------------------------------------------------
- name: "Garantir base do pipeline no host"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"
  loop:
    - "{{ deploy_base_dir }}"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 19) Limpar pasta de scripts do pacote
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: absent
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Recriar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 20) Copiar package.yml para /deploy/scripts/package/
# -----------------------------------------------------------------------------
- name: "Copiar package.yml para o host"
  ansible.builtin.copy:
    src: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    dest: "{{ deploy_scripts_package_dir }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 21) Garantir diretórios dos componentes + baixar do Nexus
# -----------------------------------------------------------------------------
- name: "Garantir diretórios dos componentes no host"
  ansible.builtin.file:
    path: "{{ deploy_base_dir }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Baixar componentes do Nexus"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url.rstrip('/') }}/{{ item }}"
    dest: "{{ deploy_base_dir }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 22) Copiar scripts do pacote para /deploy/scripts
# -----------------------------------------------------------------------------
- name: "Copiar scripts do pacote para o host"
  ansible.builtin.copy:
    src: "{{ scripts_root_dir }}/{{ package_cfg.script }}/"
    dest: "{{ deploy_scripts_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# Debug só para conferência
- name: "Listar conteúdo de /deploy/scripts (debug)"
  ansible.builtin.shell: |
    ls -Ral "{{ deploy_scripts_dir }}" || echo "diretório vazio"
  become: true
  delegate_to: "{{ current_machine }}"
  register: scripts_ls
  changed_when: false

- name: "Mostrar conteúdo de scripts (debug)"
  ansible.builtin.debug:
    var: scripts_ls.stdout_lines

# -----------------------------------------------------------------------------
# 23) Executar init_parallel.sh na máquina alvo
# -----------------------------------------------------------------------------
- name: "Executar init_parallel.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ deploy_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_parallel.sh 2>&1 | tee -a "{{ deploy_scripts_dir }}/init_parallel.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  changed_when: true

- name: "Mostrar últimas linhas do log do predeploy (sempre)"
  ansible.builtin.shell: |
    echo "===== tail -n 300 {{ deploy_scripts_dir }}/init_parallel.log ====="
    tail -n 300 "{{ deploy_scripts_dir }}/init_parallel.log" 2>/dev/null || echo "log não encontrado"
  become: true
  delegate_to: "{{ current_machine }}"
  register: predeploy_tail
  changed_when: false

- name: "DEBUG predeploy tail"
  ansible.builtin.debug:
    var: predeploy_tail.stdout_lines

# -----------------------------------------------------------------------------
# 24) Atualizar status para pre-deploy:ok ou pre-deploy:error
# -----------------------------------------------------------------------------
- name: "Atualizar status após execução do init_parallel.sh"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'pre-deploy:ok' if init_parallel_result.rc == 0 else 'pre-deploy:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

- name: "Upload File Store (predeploy) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    log_content: |
      ---- pipeline pre deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=predeploy
      rc={{ init_parallel_result.rc | default(1) }}

      ---- stdout ----
      {{ init_parallel_result.stdout | default('') }}

      ---- stderr ----
      {{ init_parallel_result.stderr | default('') }}
      ---- pipeline pre deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':pre-deploy:' ~ ('ok' if init_parallel_result.rc == 0 else 'error') }}"
    extra_tags: "{{ [ (deployment_ref | lower) ~ ':deploy:pending' ] if init_parallel_result.rc == 0 else [] }}"

- name: "Falhar se init_parallel.sh retornou erro (DEPOIS do upload)"
  ansible.builtin.fail:
    msg: "init_parallel.sh falhou na máquina {{ current_machine }} com rc={{ init_parallel_result.rc }}"
  when: init_parallel_result.rc != 0
