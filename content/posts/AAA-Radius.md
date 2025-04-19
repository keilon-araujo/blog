---
title: "Cisco - AAA usando RADIUS Windows Server 2012"
date: 2025-19-15T01:05:24-03:00
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

Para este Lab é necessário criar dois grupos de usuários no Windows: GRP-N3 para privilege 15 e GRP-N1 para privilege 1. Para o GRP-N3 temos o usuário engineer e para GRP-N1 o usuário noc.

Agora é o momento de criar as policies que serão os níveis de permissão de acesso. Para simplificar teremos apenas dois tipos de acesso: privilege=1 para o NOC e privilege=15 para os engineers. Para iniciar vamos abrir o Dashboard do Network Policy Server e em Policies, clicar com botão direto e depois em New.

![new](https://raw.githubusercontent.com/keilon-araujo/posts/master/new.png)

Com a nova janela aberta, em *Overview*, vamos dar o nome à policy (PRIV-15_policy) e marcar a opcão *Policy enabled* e *Grant Access*, para que a política seja criada e seja habilitada. 

![overview](https://raw.githubusercontent.com/keilon-araujo/posts/master/Overview.png)

Na aba *Conditions* vamos clicar em *Add* e depois e selecionar *Windows Groups*, para nosso Lab teremos dois grupos, neste momento vamos adicionar somente o GRP-N3, que terá privilégio total ao router.

![conditions](https://raw.githubusercontent.com/keilon-araujo/posts/master/conditions.png)

Na aba *Constraints*, neste momento é interessante alterar apenas o campo de *Authentication Methods* e deixar marcado apenas  a opcão *Unecrypted authentication (PAP, SPAP)*.

![constraints](https://raw.githubusercontent.com/keilon-araujo/posts/master/constraints.png)

Na aba *Settings* teremos as configuracões de RADIUS, em *Standard* vamos usar o *Attribute Service-Type Login*.

![standard](https://raw.githubusercontent.com/keilon-araujo/posts/master/Standard.png)


Em *Vendor Specific* vamos deixar os atributos como mostrados abaixo, que dará ao grupo GRP-N3 o acesso ao roteador com privilégio level 15. Feito isso a configuracão no Radius está pronta.

![vendor](https://raw.githubusercontent.com/keilon-araujo/posts/master/vendor-specific.png)

Para adicionar o grupo GRP-N1 basta seguir os mesmo passos alterando apenas o grupo adicionado e o shell privilege que será lelvel 1.

Criadas as políticas de acesso é hora de adicionar os Radius clientes, que serão os dispositivos que faram as solicitacões de validacão de credenciais. Neste caso o nosso router.

Para isto, basta expandir o menu *RADIUS Clients and Server* e em *RADIUS Clients* clicar com botão direito em *New*, aqui teremos que marcar a checkbox "Enable this RADIUS client", dar um nome ao dispositivo e depois adicionar o IP abaixo. Aqui o item mais importante é "Shared secret" que é a chave compartilhada entre o RADIUS Server e o client, essa mesma chave será configurada no router.
Na aba *Advanced*, em *Vendor name* vamos alterar para *Cisco*.
Aqui a configuracão do Radius Server está pronta.

![client](https://raw.githubusercontent.com/keilon-araujo/posts/master/R-client-settings.png)

A configuração no switch é a seguinte:

~~~
!
aaa group server radius GRP-RADIUS
 server name NPS-01
 ip radius source-interface Ethernet0/1
!
aaa authentication login VTY_ACCESS group GRP-RADIUS local
aaa authorization exec default group GRP-RADIUS if-authenticated
!
username admin.local privilege 15 password 7 133112011F5D5679
!
radius server NPS-01
 address ipv4 192.168.77.66 auth-port 1812 acct-port 1813
 key 7 0812494D1B1C11464058
!
line vty 0 4
 login authentication VTY_ACCESS
 transport input ssh
!
~~~

O *aaa authorization exec default group GRP-RADIUS if-authenticated* garante que o nível de privilégio criado na política do NPS será aplicado, caso contrário, o usuário autenticado teria o privilégio 1.
