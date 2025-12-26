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
Clonando repo em /tmp/tmp.Om23rBMUBQ...
Cloning into '/tmp/tmp.Om23rBMUBQ/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1187, done.
remote: Counting objects:   0% (1/1137)
remote: Counting objects:   1% (12/1137)
remote: Counting objects:   2% (23/1137)
remote: Counting objects:   3% (35/1137)
remote: Counting objects:   4% (46/1137)
remote: Counting objects:   5% (57/1137)
remote: Counting objects:   6% (69/1137)
remote: Counting objects:   7% (80/1137)
remote: Counting objects:   8% (91/1137)
remote: Counting objects:   9% (103/1137)
remote: Counting objects:  10% (114/1137)
remote: Counting objects:  11% (126/1137)
remote: Counting objects:  12% (137/1137)
remote: Counting objects:  13% (148/1137)
remote: Counting objects:  14% (160/1137)
remote: Counting objects:  15% (171/1137)
remote: Counting objects:  16% (182/1137)
remote: Counting objects:  17% (194/1137)
remote: Counting objects:  18% (205/1137)
remote: Counting objects:  19% (217/1137)
remote: Counting objects:  20% (228/1137)
remote: Counting objects:  21% (239/1137)
remote: Counting objects:  22% (251/1137)
remote: Counting objects:  23% (262/1137)
remote: Counting objects:  24% (273/1137)
remote: Counting objects:  25% (285/1137)
remote: Counting objects:  26% (296/1137)
remote: Counting objects:  27% (307/1137)
remote: Counting objects:  28% (319/1137)
remote: Counting objects:  29% (330/1137)
remote: Counting objects:  30% (342/1137)
remote: Counting objects:  31% (353/1137)
remote: Counting objects:  32% (364/1137)
remote: Counting objects:  33% (376/1137)
remote: Counting objects:  34% (387/1137)
remote: Counting objects:  35% (398/1137)
remote: Counting objects:  36% (410/1137)
remote: Counting objects:  37% (421/1137)
remote: Counting objects:  38% (433/1137)
remote: Counting objects:  39% (444/1137)
remote: Counting objects:  40% (455/1137)
remote: Counting objects:  41% (467/1137)
remote: Counting objects:  42% (478/1137)
remote: Counting objects:  43% (489/1137)
remote: Counting objects:  44% (501/1137)
remote: Counting objects:  45% (512/1137)
remote: Counting objects:  46% (524/1137)
remote: Counting objects:  47% (535/1137)
remote: Counting objects:  48% (546/1137)
remote: Counting objects:  49% (558/1137)
remote: Counting objects:  50% (569/1137)
remote: Counting objects:  51% (580/1137)
remote: Counting objects:  52% (592/1137)
remote: Counting objects:  53% (603/1137)
remote: Counting objects:  54% (614/1137)
remote: Counting objects:  55% (626/1137)
remote: Counting objects:  56% (637/1137)
remote: Counting objects:  57% (649/1137)
remote: Counting objects:  58% (660/1137)
remote: Counting objects:  59% (671/1137)
remote: Counting objects:  60% (683/1137)
remote: Counting objects:  61% (694/1137)
remote: Counting objects:  62% (705/1137)
remote: Counting objects:  63% (717/1137)
remote: Counting objects:  64% (728/1137)
remote: Counting objects:  65% (740/1137)
remote: Counting objects:  66% (751/1137)
remote: Counting objects:  67% (762/1137)
remote: Counting objects:  68% (774/1137)
remote: Counting objects:  69% (785/1137)
remote: Counting objects:  70% (796/1137)
remote: Counting objects:  71% (808/1137)
remote: Counting objects:  72% (819/1137)
remote: Counting objects:  73% (831/1137)
remote: Counting objects:  74% (842/1137)
remote: Counting objects:  75% (853/1137)
remote: Counting objects:  76% (865/1137)
remote: Counting objects:  77% (876/1137)
remote: Counting objects:  78% (887/1137)
remote: Counting objects:  79% (899/1137)
remote: Counting objects:  80% (910/1137)
remote: Counting objects:  81% (921/1137)
remote: Counting objects:  82% (933/1137)
remote: Counting objects:  83% (944/1137)
remote: Counting objects:  84% (956/1137)
remote: Counting objects:  85% (967/1137)
remote: Counting objects:  86% (978/1137)
remote: Counting objects:  87% (990/1137)
remote: Counting objects:  88% (1001/1137)
remote: Counting objects:  89% (1012/1137)
remote: Counting objects:  90% (1024/1137)
remote: Counting objects:  91% (1035/1137)
remote: Counting objects:  92% (1047/1137)
remote: Counting objects:  93% (1058/1137)
remote: Counting objects:  94% (1069/1137)
remote: Counting objects:  95% (1081/1137)
remote: Counting objects:  96% (1092/1137)
remote: Counting objects:  97% (1103/1137)
remote: Counting objects:  98% (1115/1137)
remote: Counting objects:  99% (1126/1137)
remote: Counting objects: 100% (1137/1137)
remote: Counting objects: 100% (1137/1137), done.
remote: Compressing objects:   0% (1/372)
remote: Compressing objects:   1% (4/372)
remote: Compressing objects:   2% (8/372)
remote: Compressing objects:   3% (12/372)
remote: Compressing objects:   4% (15/372)
remote: Compressing objects:   5% (19/372)
remote: Compressing objects:   6% (23/372)
remote: Compressing objects:   7% (27/372)
remote: Compressing objects:   8% (30/372)
remote: Compressing objects:   9% (34/372)
remote: Compressing objects:  10% (38/372)
remote: Compressing objects:  11% (41/372)
remote: Compressing objects:  12% (45/372)
remote: Compressing objects:  13% (49/372)
remote: Compressing objects:  14% (53/372)
remote: Compressing objects:  15% (56/372)
remote: Compressing objects:  16% (60/372)
remote: Compressing objects:  17% (64/372)
remote: Compressing objects:  18% (67/372)
remote: Compressing objects:  19% (71/372)
remote: Compressing objects:  20% (75/372)
remote: Compressing objects:  21% (79/372)
remote: Compressing objects:  22% (82/372)
remote: Compressing objects:  23% (86/372)
remote: Compressing objects:  24% (90/372)
remote: Compressing objects:  25% (93/372)
remote: Compressing objects:  26% (97/372)
remote: Compressing objects:  27% (101/372)
remote: Compressing objects:  28% (105/372)
remote: Compressing objects:  29% (108/372)
remote: Compressing objects:  30% (112/372)
remote: Compressing objects:  31% (116/372)
remote: Compressing objects:  32% (120/372)
remote: Compressing objects:  33% (123/372)
remote: Compressing objects:  34% (127/372)
remote: Compressing objects:  35% (131/372)
remote: Compressing objects:  36% (134/372)
remote: Compressing objects:  37% (138/372)
remote: Compressing objects:  38% (142/372)
remote: Compressing objects:  39% (146/372)
remote: Compressing objects:  40% (149/372)
remote: Compressing objects:  41% (153/372)
remote: Compressing objects:  42% (157/372)
remote: Compressing objects:  43% (160/372)
remote: Compressing objects:  44% (164/372)
remote: Compressing objects:  45% (168/372)
remote: Compressing objects:  46% (172/372)
remote: Compressing objects:  47% (175/372)
remote: Compressing objects:  48% (179/372)
remote: Compressing objects:  49% (183/372)
remote: Compressing objects:  50% (186/372)
remote: Compressing objects:  51% (190/372)
remote: Compressing objects:  52% (194/372)
remote: Compressing objects:  53% (198/372)
remote: Compressing objects:  54% (201/372)
remote: Compressing objects:  55% (205/372)
remote: Compressing objects:  56% (209/372)
remote: Compressing objects:  57% (213/372)
remote: Compressing objects:  58% (216/372)
remote: Compressing objects:  59% (220/372)
remote: Compressing objects:  60% (224/372)
remote: Compressing objects:  61% (227/372)
remote: Compressing objects:  62% (231/372)
remote: Compressing objects:  63% (235/372)
remote: Compressing objects:  64% (239/372)
remote: Compressing objects:  65% (242/372)
remote: Compressing objects:  66% (246/372)
remote: Compressing objects:  67% (250/372)
remote: Compressing objects:  68% (253/372)
remote: Compressing objects:  69% (257/372)
remote: Compressing objects:  70% (261/372)
remote: Compressing objects:  71% (265/372)
remote: Compressing objects:  72% (268/372)
remote: Compressing objects:  73% (272/372)
remote: Compressing objects:  74% (276/372)
remote: Compressing objects:  75% (279/372)
remote: Compressing objects:  76% (283/372)
remote: Compressing objects:  77% (287/372)
remote: Compressing objects:  78% (291/372)
remote: Compressing objects:  79% (294/372)
remote: Compressing objects:  80% (298/372)
remote: Compressing objects:  81% (302/372)
remote: Compressing objects:  82% (306/372)
remote: Compressing objects:  83% (309/372)
remote: Compressing objects:  84% (313/372)
remote: Compressing objects:  85% (317/372)
remote: Compressing objects:  86% (320/372)
remote: Compressing objects:  87% (324/372)
remote: Compressing objects:  88% (328/372)
remote: Compressing objects:  89% (332/372)
remote: Compressing objects:  90% (335/372)
remote: Compressing objects:  91% (339/372)
remote: Compressing objects:  92% (343/372)
remote: Compressing objects:  93% (346/372)
remote: Compressing objects:  94% (350/372)
remote: Compressing objects:  95% (354/372)
remote: Compressing objects:  96% (358/372)
remote: Compressing objects:  97% (361/372)
remote: Compressing objects:  98% (365/372)
remote: Compressing objects:  99% (369/372)
remote: Compressing objects: 100% (372/372)
remote: Compressing objects: 100% (372/372), done.
Receiving objects:   0% (1/1187)
Receiving objects:   1% (12/1187)
Receiving objects:   2% (24/1187)
Receiving objects:   3% (36/1187)
Receiving objects:   4% (48/1187)
Receiving objects:   5% (60/1187)
Receiving objects:   6% (72/1187)
Receiving objects:   7% (84/1187)
Receiving objects:   8% (95/1187)
Receiving objects:   9% (107/1187)
Receiving objects:  10% (119/1187)
Receiving objects:  11% (131/1187)
Receiving objects:  12% (143/1187)
Receiving objects:  13% (155/1187)
Receiving objects:  14% (167/1187)
Receiving objects:  15% (179/1187)
Receiving objects:  16% (190/1187)
Receiving objects:  17% (202/1187)
Receiving objects:  18% (214/1187)
Receiving objects:  19% (226/1187)
Receiving objects:  20% (238/1187)
Receiving objects:  21% (250/1187)
Receiving objects:  22% (262/1187)
Receiving objects:  23% (274/1187)
Receiving objects:  24% (285/1187)
Receiving objects:  25% (297/1187)
Receiving objects:  26% (309/1187)
Receiving objects:  27% (321/1187)
Receiving objects:  28% (333/1187)
Receiving objects:  29% (345/1187)
Receiving objects:  30% (357/1187)
Receiving objects:  31% (368/1187)
Receiving objects:  32% (380/1187)
Receiving objects:  33% (392/1187)
Receiving objects:  34% (404/1187)
Receiving objects:  35% (416/1187)
Receiving objects:  36% (428/1187)
Receiving objects:  37% (440/1187)
Receiving objects:  38% (452/1187)
Receiving objects:  39% (463/1187)
Receiving objects:  40% (475/1187)
Receiving objects:  41% (487/1187)
Receiving objects:  42% (499/1187)
Receiving objects:  43% (511/1187)
Receiving objects:  44% (523/1187)
Receiving objects:  45% (535/1187)
Receiving objects:  46% (547/1187)
Receiving objects:  47% (558/1187)
Receiving objects:  48% (570/1187)
Receiving objects:  49% (582/1187)
Receiving objects:  50% (594/1187)
Receiving objects:  51% (606/1187)
Receiving objects:  52% (618/1187)
Receiving objects:  53% (630/1187)
Receiving objects:  54% (641/1187)
Receiving objects:  55% (653/1187)
Receiving objects:  56% (665/1187)
Receiving objects:  57% (677/1187)
Receiving objects:  58% (689/1187)
Receiving objects:  59% (701/1187)
Receiving objects:  60% (713/1187)
Receiving objects:  61% (725/1187)
Receiving objects:  62% (736/1187)
Receiving objects:  63% (748/1187)
Receiving objects:  64% (760/1187)
Receiving objects:  65% (772/1187)
Receiving objects:  66% (784/1187)
Receiving objects:  67% (796/1187)
Receiving objects:  68% (808/1187)
Receiving objects:  69% (820/1187)
Receiving objects:  70% (831/1187)
Receiving objects:  71% (843/1187)
Receiving objects:  72% (855/1187)
Receiving objects:  73% (867/1187)
Receiving objects:  74% (879/1187)
Receiving objects:  75% (891/1187)
Receiving objects:  76% (903/1187)
Receiving objects:  77% (914/1187)
Receiving objects:  78% (926/1187)
Receiving objects:  79% (938/1187)
Receiving objects:  80% (950/1187)
Receiving objects:  81% (962/1187)
Receiving objects:  82% (974/1187)
Receiving objects:  83% (986/1187)
Receiving objects:  84% (998/1187)
Receiving objects:  85% (1009/1187)
remote: Total 1187 (delta 846), reused 1037 (delta 750), pack-reused 50
Receiving objects:  86% (1021/1187)
Receiving objects:  87% (1033/1187)
Receiving objects:  88% (1045/1187)
Receiving objects:  89% (1057/1187)
Receiving objects:  90% (1069/1187)
Receiving objects:  91% (1081/1187)
Receiving objects:  92% (1093/1187)
Receiving objects:  93% (1104/1187)
Receiving objects:  94% (1116/1187)
Receiving objects:  95% (1128/1187)
Receiving objects:  96% (1140/1187)
Receiving objects:  97% (1152/1187)
Receiving objects:  98% (1164/1187)
Receiving objects:  99% (1176/1187)
Receiving objects: 100% (1187/1187)
Receiving objects: 100% (1187/1187), 997.78 KiB | 1.96 MiB/s, done.
Resolving deltas:   0% (0/865)
Resolving deltas:   1% (9/865)
Resolving deltas:   2% (18/865)
Resolving deltas:   3% (26/865)
Resolving deltas:   4% (35/865)
Resolving deltas:   5% (44/865)
Resolving deltas:   6% (52/865)
Resolving deltas:   7% (61/865)
Resolving deltas:   8% (70/865)
Resolving deltas:   9% (78/865)
Resolving deltas:  10% (87/865)
Resolving deltas:  11% (96/865)
Resolving deltas:  12% (104/865)
Resolving deltas:  13% (113/865)
Resolving deltas:  14% (122/865)
Resolving deltas:  15% (130/865)
Resolving deltas:  16% (139/865)
Resolving deltas:  17% (148/865)
Resolving deltas:  18% (156/865)
Resolving deltas:  19% (165/865)
Resolving deltas:  20% (173/865)
Resolving deltas:  21% (182/865)
Resolving deltas:  22% (191/865)
Resolving deltas:  23% (199/865)
Resolving deltas:  24% (208/865)
Resolving deltas:  25% (217/865)
Resolving deltas:  26% (225/865)
Resolving deltas:  27% (234/865)
Resolving deltas:  28% (243/865)
Resolving deltas:  29% (251/865)
Resolving deltas:  30% (260/865)
Resolving deltas:  31% (269/865)
Resolving deltas:  32% (277/865)
Resolving deltas:  33% (286/865)
Resolving deltas:  34% (295/865)
Resolving deltas:  35% (303/865)
Resolving deltas:  36% (312/865)
Resolving deltas:  37% (321/865)
Resolving deltas:  38% (329/865)
Resolving deltas:  39% (338/865)
Resolving deltas:  40% (346/865)
Resolving deltas:  41% (355/865)
Resolving deltas:  42% (364/865)
Resolving deltas:  43% (372/865)
Resolving deltas:  44% (381/865)
Resolving deltas:  45% (390/865)
Resolving deltas:  46% (398/865)
Resolving deltas:  47% (407/865)
Resolving deltas:  48% (416/865)
Resolving deltas:  49% (424/865)
Resolving deltas:  50% (433/865)
Resolving deltas:  51% (442/865)
Resolving deltas:  52% (450/865)
Resolving deltas:  53% (459/865)
Resolving deltas:  54% (468/865)
Resolving deltas:  55% (476/865)
Resolving deltas:  56% (485/865)
Resolving deltas:  57% (494/865)
Resolving deltas:  58% (502/865)
Resolving deltas:  59% (511/865)
Resolving deltas:  60% (519/865)
Resolving deltas:  61% (528/865)
Resolving deltas:  62% (537/865)
Resolving deltas:  63% (545/865)
Resolving deltas:  64% (554/865)
Resolving deltas:  65% (563/865)
Resolving deltas:  66% (571/865)
Resolving deltas:  67% (580/865)
Resolving deltas:  68% (589/865)
Resolving deltas:  69% (597/865)
Resolving deltas:  70% (606/865)
Resolving deltas:  71% (615/865)
Resolving deltas:  72% (623/865)
Resolving deltas:  73% (632/865)
Resolving deltas:  74% (641/865)
Resolving deltas:  75% (649/865)
Resolving deltas:  76% (658/865)
Resolving deltas:  77% (667/865)
Resolving deltas:  78% (675/865)
Resolving deltas:  79% (684/865)
Resolving deltas:  80% (692/865)
Resolving deltas:  81% (701/865)
Resolving deltas:  82% (710/865)
Resolving deltas:  83% (718/865)
Resolving deltas:  84% (727/865)
Resolving deltas:  85% (736/865)
Resolving deltas:  86% (744/865)
Resolving deltas:  87% (753/865)
Resolving deltas:  88% (762/865)
Resolving deltas:  89% (770/865)
Resolving deltas:  90% (779/865)
Resolving deltas:  91% (788/865)
Resolving deltas:  92% (796/865)
Resolving deltas:  93% (805/865)
Resolving deltas:  94% (814/865)
Resolving deltas:  95% (822/865)
Resolving deltas:  96% (831/865)
Resolving deltas:  97% (840/865)
Resolving deltas:  98% (848/865)
Resolving deltas:  99% (857/865)
Resolving deltas: 100% (865/865)
Resolving deltas: 100% (865/865), done.
====================================================
== DEPLOY -> sitef-02
====================================================
PLAY [Deploy/Rollback por máquina] *********************************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Normalizar deploy_action (deploy/rollback)] ******************************
ok: [localhost]
TASK [Resolver filestore_env e filestore_base_dir (deploy/rollback)] ***********
fatal: [localhost]: FAILED! => {"msg": "An unhandled exception occurred while templating '{{\n  (filestore_env | default('', true) | string | trim)\n    if (filestore_env is defined and (filestore_env | string | trim) | length > 0\n    else (lookup('ansible.builtin.env','FILESTORE_ENV') | default('dev', true) | string | trim)\n}}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: template error while templating string: expected token ')', got 'else'. String: {{\n  (filestore_env | default('', true) | string | trim)\n    if (filestore_env is defined and (filestore_env | string | trim) | length > 0\n    else (lookup('ansible.builtin.env','FILESTORE_ENV') | default('dev', true) | string | trim)\n}}. expected token ')', got 'else'"}
PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
Command finished with status FAILURE


