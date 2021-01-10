---
title: "Protocolo OSPF - Formação de Vizinhança"
date: 2020-09-01T21:30:35-03:00
draft: false
tags:
- OSPF
- Redes de Computadores
- Cisco
- Linux
---

---
O protocolo OSPF (*Open Shortest Path First*) é um protocolo de roteamento de redes e foi desenvolvimento pelo IETF (Internet Engineering Task Force), trabalha diretamente sobre o IP com protocolo número 89. É um protocolo aberto sendo suportado pela maioria dos fabricantes e também por sistemas operacionais Linux, BSD's e Unix e usa o algoritmo de Dijkstra para determinar o melhor caminho para cada rede.

---

Este é um breve resumo do OSPF e algumas de suas atribuições, cabendo ainda um estudo mais aprofundado para implantação em redes de grande porte e tem referência aos comandos dos roteadores Cisco.

O OSPF é um protocolo de roteamento que utiliza padrão aberto, classless, com convergência rápida e usa o custo como uma métrica.

Os domínios de roteamento do OSPF são divididos em áreas, e deve conter obrigatoriamente a área **zero** a qual todas as outras áreas devem estar conectadas, essa divisão em áreas traz alguns benefícios:

* Minizar o número de entradas na tabela de roteamento;
* Minizar a inundação de LSA's a uma área específica;
* Minimizar o impacto de mudanças de topologia, uma vez que um link fica Down o SPF irá rodar novamente o algoritmo naquela área a qual pertence;
* Reforçar o conceito de um projeto de rede.

<img src="https://raw.githubusercontent.com/keilon-araujo/posts/master/OSPF-1.jpg" alt="area-0" style="zoom:80%;" />



O cabeçalho do OSPF, traz algumas informaçoes como a versão sendo v2 para IPv4 e v3 para IPv6, o campo `Type` identifica o tipo de pacote a ser transportado que no total são 5, além de `Router ID` e `Area ID` e autenticação caso seja configurado.



```
      0                   1                   2                   3
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |   Version     |     Type      |         Packet length         |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |                          Router ID                            |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |                           Area ID                             |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |           Checksum            |             AuType            |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |                       Authentication                          |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
      |                       Authentication                          |
      +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```



Os cindo pacotes transpostados pelo OSPF são:

| Tipo | Pacote               | Função                                                       |
| ---- | -------------------- | ------------------------------------------------------------ |
| 1    | Hello                | Descoberta/Manutenção de vizinhança                          |
| 2    | Database Description | é um resumo do LSDB (banco de dados link-state), incluindo o Router ID e o número de squencia de cada LSA no LSDB |
| 3    | Link State Request   | Solicita o LSU do vizinho                                    |
| 4    | Link State Update    | Contém entrada LSA completa, incluindo informações de topologia por exemplo, o Router ID do roteador local eo RID e custo de cada vizinho. |
| 5    | Link State Ack       | usado para reconhecer o recebimento dos outros pacotes, exceto o hello e o próprio LSAck |



Os roteadores rodando o OSPF possuem três tabelas para administração do protocolo de roteamento, são elas:

- **Adjacency Database (Neighbor Table)**: Guarda as informações das vizinhanças descobertas pelo OSPF através do protocolo `Hello`.
- **Link-State Database (Topology Table)**: Guarda a base de dados topológica do OSPF, ou seja, os LSA's que foram recebidas pelo roteador e serão utilizadas pelo SPF para descobrir as melhores rotas.
- **Fowarding Database (Routing Table)**: As melhores rotas serão inseridas na tabela de roteamento.



Os pacotes de roteamento OSPF (com exceção de Hellos) são enviados apenas por adjacências. Isso significa que todos os pacotes de protocolo OSPF percorrem um único salto de IP, exceto aqueles que são enviados por adjacências virtuais. O endereço IP de origem de um pacote de protocolo OSPF é uma extremidade de uma adjacência do roteador e o endereço IP de destino é a outra extremidade da adjacência ou um endereço IP multicast.

#### Formação de vizinhos ou Adjacências

Em um link ponto a ponto, para que dois roteadores tornem-se vizinhos ele precisam, além de estar na mesma sub-rede ou compartilharem um link virtual, que os seguintes parâmetros sejam iguais:

* temporizadores de Hello e Dead
* ID da área
* Tipo de autenticação e senha caso configurados

O processo de estabelecimento de adjacencia e troca de rota entre dois roteadores OSPF segue os seguintes passos:

* **Passo 1** - **Down State** : O processo do OSPF ainda não começou e os pacotes Hello ainda não foram enviados.
*  **Passo 2** - **Init State** : O router envia pacotes Hello para todas as interfaces OSPF configuradas no comando network . 
* **Passo 3** - **Two-way State** : o roteador recebeu um Hello do outro roteador contendo seu próprio ID de roteador na lista de vizinhos. Nessa situação todos os requisitos para vizinhança foram cumpridos e os roteadores podem se tornar vizinhos. 
* **Passo 4** - **Exstart State** : se os roteadores se tornarem adjacentes (fazer troca de rotas), nesse passo eles determinam quem começa o processo de troca de informações. 
* **Passo 5** - **Exchange State**: as bases de dados dos roteadores são trocadas listando os LSA em seu LSDB pelo RID e número de sequencia. 
* **Passo 6** - **Loading State** : cada roteador compara a base de dados recebida com o conteúdo de seu banco de dados local. Em seguida, envia um LSR para LSAs faltantes ou desatualizadas. Cada roteador responde a LSR do seu vizinho com uma atualização do estado do enlace. Cada LSU é reconhecido como um LSAck. 
* **Passo 7** - **Full State** : A LSDB foi sincronizada com o vizinho e foi finalizado o processo de inicialização do OSPF.

<img src="https://raw.githubusercontent.com/keilon-araujo/posts/master/OSPF-2.jpg" alt="ospf-2" style="zoom:67%;" />

