Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
Clonando repo em /tmp/tmp.4DZ1fovBBx/repo...
Cloning into '/tmp/tmp.4DZ1fovBBx/repo'...
remote: Enumerating objects: 33, done.
remote: Counting objects:   3% (1/33)
remote: Counting objects:   6% (2/33)
remote: Counting objects:   9% (3/33)
remote: Counting objects:  12% (4/33)
remote: Counting objects:  15% (5/33)
remote: Counting objects:  18% (6/33)
remote: Counting objects:  21% (7/33)
remote: Counting objects:  24% (8/33)
remote: Counting objects:  27% (9/33)
remote: Counting objects:  30% (10/33)
remote: Counting objects:  33% (11/33)
remote: Counting objects:  36% (12/33)
remote: Counting objects:  39% (13/33)
remote: Counting objects:  42% (14/33)
remote: Counting objects:  45% (15/33)
remote: Counting objects:  48% (16/33)
remote: Counting objects:  51% (17/33)
remote: Counting objects:  54% (18/33)
remote: Counting objects:  57% (19/33)
remote: Counting objects:  60% (20/33)
remote: Counting objects:  63% (21/33)
remote: Counting objects:  66% (22/33)
remote: Counting objects:  69% (23/33)
remote: Counting objects:  72% (24/33)
remote: Counting objects:  75% (25/33)
remote: Counting objects:  78% (26/33)
remote: Counting objects:  81% (27/33)
remote: Counting objects:  84% (28/33)
remote: Counting objects:  87% (29/33)
remote: Counting objects:  90% (30/33)
remote: Counting objects:  93% (31/33)
remote: Counting objects:  96% (32/33)
remote: Counting objects: 100% (33/33)
remote: Counting objects: 100% (33/33), done.
remote: Compressing objects:   3% (1/26)
remote: Compressing objects:   7% (2/26)
remote: Compressing objects:  11% (3/26)
remote: Compressing objects:  15% (4/26)
remote: Compressing objects:  19% (5/26)
remote: Compressing objects:  23% (6/26)
remote: Compressing objects:  26% (7/26)
remote: Compressing objects:  30% (8/26)
remote: Compressing objects:  34% (9/26)
remote: Compressing objects:  38% (10/26)
remote: Compressing objects:  42% (11/26)
remote: Compressing objects:  46% (12/26)
remote: Compressing objects:  50% (13/26)
remote: Compressing objects:  53% (14/26)
remote: Compressing objects:  57% (15/26)
remote: Compressing objects:  61% (16/26)
remote: Compressing objects:  65% (17/26)
remote: Compressing objects:  69% (18/26)
remote: Compressing objects:  73% (19/26)
remote: Compressing objects:  76% (20/26)
remote: Compressing objects:  80% (21/26)
remote: Compressing objects:  84% (22/26)
remote: Compressing objects:  88% (23/26)
remote: Compressing objects:  92% (24/26)
remote: Compressing objects:  96% (25/26)
remote: Compressing objects: 100% (26/26)
remote: Compressing objects: 100% (26/26), done.
remote: Total 33 (delta 10), reused 0 (delta 0), pack-reused 0
Receiving objects:   3% (1/33)
Receiving objects:   6% (2/33)
Receiving objects:   9% (3/33)
Receiving objects:  12% (4/33)
Receiving objects:  15% (5/33)
Receiving objects:  18% (6/33)
Receiving objects:  21% (7/33)
Receiving objects:  24% (8/33)
Receiving objects:  27% (9/33)
Receiving objects:  30% (10/33)
Receiving objects:  33% (11/33)
Receiving objects:  36% (12/33)
Receiving objects:  39% (13/33)
Receiving objects:  42% (14/33)
Receiving objects:  45% (15/33)
Receiving objects:  48% (16/33)
Receiving objects:  51% (17/33)
Receiving objects:  54% (18/33)
Receiving objects:  57% (19/33)
Receiving objects:  60% (20/33)
Receiving objects:  63% (21/33)
Receiving objects:  66% (22/33)
Receiving objects:  69% (23/33)
Receiving objects:  72% (24/33)
Receiving objects:  75% (25/33)
Receiving objects:  78% (26/33)
Receiving objects:  81% (27/33)
Receiving objects:  84% (28/33)
Receiving objects:  87% (29/33)
Receiving objects:  90% (30/33)
Receiving objects:  93% (31/33)
Receiving objects:  96% (32/33)
Receiving objects: 100% (33/33)
Receiving objects: 100% (33/33), 6.88 KiB | 6.88 MiB/s, done.
Resolving deltas:   0% (0/10)
Resolving deltas:  10% (1/10)
Resolving deltas:  20% (2/10)
Resolving deltas:  30% (3/10)
Resolving deltas:  40% (4/10)
Resolving deltas:  50% (5/10)
Resolving deltas:  60% (6/10)
Resolving deltas:  70% (7/10)
Resolving deltas:  80% (8/10)
Resolving deltas:  90% (9/10)
Resolving deltas: 100% (10/10)
Resolving deltas: 100% (10/10), done.
Conteúdo do repo:
total 8
drwxr-x---. 7 ec2-user ec2-user   98 Jan  8 18:04 .
drwx------. 3 ec2-user ec2-user   18 Jan  8 18:04 ..
drwxr-x---. 3 ec2-user ec2-user   42 Jan  8 18:04 ansible
drwxr-x---. 2 ec2-user ec2-user   27 Jan  8 18:04 execution
drwxr-x---. 8 ec2-user ec2-user  163 Jan  8 18:04 .git
drwxr-x---. 2 ec2-user ec2-user   26 Jan  8 18:04 machines
-rw-r-----. 1 ec2-user ec2-user 6376 Jan  8 18:04 README.md
drwxr-x---. 2 ec2-user ec2-user   21 Jan  8 18:04 scripts
PWD do repo:
/tmp/tmp.4DZ1fovBBx/repo
PLAY [Build dynamic target list from execution + machines folder] **************
TASK [Resolve paths] ***********************************************************
ok: [localhost]
TASK [Load execution file] *****************************************************
ok: [localhost]
TASK [Validate execution targets] **********************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Check machine files exist] ***********************************************
ok: [localhost] => (item=sitef-02)
TASK [Fail if any machine file is missing] *************************************
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '../../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 253, 'inode': 4325529, 'dev': 64775, 'nlink': 1, 'atime': 1767895464.5054407, 'mtime': 1767895464.5034406, 'ctime': 1767895464.5034406, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': 'b607fdf55dac2409db597a628248835d3329d5b4', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '853209536', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '../../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'ansible_facts': {'discovered_interpreter_python': '/usr/bin/python3'}, 'failed': False, 'item': 'sitef-02', 'ansible_loop_var': 'item'}) => {
    "ansible_loop_var": "item",
    "changed": false,
    "item": {
        "ansible_facts": {
            "discovered_interpreter_python": "/usr/bin/python3"
        },
        "ansible_loop_var": "item",
        "changed": false,
        "failed": false,
        "invocation": {
            "module_args": {
                "checksum_algorithm": "sha1",
                "follow": false,
                "get_attributes": true,
                "get_checksum": true,
                "get_md5": false,
                "get_mime": true,
                "path": "../../machines/sitef-02.yml"
            }
        },
        "item": "sitef-02",
        "stat": {
            "atime": 1767895464.5054407,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "b607fdf55dac2409db597a628248835d3329d5b4",
            "ctime": 1767895464.5034406,
            "dev": 64775,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 1000,
            "gr_name": "ec2-user",
            "inode": 4325529,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0640",
            "mtime": 1767895464.5034406,
            "nlink": 1,
            "path": "../../machines/sitef-02.yml",
            "pw_name": "ec2-user",
            "readable": true,
            "rgrp": true,
            "roth": false,
            "rusr": true,
            "size": 253,
            "uid": 1000,
            "version": "853209536",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    },
    "msg": "All assertions passed"
}
TASK [Load machine definitions] ************************************************
ok: [localhost] => (item=sitef-02)
TASK [Add targets dynamically (per machine vars)] ******************************
changed: [localhost] => (item=sitef-02)
TASK [Show selected targets] ***************************************************
ok: [localhost] => {
    "msg": [
        "Execution file: ../../execution/execution.yml",
        "Machines dir: ../../machines",
        "Selected targets: ['sitef-02']"
    ]
}
PLAY [Deliver to selected machines (download from S3 + run script)] ************
TASK [Gathering Facts] *********************************************************
ok: [sitef-02]
TASK [Ensure directories exist] ************************************************
ok: [sitef-02] => (item=/opt/SoftwareExpress/sitef)
ok: [sitef-02] => (item=/opt/SoftwareExpress/sitef)
TASK [Copy scripts to target] **************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [sitef-02]: FAILED! => {"changed": false, "msg": "Could not find or access '../../../scripts/'\nSearched in:\n\t/tmp/tmp.4DZ1fovBBx/repo/ansible/playbooks/files/../../../scripts/\n\t/tmp/tmp.4DZ1fovBBx/repo/ansible/playbooks/../../../scripts/\n\t/tmp/tmp.4DZ1fovBBx/repo/ansible/playbooks/files/../../../scripts/\n\t/tmp/tmp.4DZ1fovBBx/repo/ansible/playbooks/../../../scripts/ on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}
PLAY RECAP *********************************************************************
localhost                  : ok=8    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
sitef-02                   : ok=2    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
Command finished with status FAILURE
