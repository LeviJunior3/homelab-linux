# 🐧 Instalação e Configuração do Debian Server

Este módulo aborda o processo de provisionamento, verificação de recursos de hardware e liberação de acesso remoto via SSH em um servidor Linux Debian.

---

## 1. Verificação de Recursos do Sistema
Após concluir a instalação básica, o primeiro passo é validar se os recursos de hardware (armazenamento e memória) foram alocados corretamente no sistema.

### 💾 Alocação de Armazenamento (`df -h`)
Exibe o espaço livre e utilizado nas partições montadas no disco rígido.
```bash
Sist. Arq.      Tam. Usado Disp. Uso% Montado em
udev            974M     0  974M   0% /dev
tmpfs           197M  3,7M  193M   2% /run
/dev/sdb2       224G  1,2G  223G   1% /
tmpfs           983M     0  983M   0% /dev/shm
tmpfs           1,0M     0  1,0M   0% /run/credentials/systemd-journald.service
tmpfs           5,0M     0  5,0M   0% /run/lock
/dev/sda1       293G  991M  292G   1% /home
tmpfs           983M     0  983M   0% /tmp
/dev/sdb1       920M   36M  869M   4% /boot
tmpfs           1,0M     0  1,0M   0% /run/credentials/getty@tty1.service
tmpfs           197M  4,0K  197M   1% /run/user/1000
```

### 🧠 Uso de Memória RAM (`free -h`)
Verifica a quantidade de memória RAM física e partições Swap disponíveis e em uso.
```bash
               total       usada       livre    compart.  buff/cache  disponível
Mem.:          1,9Gi       258Mi       1,6Gi       3,7Mi       189Mi       1,7Gi
Swap:          3,8Gi          0B       3,8Gi
```

### 📊 Estrutura de Blocos (`lsblk`)
Lista os detalhes sobre todos os dispositivos de bloco (discos e partições) conectados à máquina.
```bash
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 298,1G  0 disk
└─sda1   8:1    0 298,1G  0 part /home
sdb      8:16   0 232,9G  0 disk
├─sdb1   8:17   0   953M  0 part /boot
├─sdb2   8:18   0 228,2G  0 part /
└─sdb4   8:20   0   3,8G  0 part [SWAP]
sdc      8:32   1     0B  0 disk
sdd      8:48   1     0B  0 disk
```

---

## 2. Configurações de Rede e Hostname

### 🪪 Identificação do Servidor (`hostnamectl`)
Mostra informações detalhadas sobre o nome da máquina, arquitetura e a versão do Kernel instalada.
```bash
Static hostname: levi
Icon name: computer-desktop
Chassis: desktop 🖥️
Operating System: Debian GNU/Linux 13 (trixie)
Kernel: Linux 6.12.86+deb13-amd64
Architecture: x86-64
Hardware Vendor: Semp Toshiba Informatica Ltda
Hardware Model: STI
Firmware Version: 080014
Firmware Date: Wed 2009-05-27
Firmware Age: 17y 1month 4w 2d
```

### 🌐 Endereçamento de Rede (`ip a`)
Identifica as interfaces de rede ativas e o endereço IP privado atribuído ao servidor para conexões locais.
```bash
2: ens32: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:25:11:f7:2c:02 brd ff:ff:ff:ff:ff:ff
    altname enp1s0
    altname enx002511f72c02
    inet 192.168.1.145/24 brd 192.168.1.255 scope global ens32
       valid_lft forever preferred_lft forever
    inet6 fe80::225:11ff:fef7:2c02/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
```

---

## 3. Acesso Remoto Segurado (SSH)

### ⚙️ Status do Serviço (`systemctl status ssh`)
Valida se o servidor OpenSSH está ativo, em execução e escutando na porta correta (padrão 22).
```bash
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-07-26 17:36:13 -03; 30min ago
 Invocation: 6fe2fef8b10d42bbb67bf5af92615160
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 664 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 698 (sshd)
      Tasks: 1 (limit: 2335)
     Memory: 11.1M (peak: 28.5M)
        CPU: 228ms
     CGroup: /system.slice/ssh.service
             └─698 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"
```
