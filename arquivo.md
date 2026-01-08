Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
Clonando repo em /tmp/tmp.DK7Iz1kvPa/repo...
Cloning into '/tmp/tmp.DK7Iz1kvPa/repo'...
remote: Enumerating objects: 53, done.
remote: Total 53 (delta 0), reused 0 (delta 0), pack-reused 53
Receiving objects:   1% (1/53)
Receiving objects:   3% (2/53)
Receiving objects:   5% (3/53)
Receiving objects:   7% (4/53)
Receiving objects:   9% (5/53)
Receiving objects:  11% (6/53)
Receiving objects:  13% (7/53)
Receiving objects:  15% (8/53)
Receiving objects:  16% (9/53)
Receiving objects:  18% (10/53)
Receiving objects:  20% (11/53)
Receiving objects:  22% (12/53)
Receiving objects:  24% (13/53)
Receiving objects:  26% (14/53)
Receiving objects:  28% (15/53)
Receiving objects:  30% (16/53)
Receiving objects:  32% (17/53)
Receiving objects:  33% (18/53)
Receiving objects:  35% (19/53)
Receiving objects:  37% (20/53)
Receiving objects:  39% (21/53)
Receiving objects:  41% (22/53)
Receiving objects:  43% (23/53)
Receiving objects:  45% (24/53)
Receiving objects:  47% (25/53)
Receiving objects:  49% (26/53)
Receiving objects:  50% (27/53)
Receiving objects:  52% (28/53)
Receiving objects:  54% (29/53)
Receiving objects:  56% (30/53)
Receiving objects:  58% (31/53)
Receiving objects:  60% (32/53)
Receiving objects:  62% (33/53)
Receiving objects:  64% (34/53)
Receiving objects:  66% (35/53)
Receiving objects:  67% (36/53)
Receiving objects:  69% (37/53)
Receiving objects:  71% (38/53)
Receiving objects:  73% (39/53)
Receiving objects:  75% (40/53)
Receiving objects:  77% (41/53)
Receiving objects:  79% (42/53)
Receiving objects:  81% (43/53)
Receiving objects:  83% (44/53)
Receiving objects:  84% (45/53)
Receiving objects:  86% (46/53)
Receiving objects:  88% (47/53)
Receiving objects:  90% (48/53)
Receiving objects:  92% (49/53)
Receiving objects:  94% (50/53)
Receiving objects:  96% (51/53)
Receiving objects:  98% (52/53)
Receiving objects: 100% (53/53)
Receiving objects: 100% (53/53), 9.25 KiB | 9.25 MiB/s, done.
Resolving deltas:   0% (0/22)
Resolving deltas:   4% (1/22)
Resolving deltas:   9% (2/22)
Resolving deltas:  13% (3/22)
Resolving deltas:  18% (4/22)
Resolving deltas:  22% (5/22)
Resolving deltas:  27% (6/22)
Resolving deltas:  31% (7/22)
Resolving deltas:  36% (8/22)
Resolving deltas:  40% (9/22)
Resolving deltas:  45% (10/22)
Resolving deltas:  50% (11/22)
Resolving deltas:  54% (12/22)
Resolving deltas:  59% (13/22)
Resolving deltas:  63% (14/22)
Resolving deltas:  68% (15/22)
Resolving deltas:  72% (16/22)
Resolving deltas:  77% (17/22)
Resolving deltas:  81% (18/22)
Resolving deltas:  86% (19/22)
Resolving deltas:  90% (20/22)
Resolving deltas:  95% (21/22)
Resolving deltas: 100% (22/22)
Resolving deltas: 100% (22/22), done.
Conteúdo do repo:
total 8
drwxr-x---. 7 ec2-user ec2-user   98 Jan  8 18:17 .
drwx------. 3 ec2-user ec2-user   18 Jan  8 18:17 ..
drwxr-x---. 3 ec2-user ec2-user   42 Jan  8 18:17 ansible
drwxr-x---. 2 ec2-user ec2-user   27 Jan  8 18:17 execution
drwxr-x---. 8 ec2-user ec2-user  163 Jan  8 18:17 .git
drwxr-x---. 2 ec2-user ec2-user   26 Jan  8 18:17 machines
-rw-r-----. 1 ec2-user ec2-user 6376 Jan  8 18:17 README.md
drwxr-x---. 2 ec2-user ec2-user   21 Jan  8 18:17 scripts
PWD do repo:
/tmp/tmp.DK7Iz1kvPa/repo
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
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '../../machines/sitef-02.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 253, 'inode': 2113884, 'dev': 64775, 'nlink': 1, 'atime': 1767896225.074461, 'mtime': 1767896225.0704608, 'ctime': 1767896225.0704608, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': 'b607fdf55dac2409db597a628248835d3329d5b4', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '540671334', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '../../machines/sitef-02.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'ansible_facts': {'discovered_interpreter_python': '/usr/bin/python3'}, 'failed': False, 'item': 'sitef-02', 'ansible_loop_var': 'item'}) => {
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
            "atime": 1767896225.074461,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "b607fdf55dac2409db597a628248835d3329d5b4",
            "ctime": 1767896225.0704608,
            "dev": 64775,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 1000,
            "gr_name": "ec2-user",
            "inode": 2113884,
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
            "mtime": 1767896225.0704608,
            "nlink": 1,
            "path": "../../machines/sitef-02.yml",
            "pw_name": "ec2-user",
            "readable": true,
            "rgrp": true,
            "roth": false,
            "rusr": true,
            "size": 253,
            "uid": 1000,
            "version": "540671334",
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
ok: [sitef-02]
TASK [Detect aws binary path] **************************************************
ok: [sitef-02]
TASK [Set aws_cli_cmd (absolute path)] *****************************************
ok: [sitef-02]
TASK [Show aws path] ***********************************************************
ok: [sitef-02] => {
    "msg": [
        "aws_cli_cmd=/usr/local/bin/aws",
        "PATH=/sbin:/bin:/usr/sbin:/usr/bin"
    ]
}
TASK [Check AWS CLI exists on target] ******************************************
ok: [sitef-02]
TASK [Download artifacts from S3 to target folder] *****************************
changed: [sitef-02]
TASK [Execute init script] *****************************************************
changed: [sitef-02]
TASK [Show init output] ********************************************************
ok: [sitef-02] => {
    "init_out.stdout_lines": [
        "/opt/SoftwareExpress/sitef",
        "total 260",
        "drwxr-xr-x. 19 root   root    4096 Jan  8 18:17 .",
        "drwxr-xr-x.  7 root   root     111 Dec 30 11:23 ..",
        "drwxrwxr--.  2 sitefd sitef   4096 Jan  8 10:01 audit",
        "drwxr-xr--.  3 root   sitef   4096 Jan  7 16:27 bin",
        "drwxrwxr--. 11 sitefd sitef   4096 Jan  8 10:01 config",
        "-rw-r-----.  1 root   root  240898 Jan  8 17:50 config-migrado.tgz",
        "drwxr-x---. 19 sitefd sitef   4096 Jan  8 10:01 config.pgsql",
        "drwxr-x---.  3 root   root      27 Jan  7 16:28 config.pgsql.BACKUP",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 erro",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 espelho.log",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 estab",
        "drwxrwxr--.  4 sitefd sitef    189 Jan  8 10:01 etc",
        "-rwxr-xr-x.  1 root   root      49 Jan  8 18:06 init.sh",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 licenca",
        "drwxrwxr--.  2 sitefd sitef     82 Jan  8 10:01 log",
        "drwxrwxr--.  2 sitefd sitef    191 Jan  7 16:31 lst",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 queues",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 tabelas",
        "drwxrwxr--.  3 sitefd sitef     17 Jan  8 10:01 tmp",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 trace",
        "drwxrwxr--.  2 sitefd sitef      6 Dec 16 13:23 versaoterminais"
    ]
}
PLAY RECAP *********************************************************************
localhost                  : ok=8    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
sitef-02                   : ok=10   changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
Command finished with status SUCCESS
