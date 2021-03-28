---
title: "Pendrive Bootavel no Linux"
date: 2021-03-28T14:44:05-03:00
draft: false
---

---
Para um usuário linux sem muita experiência em Linux criar um pen drive bootável pode ser um problema e para facilitar essa tarefa trago este post que de maneira simples resolve esta questão.

---


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


