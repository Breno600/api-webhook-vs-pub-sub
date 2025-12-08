ansible: 
# PIPELINE 2 - DEPLOY (PARTE PESADA)
# - Lê status/<TAG>/predeploy-*.json
# - Extrai máquinas aptas
# - Para cada máquina chama deploy_per_machine.yml
# - Cada máquina ganha um JSON em status/<TAG>/deploy-<machine>.json

- name: "Deploy a partir do status do predeploy"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:
    - name: "Resolver defaults de pacote (sem recursão)"
      ansible.builtin.set_fact:
        package_name_eff: "{{ vars.get('package_name', 'sitef-core') }}"
        package_version_eff: "{{ vars.get('package_version', '0.0.2-0') }}"

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "deployment_ref      = {{ deployment_ref }}"
          - "repo_root           = {{ repo_root }}"
          - "status_dir          = {{ status_dir }}"
          - "package_name_eff    = {{ package_name_eff }}"
          - "package_version_eff = {{ package_version_eff }}"

    - name: "Garantir que o diretório de status exista"
      ansible.builtin.stat:
        path: "{{ status_dir }}"
      register: status_dir_stat

    - name: "Falhar se status_dir não existir"
      ansible.builtin.fail:
        msg: "Diretório de status não encontrado: {{ status_dir }}. Rode o predeploy primeiro."
      when: not status_dir_stat.stat.exists

    - name: "Listar arquivos de predeploy da TAG"
      ansible.builtin.find:
        paths: "{{ status_dir }}"
        patterns: "predeploy-*.json"
        file_type: file
      register: predeploy_files

    - name: "Falhar se não houver predeploy-*.json"
      ansible.builtin.fail:
        msg: "Nenhum arquivo predeploy-*.json encontrado em {{ status_dir }}"
      when: predeploy_files.matched | int == 0

    - name: "Carregar JSONs de predeploy"
      ansible.builtin.slurp:
        src: "{{ item.path }}"
      loop: "{{ predeploy_files.files }}"
      register: predeploy_slurped

    - name: "Montar lista de predeploys parseados"
      ansible.builtin.set_fact:
        predeploy_jsons: "{{ predeploy_jsons | default([]) + [ (item.content | b64decode | from_json) ] }}"
      loop: "{{ predeploy_slurped.results }}"

    - name: "Inicializar lista de máquinas para deploy"
      ansible.builtin.set_fact:
        machines_to_deploy: []

    - name: "Montar lista de máquinas com predeploy success (machine ou machine_name)"
      ansible.builtin.set_fact:
        machines_to_deploy: "{{ machines_to_deploy + [ (item.machine_name | default(item.machine)) ] }}"
      loop: "{{ predeploy_jsons }}"
      when:
        - item.status is defined
        - item.status == "success"


    - name: "Falhar se nenhuma máquina estiver apta para deploy"
      ansible.builtin.fail:
        msg: "Nenhuma máquina com status=success no predeploy para a TAG {{ deployment_ref }}"
      when: machines_to_deploy | length == 0

    - name: "Exibir máquinas aptas para deploy"
      ansible.builtin.debug:
        var: machines_to_deploy

    - name: "Executar deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      loop: "{{ machines_to_deploy }}"
      loop_control:
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        package_name: "{{ package_name_eff }}"
        package_version: "{{ package_version_eff }}"


