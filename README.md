Rede UEMA — Cisco Packet Tracer

Projeto de rede desenvolvido no Cisco Packet Tracer utilizando configuração via CLI.

Sumário

Sobre o projeto

Topologia

Divisão de sub-redes

Plano de endereçamento

Configuração inicial e SSH

Configuração dos roteadores

Configuração dos switches

Configuração dos servidores

Verificação

Sobre o projeto

A atividade implementa uma rede acadêmica dividida entre os setores CCT, NTI, PROGEP e REITORIA, com acesso a uma rede externa por meio de um ISP.

Tecnologias utilizadas

Cisco Packet Tracer

IPv4 e VLSM

VLAN e Router-on-a-Stick

VLAN de gerenciamento

EtherChannel com LACP

DHCP

DNS e HTTP

OSPF

Rotas estáticas

SSH

Topologia

flowchart TD
    ISP["ISP / Internet"] --- NTI["NTI"]
    NTI --- CCT["CCT"]
    NTI --- PROGEP["PROGEP"]
    CCT --- PROGEP
    PROGEP --- REITORIA["REITORIA"]

Equipamentos

Tipo

Quantidade

Identificação

Roteadores

5

CCT, NTI, PROGEP, REITORIA e ISP

Switches

5

SW1_CCT, SW2_CCT, SW_NTI, SW_PROGEP e SW_REITORIA

Servidores

2

SERVER_DNS e SERVER_HTTP

Rede externa

1

HOST_INTERNET

Divisão de sub-redes

A rede interna utilizada é 10.40.0.0/24. A divisão foi realizada com VLSM, começando pelas redes com maior quantidade de hosts.

Máscaras utilizadas

Prefixo

Máscara decimal

Hosts utilizáveis

Salto

/26

255.255.255.192

62

64

/27

255.255.255.224

30

32

/28

255.255.255.240

14

16

/30

255.255.255.252

2

4

Sub-redes internas

Rede

Endereço/prefixo

Primeiro host

Último host

Broadcast

PROGEP

10.40.0.0/26

10.40.0.1

10.40.0.62

10.40.0.63

REITORIA

10.40.0.64/27

10.40.0.65

10.40.0.94

10.40.0.95

NTI

10.40.0.96/27

10.40.0.97

10.40.0.126

10.40.0.127

CCT — VLAN 2

10.40.0.128/27

10.40.0.129

10.40.0.158

10.40.0.159

CCT — VLAN 3

10.40.0.160/27

10.40.0.161

10.40.0.190

10.40.0.191

CCT — VLAN 4

10.40.0.192/28

10.40.0.193

10.40.0.206

10.40.0.207

CCT — VLAN 90

10.40.0.208/28

10.40.0.209

10.40.0.222

10.40.0.223

PROGEP ↔ NTI

10.40.0.224/30

10.40.0.225

10.40.0.226

10.40.0.227

PROGEP ↔ REITORIA

10.40.0.228/30

10.40.0.229

10.40.0.230

10.40.0.231

CCT ↔ PROGEP

10.40.0.232/30

10.40.0.233

10.40.0.234

10.40.0.235

CCT ↔ NTI

10.40.0.236/30

10.40.0.237

10.40.0.238

10.40.0.239

Máscaras curinga do OSPF

Prefixo

Máscara de rede

Máscara curinga

/26

255.255.255.192

0.0.0.63

/27

255.255.255.224

0.0.0.31

/28

255.255.255.240

0.0.0.15

/30

255.255.255.252

0.0.0.3

Plano de endereçamento

Roteadores

Equipamento

Interface

Endereço IP

Destino

CCT

G0/0.2

10.40.0.129/27

VLAN 2

CCT

G0/0.3

10.40.0.161/27

VLAN 3

CCT

G0/0.4

10.40.0.193/28

VLAN 4

CCT

G0/0.90

10.40.0.209/28

VLAN 90

CCT

S0/2/0

10.40.0.237/30

NTI

CCT

S0/2/1

10.40.0.233/30

PROGEP

NTI

G0/0

10.40.0.97/27

LAN NTI

NTI

S0/2/0

10.40.0.238/30

CCT

NTI

S0/3/1

10.40.0.226/30

PROGEP

NTI

S0/3/0

6.7.8.10/30

ISP

PROGEP

G0/0

10.40.0.1/26

LAN PROGEP

PROGEP

S0/3/0

10.40.0.225/30

NTI

PROGEP

S0/3/1

10.40.0.229/30

REITORIA

PROGEP

S0/2/1

10.40.0.234/30

CCT

REITORIA

G0/0

10.40.0.65/27

