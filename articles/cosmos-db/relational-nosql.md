---
title: Entendendo as diferenças entre o Azure Cosmos DB NoSQL e os bancos de dados relacionais
description: Este artigo enumera as diferenças entre noSQL e bancos de dados relacionais
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896617"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Entendendo as diferenças entre noSQL e bases de dados relacionais

Este artigo irá enumerar alguns dos principais benefícios dos bancos de dados NoSQL sobre bancos de dados relacionais. Também discutiremos alguns dos desafios em trabalhar com o NoSQL. Para uma análise aprofundada dos diferentes armazenamentos de dados existentes, veja nosso artigo sobre [a escolha do armazenamento de dados certo.](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview)

## <a name="high-throughput"></a>Alta taxa de transferência

Um dos desafios mais óbvios na manutenção de um sistema de banco de dados relacional é que a maioria dos motores relacionais aplicam travas e travas para impor [semântica acidial](https://en.wikipedia.org/wiki/ACID)rigorosa. Essa abordagem tem benefícios em termos de garantir um estado de dados consistente dentro do banco de dados. No entanto, há grandes trocas no que diz respeito à concorrência, latência e disponibilidade. Devido a essas restrições arquitetônicas fundamentais, altos volumes transacionais podem resultar na necessidade de fragmentos manualmente de dados. A implementação de fragmentação manual pode ser um exercício demorado e doloroso.

Nesses cenários, [bancos de dados distribuídos](https://en.wikipedia.org/wiki/Distributed_database) podem oferecer uma solução mais escalável. No entanto, a manutenção ainda pode ser um exercício caro e demorado. Os administradores podem ter que fazer um trabalho extra para garantir que a natureza distribuída do sistema seja transparente. Eles também podem ter que responder pela natureza "desconectada" do banco de dados.

[O Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) simplifica esses desafios, sendo implantado em todo o mundo em todas as regiões do Azure. Os intervalos de partição são capazes de ser subdivididos dinamicamente para aumentar perfeitamente o banco de dados em linha com o aplicativo, mantendo simultaneamente alta disponibilidade. Multi-locação de grãos finos e governança de recursos fortemente controlada, nativa da nuvem, facilita garantias surpreendentes de [latência](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) e desempenho previsível. A partição é totalmente gerenciada, portanto, os administradores não precisam escrever código ou gerenciar partições.

Se seus volumes transacionais estiverem atingindo níveis extremos, como muitos milhares de transações por segundo, você deve considerar um banco de dados NoSQL distribuído. Considere o Azure Cosmos DB para obter a máxima eficiência, facilidade de manutenção e redução do custo total de propriedade.

![Back-end](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Dados hierárquicos

Há um número significativo de casos de uso em que as transações no banco de dados podem conter muitas relações pai-filho. Essas relações podem crescer significativamente ao longo do tempo, e se mostram difíceis de gerenciar. Formas de bancos de [dados hierárquicos](https://en.wikipedia.org/wiki/Hierarchical_database_model) surgiram durante a década de 1980, mas não eram populares devido à ineficiência no armazenamento. Eles também perderam tração quando o [modelo relacional de Ted Codd](https://en.wikipedia.org/wiki/Relational_model) tornou-se o padrão de fato usado por praticamente todos os principais sistemas de gerenciamento de banco de dados.

No entanto, hoje, a popularidade dos bancos de dados de estilo de documento cresceu significativamente. Essas bases de dados podem ser consideradas uma reinvenção do paradigma hierárquico do banco de dados, agora desinibido por preocupações em torno do custo de armazenamento de dados em disco. Como resultado, a manutenção de muitas relações complexas entre pais e filhos em um banco de dados relacional poderia agora ser considerada um anti-padrão em comparação com abordagens modernas orientadas a documentos.

O surgimento do [design orientado a objetos](https://en.wikipedia.org/wiki/Object-oriented_design), e a [incompatibilidade de impedância](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) que surge ao combiná-lo com modelos relacionais, também destaca um anti-padrão em bancos de dados relacionais para determinados casos de uso. Custos de manutenção ocultos, mas muitas vezes significativos, podem surgir como resultado. Embora [as abordagens orm](https://en.wikipedia.org/wiki/Object-relational_mapping) tenham evoluído para mitigar parcialmente isso, os bancos de dados orientados a documentos, no entanto, se unem muito melhor com abordagens orientadas a objetos. Com essa abordagem, os desenvolvedores não são forçados a se comprometer com drivers ORM ou mecanismos de banco de [dados oo](https://en.wikipedia.org/wiki/Object_database)específicos medida. Se seus dados contêm muitas relações pai-filho e níveis profundos de hierarquia, você pode querer considerar o uso de um banco de dados de documentos NoSQL, como a [API Azure Cosmos DB SQL](https://docs.microsoft.com/azure/cosmos-db/introduction).

![Orderdetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Redes e relacionamentos complexos

Ironicamente, dado o seu nome, as bases de dados relacionais apresentam uma solução menos que ideal para a modelagem de relações profundas e complexas. A razão para isso é que as relações entre entidades não existem de fato em um banco de dados relacional. Eles precisam ser computados em tempo de execução, com relacionamentos complexos que requerem adesão cartesiana para permitir o mapeamento usando consultas. Como resultado, as operações se tornam exponencialmente mais caras em termos de computação à medida que as relações aumentam. Em alguns casos, um banco de dados relacional que tenta gerenciar tais entidades se tornará inutilizável.

Várias formas de bases de dados "Rede" surgiram durante o tempo em que surgiram bancos de dados relacionais, mas como com bases de dados hierárquicas, esses sistemas lutaram para ganhar popularidade. A adoção lenta foi devido à falta de casos de uso na época, e ineficiências de armazenamento. Hoje, os mecanismos de banco de dados gráficos podem ser considerados um ressurgimento do paradigma do banco de dados de rede. O principal benefício com esses sistemas é que as relações são armazenadas como "cidadãos de primeira classe" dentro do banco de dados. Assim, a travessia de relacionamentos pode ser feita em tempo constante, em vez de aumentar na complexidade do tempo a cada novo produto de adesão ou cruzamento.

Se você está mantendo uma complexa rede de relacionamentos em seu banco de dados, você pode querer considerar um banco de dados gráfico, como a [API Azure Cosmos DB Gremlin,](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) para gerenciar esses dados.

![Grafo](./media/relational-or-nosql/graph.png)

O Azure Cosmos DB é um serviço de banco de dados multi-modelo, que oferece uma projeção de API para todos os principais tipos de modelos NoSQL; Coluna-família, documento, gráfico e valor-chave. As camadas de API de documento [Gremlin (gráfico)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) e SQL (Core) são totalmente interoperáveis. Isso tem benefícios para a troca entre diferentes modelos no nível de programação. As lojas de gráficos podem ser consultadas em termos de travessias de rede complexas, bem como transações modeladas como registros de documentos na mesma loja.

## <a name="fluid-schema"></a>Esquema de fluidos

Outra característica particular das bases de dados relacionais é que os esquemas são necessários para serem definidos na hora do projeto. Isso tem benefícios em termos de integridade referencial e conformidade dos dados. No entanto, também pode ser restritivo à medida que a aplicação cresce. Responder a alterações no esquema em modelos logicamente separados compartilhando a mesma tabela ou definição de banco de dados pode se tornar complexo ao longo do tempo. Esses casos de uso muitas vezes se beneficiam do esquema que está sendo desviado para o aplicativo para gerenciar por registro. Isso exige que o banco de dados seja "agnóstico de esquema" e permita que os registros sejam "auto-descritos" em termos dos dados contidos neles.

Se você está gerenciando dados cujas estruturas estão constantemente mudando a uma taxa alta, particularmente se as transações podem vir de fontes externas onde é difícil impor conformidade em todo o banco de dados, você pode querer considerar uma abordagem mais esquema-agnóstica usando um serviço de banco de dados NoSQL gerenciado como o Azure Cosmos DB.

## <a name="microservices"></a>Microsserviços

O padrão [de microsserviços](https://en.wikipedia.org/wiki/Microservices) cresceu significativamente nos últimos anos. Esse padrão tem suas raízes na [Arquitetura Orientada a Serviços](https://en.wikipedia.org/wiki/Service-oriented_architecture). O padrão de fato para transmissão de dados nessas modernas arquiteturas de microsserviços é [o JSON](https://en.wikipedia.org/wiki/JSON), que também é o meio de armazenamento para a grande maioria dos bancos de dados NoSQL orientados a documentos. Isso torna as lojas de documentos NoSQL um ajuste muito mais perfeito tanto para a persistência quanto para a sincronização (usando [padrões de sourcing de eventos)](https://en.wikipedia.org/wiki/Event-driven_architecture)em implementações complexas do Microservice. Bancos de dados relacionais mais tradicionais podem ser muito mais complexos de manter nessas arquiteturas. Isso se deve à maior quantidade de transformação necessária tanto para o estado quanto para a sincronização entre ASIs. O Azure Cosmos DB, em particular, tem uma série de recursos que o tornam um ajuste ainda mais perfeito para arquiteturas de microserviços baseadas em JSON do que muitos bancos de dados NoSQL:

* uma escolha de tipos de dados JSON puros
* um mecanismo JavaScript e [uma API de consulta](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) incorporada ao banco de dados.
* um feed de mudança de última geração [que](https://docs.microsoft.com/azure/cosmos-db/change-feed) os clientes podem assinar para serem notificados de modificações em um contêiner.

## <a name="some-challenges-with-nosql-databases"></a>Alguns desafios com bancos de dados NoSQL

Embora existam algumas vantagens claras ao implementar bancos de dados NoSQL, existem também alguns desafios que você pode querer levar em consideração. Estes podem não estar presentes no mesmo grau ao trabalhar com o modelo relacional:

* transações com muitas relações apontando para a mesma entidade.
* transações que requerem forte consistência em todo o conjunto de dados.

Olhando para o primeiro desafio, a regra de ouro nos bancos de dados NoSQL é geralmente a desnormalização, que, como articulado anteriormente, produz leituras mais eficientes em um sistema distribuído. No entanto, existem alguns desafios de design que entram em jogo com essa abordagem. Vamos dar um exemplo de um produto relacionado a uma categoria e várias tags:

![Junções](./media/relational-or-nosql/many-joins.png)

Uma abordagem de práticas recomendadas em um banco de dados de documentos NoSQL seria desnormalizar o nome da categoria e marcar nomes diretamente em um "documento do produto". No entanto, a fim de manter categorias, tags e produtos em sincronia, as opções de design para facilitar isso têm adicionado complexidade de manutenção, porque os dados são duplicados em vários registros no produto, em vez de ser uma simples atualização em um "one-to-many" relacionamento, e uma adesão para recuperar os dados. 

A compensação é que as leituras são mais eficientes no registro desnormalizado, e se tornam cada vez mais eficientes à medida que o número de entidades conceitualmente unidas aumenta. No entanto, assim como a eficiência da leitura aumenta com o aumento do número de entidades unidas em um registro desnormalizado, também a complexidade de manutenção de manter as entidades em sincronia. Uma maneira de mitigar essa troca é criar um [modelo de dados híbridos.](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models)

Embora haja mais flexibilidade disponível nos bancos de dados NoSQL para lidar com essas compensações, o aumento da flexibilidade também pode produzir mais decisões de design. Consulte nosso artigo [como modelar e particionar dados no Azure Cosmos DB usando um exemplo do mundo real](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), que inclui uma abordagem para manter dados de usuários [desnormalizados em sincronia](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) onde os usuários não apenas se sentam em diferentes partições, mas em diferentes contêineres.

Com relação à forte consistência, é raro que isso seja necessário em todo o conjunto de dados. No entanto, nos casos em que isso é necessário, pode ser um desafio em bancos de dados distribuídos. Para garantir uma consistência forte, os dados precisam ser sincronizados em todas as réplicas e regiões antes de permitir que os clientes os leiam. Isso pode aumentar a latência das leituras.

Mais uma vez, o Azure Cosmos DB oferece mais flexibilidade do que bancos de dados relacionais para as várias trocas que são relevantes aqui, mas para implementações em pequena escala, essa abordagem pode adicionar mais considerações de design. Consulte nosso artigo sobre [consistência, disponibilidade e compensações de desempenho](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs) para obter mais detalhes sobre este tópico.

## <a name="next-steps"></a>Próximas etapas

Saiba como gerenciar sua conta do Azure Cosmos e outros conceitos:

* [Como gerenciar sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Trabalhando com itens e contêineres do Azure Cosmos](databases-containers-items.md)
* [Ponto de extremidade de serviço de rede virtual para sua conta do Azure Cosmos](vnet-service-endpoint.md)
* [Firewall de IP para sua conta do Azure Cosmos](firewall-support.md)
* [Como adicionar e remover regiões do Azure para sua conta do Azure Cosmos](how-to-manage-database-account.md)
* [SLAs do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
