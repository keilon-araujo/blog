---
title: "Criando Pendrive Bootavel no Linux"
date: 2021-03-28T14:44:05-03:00
draft: false
tags:
- Pendrive Bootável
- Linux
- dd
- pv
---

---
Para um usuário linux sem muita experiência em Linux criar um pen drive bootável pode ser um problema e para facilitar essa tarefa trago este post que de maneira simples resolve esta questão.

---

[1. Overview](#1. Overview)

[2. Listando as unidades disponíveis](#2. Listando as unidades disponíveis)

[3. Criando o Pen Drive Bootável](#3. Criando o Pen Drive Bootável)

[4. Usando o PV](#4. Usando o PV)

[5. Resumo](#5. Resumo)



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



### 3. Criando o Pen Drive Bootável

Agora que identificamos `/dev/sdb` como a unidade que vamos montar a imagem, vamos de fato montar botar a mão na massa.

O comando será `sudo dd if=~/Downloads/debian-10.8.0-amd64-netinst.iso of=/dev/sdb`, onde `if=` é o arquivo de origem, ou seja, a ISO que baixamos, neste caso temos um `debian netinst`. `of=` é o caminho da unidade onde a imagem será montada, neste caso `/dev/sdb`. Caso esteja logado com usuário root não é necessário o `sudo` no início do comando.

Após dar Enter no comando acima a tarefa seja executada e o prompt ficará ocupado e com o cursor piscando até que seja finalizado, caso nada de errado ocorra temos o pen drive bootável pronto para uso.

### 4. Usando o PV

Caso deseje acompanhar de forma dinâmica, o `pv` é uma solução que mostra uma barra de progresso da nossa tarefa. Ele não vem instalado por padrão na maioria das distribuições então pode ser necessário instalar com o comando `apt install pv` para distribuições debian-based.

Com o PV o comando fica um pouco diferente, `~/Documents/blog$ pv ~/Downloads/debian-10.8.0-amd64-netinst.iso | sudo dd of=/dev/sdb`.

Ao final da barra de progresso temos o pen drive pronto!

```
use@dragon:~/Documents/blog$ pv ~/Downloads/debian-10.8.0-amd64-netinst.iso | sudo dd of=/dev/sdb

 336MiB 0:00:01 [ 242MiB/s] [============================================================>] 100%            
688128+0 registros de entrada
688128+0 registros de saída
352321536 bytes (352 MB, 336 MiB) copiados, 1,38123 s, 255 MB/s
```

### 5. Resumo

É bem simples a criação de um pen drive bootável usando este método, aqui um resumo:

1. Identificar a unidade com `lsblk`
2. Usar o comando ``sudo dd if=~/Downloads/debian-10.8.0-amd64-netinst.iso of=/dev/sdb`
3. Caso deseje usar a barra de progresso deve ser instalado o `pv` e o comando `~/Documents/blog$ pv ~/Downloads/debian-10.8.0-amd64-netinst.iso | sudo dd of=/dev/sdb`



> Vale lembrar que esta tarefa apagará todos os itens da unidade apontada para montagem do pen drive bootável, então faça um backup dos seus arquivos.







