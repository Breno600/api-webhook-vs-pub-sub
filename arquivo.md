[defaults]
host_key_checking = False
retry_files_enabled = False
timeout = 30
interpreter_python = auto_silent
forks = 20
stdout_callback = default

[ssh_connection]
pipelining = True
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o ServerAliveInterval=30 -o ServerAliveCountMax=4