logs: 
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
Clonando repo em /tmp/tmp.tFdpGTqYJW...
Cloning into '/tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 495, done.
remote: Counting objects:   0% (1/445)
remote: Counting objects:   1% (5/445)
remote: Counting objects:   2% (9/445)
remote: Counting objects:   3% (14/445)
remote: Counting objects:   4% (18/445)
remote: Counting objects:   5% (23/445)
remote: Counting objects:   6% (27/445)
remote: Counting objects:   7% (32/445)
remote: Counting objects:   8% (36/445)
remote: Counting objects:   9% (41/445)
remote: Counting objects:  10% (45/445)
remote: Counting objects:  11% (49/445)
remote: Counting objects:  12% (54/445)
remote: Counting objects:  13% (58/445)
remote: Counting objects:  14% (63/445)
remote: Counting objects:  15% (67/445)
remote: Counting objects:  16% (72/445)
remote: Counting objects:  17% (76/445)
remote: Counting objects:  18% (81/445)
remote: Counting objects:  48% (214/445)
remote: Counting objects:  19% (85/445)
remote: Counting objects:  20% (89/445)
remote: Counting objects:  21% (94/445)
remote: Counting objects:  22% (98/445)
remote: Counting objects:  23% (103/445)
remote: Counting objects:  24% (107/445)
remote: Counting objects:  25% (112/445)
remote: Counting objects:  26% (116/445)
remote: Counting objects:  27% (121/445)
remote: Counting objects:  28% (125/445)
remote: Counting objects:  29% (130/445)
remote: Counting objects:  30% (134/445)
remote: Counting objects:  31% (138/445)
remote: Counting objects:  32% (143/445)
remote: Counting objects:  33% (147/445)
remote: Counting objects:  34% (152/445)
remote: Counting objects:  35% (156/445)
remote: Counting objects:  36% (161/445)
remote: Counting objects:  37% (165/445)
remote: Counting objects:  38% (170/445)
remote: Counting objects:  39% (174/445)
remote: Counting objects:  40% (178/445)
remote: Counting objects:  41% (183/445)
remote: Counting objects:  42% (187/445)
remote: Counting objects:  43% (192/445)
remote: Counting objects:  44% (196/445)
remote: Counting objects:  45% (201/445)
remote: Counting objects:  46% (205/445)
remote: Counting objects:  47% (210/445)
remote: Counting objects:  49% (219/445)
remote: Counting objects:  50% (223/445)
remote: Counting objects:  51% (227/445)
remote: Counting objects:  52% (232/445)
remote: Counting objects:  53% (236/445)
remote: Counting objects:  54% (241/445)
remote: Counting objects:  55% (245/445)
remote: Counting objects:  56% (250/445)
remote: Counting objects:  57% (254/445)
remote: Counting objects:  58% (259/445)
remote: Counting objects:  59% (263/445)
remote: Counting objects:  60% (267/445)
remote: Counting objects:  61% (272/445)
remote: Counting objects:  62% (276/445)
remote: Counting objects:  63% (281/445)
remote: Counting objects:  64% (285/445)
remote: Counting objects:  65% (290/445)
remote: Counting objects:  66% (294/445)
remote: Counting objects:  67% (299/445)
remote: Counting objects:  68% (303/445)
remote: Counting objects:  69% (308/445)
remote: Counting objects:  70% (312/445)
remote: Counting objects:  71% (316/445)
remote: Counting objects:  72% (321/445)
remote: Counting objects:  73% (325/445)
remote: Counting objects:  74% (330/445)
remote: Counting objects:  75% (334/445)
remote: Counting objects:  76% (339/445)
remote: Counting objects:  77% (343/445)
remote: Counting objects:  78% (348/445)
remote: Counting objects:  79% (352/445)
remote: Counting objects:  80% (356/445)
remote: Counting objects:  81% (361/445)
remote: Counting objects:  82% (365/445)
remote: Counting objects:  83% (370/445)
remote: Counting objects:  84% (374/445)
remote: Counting objects:  85% (379/445)
remote: Counting objects:  86% (383/445)
remote: Counting objects:  87% (388/445)
remote: Counting objects:  88% (392/445)
remote: Counting objects:  89% (397/445)
remote: Counting objects:  90% (401/445)
remote: Counting objects:  91% (405/445)
remote: Counting objects:  92% (410/445)
remote: Counting objects:  93% (414/445)
remote: Counting objects:  94% (419/445)
remote: Counting objects:  95% (423/445)
remote: Counting objects:  96% (428/445)
remote: Counting objects:  97% (432/445)
remote: Counting objects:  98% (437/445)
remote: Counting objects:  99% (441/445)
remote: Counting objects: 100% (445/445)
remote: Counting objects: 100% (445/445), done.
remote: Compressing objects:   0% (1/432)
remote: Compressing objects:   1% (5/432)
remote: Compressing objects:   2% (9/432)
remote: Compressing objects:   3% (13/432)
remote: Compressing objects:   4% (18/432)
remote: Compressing objects:   5% (22/432)
remote: Compressing objects:   6% (26/432)
remote: Compressing objects:   7% (31/432)
remote: Compressing objects:   8% (35/432)
remote: Compressing objects:   9% (39/432)
remote: Compressing objects:  10% (44/432)
remote: Compressing objects:  11% (48/432)
remote: Compressing objects:  12% (52/432)
remote: Compressing objects:  13% (57/432)
remote: Compressing objects:  14% (61/432)
remote: Compressing objects:  15% (65/432)
remote: Compressing objects:  16% (70/432)
remote: Compressing objects:  17% (74/432)
remote: Compressing objects:  18% (78/432)
remote: Compressing objects:  19% (83/432)
remote: Compressing objects:  20% (87/432)
remote: Compressing objects:  21% (91/432)
remote: Compressing objects:  22% (96/432)
remote: Compressing objects:  23% (100/432)
remote: Compressing objects:  24% (104/432)
remote: Compressing objects:  25% (108/432)
remote: Compressing objects:  26% (113/432)
remote: Compressing objects:  27% (117/432)
remote: Compressing objects:  28% (121/432)
remote: Compressing objects:  29% (126/432)
remote: Compressing objects:  30% (130/432)
remote: Compressing objects:  31% (134/432)
remote: Compressing objects:  32% (139/432)
remote: Compressing objects:  33% (143/432)
remote: Compressing objects:  34% (147/432)
remote: Compressing objects:  35% (152/432)
remote: Compressing objects:  36% (156/432)
remote: Compressing objects:  37% (160/432)
remote: Compressing objects:  38% (165/432)
remote: Compressing objects:  39% (169/432)
remote: Compressing objects:  40% (173/432)
remote: Compressing objects:  41% (178/432)
remote: Compressing objects:  42% (182/432)
remote: Compressing objects:  43% (186/432)
remote: Compressing objects:  44% (191/432)
remote: Compressing objects:  45% (195/432)
remote: Compressing objects:  46% (199/432)
remote: Compressing objects:  47% (204/432)
remote: Compressing objects:  48% (208/432)
remote: Compressing objects:  49% (212/432)
remote: Compressing objects:  50% (216/432)
remote: Compressing objects:  51% (221/432)
remote: Compressing objects:  52% (225/432)
remote: Compressing objects:  53% (229/432)
remote: Compressing objects:  54% (234/432)
remote: Compressing objects:  55% (238/432)
remote: Compressing objects:  56% (242/432)
remote: Compressing objects:  57% (247/432)
remote: Compressing objects:  58% (251/432)
remote: Compressing objects:  59% (255/432)
remote: Compressing objects:  60% (260/432)
remote: Compressing objects:  61% (264/432)
remote: Compressing objects:  62% (268/432)
remote: Compressing objects:  63% (273/432)
remote: Compressing objects:  64% (277/432)
remote: Compressing objects:  65% (281/432)
remote: Compressing objects:  66% (286/432)
remote: Compressing objects:  67% (290/432)
remote: Compressing objects:  68% (294/432)
remote: Compressing objects:  69% (299/432)
remote: Compressing objects:  70% (303/432)
remote: Compressing objects:  71% (307/432)
remote: Compressing objects:  72% (312/432)
remote: Compressing objects:  73% (316/432)
remote: Compressing objects:  74% (320/432)
remote: Compressing objects:  75% (324/432)
remote: Compressing objects:  76% (329/432)
remote: Compressing objects:  77% (333/432)
remote: Compressing objects:  78% (337/432)
remote: Compressing objects:  79% (342/432)
remote: Compressing objects:  80% (346/432)
remote: Compressing objects:  81% (350/432)
remote: Compressing objects:  82% (355/432)
remote: Compressing objects:  83% (359/432)
remote: Compressing objects:  84% (363/432)
remote: Compressing objects:  85% (368/432)
remote: Compressing objects:  86% (372/432)
remote: Compressing objects:  87% (376/432)
remote: Compressing objects:  88% (381/432)
remote: Compressing objects:  89% (385/432)
remote: Compressing objects:  90% (389/432)
remote: Compressing objects:  91% (394/432)
remote: Compressing objects:  92% (398/432)
remote: Compressing objects:  93% (402/432)
remote: Compressing objects:  94% (407/432)
remote: Compressing objects:  95% (411/432)
remote: Compressing objects:  96% (415/432)
remote: Compressing objects:  97% (420/432)
remote: Compressing objects:  98% (424/432)
remote: Compressing objects:  99% (428/432)
remote: Compressing objects: 100% (432/432)
remote: Compressing objects: 100% (432/432), done.
remote: Total 495 (delta 254), reused 0 (delta 0), pack-reused 50
Receiving objects:   0% (1/495)
Receiving objects:   1% (5/495)
Receiving objects:   2% (10/495)
Receiving objects:   3% (15/495)
Receiving objects:   4% (20/495)
Receiving objects:   5% (25/495)
Receiving objects:   6% (30/495)
Receiving objects:   7% (35/495)
Receiving objects:   8% (40/495)
Receiving objects:   9% (45/495)
Receiving objects:  10% (50/495)
Receiving objects:  11% (55/495)
Receiving objects:  12% (60/495)
Receiving objects:  13% (65/495)
Receiving objects:  14% (70/495)
Receiving objects:  15% (75/495)
Receiving objects:  16% (80/495)
Receiving objects:  17% (85/495)
Receiving objects:  18% (90/495)
Receiving objects:  19% (95/495)
Receiving objects:  20% (99/495)
Receiving objects:  21% (104/495)
Receiving objects:  22% (109/495)
Receiving objects:  23% (114/495)
Receiving objects:  24% (119/495)
Receiving objects:  25% (124/495)
Receiving objects:  26% (129/495)
Receiving objects:  27% (134/495)
Receiving objects:  28% (139/495)
Receiving objects:  29% (144/495)
Receiving objects:  30% (149/495)
Receiving objects:  31% (154/495)
Receiving objects:  32% (159/495)
Receiving objects:  33% (164/495)
Receiving objects:  34% (169/495)
Receiving objects:  35% (174/495)
Receiving objects:  36% (179/495)
Receiving objects:  37% (184/495)
Receiving objects:  38% (189/495)
Receiving objects:  39% (194/495)
Receiving objects:  40% (198/495)
Receiving objects:  41% (203/495)
Receiving objects:  42% (208/495)
Receiving objects:  43% (213/495)
Receiving objects:  44% (218/495)
Receiving objects:  45% (223/495)
Receiving objects:  46% (228/495)
Receiving objects:  47% (233/495)
Receiving objects:  48% (238/495)
Receiving objects:  49% (243/495)
Receiving objects:  50% (248/495)
Receiving objects:  51% (253/495)
Receiving objects:  52% (258/495)
Receiving objects:  53% (263/495)
Receiving objects:  54% (268/495)
Receiving objects:  55% (273/495)
Receiving objects:  56% (278/495)
Receiving objects:  57% (283/495)
Receiving objects:  58% (288/495)
Receiving objects:  59% (293/495)
Receiving objects:  60% (297/495)
Receiving objects:  61% (302/495)
Receiving objects:  62% (307/495)
Receiving objects:  63% (312/495)
Receiving objects:  64% (317/495)
Receiving objects:  65% (322/495)
Receiving objects:  66% (327/495)
Receiving objects:  67% (332/495)
Receiving objects:  68% (337/495)
Receiving objects:  69% (342/495)
Receiving objects:  70% (347/495)
Receiving objects:  71% (352/495)
Receiving objects:  72% (357/495)
Receiving objects:  73% (362/495)
Receiving objects:  74% (367/495)
Receiving objects:  75% (372/495)
Receiving objects:  76% (377/495)
Receiving objects:  77% (382/495)
Receiving objects:  78% (387/495)
Receiving objects:  79% (392/495)
Receiving objects:  80% (396/495)
Receiving objects:  81% (401/495)
Receiving objects:  82% (406/495)
Receiving objects:  83% (411/495)
Receiving objects:  84% (416/495)
Receiving objects:  85% (421/495)
Receiving objects:  86% (426/495)
Receiving objects:  87% (431/495)
Receiving objects:  88% (436/495)
Receiving objects:  89% (441/495)
Receiving objects:  90% (446/495)
Receiving objects:  91% (451/495)
Receiving objects:  92% (456/495)
Receiving objects:  93% (461/495)
Receiving objects:  94% (466/495)
Receiving objects:  95% (471/495)
Receiving objects:  96% (476/495)
Receiving objects:  97% (481/495)
Receiving objects:  98% (486/495)
Receiving objects:  99% (491/495)
Receiving objects: 100% (495/495)
Receiving objects: 100% (495/495), 76.59 KiB | 10.94 MiB/s, done.
Resolving deltas:   0% (0/273)
Resolving deltas:   1% (3/273)
Resolving deltas:   2% (6/273)
Resolving deltas:   3% (9/273)
Resolving deltas:   4% (11/273)
Resolving deltas:   5% (14/273)
Resolving deltas:   6% (17/273)
Resolving deltas:   7% (20/273)
Resolving deltas:   8% (22/273)
Resolving deltas:   9% (25/273)
Resolving deltas:  10% (28/273)
Resolving deltas:  11% (31/273)
Resolving deltas:  12% (33/273)
Resolving deltas:  13% (36/273)
Resolving deltas:  14% (39/273)
Resolving deltas:  15% (41/273)
Resolving deltas:  16% (44/273)
Resolving deltas:  17% (47/273)
Resolving deltas:  18% (50/273)
Resolving deltas:  19% (52/273)
Resolving deltas:  20% (55/273)
Resolving deltas:  21% (58/273)
Resolving deltas:  22% (61/273)
Resolving deltas:  23% (63/273)
Resolving deltas:  24% (66/273)
Resolving deltas:  25% (69/273)
Resolving deltas:  26% (71/273)
Resolving deltas:  27% (74/273)
Resolving deltas:  28% (77/273)
Resolving deltas:  29% (80/273)
Resolving deltas:  30% (82/273)
Resolving deltas:  31% (85/273)
Resolving deltas:  32% (88/273)
Resolving deltas:  33% (91/273)
Resolving deltas:  34% (93/273)
Resolving deltas:  35% (96/273)
Resolving deltas:  36% (99/273)
Resolving deltas:  37% (102/273)
Resolving deltas:  38% (104/273)
Resolving deltas:  39% (107/273)
Resolving deltas:  40% (110/273)
Resolving deltas:  41% (112/273)
Resolving deltas:  42% (115/273)
Resolving deltas:  43% (119/273)
Resolving deltas:  44% (121/273)
Resolving deltas:  45% (123/273)
Resolving deltas:  46% (126/273)
Resolving deltas:  47% (129/273)
Resolving deltas:  48% (132/273)
Resolving deltas:  49% (134/273)
Resolving deltas:  50% (137/273)
Resolving deltas:  51% (140/273)
Resolving deltas:  52% (142/273)
Resolving deltas:  53% (145/273)
Resolving deltas:  54% (148/273)
Resolving deltas:  55% (151/273)
Resolving deltas:  56% (153/273)
Resolving deltas:  57% (156/273)
Resolving deltas:  58% (159/273)
Resolving deltas:  59% (162/273)
Resolving deltas:  60% (165/273)
Resolving deltas:  61% (167/273)
Resolving deltas:  62% (170/273)
Resolving deltas:  63% (172/273)
Resolving deltas:  64% (175/273)
Resolving deltas:  65% (178/273)
Resolving deltas:  66% (181/273)
Resolving deltas:  67% (183/273)
Resolving deltas:  68% (186/273)
Resolving deltas:  69% (189/273)
Resolving deltas:  70% (192/273)
Resolving deltas:  71% (194/273)
Resolving deltas:  72% (197/273)
Resolving deltas:  73% (200/273)
Resolving deltas:  74% (203/273)
Resolving deltas:  75% (205/273)
Resolving deltas:  76% (208/273)
Resolving deltas:  77% (211/273)
Resolving deltas:  78% (213/273)
Resolving deltas:  79% (216/273)
Resolving deltas:  80% (219/273)
Resolving deltas:  81% (222/273)
Resolving deltas:  82% (224/273)
Resolving deltas:  83% (227/273)
Resolving deltas:  84% (230/273)
Resolving deltas:  85% (233/273)
Resolving deltas:  86% (235/273)
Resolving deltas:  87% (238/273)
Resolving deltas:  88% (241/273)
Resolving deltas:  89% (243/273)
Resolving deltas:  90% (246/273)
Resolving deltas:  91% (249/273)
Resolving deltas:  92% (252/273)
Resolving deltas:  93% (254/273)
Resolving deltas:  94% (257/273)
Resolving deltas:  95% (260/273)
Resolving deltas:  96% (263/273)
Resolving deltas:  97% (265/273)
Resolving deltas:  98% (268/273)
Resolving deltas:  99% (271/273)
Resolving deltas: 100% (273/273)
Resolving deltas: 100% (273/273), done.
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
        "repo_root           = /tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/ansible/..",
        "status_dir          = /tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/ansible/../status/DEV000000007",
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
ok: [localhost] => (item={'path': '/tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 4328777, 'dev': 64775, 'nlink': 1, 'atime': 1765220392.1570306, 'mtime': 1765220392.1380305, 'ctime': 1765220392.1380305, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False})
TASK [Montar lista de predeploys parseados] ************************************
ok: [localhost] => (item={'content': 'eyJtYWNoaW5lIjogInNpdGVmLTAxIiwgImhvc3QiOiAiMTAwLjk5LjQxLjU4IiwgInBhY2thZ2UiOiAic2l0ZWYtY29yZS0wLjAuMi0wIiwgInJvbGxiYWNrIjogIiIsICJzdGF0dXMiOiAic3VjY2VzcyIsICJkZXBsb3ltZW50X3JlZiI6ICJERVYwMDAwMDAwMDciLCAidGltZXN0YW1wIjogIjIwMjUtMTItMDhUMTg6MTU6NTJaIiwgInN0ZG91dCI6ICIrIGVjaG8gJz09IEluXHUwMGVkY2lvIGluaXRfcGFyYWxsZWwuc2ggPT0nXG49PSBJblx1MDBlZGNpbyBpbml0X3BhcmFsbGVsLnNoID09XG4rIGRhdGVcbk1vbiBEZWMgIDggMDY6MTY6MTMgUE0gVVRDIDIwMjVcbisgc2V0IC14XG4rIGVjaG8gJ0luc3RhbGFjYW8gZGUgZGVwZW5kZW5jaWFzIGRvIHNpc3RlbWEuLi4nXG5JbnN0YWxhY2FvIGRlIGRlcGVuZGVuY2lhcyBkbyBzaXN0ZW1hLi4uXG4rIGRuZiBpbnN0YWxsIC15IC0tbm9kb2NzIHB5dGhvbjMgcHl0aG9uMy1waXBcblVwZGF0aW5nIFN1YnNjcmlwdGlvbiBNYW5hZ2VtZW50IHJlcG9zaXRvcmllcy5cblxuVGhpcyBzeXN0ZW0gaGFzIHJlbGVhc2Ugc2V0IHRvIDkgYW5kIGl0IHJlY2VpdmVzIHVwZGF0ZXMgb25seSBmb3IgdGhpcyByZWxlYXNlLlxuXG5SZWQgSGF0IENvZGVSZWFkeSBMaW51eCBCdWlsZGVyIGZvciBSSEVMIDkgeDg2XyAyLjgga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQXBwU3RyZSA0LjEga0IvcyB8IDQuNSBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IFNhdGVsbGl0ZSBDbGllbnQgNiBmb3IgUkhFTCA5IHg4Nl82NCAoUiAzLjQga0IvcyB8IDMuOCBrQiAgICAgMDA6MDEgICAgXG5SZWQgSGF0IEVudGVycHJpc2UgTGludXggOSBmb3IgeDg2XzY0IC0gQmFzZU9TICAzLjcga0IvcyB8IDQuMSBrQiAgICAgMDA6MDEgICAgXG5FUEVMOV9YODYgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAxLjYga0IvcyB8IDIuMyBrQiAgICAgMDA6MDEgICAgXG5QYWNrYWdlIHB5dGhvbjMtMy45LjIzLTIuZWw5Lng4Nl82NCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cblBhY2thZ2UgcHl0aG9uMy1waXAtMjEuMy4xLTEuZWw5Lm5vYXJjaCBpcyBhbHJlYWR5IGluc3RhbGxlZC5cbkRlcGVuZGVuY2llcyByZXNvbHZlZC5cbk5vdGhpbmcgdG8gZG8uXG5Db21wbGV0ZSFcbisgcHl0aG9uMyAtbSB2ZW52IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisgc291cmNlIC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnYvYmluL2FjdGl2YXRlXG4rKyBkZWFjdGl2YXRlIG5vbmRlc3RydWN0aXZlXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKysgaGFzaCAtclxuKysgJ1snIC1uICcnICddJ1xuKysgdW5zZXQgVklSVFVBTF9FTlZcbisrICdbJyAnIScgbm9uZGVzdHJ1Y3RpdmUgPSBub25kZXN0cnVjdGl2ZSAnXSdcbisrIFZJUlRVQUxfRU5WPS9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL3ZlbnZcbisrIGV4cG9ydCBWSVJUVUFMX0VOVlxuKysgX09MRF9WSVJUVUFMX1BBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIFBBVEg9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvdmVudi9iaW46L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisrIGV4cG9ydCBQQVRIXG4rKyAnWycgLW4gJycgJ10nXG4rKyAnWycgLXogJycgJ10nXG4rKyBfT0xEX1ZJUlRVQUxfUFMxPVxuKysgUFMxPScodmVudikgJ1xuKysgZXhwb3J0IFBTMVxuKysgJ1snIC1uIC9iaW4vYmFzaCAtbyAtbiAnJyAnXSdcbisrIGhhc2ggLXJcbisgZWNobyAnSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uJ1xuSW5zdGFsYWNhbyBkZSBkZXBlbmRlbmNpYXMgZG8gcHl0aG9uLi4uXG4rIHB5dGhvbjMgLXUgc2NyaXB0X3ByZXBhcmUucHlcbkV4ZWN1dGFuZG8gYmFja3VwLi4uXG5GYXplbmRvIGxldmFudGFtZW50byBkZSBhcnF1aXZvcy9wYXN0YXNcblBhdGggZW5jb250cmFkbzogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvY29uZmlnXG5QcmVwYXJhY2FvIGRvIGJhY2t1cDogL29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYtc2V0dXAvYmFja3Vwcy9iYWNrdXAtYmVmb3JlLWRlZmF1bHQudGFyLmd6XG5BcGFnYW5kbyBiYWNrdXA6IC9vcHQvU29mdHdhcmVFeHByZXNzL3NpdGVmLXNldHVwL2JhY2t1cHMvYmFja3VwLWJlZm9yZS1kZWZhdWx0LnRhci5nelxuTWV0YWRhZG9zOiB7J3BhY2thZ2UnOiB7J2JhY2t1cCc6IHsnYmVmb3JlX2luc3RhbGxfdmVyc2lvbic6ICdkZWZhdWx0JywgJ2V4ZWN1dGVkX2F0JzogJzIwMjUtMTItMDggMTg6MTY6MjQnfX19XG5CYWNrdXAgZmluYWxpemFkby5cbisgZWNobyAnc2NyaXB0X3ByZXBhcmUucHkgZmluYWxpemFkbyBjb20gcmM9MCdcbnNjcmlwdF9wcmVwYXJlLnB5IGZpbmFsaXphZG8gY29tIHJjPTBcbisgZGVhY3RpdmF0ZVxuKyAnWycgLW4gL3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW4gJ10nXG4rIFBBVEg9L3NiaW46L2JpbjovdXNyL3NiaW46L3Vzci9iaW5cbisgZXhwb3J0IFBBVEhcbisgdW5zZXQgX09MRF9WSVJUVUFMX1BBVEhcbisgJ1snIC1uICcnICddJ1xuKyAnWycgLW4gL2Jpbi9iYXNoIC1vIC1uICcnICddJ1xuKyBoYXNoIC1yXG4rICdbJyAtbiAnJyAnXSdcbisgdW5zZXQgVklSVFVBTF9FTlZcbisgJ1snICchJyAnJyA9IG5vbmRlc3RydWN0aXZlICddJ1xuKyB1bnNldCAtZiBkZWFjdGl2YXRlXG4rIHNldCAreFxuPT0gRmltIGluaXRfcGFyYWxsZWwuc2ggPT1cbk1vbiBEZWMgIDggMDY6MTY6MjQgUE0gVVRDIDIwMjUiLCAic3RkZXJyIjogIisgc2V0IC1ldW8gcGlwZWZhaWxcbisrKyBkaXJuYW1lIC4vaW5pdF9wYXJhbGxlbC5zaFxuKysgY2QgLlxuKysgcHdkXG4rIFNDUklQVF9ESVI9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjJcbisgTE9HX0ZJTEU9L29wdC9Tb2Z0d2FyZUV4cHJlc3Mvc2l0ZWYvc2NyaXB0cy9kZXBsb3ktc2l0ZWYtMC4wLjIvcGFyYWxsZWwudHh0XG4rIGV4ZWNcbisrIHRlZSAtYSAvb3B0L1NvZnR3YXJlRXhwcmVzcy9zaXRlZi9zY3JpcHRzL2RlcGxveS1zaXRlZi0wLjAuMi9wYXJhbGxlbC50eHQifQ==', 'source': '/tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'encoding': 'base64', 'invocation': {'module_args': {'src': '/tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json'}}, 'failed': False, 'changed': False, 'item': {'path': '/tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/status/DEV000000007/predeploy-sitef-01.json', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 3184, 'inode': 4328777, 'dev': 64775, 'nlink': 1, 'atime': 1765220392.1570306, 'mtime': 1765220392.1380305, 'ctime': 1765220392.1380305, 'gr_name': 'ec2-user', 'pw_name': 'ec2-user', 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False}, 'ansible_loop_var': 'item'})
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
[WARNING]: TASK: Executar deploy por máquina: The loop variable 'machine_name'
is already in use. You should set the `loop_var` value in the `loop_control`
option for the task to something else to avoid variable collisions and
unexpected behavior.
included: /tmp/tmp.tFdpGTqYJW/elastic-compute-cloud-sitef/ansible/deploy_per_machine.yml for localhost => (item=sitef-01)
TASK [Definir caminhos para sitef-01] ******************************************
fatal: [localhost]: FAILED! => {"msg": "An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: An unhandled exception occurred while templating '{{ repo_root }}'. Error was a &lt;class 'ansible.errors.AnsibleError'>, original message: recursive loop detected in template string: {{ repo_root }}. maximum recursion depth exceeded while calling a Python object"}
PLAY RECAP *********************************************************************
localhost                  : ok=11   changed=0    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0   
Command finished with status FAILURE
