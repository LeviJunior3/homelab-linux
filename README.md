# 🐧 Homelab Linux

> Laboratório pessoal criado para desenvolver habilidades práticas em administração Linux, redes, serviços de infraestrutura e automação.

---

## 🎯 Objetivos do Aprendizado

O foco deste ambiente é desenvolver experiência prática nas seguintes áreas:

* 🛠️ **Administração Linux** (Debian)
* 🌐 **Redes TCP/IP e configuração de interfaces**
* 📜 **Automação de tarefas com Bash Scripting**
* ⚙️ **Gerenciamento de serviços Linux**
* 🔐 **Segurança e controle de acesso**
* 📦 **Virtualização e Containerização** (planejado)

---

## 🖥️ Especificações de Hardware
Para este laboratório, reutilizei um hardware antigo (*legacy*) transformando-o em um servidor funcional:

| Componente | Detalhes |
| :--- | :--- |
| **Máquina** | PC Semp Toshiba Info |
| **Processador** | Intel Pentium Dual Core E2220 |
| **Memória RAM** | 2GB RAM |
| **Armazenamento 1** | SSD 240GB (Montado em `/`) |
| **Armazenamento 2** | HD 300GB (Montado em `/home`) |

---

**Tipo de ambiente:** Servidor físico de laboratório (Bare Metal)

**Objetivo:** Reaproveitamento de hardware antigo para simular um ambiente de servidor real.

---

## 💿 Sistema Operacional
O laboratório roda a distribuição estável voltada para servidores:
* **OS:** `Debian 13 Trixie` (Ambiente de laboratório)

---

## 🛠️ Status dos Serviços Configurados

Abaixo está o mapa de progresso de ativação dos serviços no servidor:

| Serviço | Status | Descrição Rápida |
| :---: | :---: | :--- |
| **SSH** | 🟢 Ativo | Acesso remoto seguro via terminal |
| **IP Estático** | 🟢 Ativo | Endereçamento fixo para o servidor na rede local |
| **Samba** | 🟢 Ativo | Compartilhamento de arquivos entre Linux/Windows |
| **Bash** | 🟢 Ativo | Scripts de automação internos |
| **DNS** | 🟢 Ativo | Resolução de nomes local |
| **Docker** | 🟡 Em Breve | Deploy de containers e microserviços |
| **Firewall (nftables/UFW)** | 🟡 Em Breve | Controle de tráfego e segurança do servidor |

---
