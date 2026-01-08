Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
Clonando repo em /tmp/tmp.IjSUthF0xI/repo...
Cloning into '/tmp/tmp.IjSUthF0xI/repo'...
remote: Enumerating objects: 28, done.
remote: Counting objects:   3% (1/28)
remote: Counting objects:   7% (2/28)
remote: Counting objects:  10% (3/28)
remote: Counting objects:  14% (4/28)
remote: Counting objects:  17% (5/28)
remote: Counting objects:  21% (6/28)
remote: Counting objects:  25% (7/28)
remote: Counting objects:  28% (8/28)
remote: Counting objects:  32% (9/28)
remote: Counting objects:  35% (10/28)
remote: Counting objects:  39% (11/28)
remote: Counting objects:  42% (12/28)
remote: Counting objects:  46% (13/28)
remote: Counting objects:  50% (14/28)
remote: Counting objects:  53% (15/28)
remote: Counting objects:  57% (16/28)
remote: Counting objects:  60% (17/28)
remote: Counting objects:  64% (18/28)
remote: Counting objects:  67% (19/28)
remote: Counting objects:  71% (20/28)
remote: Counting objects:  75% (21/28)
remote: Counting objects:  78% (22/28)
remote: Counting objects:  82% (23/28)
remote: Counting objects:  85% (24/28)
remote: Counting objects:  89% (25/28)
remote: Counting objects:  92% (26/28)
remote: Counting objects:  96% (27/28)
remote: Counting objects: 100% (28/28)
remote: Counting objects: 100% (28/28), done.
remote: Compressing objects:   4% (1/21)
remote: Compressing objects:   9% (2/21)
remote: Compressing objects:  14% (3/21)
remote: Compressing objects:  19% (4/21)
remote: Compressing objects:  23% (5/21)
remote: Compressing objects:  28% (6/21)
remote: Compressing objects:  33% (7/21)
remote: Compressing objects:  38% (8/21)
remote: Compressing objects:  42% (9/21)
remote: Compressing objects:  47% (10/21)
remote: Compressing objects:  52% (11/21)
remote: Compressing objects:  57% (12/21)
remote: Compressing objects:  61% (13/21)
remote: Compressing objects:  66% (14/21)
remote: Compressing objects:  71% (15/21)
remote: Compressing objects:  76% (16/21)
remote: Compressing objects:  80% (17/21)
remote: Compressing objects:  85% (18/21)
remote: Compressing objects:  90% (19/21)
remote: Compressing objects:  95% (20/21)
remote: Compressing objects: 100% (21/21)
remote: Compressing objects: 100% (21/21), done.
remote: Total 28 (delta 7), reused 0 (delta 0), pack-reused 0
Receiving objects:   3% (1/28)
Receiving objects:   7% (2/28)
Receiving objects:  10% (3/28)
Receiving objects:  14% (4/28)
Receiving objects:  17% (5/28)
Receiving objects:  21% (6/28)
Receiving objects:  25% (7/28)
Receiving objects:  28% (8/28)
Receiving objects:  32% (9/28)
Receiving objects:  35% (10/28)
Receiving objects:  39% (11/28)
Receiving objects:  42% (12/28)
Receiving objects:  46% (13/28)
Receiving objects:  50% (14/28)
Receiving objects:  53% (15/28)
Receiving objects:  57% (16/28)
Receiving objects:  60% (17/28)
Receiving objects:  64% (18/28)
Receiving objects:  67% (19/28)
Receiving objects:  71% (20/28)
Receiving objects:  75% (21/28)
Receiving objects:  78% (22/28)
Receiving objects:  82% (23/28)
Receiving objects:  85% (24/28)
Receiving objects:  89% (25/28)
Receiving objects:  92% (26/28)
Receiving objects:  96% (27/28)
Receiving objects: 100% (28/28)
Receiving objects: 100% (28/28), 6.55 KiB | 3.27 MiB/s, done.
Resolving deltas:   0% (0/7)
Resolving deltas:  14% (1/7)
Resolving deltas:  28% (2/7)
Resolving deltas:  42% (3/7)
Resolving deltas:  57% (4/7)
Resolving deltas:  71% (5/7)
Resolving deltas:  85% (6/7)
Resolving deltas: 100% (7/7)
Resolving deltas: 100% (7/7), done.
Conteúdo do repo:
total 8
drwxr-x---. 7 ec2-user ec2-user   98 Jan  8 18:03 .
drwx------. 3 ec2-user ec2-user   18 Jan  8 18:02 ..
drwxr-x---. 3 ec2-user ec2-user   42 Jan  8 18:03 ansible
drwxr-x---. 2 ec2-user ec2-user   27 Jan  8 18:03 execution
drwxr-x---. 8 ec2-user ec2-user  163 Jan  8 18:03 .git
drwxr-x---. 2 ec2-user ec2-user   26 Jan  8 18:03 machines
-rw-r-----. 1 ec2-user ec2-user 6376 Jan  8 18:03 README.md
drwxr-x---. 2 ec2-user ec2-user   21 Jan  8 18:03 scripts
PWD do repo:
/tmp/tmp.IjSUthF0xI/repo

PLAY [Build dynamic target list from execution + machines folder] **************
TASK [Resolve paths] ***********************************************************
ok: [localhost]
TASK [Load execution file] *****************************************************
fatal: [localhost]: FAILED! => {"ansible_facts": {"exec": {}}, "ansible_included_var_files": [], "changed": false, "message": "Could not find or access '../execution/execution.yml'\nSearched in:\n\t/tmp/tmp.IjSUthF0xI/repo/ansible/playbooks/vars/../execution/execution.yml\n\t/tmp/tmp.IjSUthF0xI/repo/ansible/playbooks/../execution/execution.yml\n\t/tmp/tmp.IjSUthF0xI/repo/ansible/playbooks/vars/../execution/execution.yml\n\t/tmp/tmp.IjSUthF0xI/repo/ansible/playbooks/../execution/execution.yml on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}
PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
Command finished with status FAILURE
