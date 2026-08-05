# Estudo 03 - Ansible: Handlers e gerenciamento de serviços

## Objetivo

Neste laboratório continuei a evolução do playbook de provisionamento da aplicação **Inventario**, aprendendo a automatizar o gerenciamento de serviços utilizando o módulo `systemd` e o conceito de **Handlers**.

---

# Conteúdo estudado

## 1. Cópia do arquivo de serviço (systemd)

Criei um diretório para armazenar os arquivos do serviço dentro do projeto.

```text
ansible-lab/
├── files/
│   ├── inventario.sh
│   └── systemd/
│       └── inventario.service
├── inventory.ini
└── playbook.yml
```

Copiei o arquivo de serviço existente para o projeto.

```bash
mkdir -p files/systemd
cp /etc/systemd/system/inventario.service ~/ansible-lab/files/systemd/
```

---

## 2. Automatizando a instalação do serviço

Adicionei ao playbook a tarefa responsável por copiar automaticamente o arquivo do serviço para o servidor.

```yaml
- name: Copiar arquivo do systemd
  copy:
    src: files/systemd/inventario.service
    dest: /etc/systemd/system/inventario.service
    owner: root
    group: root
    mode: "0644"
  notify: Recarregar systemd
```

### Aprendizado

Percebi que um arquivo `.service` não precisa de permissão de execução (`0755`), pois ele é apenas um arquivo de configuração lido pelo `systemd`.

Por isso o modo correto é:

```text
0644
rw-r--r--
```

---

# Handlers

Aprendi o conceito de **Handlers**, que executam ações apenas quando alguma tarefa informa que houve alteração.

Antes disso, o playbook executava:

```text
copy
↓

daemon-reload
```

Mesmo quando nada havia mudado.

Agora o comportamento ficou:

```text
copy
↓

notify
↓

handler
↓

daemon-reload
```

O `daemon-reload` somente é executado quando o arquivo `.service` sofre alterações.

Handler criado:

```yaml
handlers:

  - name: Recarregar systemd
    systemd:
      daemon_reload: true
```

---

# Módulo systemd

Também estudei o módulo `systemd`, responsável pelo gerenciamento de serviços.

Exemplo:

```yaml
- name: Garantir que o serviço esteja iniciado
  systemd:
    name: inventario
    state: started
    enabled: true
```

### Conceitos aprendidos

* `state: started`

Garante que o serviço esteja em execução.

Caso já esteja iniciado, o Ansible retorna **ok**.

Caso esteja parado, o Ansible o inicia automaticamente.

---

* `enabled: true`

Equivalente ao comando:

```bash
systemctl enable inventario
```

Garante que o serviço seja iniciado automaticamente durante o boot do sistema.

---

# Reinicialização inteligente

Também aprendi que um serviço **não deve ser reiniciado toda vez que o playbook for executado**.

O comportamento correto é utilizar outro Handler.

Exemplo:

```yaml
- name: Copiar script da aplicação
  copy:
    src: files/inventario.sh
    dest: /opt/inventario/inventario.sh
    owner: inventario
    group: inventario
    mode: "0755"
  notify: Reiniciar Inventario
```

Handler:

```yaml
- name: Reiniciar Inventario
  systemd:
    name: inventario
    state: restarted
```

Dessa forma, a aplicação somente será reiniciada quando o script realmente sofrer alterações.

---

# Resultado do laboratório

Execução do playbook:

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

TASK [Copiar arquivo do systemd]
ok: [localhost]

PLAY RECAP

localhost : ok=5 changed=0 unreachable=0 failed=0
```

Como nenhuma alteração foi realizada, nenhuma ação desnecessária foi executada.

---

# O que aprendi

Durante este estudo compreendi que o Ansible não deve ser utilizado apenas para executar comandos remotamente.

Ele trabalha descrevendo o **estado desejado** do servidor.

Além disso, aprendi a utilizar **Handlers**, permitindo que ações como recarregar o `systemd` ou reiniciar uma aplicação aconteçam somente quando realmente necessário.

Esse comportamento torna os playbooks mais eficientes, reduz reinicializações desnecessárias e segue boas práticas utilizadas em ambientes de produção.
