---
title: "Criando Pendrive Bootavel no Linux"
date: 2021-03-28T14:44:05-03:00
draft: false
---

---
Para um usuário linux sem muita experiência em Linux criar um pen drive bootável pode ser um problema e para facilitar essa tarefa trago este post que de maneira simples resolve esta questão.

---

[1. Overview](#1. Overview)

[2. Listando as unidades disponíveis](#2. Listando as unidades disponíveis)

[3. Formatando a unidade](#3. Formatando a unidade)





### 1. Overview

Vamos realizar esta tarefa via terminal, ou seja, usando linhas de comando. Isto porque a partir de qualquer distribuição Linux será possível criar o pen drive bootável. 

A ferramenta aqui usada será o `dd` que é um ferramenta de linha de comando que por padrão vem em todas as distribuições GNU/Linux e faz parte do pacote `coreutils`.

Usando o comando `man` temos a seguinte saída para descrever o `dd`:


```
DD(1) 

NOME
       dd - converte e copia um arquivo

SINOPSE
       dd [--help] [--version] [if=arquivo] [of=arquivo] [ibs=bytes] [obs=bytes] [bs=bytes] [cbs=bytes] [skip=blocos] [seek=blocos] [count=blocos] [conv={ascii, ebcdic, ibm, block, unblock, lcase, ucase, swab, noerror, notrunc, sync}]

DESCRIÇÃO
       dd copia um arquivo (da entrada padrão para a saída padrão, por padrão) usando tamanhos de blocos de entrada e saída especificados, enquanto está fazendo opcionalmente conversões nele.
```



### 2. Listando as unidades disponíveis

Formatar uma unidade não desejada pode ser uma dor de cabeça e tanto, por isso é importante prestar atenção nesse item. O comando `lsblk` vai nos mostrar as unidade.

```
user@dragon:~/Documents/blog$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 223,6G  0 disk 
├─sda1   8:1    0   487M  0 part /boot/efi
├─sda2   8:2    0   3,8G  0 part [SWAP]
└─sda3   8:3    0 219,3G  0 part /
sdb      8:16   1   7,3G  0 disk 
├─sdb1   8:17   1   415M  0 part /media/user/flashdrive
└─sdb2   8:18   1   6,4M  0 part 
```

A saída do comando `lsblk` nos mostra duas unidades de disponíveis, `sda` e `sdb`, sendo que `sda` é onde está nosso sistema operacional com as respectivas partições `/boot, /swap` e `/`. `sdb` que é a unidade que vamos montar pen drive bootável está identificado e tem volume de 7,3G. 



### 3. Formatando a unidade

