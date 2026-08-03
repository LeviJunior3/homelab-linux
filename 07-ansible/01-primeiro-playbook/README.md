# Primeiro Playbook com Ansible

## Objetivo

Criar meu primeiro playbook utilizando Ansible para entender os conceitos de:

- Inventory
- Playbook
- Tasks
- Idempotência
- Infraestrutura como Código (IaC)

---

## Estrutura do laboratório

```text
ansible-lab/
├── inventory.ini
└── playbook.yml
```

---

## Playbook

```yaml
---
- name: Primeiro Playbook
  hosts: homelab
  become: true

  tasks:

    - name: Criar usuário inventario
      user:
        name: inventario
        state: present

    - name: Criar diretório da aplicação
      file:
        path: /opt/inventario
        state: directory
        owner: inventario
        group: inventario
        mode: "0770"
```

---

## Primeira execução

```text
PLAY [Primeiro Playbook]

TASK [Gathering Facts]
ok: [localhost]

TASK [Criar usuário inventario]
changed: [localhost]

PLAY RECAP
localhost : ok=2 changed=1 unreachable=0 failed=0
```

Na primeira execução o usuário ainda não existia, portanto o Ansible realizou alterações.

---

## Segunda execução

```text
PLAY [Primeiro Playbook]

TASK [Gathering Facts]
ok: [localhost]

TASK [Criar usuário inventario]
ok: [localhost]

PLAY RECAP
localhost : ok=2 changed=0 unreachable=0 failed=0
```

Como o usuário já existia, nenhuma alteração foi necessária.

Esse comportamento demonstra um dos principais conceitos do Ansible: **idempotência** que é uma operação que pode ser executada várias vezes sem alterar o resultado final após a primeira execução.

---

## O que aprendi

- Como criar meu primeiro playbook.
- Como utilizar o módulo `user`.
- Como utilizar o módulo `file`.
- Como funciona o `become: true`.
- Diferença entre `ok` e `changed`.
- O Ansible descreve o estado desejado do servidor, não apenas executa comandos.