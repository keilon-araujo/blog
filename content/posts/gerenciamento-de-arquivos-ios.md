---
title: "Gerenciamento De Arquivos no Cisco IOs"
date: 2020-11-04T21:31:07-03:00
draft: false
tags:
- Cisco IOS
- Arquivos
- Redes de Computadores
---

---

É de grande importância saber trabalhar com o sistema de arquivos no Cisco IOS, tanto para implantação de novos equipamento, backup de arquivos e configuração e até mesmo recuperação de um equipamento venha a ser danificado.

---



Antes de verificar o sistema de arquivos no Cisco IOS, precisamos conhecer os tipos de memórias que é onde serão armazenados os arquivos, configuração, IOS e etc.

Todos os exemplos aqui demonstrados foram feitos utilizando o Cisco C1900 Series com IOS versão 15. 

Tipos de memórias memórias:

**DRAM - Dynamic Random-Access Memory**:  é uma memória volátil, ou seja as informações são carregadas no momento do boot mas quando o equipamento é desligado a informação não fica gravada, comparável à memória RAM nos Computadores.

**EPROM -** **Erasable Programmable Read-Only Memory**: Também chamada de memória ROM, é do tipo não volátil. É utilizada para armazenar o ROM Monitor Software (ROMMOM), e o bootloader que permite acessar o equipamento mesmo se estiver sem o IOS.

**NVRAM - Non-Volatile Rando-Access Memory:** Responsável por armazenar a startup-config e também o configuration-register.

**Flash**: É usada para armazenar o IOS e outros arquivos, podemos comparar aos HDs ou SSDs dos computadores.

## Comandos

**show file system:** comando usado para mostrar o sistema de arquivos da caixa além de informações da nvram,flash e sua capacidade de armazenamento.

No exemplo abaixo o asterisco ( * ) nos mostra qual unidade de armazenamento padrão, que é *usbflash0* e tem aproximadamente 256mb, a *nvram* possui armazenamento de 255k além de uma unidade removível usb que foi inserida e tem aproximadamente 3.5gb.

As Flags *ro, rw e wo* descrevem as permissões para o sistema de arquivos.



```
Router#show file systems 
File Systems:

       Size(b)       Free(b)      Type  Flags  Prefixes
             -             -    opaque     rw   archive:
             -             -    opaque     rw   system:
             -             -    opaque     rw   tmpsys:
             -             -    opaque     rw   null:
             -             -   network     rw   tftp:
        262136        255065     nvram     rw   nvram:
             -             -    opaque     wo   syslog:
             -             -    opaque     rw   xmodem:
             -             -    opaque     rw   ymodem:
             -             -   network     rw   rcp:
             -             -   network     rw   http:
             -             -   network     rw   ftp:
             -             -   network     rw   scp:
             -             -    opaque     ro   tar:
             -             -   network     rw   https:
             -             -    opaque     ro   cns:
*    255537152     209698816  usbflash     rw   usbflash0: flash:
    3505545216    3505541120  usbflash     rw   usbflash1:
```

**pwd**: *print working directory*, para se localizar a qualquer momento em qual diretório estamos trabalhando este é o comando.

```
Router#pwd
usbflash0:
```



**cd**: *change directory*, este é o comando que nos permite passar de um diretório para outro apontando o caminho do diretório que se deseja alcançar.

```
Router#cd usbflash0:backup
```



**dir**: mostra uma lista de arquivos e diretórios em um diretório específico.

```
Router#dir
Directory of usbflash0:/

    4  drw-           0   Nov 5 2020 00:21:56 +00:00  backup
    1  drw-           0   Nov 3 2020 15:46:24 +00:00  back
    3  -rw-    45832792   Jul 2 2015 22:03:42 +00:00  c1900-universalk9-mz.SPA.150-1.M8.bin
```



**mkdir** permite criar um novo diretório.

```
Router#mkdir backup 
Create directory filename [backup]? 
Created dir usbflash0:backup
```



**rmdir**: permite remover um diretório, um detalhe é que caso o diretório contenha algum arquivo ou até mesmo outro diretório o comando não vai funcionar, tendo efeito somente em diretórios vazios. No exemplo abaixo não é possível remover o diretório *back* pois não é um diretório vazio.

```
Router#rmdir usbflash0:back
Remove directory filename [back]? 
Delete usbflash0:back? [confirm]
%Error Removing dir usbflash0:back (Can't delete a directory that has files in it)
```



**delete**: permite deletar arquivos dentro file system, um detalhe que ele pode ser forçado e apagar até mesmo um diretório com vários arquivos dentro usando a opção *delete /recursive /force /pasta*.

```
Router#delete usbflash0:/back/bkpp
Delete filename [/back/bkpp]? 
Delete usbflash0:/back/bkpp? [confirm]	
```



**format**: permite formatar um file system, pode ser usado até mesmo para particionar um pen drive inserido por exemplo.

```
Router#format ?
  flash:      Filesystem to be formatted
  usbflash0:  Filesystem to be formatted
```



**erase:** permite apagar os arquivos na nvram, o *startup-config* por exemplo.

```
Router#erase ?
  /all                       Erase all files(in NVRAM)
  /no-squeeze-reserve-space  Do not reserve space for squeeze operation
  nvram:                     Filesystem to be erased
  startup-config             Erase contents of configuration memory
```



**verify** : verifica a integridade dos arquivos, ai disponibilizar um IOS a Cisco disponibiliza juntamente o MD5 para checar que não foi alterado do servidor da Cisco até o usuário que está fazendo o Download.

