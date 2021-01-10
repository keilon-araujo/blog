---
title: "Conexão USB-Serial via Terminal no Linux"
date: 2020-08-29T21:30:24-03:00
draft: false
---

---
 Neste post farei uma breve explicação de como se conectar à dispositivos de rede via cabo USB-Serial, usado em vários dispositivos de Rede como roteadores e Switches.

---


**Como usar USB-Serial no linux para conexão em via Terminal**

A maioria dos dispositivos de Rede oferecem a conexão serial-usb como interface de manutenção e configuração, apesar de muitos já oferecerem uma interface gráfica, muitos administradores ainda preferem estar conectados via terminal, no caso do Windows basta instalar o drive do adaptador USB e conectar via cliente porém no Linux o módulo precisa ser carregado e neste exemplo iremos usar um cliente via Terminal.

Após conectar o dispositivo serial em alguma das entradas USB vamos usar o comando `lsusb` que identifica as conexões USB.

![lsusb](https://raw.githubusercontent.com/keilon-araujo/posts/master/lsusb-1.png)

Neste exemplo o nosso dispositivo tem a seguinte identificação:

```
Bus 001 Device 024: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port
```

Agora é necessário carregar o módulo do dispositivo, de acordo com as informações obtidas no comando anterior:

![modprobe](https://raw.githubusercontent.com/keilon-araujo/posts/master/modprobe-1.png)

Para verificar se realmente o módulo foi carregado e está pronto para conexão vamos usar o `dmesg`:

![dmesg](https://raw.githubusercontent.com/keilon-araujo/posts/master/dmesg-1.png)

Basta agora dar as permissões de leitura e escrita ao nosso dispositivo que está identificado como ttyUSB0.

![chmod](https://raw.githubusercontent.com/keilon-araujo/posts/master/chmod-1.png)



Nosso dispositivo está pronto, podemos ainda tornar esta configuração persistente editando o arquivo `/etc/modules` e inserindo ao final do arquivo a seguinte linha `usbserial vendor=0x067b product=0x2303`.

Agora é necessário um cliente para conectar ao dispositivos, as opções são várias como: putty, teraterm, secureCRT, etc. para este exemplo vamos continuar no terminal e usar o `cu` que é extremamente leve e está disponível na maioria dos repositórios. No caso Debian e derivados pode ser instalado com o comando `apt install cu`.

Para conectar vamos usar o comando `cu -l /dev/ttyUSB0 -s 9600`, onde a opção `-l` nos permite apontar o dispositvo USB e a opção `-s` permiteinformar o baud rate(taxa de transmissão), para dispositivos Cisco o padrão é 9600, em dispositivos Nokia por exemplo, esse valor é 155200.

![cu](https://raw.githubusercontent.com/keilon-araujo/posts/master/cu-1.png)

Para encerrar a sessão é simples, ainda com a sessão aberta no terminal basta digitar o comando `~.` que a sessão será encerrada.

![close](https://raw.githubusercontent.com/keilon-araujo/posts/master/close-1.png)

