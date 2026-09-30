# Estudo 05 - Ansible: Playbook Final

## Objetivo

Finalizar a evolução do laboratório de Ansible reunindo os conceitos estudados anteriormente em um único playbook.

O objetivo foi automatizar a configuração completa da aplicação **Inventario**, desde a criação do usuário e diretório até o gerenciamento do serviço pelo `systemd`.

---

## Estrutura final

```text
ansible-lab/
├── files/
│   ├── inventario.sh
│   └── systemd/
│       └── inventario.service.j2
├── inventory.ini
└── playbook.yml
```

---

## Variáveis

O playbook utiliza variáveis para centralizar as configurações:

```yaml
vars:
  app_name: inventario
  app_dir_mode: "0770"
  app_user: app-inventario
```

---

## Playbook

```yaml
---
- name: Primeiro Playbook
  hosts: homelab
  become: true

  vars:
    app_name: inventario
    app_dir_mode: "0770"
    app_user: app-inventario

  tasks:

    - name: Criar usuário inventario
      user:
        name: "{{ app_user }}"
        state: present

    - name: Criar diretório da aplicação
      file:
        path: "/opt/{{ app_name }}"
        state: directory
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: "{{ app_dir_mode }}"

    - name: Copiar script da aplicação
      copy:
        src: files/inventario.sh
        dest: "/opt/{{ app_name }}/{{ app_name }}.sh"
        owner: "{{ app_user }}"
        group: "{{ app_user }}"
        mode: "0755"
      notify: Recarregar e reiniciar aplicação

    - name: Copiar arquivo do systemd
      template:
        src: files/systemd/inventario.service.j2
        dest: /etc/systemd/system/inventario.service
        owner: root
        group: root
        mode: "0644"
      notify: Recarregar e reiniciar aplicação

    - name: Garantir que o serviço esteja iniciado e habilitado
      systemd:
        name: "{{ app_name }}"
        state: started
        enabled: true

  handlers:

    - name: Recarregar e reiniciar aplicação
      systemd:
        name: "{{ app_name }}"
        daemon_reload: true
        state: restarted
```

---

## O que o playbook faz

A execução segue basicamente este fluxo:

```text
Criar usuário
      ↓
Criar diretório
      ↓
Copiar aplicação
      ↓
Gerar serviço systemd
      ↓
Recarregar systemd
      ↓
Reiniciar aplicação se necessário
      ↓
Garantir serviço iniciado
      ↓
Habilitar no boot
```

---

## Usuário da aplicação

A aplicação não é executada diretamente pelo usuário `root`.

O Ansible cria:

```text
app-inventario
```

E configura o serviço para utilizar esse usuário:

```ini
[Service]
User=app-inventario
Group=app-inventario
```

O diretório da aplicação também pertence ao usuário:

```text
/opt/inventario
```

Com:

```text
owner: app-inventario
group: app-inventario
mode: 0770
```

Isso mantém a aplicação separada do usuário administrativo.

---

## Handlers

O playbook utiliza um Handler:

```yaml
handlers:

  - name: Recarregar e reiniciar aplicação
    systemd:
      name: "{{ app_name }}"
      daemon_reload: true
      state: restarted
```

Ele é acionado através de:

```yaml
notify: Recarregar e reiniciar aplicação
```

Isso significa que o serviço não é reiniciado desnecessariamente a cada execução.

Se o script ou o arquivo do serviço não sofrer alterações, o Handler não é executado.

---

## Idempotência

Um dos principais pontos testados durante o laboratório foi a idempotência.

Após alterações no projeto, uma execução apresentou:

```text
PLAY RECAP

localhost : ok=7 changed=2 unreachable=0 failed=0
```

As alterações necessárias foram realizadas.

Depois de executar novamente o mesmo playbook:

```text
PLAY RECAP

localhost : ok=7 changed=0 unreachable=0 failed=0
```

Nenhuma alteração adicional foi necessária.

Isso demonstra que o playbook consegue verificar o estado atual do servidor e realizar somente as alterações necessárias.

---

## Validação do serviço

Após a execução, validei o serviço utilizando:

```bash
systemctl status inventario
```

Também foi possível verificar os logs:

```bash
journalctl -u inventario
```

O serviço permaneceu ativo e executando a aplicação com o usuário:

```text
app-inventario
```

---

## Conceitos utilizados

Durante a evolução deste laboratório foram utilizados:

* Inventory
* Playbook
* Tasks
* Modules
* `user`
* `file`
* `copy`
* `template`
* `systemd`
* Variables
* Jinja2
* Handlers
* `notify`
* `become`
* Idempotência
* Infraestrutura como Código (IaC)

---

## O que aprendi

Este laboratório começou com um playbook simples para criar um usuário e um diretório e foi evoluindo até automatizar uma aplicação completa.

Durante o processo compreendi que o Ansible não serve apenas para executar comandos em um servidor.

A ideia principal é **descrever o estado desejado da infraestrutura** e permitir que o Ansible faça as alterações necessárias para chegar a esse estado.

Também aprendi a combinar diferentes recursos, como:

```text
Variáveis
   +
Templates
   +
Handlers
   +
systemd
   +
Idempotência
```

O resultado foi um playbook capaz de configurar e manter uma aplicação simples de forma automatizada e reproduzível.

Este foi o encerramento do laboratório de Ansible do meu homelab. O próximo passo dos estudos será utilizar Docker para trabalhar com aplicações em containers.
