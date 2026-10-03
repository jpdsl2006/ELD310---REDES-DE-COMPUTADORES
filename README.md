# Projeto de Interconexão de Redes

Projeto desenvolvido para a disciplina de Laboratório de Redes - ELD310.

## Integrantes

- João Pedro de Souza Lima - RA: 22.125.101-0
- Luigi Bernardo de Oliveira - RA: 22.126.091-2
- Igor Marques Pieralini - RA: 22.225.027-6
- Gabriel Rocha De Lima - RA: 22.225.001-1

## Descrição

O projeto consiste na implementação de uma rede no Cisco Packet Tracer, utilizando três LANs interligadas por roteadores.

O projeto contempla:

- Subnetting e VLSM
- Endereçamento IPv4
- Roteamento dinâmico RIP
- DHCP
- DNS
- Servidor HTTP
- Rede Wi-Fi com WPA2
- ACL para controle de acesso

## Endereçamento

### LAN1

Rede original: `120.20.30.0/24`

| Sub-rede | Prefixo | Máscara |
| --- | --- | --- |
| 01 | `120.20.30.0/25` | `255.255.255.128` |
| 02 | `120.20.30.128/26` | `255.255.255.192` |

### LAN2

Rede original: `192.168.0.0/16`

Dividida em quatro sub-redes `/18`. Na implementação são utilizadas as duas primeiras.

| Sub-rede | Prefixo |
| --- | --- |
| 01 | `192.168.0.0/18` |
| 02 | `192.168.64.0/18` |
| 03 | `192.168.128.0/18` |
| 04 | `192.168.192.0/18` |

### LAN3

Rede: `10.0.0.0/18`

A LAN3 não possui subdivisão.

## Arquivos

- `.pkt` - Projeto implementado no Cisco Packet Tracer
- `.pdf` - Projeto de endereçamento das sub-redes
