- step:
    type: Run
    name: Clone e roda Ansible
    identifier: clone_e_ansible
    spec:
      shell: Bash
      command: |
        set -e

        # 1) Configura SSH para o GitLab
        mkdir -p ~/.ssh
        echo "<+secrets.getValue(\"gitlab_ssh_key_ansible\")>" > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        ssh-keyscan gitlab.com >> ~/.ssh/known_hosts

        # 2) Clona o repositório
        REPO_URL="git@gitlab.com:SEU-GRUPO/SEU-REPO.git"
        WORKDIR="/tmp/ansible-repo"

        rm -rf "$WORKDIR"
        git clone "$REPO_URL" "$WORKDIR"

        # 3) Entra na pasta onde está o Ansible
        cd "$WORKDIR/ansible"

        # (opcional) mostra os arquivos
        ls -R

        # 4) Roda o playbook
        # se seu playbook for o deploy_from_machine.yml:
        ansible-playbook deploy_from_machine.yml \
          -i inventory  # ajuste aqui se o inventário for diferente