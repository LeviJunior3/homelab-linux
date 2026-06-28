# 🌐 Acesso SSH em Servidor Headless (Simulação Real)

Este projeto documenta a configuração e o acesso remoto a um servidor Linux em ambiente **Headless** (servidor físico sem monitor, teclado ou mouse, conectado apenas pelo cabo de rede). 

Para tornar a simulação o mais próxima possível do mundo real, a administração e os testes de conectividade foram realizados a partir de uma máquina **Windows**.

---

## 🛠️ 1. Instalação e Configuração (No Servidor)

Os comandos executados diretamente no terminal do Linux para instalar e ativar o serviço OpenSSH foram:

```bash
# Atualiza a lista de pacotes do sistema
sudo apt update

# Instala o serviço do servidor SSH
sudo apt install openssh-server -y

# Verifica se o serviço está ativo e rodando corretamente
sudo systemctl status ssh
```

### 📌 Configuração de IP Estático (Direto no Linux)
Como o servidor não possui tela ou teclado, se o IP fosse dinâmico (DHCP), ele mudaria a cada reinicialização, quebrando o acesso remoto. Para garantir consistência, o IP foi fixado diretamente no sistema operacional do servidor como `192.168.1.104`.

A configuração foi aplicada editando o arquivo de interfaces de rede localizado em `/etc/network/interfaces`:

```text
# Configuração da interface de rede cabeada ens32
auto eth0
iface ens32 inet static
    address 192.168.1.104
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 1.1.1.1 8.8.8.8
```

Após editar o arquivo, o serviço de rede foi reiniciado para aplicar as alterações:
```bash
# Reinicia o serviço de rede do sistema
sudo systemctl restart networking
```

---

## 💻 2. Acesso Remoto (A partir do Windows)

Com o servidor conectado à rede, abra o terminal do Windows (Prompt de Comando, PowerShell ou Windows Terminal) e execute o comando de conexão estável:

```bash
ssh levi@192.168.1.104
```

---

## 🎥 3. Demonstração em Vídeo

Assista abaixo à gravação da tela mostrando o processo completo de conexão e autenticação SSH do Windows para o servidor:

https://github.com/user-attachments/assets/b626d9a4-1de3-4b87-a927-34b908c029bc
