# Módulo Copy

## Objetivo

Automatizar a distribuição de arquivos utilizando o módulo `copy`.

---

## Estrutura utilizada

```text
ansible-lab/
├── files/
│   └── inventario.sh
├── inventory.ini
└── playbook.yml
```

---

## Tarefa adicionada

```yaml
- name: Copiar script da aplicação
  copy:
    src: files/inventario.sh
    dest: /opt/inventario/inventario.sh
    owner: inventario
    group: inventario
    mode: "0755"
```

---

## Primeira execução

```text
PLAY [Primeiro Playbook]

TASK [Gathering Facts]
ok: [localhost]

TASK [Criar usuário inventario]
ok: [localhost]

TASK [Criar diretório da aplicação]
ok: [localhost]

TASK [Copiar script da aplicação]
changed: [localhost]

PLAY RECAP
localhost : ok=4 changed=1 unreachable=0 failed=0
```

Como o arquivo ainda não existia, o Ansible realizou a cópia.

---

## Validação

### Permissões do diretório

```text
$ ls -l /opt/inventario/
ls: não foi possível abrir o diretório '/opt/inventario/': Permissão negada
```

O usuário `levi` não possui permissão para listar o conteúdo do diretório.

Utilizando `sudo`:

```text
$ sudo ls -l /opt/inventario/

total 4
-rwxr-xr-x 1 inventario inventario 41 ago 3 19:14 inventario.sh
-rw-rw-r-- 1 inventario inventario  0 jul 28 21:49 teste.txt
```

Foi possível confirmar:

- proprietário
- grupo
- permissões

---

### Conteúdo do arquivo

Sem privilégios:

```text
$ cat /opt/inventario/inventario.sh

cat: /opt/inventario/inventario.sh: Permissão negada
```

Com sudo:

```text
$ sudo cat /opt/inventario/inventario.sh

#!/bin/bash

echo "Inventario iniciado!"
```

---

## Segunda execução

```text
PLAY [Primeiro Playbook]

TASK [Gathering Facts]
ok: [localhost]

TASK [Criar usuário inventario]
ok: [localhost]

TASK [Criar diretório da aplicação]
ok: [localhost]

TASK [Copiar script da aplicação]
ok: [localhost]

PLAY RECAP
localhost : ok=4 changed=0 unreachable=0 failed=0
```

Como nenhuma alteração foi realizada no arquivo, o Ansible apenas confirmou que o estado já estava correto.

---

## Aprendizados

- O módulo `copy` copia arquivos apenas quando necessário.
- Também garante proprietário, grupo e permissões.
- Caso o arquivo seja alterado manualmente, o Ansible volta a deixá-lo conforme definido no playbook.
- O comportamento idempotente evita cópias e alterações desnecessárias.