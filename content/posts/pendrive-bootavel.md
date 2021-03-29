# Configuração NodeB Huawei



### Objetivos

Este procedimento tem por objetivo a configuração de NodeB Huawei, tanto a versão de software quanto a recuperação de configuração e foi baseado em experiências pessoais.

### Sumário

[1. Requerimentos](#1. Requerimentos)

[2. Conexão](#2. Conexão)

[3. Placas UMPT](#3. Placas UMPT)

[4. Servidor FTP](#4. Servidor FTP)

​	[4.1 Configurando FTPS](#4.1 Configurando FTPS)

[5. Carregando e ativando Software](#5. Carregando e ativando Software)

​	[5.1 Carregando SPL](#5.1 Carregando SPL)

[6. Carregando e ativando Script](#6. Carregando e ativando Script)





### 1. Requerimentos

```
- Notebook
- Cabo Ethernet
- Adaptador USB-Ethernet Huawei
- Backup de Configuração
- Versão de Software a ser instalada
- FTPS Huawei (File Transfer Protocol over SSL)
- Navegador Firefox
```



### 2. Conexão

A conexão Huawei é feita via cabo Ethernet junto ao adaptador Huawei na porta USB da placa UMPT e são 2 os possíveis IPs de acesso, sendo eles:

```
192.168.0.50
255.255.255.0
192.168.0.49
```

````
17.21.2.30
255.255.255.0
17.21.2.15
````

Deve ser usado o adaptador USB Huawei.

<img src="https://raw.githubusercontent.com/keilon-araujo/Huawei/master/usb-ethernet.jpg" alt="usb1" style="zoom: 25%;" />



Com o cabo conectado basta abrir o Firefox e digitar o IP, que irá abrir uma tela de login:

<img src="https://raw.githubusercontent.com/keilon-araujo/Huawei/master/login.PNG" alt="login" style="zoom: 67%;" />

Os dados de login são:

````
user:     admin
password: hwbs@com
````



### 3.  Placas UMPT

Existem alguns modelos diferentes de placas UMPT para diferentes aplicações e deve ser observado na solicitação do material.

![umpt](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/UMPT.PNG)

A mais indicada e que esta disponível para todas as aplicações é `UMPTb1`.



### 4. Servidor FTP

Para transferência tanto do arquivo de configuração quanto do software será usado o FTPS que é fornecido pela própria Huawei e o Laptop será o servidor FTP.

Caso ainda não tenha instalado o FTPS, ao fazer login podemos fazar download da ferramenta e instalar.

![ftptool](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/ftptoll.PNG)



#### 4.1 Configurando FTPS

No servidor FTPS tem que ser apontado o caminho local do software ou script a ser carregado para a NodeB. No exemplo temos a pasta de software que foi inserida dentro da unidade `C:\` do Windows e este caminho tem que ser especificado no FTPS.

Por padrão o login é o mesmo para acessar a NodeB mas pode ser alterado.

![configftp](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/configftp1.jpg)



### 5. Carregando e ativando Software

Com o FTPS configurado podemos carregar o software, para listar os softwares já disponíveis usamos o comando `LST SOFTWARE`.

Para carregar o novo software do laptop para NodeB usamos o comando `DLD SOFTWARE` e setar os seguintes parametros.

* FTP Server IP: deve ser o IP do laptop
* Usuário e Password: Caso não tenha sido alterado será o padrão admin/hwbs@com
* Application Type List: Para garantir que sejam carregados GBTS,NodeB e eNodeB é recomendado especificar neste campo.

Caso tudo ocorra corretamente o status de progressão do upload será mostrado e  ao final `Download Software Success`.

Vamos usar novamente o comando `LST SOFTWARE` para ver que foi carregado.

![lst](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/LST.PNG)

Foi carregado o software `BTS3900_5900 V100R015C10SPC260` que no momento é a versão mais recente da Huawei e as opções GBTS, NodeB, eNodeB e RFA, vamos ativá-las pois só está ativa a versão NodeB.

Usamos o comando `ACT SOFTWARE` em software version deve ser inserida a versão de software a ser ativada, no caso `BTS3900_5900 V100R015C10SPC260` e em Application Type List ativamos as versões abaixo:

![acts](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/ACTS.PNG)

Assim que finalizado o processo com sucesso ao usar o comando `LST SOFTWARE` será mostrado todas as aplicações ativadas:

![lst1](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/LST1.PNG)



### 6. Carregando e ativando Script

Com o script baixado e extraído, ele deve ser apontado no FTPS assim como no passo anterior onde foi carregado o software. No exemplo abaixo será carregado o script do site `S01GOALS02`.

![ftp2](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/ftp2.jpg)



Os parametros a seguir devem ser usados com atenção ao file name que deve ser inserido:

![dldcfg](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/DLDCFG.PNG)

Feito o upload do Script podemos agora listar com o comando `LST CFGFILE` que mostra o arquivo que foi carregado.

![lstcfg](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/LSTCFG.PNG)

Para ativar vamos usar o comando `ACT CFGFILE`:

![actcfg](https://raw.githubusercontent.com/keilon-araujo/Huawei/master/ACTCFG.PNG)



Após rodar este comando a NodeB irá reiniciar e está pronta.