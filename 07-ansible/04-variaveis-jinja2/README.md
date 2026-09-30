# Estudo 04 - Ansible: Variáveis e Templates Jinja2

## Objetivo

Continuar a evolução do playbook utilizando **variáveis** e **templates Jinja2**, evitando valores fixos na configuração da aplicação.

Neste laboratório comecei a separar informações que podem mudar, como nome da aplicação, usuário e permissões, da estrutura do playbook.

---

## Variáveis

Criei uma seção `vars` no playbook:

```yaml
vars:
  app_name: inventario
  app_dir_mode: "0770"
  app_user: app-inventario
```

Essas variáveis representam:

* `app_name`: nome da aplicação.
* `app_dir_mode`: permissão desejada para o diretório.
* `app_user`: usuário responsável pela execução da aplicação.

---

## Utilizando variáveis

Em vez de deixar os valores diretamente no playbook:

```yaml
- name: Criar diretório da aplicação
  file:
    path: /opt/inventario
    state: directory
    owner: app-inventario
    group: app-inventario
    mode: "0770"
```

Passei a utilizar as variáveis:

```yaml
- name: Criar diretório da aplicação
  file:
    path: "/opt/{{ app_name }}"
    state: directory
    owner: "{{ app_user }}"
    group: "{{ app_user }}"
    mode: "{{ app_dir_mode }}"
```

Isso permite alterar a configuração da aplicação sem precisar modificar várias partes do playbook.

---

## Templates Jinja2

Também comecei a utilizar templates para gerar arquivos de configuração.

O arquivo do serviço foi transformado em:

```text
inventario.service.j2
```

Estrutura:

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

## Template do systemd

O arquivo passou a utilizar variáveis:

```ini
[Unit]
Description=Inventario API
After=network.target

[Service]
Type=simple
User={{ app_user }}
Group={{ app_user }}
WorkingDirectory=/opt/{{ app_name }}
ExecStart=/opt/{{ app_name }}/{{ app_name }}.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Os valores entre `{{ }}` são processados pelo Jinja2 durante a execução do Ansible.

Por exemplo:

```text
{{ app_name }}
```

resulta em:

```text
inventario
```

E:

```text
{{ app_user }}
```

resulta em:

```text
app-inventario
```

---

## Módulo template

No playbook, substituí o módulo `copy` pelo módulo `template`:

```yaml
- name: Copiar arquivo do systemd
  template:
    src: files/systemd/inventario.service.j2
    dest: /etc/systemd/system/inventario.service
    owner: root
    group: root
    mode: "0644"
  notify: Recarregar e reiniciar aplicação
```

A diferença é que o `copy` copia o arquivo como ele está, enquanto o `template` processa o arquivo Jinja2 antes de colocá-lo no destino.

---

## Separação entre aplicação e usuário

Também percebi que o nome da aplicação e o usuário que executa a aplicação são conceitos diferentes.

Por isso foram criadas duas variáveis:

```yaml
app_name: inventario
app_user: app-inventario
```

Assim:

```text
Aplicação
└── inventario

Usuário do serviço
└── app-inventario
```

Essa separação torna a configuração mais clara e permite alterar cada parte de forma independente.

---

## Validação

Depois das alterações, executei uma verificação de sintaxe:

```bash
ansible-playbook -i inventory.ini playbook.yml --syntax-check
```

Resultado:

```text
playbook: playbook.yml
```

Também executei o playbook e validei o serviço:

```bash
systemctl status inventario
```

O serviço permaneceu ativo após a configuração.

---

## O que aprendi

Durante este laboratório aprendi:

* Como criar e utilizar variáveis no Ansible.
* Como utilizar expressões Jinja2.
* Diferença entre `copy` e `template`.
* Como utilizar arquivos `.j2`.
* Como gerar arquivos de configuração dinamicamente.
* Como separar configuração da lógica do playbook.
* Diferença entre usuário da aplicação e nome da aplicação.
* Como validar a sintaxe do playbook antes da execução.

O principal aprendizado foi perceber que o Ansible pode gerar configurações a partir de um modelo, em vez de simplesmente copiar arquivos prontos.