LAN REITORIA

REITORIA

S0/3/1

10.40.0.230/30

PROGEP

ISP

S0/3/0

6.7.8.9/30

NTI

ISP

G0/0

100.100.100.9/29

HOST_INTERNET

Switches e servidores

Equipamento

Endereço IP

Máscara

Gateway

SW_PROGEP

10.40.0.2

255.255.255.192

10.40.0.1

SW_REITORIA

10.40.0.66

255.255.255.224

10.40.0.65

SW_NTI

10.40.0.98

255.255.255.224

10.40.0.97

SW1_CCT

10.40.0.210

255.255.255.240

10.40.0.209

SW2_CCT

10.40.0.211

255.255.255.240

10.40.0.209

SERVER_DNS

10.40.0.194

255.255.255.240

10.40.0.193

SERVER_HTTP

10.40.0.195

255.255.255.240

10.40.0.193

HOST_INTERNET

100.100.100.10

255.255.255.248

100.100.100.9

Configuração inicial e SSH

Execute esta configuração em cada roteador e switch, alterando o hostname.

<details>
<summary><strong>Abrir configuração-base</strong></summary>

enable

configure terminal

hostname NOME_DO_EQUIPAMENTO

enable secret cisco

service password-encryption

no ip domain-lookup

ip domain-name engcomp.uema.br

username admin privilege 15 secret adminssh

crypto key generate rsa

line console 0
 
 password cisco

 login

exit

line vty 0 4
 login local
 transport input ssh
 exec-timeout 10 0
exit

end
copy running-config startup-config

Quando o IOS solicitar o tamanho da chave RSA, informe 1024.

</details>

Configuração dos roteadores

<details>
<summary><strong>Roteador CCT</strong></summary>

enable
configure terminal
hostname CCT

interface gigabitEthernet 0/0
 no ip address
 no shutdown
exit

interface gigabitEthernet 0/0.2
 encapsulation dot1Q 2
 ip address 10.40.0.129 255.255.255.224
exit

interface gigabitEthernet 0/0.3
 encapsulation dot1Q 3
 ip address 10.40.0.161 255.255.255.224
exit

interface gigabitEthernet 0/0.4
 encapsulation dot1Q 4
 ip address 10.40.0.193 255.255.255.240
exit

interface gigabitEthernet 0/0.90
 encapsulation dot1Q 90 native
 ip address 10.40.0.209 255.255.255.240
exit

interface serial 0/2/0
 ip address 10.40.0.237 255.255.255.252
 no shutdown
exit

interface serial 0/2/1
 ip address 10.40.0.233 255.255.255.252
 no shutdown
exit


ip dhcp excluded-address 10.40.0.129

ip dhcp excluded-address 10.40.0.161

ip dhcp excluded-address 10.40.0.193 10.40.0.195


ip dhcp excluded-address 10.40.0.209 10.40.0.211

ip dhcp pool CCT_VLAN2
 network 10.40.0.128 255.255.255.224
 default-router 10.40.0.129
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

ip dhcp pool CCT_VLAN3
 network 10.40.0.160 255.255.255.224
 default-router 10.40.0.161
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

ip dhcp pool CCT_VLAN4
 network 10.40.0.192 255.255.255.240
 default-router 10.40.0.193
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

ip dhcp pool CCT_VLAN90
 network 10.40.0.208 255.255.255.240
 default-router 10.40.0.209
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

router ospf 1

 router-id 1.1.1.1

 passive-interface default

 no passive-interface serial 0/2/0
 
 no passive-interface serial 0/2/1

 network 10.40.0.128 0.0.0.31 area 0

 network 10.40.0.160 0.0.0.31 area 0

 network 10.40.0.192 0.0.0.15 area 0

 network 10.40.0.208 0.0.0.15 area 0
 
 network 10.40.0.232 0.0.0.3 area 0
 
 network 10.40.0.236 0.0.0.3 area 0

exit

end
copy running-config startup-config

</details>

<details>
<summary><strong>Roteador NTI</strong></summary>

enable
configure terminal
hostname NTI

interface gigabitEthernet 0/0
 ip address 10.40.0.97 255.255.255.224
 no shutdown
exit

interface serial 0/2/0
 ip address 10.40.0.238 255.255.255.252
 no shutdown
exit

interface serial 0/3/1
 ip address 10.40.0.226 255.255.255.252
 no shutdown
exit

interface serial 0/3/0
 ip address 6.7.8.10 255.255.255.252
 no shutdown
exit

ip dhcp excluded-address 10.40.0.97 10.40.0.98

