# Projeto 06 - Firewall com nftables

## Objetivo

Criar um firewall baseado em política padrão DROP.

## Estrutura

- INPUT
- OUTPUT
- FORWARD

## Serviços permitidos

- SSH
- HTTP
- DNS
- Samba
- ICMP

## Recursos implementados

- Stateful Firewall
- Loopback
- Conexões estabelecidas
- Logs de pacotes descartados

## Nft Rules

```
table inet filter {
        chain input {
                type filter hook input priority filter; policy drop;
                iif "lo" accept
                ct state established,related accept
                ip saddr 192.168.1.0/24 icmp type echo-request accept
                ip saddr 192.168.1.0/24 tcp dport 22 accept
                ip saddr 192.168.1.0/24 tcp dport 80 accept
                ip saddr 192.168.1.0/24 udp dport 53 accept
                ip saddr 192.168.1.0/24 tcp dport 53 accept
                ip saddr 192.168.1.0/24 udp dport { 137, 138 } accept
                ip saddr 192.168.1.0/24 tcp dport { 139, 445 } accept
                limit rate 10/minute burst 5 packets log prefix "NFT_DROP: "
        }

        chain forward {
                type filter hook forward priority filter; policy drop;
        }

        chain output {
                type filter hook output priority filter; policy accept;
        }
}
```
## Teste Porta Não Liberada

```
Test-NetConnection 192.168.1.145 -Port 3389
AVISO: TCP connect to (192.168.1.145 : 3389) failed


ComputerName           : 192.168.1.145
RemoteAddress          : 192.168.1.145
RemotePort             : 3389
InterfaceAlias         : Ethernet
SourceAddress          : 192.168.1.105
PingSucceeded          : True
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : False

```

## Log

```nft
limit rate 10/minute burst 5 packets log prefix "NFT_DROP: "
```

Visualização

```bash
journalctl -k
```

```
jul 26 19:12:35 levi kernel: NFT_DROP: IN=ens32 OUT= MAC=01:00:5e:00:00:fb:b0:25:aa:2c:f1:51:08:00 SRC=192.168.1.105 DST=224.0.0.251 LEN=72 TOS=0x00 PREC=0x00 TTL=1 ID=39710 PROTO=UDP SPT=5353 DPT=5353 LEN=52
jul 26 19:12:35 levi kernel: NFT_DROP: IN=ens32 OUT= MAC=01:00:5e:00:00:fb:b0:25:aa:2c:f1:51:08:00 SRC=192.168.1.105 DST=224.0.0.251 LEN=72 TOS=0x00 PREC=0x00 TTL=1 ID=39711 PROTO=UDP SPT=5353 DPT=5353 LEN=52
jul 26 19:12:36 levi kernel: NFT_DROP: IN=ens32 OUT= MAC=01:00:5e:00:00:fb:b0:25:aa:2c:f1:51:08:00 SRC=192.168.1.105 DST=224.0.0.251 LEN=72 TOS=0x00 PREC=0x00 TTL=1 ID=39712 PROTO=UDP SPT=5353 DPT=5353 LEN=52
jul 26 19:13:29 levi kernel: NFT_DROP: IN=ens32 OUT= MAC=01:00:5e:00:00:01:c0:25:2f:84:28:7f:08:00 SRC=192.168.1.1 DST=224.0.0.1 LEN=32 TOS=0x00 PREC=0x00 TTL=1 ID=29034 PROTO=2
```

## Conceitos aprendidos

- Stateful Firewall
- INPUT
- OUTPUT
- FORWARD
- ACCEPT
- DROP
- Logging
- Rate Limiting