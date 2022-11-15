---
title: "Cisco - AAA usando RADIUS Windows Server 2012"
date: 2022-11-15T15:55:24-03:00
draft: false
tags:
- Redes de computadores
- AAA
- Cisco
- Windows Server 2012
---

---
Uma solução de autenticação centralizada para dispositivos de rede usando os recursos do Windows Server 2012.

---

![cover](https://raw.githubusercontent.com/keilon-araujo/posts/master/AAA-Title.png)

**Cisco - AAA usando RADIUS Windows Server 2012**

Fazer o gerenciamento de acesso à equipamentos de rede como routers, switches em redes de pequeno porte, pode ser feito diretamente no dispositivo, porém, a medida que a rede começa a crescer fica difícil de manter a consistência.

O ideal é desde o início provisionar um servidor de autenticação para dispositivos de rede. Atualmente temos dois protocolos disponíveis para alcançar este objetivo: RADIUS e TACACS+. Ambos tem características diferentes e são mais assertivos para diferentes cenários. 

Neste post vamos focar apenas na configuração do Radius em um Windows Server 2012.

Dada a topologia acima, onde temos a rede onde está localizado o Windows Server e outras duas redes, a rede NOC que representa os usuários que tem apenas acesso de leitura e a rede Engineer, que possui usuário com acesso mais privilegiado.
O dispositivo alvo é o roteador que está configurado com a arquitetura Router-on-a-Stick, onde ele é o gateway de todas as redes e faz o roteamento inter-vlan. É nele que faremos a configuração para que os usuários se autentiquem no Radius Server.
Temos as seguintes interfaces no router:

- WAN: 192.168.77.11
- VLAN10: 172.16.10.1
- VLAN20: 172.16.20.1
- VLAN30: 172.16.30.1

Para este exemplo, cada rede de origem terá um tipo de permissão diferente. Mas a granularidade de configuração no RADIUS permite que essa definição seja feita baseada em grupos de usuários, como veremos mais à frente.

Vamos para o Windows Server 2012, onde iremos instalar o Network Policy Server (NPS) que permitirá usar o RADIUS. Basta ir até o *Server Manager DashBoard*, clicar em  *Add Roles and Features* e em *Server Roles* selecionar a opção *Network Policy and Access Services* e clicar em *Next* até finalizar todas as opções. Feito isso já temos disponível nosso Radius Server.

![NPS-Install](https://raw.githubusercontent.com/keilon-araujo/posts/master/NPS-Install.png)