```
Router#verify flash:c1900-universalk9-mz.SPA.150-1.M8.bin
Starting image verification
Hash Computation:    100% Done!
Computed Hash   SHA2: 006062C86B23F1186BF72FB628D8C63C
                      92787CF94FDD436BC88BE77BBE373B14
                      E6021DAD66E1D25ABA676B0A7B95F9D2
                      8A0778E8F70C6D3A48353F7C2566CCF1
                      
Embedded Hash   SHA2: 006062C86B23F1186BF72FB628D8C63C
                      92787CF94FDD436BC88BE77BBE373B14
                      E6021DAD66E1D25ABA676B0A7B95F9D2
                      8A0778E8F70C6D3A48353F7C2566CCF1
                      
CCO Hash        MD5 : 6DCA357446C1E6ED204F0C578CC465DC
Digital signature successfully verified in file usbflash0:c1900-universalk9-mz.SPA.150-1.M8.bin
```

```
Router#verify /md5 usbflash0:c1900-universalk9-mz.SPA.150-1.M8.bin
..................................................................
Done!
verify /md5 (usbflash0:c1900-universalk9-mz.SPA.150-1.M8.bin) = 6dca357446c1e6ed204f0c578cc465dc
```

Pode ser checado também arquivos de configuração como o startup-config que pode ter sido baixado de um servidor FTP, para garantir que não houve alteração rodamos o comando:

```
Router#verify /md5 nvram:startup-config
.Done!
verify /md5 (nvram:startup-config) = d65faaba055e3c829420dadddd808c6f
```

Este md5 tem que ser igual ao que está no servidor e este por sua vez pode também ter sido verificado anteriormente.

**fsck:** Faz a checagem do sistema de arquivos para verificar se há alguma falha na unidade *flash* por exemplo.

```
Router#fsck ?
  /all        Check all partitions of this disk
  /force      Force fsck of disk
  flash:      Filesystem to be checked
  usbflash0:  Filesystem to be checked
  <cr>
```

**copy:** permite copiar arquivos ou diretórios de várias origens, por exemplo de um servidor FTP ou TFTP para a unidade flash. É um comando bem versátil.

```
Router#copy startup-config ftp
Address or name of remote host []? 192.168.1.1
Destination filename [router-confg]? 
Writing router-confg 
```



**rename:** renomear arquivos ou diretorios.

```
Router#rename usbflash0:/back/startup-config config-old
Destination filename [/back/config-old]? 
```



**show version:** um comando que mostra várias informações do equipamento Cisco.

```
Router#show version 
Cisco IOS Software, C1900 Software (C1900-UNIVERSALK9-M), Version 15.0(1)M8, RELEASE SOFTWARE (fc1)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2012 by Cisco Systems, Inc.
Compiled Wed 15-Feb-12 21:27 by prod_rel_team

ROM: System Bootstrap, Version 15.0(1r)M15, RELEASE SOFTWARE (fc1)

Router uptime is 56 minutes
System returned to ROM by power-on
System restarted at 23:32:36 UTC Mon Nov 2 2020
System image file is "usbflash0:c1900-universalk9-mz.SPA.150-1.M8.bin"
Last reload type: Normal Reload


This product contains cryptographic features and is subject to United
States and local country laws governing import, export, transfer and
use. Delivery of Cisco cryptographic products does not imply
third-party authority to import, export, distribute or use encryption.
Importers, exporters, distributors and users are responsible for
compliance with U.S. and local country laws. By using this product you
agree to comply with applicable laws and regulations. If you are unable
to comply with U.S. and local laws, return this product immediately.
          
A summary of U.S. laws governing Cisco cryptographic products may be found at:
http://www.cisco.com/wwl/export/crypto/tool/stqrg.html

If you require further assistance please contact us by sending email to
export@cisco.com.

Cisco CISCO1905/K9 (revision 1.0) with 225280K/36864K bytes of memory.
Processor board ID FTX16218269
2 Gigabit Ethernet interfaces
1 Serial(sync/async) interface
DRAM configuration is 64 bits wide with parity disabled.
255K bytes of non-volatile configuration memory.
249840K bytes of USB Flash usbflash0 (Read/Write)
3430096K bytes of USB Flash usbflash1 (Read/Write)


License Info:

License UDI:

-------------------------------------------------
Device#   PID                   SN
-------------------------------------------------
*0        CISCO1905/K9          FTX16218269     



Technology Package License Information for Module:'c1900' 

-----------------------------------------------------------------
Technology    Technology-package           Technology-package
              Current       Type           Next reboot  
------------------------------------------------------------------
ipbase        ipbasek9      Permanent      ipbasek9
security      None          None           None
data          None          None           None

Configuration register is 0x2102
```

Dentre outras podemos extrair as seguintes informações:

* 225MB de DRAM
* 255K de NVRAM
* 250MB de Flash
* ~3.5GB de Flash USB (Pen Drive)
* 2 Gigabit Ethernet interfaces
* 1 Serial(sync/async) interface
* Configuration register is 0x2102

**show flash:** descreve a memória *flash* e mostra os arquivos e tamanho dos mesmo.

```
Router#show flash: 
-#- --length-- -----date/time------ path
1     45832792 Jul 02 2015 22:03:42 c1900-universalk9-mz.SPA.150-1.M8.bin
2            0 Nov 03 2020 00:22:28 backup

209698816 bytes available (45838336 bytes used)
```

* Podemos notar que o IOS tem aproximadamente 45mb.