ip dhcp pool LAN_NTI
 network 10.40.0.96 255.255.255.224
 default-router 10.40.0.97
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

ip route 0.0.0.0 0.0.0.0 6.7.8.9

router ospf 1

 router-id 2.2.2.2

 passive-interface default

 no passive-interface serial 0/2/0

 no passive-interface serial 0/3/1

 network 10.40.0.96 0.0.0.31 area 0
 
 network 10.40.0.224 0.0.0.3 area 0

 network 10.40.0.236 0.0.0.3 area 0

 default-information originate

exit

end
copy running-config startup-config

</details>

<details>
<summary><strong>Roteador PROGEP</strong></summary>

enable
configure terminal
hostname PROGEP

interface gigabitEthernet 0/0

 ip address 10.40.0.1 255.255.255.192

 no shutdown

exit

interface serial 0/3/0
 ip address 10.40.0.225 255.255.255.252
 no shutdown
exit

interface serial 0/3/1
 ip address 10.40.0.229 255.255.255.252
 no shutdown
exit

interface serial 0/2/1
 ip address 10.40.0.234 255.255.255.252
 no shutdown
exit

ip dhcp excluded-address 10.40.0.1 10.40.0.2

ip dhcp pool LAN_PROGEP

 network 10.40.0.0 255.255.255.192

 default-router 10.40.0.1
 
 dns-server 10.40.0.194

 domain-name engcomp.uema.br

exit

ip route 10.40.0.64 255.255.255.224 10.40.0.230

router ospf 1

 router-id 3.3.3.3

 passive-interface default

 no passive-interface serial 0/3/0

 no passive-interface serial 0/2/1

 network 10.40.0.0 0.0.0.63 area 0

 network 10.40.0.224 0.0.0.3 area 0
 
 network 10.40.0.232 0.0.0.3 area 0

 redistribute static subnets

exit

end
copy running-config startup-config

</details>

<details>
<summary><strong>Roteador REITORIA</strong></summary>

enable
configure terminal
hostname REITORIA

interface gigabitEthernet 0/0
 ip address 10.40.0.65 255.255.255.224
 no shutdown
exit

interface serial 0/3/1
 ip address 10.40.0.230 255.255.255.252
 no shutdown
exit

ip dhcp excluded-address 10.40.0.65 10.40.0.66

ip dhcp pool LAN_REITORIA
 network 10.40.0.64 255.255.255.224
 default-router 10.40.0.65
 dns-server 10.40.0.194
 domain-name engcomp.uema.br
exit

ip route 0.0.0.0 0.0.0.0 10.40.0.229

end
copy running-config startup-config

</details>

<details>
<summary><strong>Roteador ISP</strong></summary>

enable
configure terminal
hostname ISP

interface serial 0/3/0
 ip address 6.7.8.9 255.255.255.252
 no shutdown
exit

interface gigabitEthernet 0/0
 ip address 100.100.100.9 255.255.255.248
 no shutdown
exit

ip route 10.40.0.0 255.255.255.0 6.7.8.10

end
copy running-config startup-config

</details>

Use clock rate 64000 somente na interface serial identificada como DCE.

Configuração dos switches

<details>
<summary><strong>SW1_CCT</strong></summary>

enable
configure terminal
hostname SW1_CCT

vlan 2
 name CCT_VLAN2
exit
vlan 3
 name CCT_VLAN3
exit
vlan 4
 name SERVIDORES
exit
vlan 90
 name GERENCIAMENTO
exit

interface fastEthernet 0/1

 switchport mode trunk

 switchport trunk native vlan 90
 
 switchport trunk allowed vlan 2,3,4,90
 
 no shutdown

exit

interface fastEthernet 0/2
 
 switchport mode access
 
 switchport access vlan 2
 
 spanning-tree portfast

exit

interface fastEthernet 0/3

 switchport mode access

 switchport access vlan 3
 
 spanning-tree portfast

exit

interface fastEthernet 0/4

 switchport mode access
 
 switchport access vlan 4

 spanning-tree portfast

exit

interface range fastEthernet 0/23 - 24
 
 switchport mode trunk

 switchport trunk native vlan 90
 
 switchport trunk allowed vlan 2,3,4,90

 channel-group 1 mode active
 
 no shutdown

exit

interface port-channel 1

 switchport mode trunk

 switchport trunk native vlan 90
 
 switchport trunk allowed vlan 2,3,4,90

exit

interface vlan 90

 ip address 10.40.0.210 255.255.255.240
 
 no shutdown

exit

ip default-gateway 10.40.0.209

end
copy running-config startup-config

</details>

<details>
<summary><strong>SW2_CCT</strong></summary>

