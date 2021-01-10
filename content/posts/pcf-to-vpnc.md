---
title: "PCF to VPNC"
date: 2020-07-05T21:30:11-03:00
draft: false
---

---
Este procedimento é necessário quando, temos um arquivo .pcf que é usado para conectar em uma VPN Cisco via Client do Windows e precisamos conectar em SO Linux ou Unix-like e não sabemos a chave pré-compartilhada.

---

Vamos ao primeiro passo:

* Baixar o pcf2vpnc

```shell
$ wget http://svn.unix-ag.uni-kl.de/vpnc/trunk/pcf2vpnc
```

* Torná-lo executável

```shell
$ chmod +x pcf2vpnc
```

* Mover para o $PATH - opcionalmente /usr/bin

```shell
$ vm pcf2vpnv /usr/local/bin/
```

Segundo, cisco decrypt, esse fará a decriptografia:

* Baixar o decrypt

```shell
$ wget http://www.unix-ag.uni-kl.de/~massar/soft/cisco-decrypt.c
```

* Instalar as dependencias necessários, no caso debian...

```shell
$ sudo apt install libgcrypt-dev libgpg-error-dev
```

* Compilar os binários

```shell
$ gcc -Wall -o cisco-decrypt cisco-decrypt.c $(libgcrypt-config --libs --cflags)
```

* Mover para o $PATH

```shell
$ sudo mv cisco-decrypt /usr/local/bin
```

Agora é hora de gerar o .conf

```shel
$ pcf2vpnc cisco.pcf > client.conf
```


Feito isso basta editar o arquivo client.conf caso necessário, exemplo `user` e mover para o diretório do `VPNC`.

```shell
$ mv client.cong /etc/vpnc/default.conf
```

O comando acima vai sobrescrever o arquivo `default.conf` existente. Já estamos prontos para conectar a VPN, caso tudo tenha ocorrido corretamente, será necessário digitar a senha autenticação.

```shell
$ vpnc
```
