---
title: "Link Aggregation - Ethernet Bonding"
date: 2020-07-15T21:29:54-03:00
draft: false
---

---
 Neste post tentarei explicar como funciona o Link Aggregation, que é bastante comum em Uplinks entre switches e routers mas que também pode ser aplicado em hosts, em nosso caso aplicaremos em um servidor GNU/Linux Debian Buster. 

---


**Como funciona o Link Aggregation?**

O Link Aggregation está descrito na norma IEEE 802.3ad, e possibilita vincular dois ou mais links ethernet através de um único link virtual. 

Esta funcionalidade nos permite prover alta disponibilidade para rescursos importantes em uma rede, neste exemplo será usado um servidor Linux com duas interfaces Ethernet e um módulo que está no kernel chamado `bonding`. 

![lag](https://raw.githubusercontent.com/keilon-araujo/teste/master/lag.png)

O módulo bonding tem 7 modos de operação e cada oferece recursos diferentes. Neste cenário vamos usar `Mode=0 (balance round-robin)` o qual provê tolerancia à falhas dos links e também faz o balanceamento de cargas. Outros parametros também podem ser utilizados, como o `miimom` que define o tempo de espera para monitoramento da interface bounding, `downdelay` que define o tempo de espera para desabilitar uma interface escrava em caso de falha e deve ser usada em conjunto com miimom e também o parametro `updelay` o qual define o tempo de espera para ativar uma interface escrava após detecção de normalização do link principal.


Para configuração vamos utilizar duas interfaces físicas sendo elas `enp1s0` e `enp7s0` como escravas e criaremos uma interface virtual `bond0` no modo `round-robin`:
~~~
miimom=100    -> o link será monitorado a cada 100 ms
downdelay=200 -> a interface só será desabilitada em caso de falha após 200 ms
updelay=220   -> tempo em ms que o link irá retomar após falha
~~~

O Debian Buster temos que instalar o `ifenslave` que irá ativar/desativar as interfaces, como usuário root vamos às configurações:

~~~
root@server~# apt install ifenslave
~~~

Em seguida ativamos o módulo bonding no Kernel:

~~~
root@server~# modprobe bonding
~~~

Verificando se o módulo foi ativado:

~~~
root@server~# lsmod | grep bonding
bonding               180224  0
~~~

O próximo passo é configurar as interfaces, aqui será criado o link virtual `bond0` e as interfaces que farão parte do Link Aggregation serão declaradas dentro desta. No Debian Buster todas interfaces são configuradas em um único arquivo que fica em `/etc/network/interfaces`.

~~~
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
#allow-hotplug enp1s0
#iface enp1s0 inet dhcp
#

# Interface bonding
# Static IP address
auto bond0
iface bond0 inet static
        bond-slaves enp1s0 enp7s0
        bond-mode  balance-rr
        bond-miimon 100
	bond-updelay 220
	bond-downdelay 220
        bond-primary enp1s0 enp7s0

        address 192.168.122.188
        netmask 255.255.255.0
        network 192.168.122.0
        broadcast 192.168.122.255
        gateway 192.168.122.1
~~~

Feito isso nossa configuração está pronta, basta reiniciar os serviços de rede:

~~~
root@server:~# /etc/init.d/networking restart
~~~


Para verificar se tudo está funcionando corretamente vamos rodar o comando `ip addr show`

~~~
root@server:~$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master bond0 state UP group default qlen 1000
    link/ether 52:54:00:82:9d:48 brd ff:ff:ff:ff:ff:ff
3: enp7s0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast master bond0 state UP group default qlen 1000
    link/ether 52:54:00:82:9d:48 brd ff:ff:ff:ff:ff:ff
4: bond0: <BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 52:54:00:82:9d:48 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.188/24 brd 192.168.122.255 scope global bond0
       valid_lft forever preferred_lft forever
~~~


Podemos perceber que as interfaces enp1s0 e enp7s0 não receberam enderaçamento IP e a interface virtual `bond0` está endereçada, podemos ainda ver o processo rodando em `/proc` com o comando `cat /proc/net/bonding/bond0` :

~~~
Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

Bonding Mode: load balancing (round-robin)
MII Status: up
MII Polling Interval (ms): 100
Up Delay (ms): 200
Down Delay (ms): 200

Slave Interface: enp1s0
MII Status: up
Speed: Unknown
Duplex: Unknown
Link Failure Count: 0
Permanent HW addr: 52:54:00:82:9d:48
Slave queue ID: 0

Slave Interface: enp7s0
MII Status: up
Speed: 1000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 52:54:00:3e:74:69
Slave queue ID: 0
~~~


Nosso servidor já está rodando com balanceamento de carga e tem tolerancia à falhas, qualquer interface física que vier a cair, a segunda continuará rodando. 



`Referência: https://servidordebian.org/pt/squeeze/config/network/bonding`