enable

configure terminal

hostname SW2_CCT

vlan 2

 name CCT_VLAN2

exit

vlan 3

 name CCT_VLAN3

exit

vlan 4
 
 name SERVIDORES

exit

vlan 90

 name GERENCIAMENTO

exit


interface fastEthernet 0/2

 switchport mode access
 
 switchport access vlan 2
 
 spanning-tree portfast

exit

interface fastEthernet 0/3

 switchport mode access
 
 switchport access vlan 3

 spanning-tree portfast

exit

interface fastEthernet 0/4

 switchport mode access

 switchport access vlan 4

 spanning-tree portfast

exit

interface range fastEthernet 0/23 - 24

 switchport mode trunk
 
 switchport trunk native vlan 90

 switchport trunk allowed vlan 2,3,4,90
 
 channel-group 1 mode active
 
 no shutdown

exit

interface port-channel 1

 switchport mode trunk
 
 switchport trunk native vlan 90

 switchport trunk allowed vlan 2,3,4,90

exit

interface vlan 90

 ip address 10.40.0.211 255.255.255.240

 no shutdown

exit

ip default-gateway 10.40.0.209

end
copy running-config startup-config

</details>

<details>
<summary><strong>SW_NTI</strong></summary>

enable
configure terminal
hostname SW_NTI

interface vlan 1
 ip address 10.40.0.98 255.255.255.224
 no shutdown
exit

ip default-gateway 10.40.0.97

interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 1
exit

interface range fastEthernet 0/2 - 3
 switchport mode access
 switchport access vlan 1
 spanning-tree portfast
exit

end
copy running-config startup-config

</details>

<details>
<summary><strong>SW_PROGEP</strong></summary>

enable

configure terminal

hostname SW_PROGEP

interface vlan 1
 
 ip address 10.40.0.2 255.255.255.192
 
 no shutdown

exit

ip default-gateway 10.40.0.1

interface fastEthernet 0/1

 switchport mode access

 switchport access vlan 1

exit

interface range fastEthernet 0/2 - 3

 switchport mode access

 switchport access vlan 1
 
 spanning-tree portfast

exit

end
copy running-config startup-config

</details>

<details>
<summary><strong>SW_REITORIA</strong></summary>

enable
configure terminal
hostname SW_REITORIA


interface vlan 1

 ip address 10.40.0.66 255.255.255.224

 no shutdown

exit

ip default-gateway 10.40.0.65

interface fastEthernet 0/1

 switchport mode access
 
 switchport access vlan 1

exit

interface range fastEthernet 0/2 - 3

 switchport mode access
 
 switchport access vlan 1

 spanning-tree portfast

exit

end
copy running-config startup-config

</details>

Configuração dos servidores

SERVER_DNS

Campo

Valor

IP

10.40.0.194

Máscara

255.255.255.240

Gateway

10.40.0.193

DNS

10.40.0.194

Em Services → DNS, ative o serviço e adicione:

Nome

Tipo

Endereço

www.engcomp.uema.br

A

10.40.0.195

SERVER_HTTP

Campo

Valor

IP

10.40.0.195

Máscara

255.255.255.240

Gateway

10.40.0.193

DNS

10.40.0.194

Em Services → HTTP, mantenha o serviço HTTP ativado.

HOST_INTERNET

Campo

Valor

IP

100.100.100.10

Máscara

255.255.255.248

Gateway

100.100.100.9

DNS

10.40.0.194

Verificação

Roteadores

show ip interface brief
show cdp neighbors
show ip route
show ip protocols
show ip ospf neighbor
show ip dhcp pool
show ip dhcp binding

Switches

show ip interface brief
show vlan brief
show interfaces trunk
show etherchannel summary
show cdp neighbors

Testes de conectividade

ping 10.40.0.238
ping 10.40.0.234
ping 10.40.0.230
ping 6.7.8.9
ping 10.40.0.194
ping 10.40.0.195
ping 100.100.100.10

Checklist

Todas as interfaces utilizadas estão up/up.

As VLANs 2, 3, 4 e 90 existem nos dois switches do CCT.

Os troncos transportam as VLANs 2, 3, 4 e 90.

O EtherChannel aparece como Po1(SU).

OSPF está em estado FULL entre CCT, NTI e PROGEP.

A REITORIA alcança as demais redes por meio do PROGEP.

Os computadores recebem endereço por DHCP.

O DNS resolve www.engcomp.uema.br.

O HOST_INTERNET responde aos testes.

Todas as configurações foram salvas.

Autor

Carlos Vinícius Rodrigues Amorim
Engenharia de Computação — UEMA