---
- name: "Deploy/Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # =====================================================================
    # INPUTS (preferência: vars > env > default)
    # =====================================================================
    deployment_ref: >-
      {{
        (deployment_ref | default('', true) | string | trim)
          if (deployment_ref is defined and (deployment_ref | string | trim) | length > 0)
          else (lookup('ansible.builtin.env','GIT_TAG') | default('DEV000000001', true) | string | trim)
      }}

    machine_name: >-
      {{
        (machine_name | default('', true) | string | trim)
          if (machine_name is defined and (machine_name | string | trim) | length > 0)
          else (
            (lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim)
              if (lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim) | length > 0
              else (lookup('ansible.builtin.env','MACHINES') | default('', true) | string | trim)
          )
      }}

    deploy_action: >-
      {{
        (deploy_action | default('', true) | string | trim)
          if (deploy_action is defined and (deploy_action | string | trim) | length > 0)
          else (
            (lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim)
              if (lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim) | length > 0
              else (lookup('ansible.builtin.env','STRATEGY') | default('deploy', true) | string | trim)
          )
      }}

    # Paths do repositório
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref }}"

    # File Store (preferência: vars > env > default)
    filestore_env_input: >-
      {{
        (filestore_env | default('', true) | string | trim)
          if (filestore_env is defined and (filestore_env | string | trim) | length > 0
          else (lookup('ansible.builtin.env','FILESTORE_ENV') | default('dev', true) | string | trim)
      }}

    filestore_base_dir_input: >-
      {{
        (filestore_base_dir | default('', true) | string | trim)
          if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0
          else (lookup('ansible.builtin.env','FILESTORE_BASEDIR') | default('', true) | string | trim)
      }}

    # Nexus default (evita recursão)
    nexus_base_url_default: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"

  tasks:
    # -------------------------------------------------------------------
    # Normalizar deploy_action (deploy/rollback)
    # -------------------------------------------------------------------
    - name: "Normalizar deploy_action (deploy/rollback)"
      ansible.builtin.set_fact:
        deploy_action_resolved: >-
          {{
            (deploy_action | string | lower | trim)
              if (deploy_action is defined and (deploy_action | string | trim) | length > 0)
              else 'deploy'
          }}

    # -------------------------------------------------------------------
    # Resolver filestore_env e filestore_base_dir sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver filestore_env e filestore_base_dir (deploy/rollback)"
      ansible.builtin.set_fact:
        filestore_env_resolved: "{{ filestore_env_input | default('dev', true) | string | trim }}"
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir_input | string | trim)
              if (filestore_base_dir_input | string | trim) | length > 0
              else ((filestore_env_input | default('dev', true) | string | trim) ~ '/' ~ deployment_ref)
          }}

    # -------------------------------------------------------------------
    # Resolver Nexus (base_url, user, password) sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver Nexus (base_url, user, password)"
      ansible.builtin.set_fact:
        nexus_base_url_resolved: >-
          {{
            (nexus_base_url | default('', true) | string | trim)
              if (nexus_base_url is defined and (nexus_base_url | string | trim) | length > 0)
              else nexus_base_url_default
          }}
        nexus_user_resolved: "{{ (nexus_user | default('', true) | string | trim) if (nexus_user is defined) else '' }}"
        nexus_password_resolved: "{{ (nexus_password | default('', true) | string | trim) if (nexus_password is defined) else '' }}"

    # -------------------------------------------------------------------
    # Debug das principais variáveis (sem vazar segredo)
    # -------------------------------------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas (deploy/rollback)"
      ansible.builtin.debug:
        msg:
          - "deployment_ref               = {{ deployment_ref }}"
          - "machine_name                 = {{ machine_name }}"
          - "deploy_action                = {{ deploy_action }}"
          - "deploy_action_resolved       = {{ deploy_action_resolved }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
          - "nexus_user_resolved          = {{ '***' if (nexus_user_resolved | length) > 0 else '(vazio)' }}"

    # -------------------------------------------------------------------
    # Garantir diretório base de status da TAG
    # -------------------------------------------------------------------
    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # -------------------------------------------------------------------
    # Validar machine_name
    # -------------------------------------------------------------------
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - (machine_name | string | trim) | length > 0
        fail_msg: "machine_name não informado (vars ou env MACHINE/MACHINES)"

    # -------------------------------------------------------------------
    # Branch de DEPLOY
    # -------------------------------------------------------------------
    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      when: deploy_action_resolved == 'deploy'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "deploy"

    # -------------------------------------------------------------------
    # Branch de ROLLBACK (mantém compatível com seu padrão)
    # -------------------------------------------------------------------
    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      when: deploy_action_resolved == 'rollback'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "rollback"
