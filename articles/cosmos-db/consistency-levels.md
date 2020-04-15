---
title: Níveis de coerência no Azure Cosmos DB
description: O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380071"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de coerência no Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos disponíveis comercialmente pede aos desenvolvedores que escolham entre os dois modelos de consistência extrema: *consistência forte* e *eventual* consistência. A linearizabilidade do modelo de consistência forte é o padrão ouro da programação de dados. Mas adiciona um preço de maior latência de gravação (em estado estável) e disponibilidade reduzida (durante falhas). Por outro lado, a eventual consistência oferece maior disponibilidade e melhor desempenho, mas dificulta o programa de aplicações.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos. Os desenvolvedores podem usar essas opções para fazer escolhas precisas e compensações granulares em relação à alta disponibilidade e desempenho.

Com o Azure Cosmos DB, os desenvolvedores podem escolher entre cinco níveis de consistência bem definidos no espectro de consistência. Esses níveis incluem *obsoleto* *forte,* limitado, *sessão,* *prefixo consistente*e *eventual* consistência. Os níveis são bem definidos e intuitivos e podem ser usados para cenários específicos do mundo real. Cada nível fornece [negociações de disponibilidade e desempenho](consistency-levels-tradeoffs.md) e são apoiados por SLAs. A imagem a seguir mostra os diferentes níveis de consistência como um espectro.

![Consistência como um espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são agnósticos da região e são garantidos para todas as operações, independentemente da região a partir da qual as leituras e gravações são servidas, o número de regiões associadas à sua conta Azure Cosmos ou se sua conta está configurada com uma única ou múltipla região de gravação.

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A consistência de leitura se aplica a uma única operação de leitura escopo dentro de uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

Você pode configurar o nível de coerência padrão em sua conta do Azure Cosmos a qualquer momento. O nível de consistência padrão configurado em sua conta se aplica a todos os bancos de dados e contêineres do Azure Cosmos sob essa conta. Todas as leituras e consultas emitidas em um contêiner ou banco de dados usam o nível de consistência especificado por padrão. Para obter mais informações, consulte [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Os SLAs abrangentes fornecidos pelo Azure Cosmos DB garantem que 100% de solicitações de leitura atendam a garantia de consistência para qualquer nível de consistência que você escolher. Uma solicitação de leitura atende ao SLA de consistência, se todas as garantias de consistência associadas ao nível de consistência forem atendidas. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB usando a linguagem de especificação TLA+ são fornecidas no repo [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

A semântica dos cinco níveis de coerência é descrita aqui:

- **Forte**: Consistência forte oferece uma garantia de linearizabilidade. Linearizabilidade refere-se a solicitações de atendimento simultaneamente. As leituras são garantidas para retornar a versão mais recente de um item. Um cliente nunca vê uma gravação não comprometida ou parcial. Os usuários sempre terão a garantia de ler a última gravação confirmada.

  O gráfico a seguir ilustra a forte consistência com notas musicais. Depois que os dados são gravados na região "West US 2", quando você lê os dados de outras regiões, você recebe o valor mais recente:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Obsoleto limitado**: As leituras são garantidas para honrar a garantia de prefixo consistente. As leituras podem ficar para trás das gravações na maioria das versões *"K"* (ou seja, "atualizações") de um item ou por intervalo de tempo *"T".* Em outras palavras, quando você escolhe o obsoleto delimitado, a "obsolete" pode ser configurada de duas maneiras:

- O número de versões *(K)* do item
- O intervalo de tempo *(T)* pelo qual as leituras podem ficar para trás das gravações

A estagnação limitada oferece ordem global total fora da "janela de obsoleto". Quando um cliente realiza operações de leitura dentro de uma região que aceita gravações, as garantias fornecidas pela consistência de sobness delimitada são idênticas às que garantem pela forte consistência.

Dentro da janela de obsoleto, a obsoleta limitada fornece as seguintes garantias de consistência:

- Consistência para clientes na mesma região para uma conta de mestre único = Forte
- Consistência para clientes em diferentes regiões para uma conta única-master = Prefixo consistente
- Consistência para clientes escrevendo para uma única região para uma conta multi-master = Prefixo consistente
- Consistência para clientes que escrevem em diferentes regiões para uma conta multi-master = Eventual

  O obsoleto delimitado é frequentemente escolhido por aplicativos distribuídos globalmente que esperam latências de baixa gravação, mas exigem garantia total de pedidos globais. A estagnação limitada é ótima para aplicativos com colaboração e compartilhamento de grupo, ticker de ações, assinatura/fila de publicações etc. O gráfico a seguir ilustra a consistência de obsoleto limitado com notas musicais. Depois que os dados são gravados na região "West US 2", as regiões "East US 2" e "Australia East" lêem o valor escrito com base no tempo máximo de defasagem configurado ou nas operações máximas:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sessão**: Dentro de uma única sessão de cliente, as leituras são garantidas para honrar as leituras consistentes, monotônicas, gravações monótonicas, leitura-suas-gravações e garantias de leitura-follow-reads. Isso pressupõe uma única sessão de "escritor" ou compartilhar o token de sessão para vários escritores.

Os clientes que não realizam as gravações da sessão verão as seguintes garantias:

- Consistência para clientes na mesma região para uma conta de mestre único = Prefixo consistente
- Consistência para clientes em diferentes regiões para uma conta única-master = Prefixo consistente
- Consistência para clientes escrevendo para uma única região para uma conta multi-master = Prefixo consistente
- Consistência para a escrita de clientes em várias regiões para uma conta multi-master = Eventual

  A consistência da sessão é o nível de consistência amplamente utilizado tanto para uma única região quanto para aplicações distribuídas globalmente. Ele fornece latências de gravação, disponibilidade e throughput de leitura comparáveis à de eventual consistência, mas também fornece as garantias de consistência que atendem às necessidades dos aplicativos escritos para operar no contexto de um usuário. O gráfico a seguir ilustra a consistência da sessão com notas musicais. O "Escritor dos EUA Ocidentais" e o "Leitor west US 2" estão usando a mesma sessão (Sessão A) para que ambos leiam os mesmos dados ao mesmo tempo. Considerando que a região "Austrália Leste" está usando a "Sessão B" assim, ele recebe dados mais tarde, mas na mesma ordem que as gravações.

  ![video](media/consistency-levels/session-consistency.gif)

- **Prefixo consistente**: As atualizações que são retornadas contêm algum prefixo de todas as atualizações, sem lacunas. O nível de consistência de prefixo consistente garante que a leitura nunca veja gravações fora de ordem.

Se as gravações tiverem sido realizadas na ordem `A, B, C`, o cliente verá `A`, `A,B` ou `A,B,C`, mas nunca fora de ordem, como `A,C` ou `B,A,C`. O Prefixo Consistente fornece latências de gravação, disponibilidade e leitura de throughput comparável à de eventual consistência, mas também fornece as garantias de ordem que atendem às necessidades de cenários onde a ordem é importante. 

Abaixo estão as garantias de consistência para prefixo consistente:

- Consistência para clientes na mesma região para uma conta de mestre único = Prefixo consistente
- Consistência para clientes em diferentes regiões para uma conta única-master = Prefixo consistente
- Consistência para clientes escrevendo para uma única região para uma conta multi-master = Prefixo consistente
- Consistência para a escrita de clientes em várias regiões para uma conta multi-master = Eventual

O gráfico a seguir ilustra a consistência do prefixo de consistência com notas musicais. Em todas as regiões, as leituras nunca vêem fora de ordem escreve:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventual**: Não há garantia de pedido para leituras. Na ausência de qualquer gravação adicional, as réplicas eventualmente convergem.  
A consistência eventual é a forma mais fraca de consistência porque um cliente pode ler os valores mais antigos do que os que tinha lido antes. A consistência eventual é ideal quando a aplicação não requer garantias de pedidos. Exemplos incluem contagem de Retweets, Curtidas ou comentários não rosqueados. O gráfico a seguir ilustra a eventual consistência com notas musicais.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre conceitos de coerência, leia os artigos a seguir:

- [Especificações de TLA+ de alto nível para os cinco níveis de coerência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistência de dados replicada explicada através do beisebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistência de dados replicada explicada através do beisebol (whitepaper) por Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Session Guarantees for Weakly Consistent Replicated Data](https://dl.acm.org/citation.cfm?id=383631)
- [Equilíbrio de coerência no design do banco de dados distribuído moderno: CAP é só uma parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
- [Eventualmente Coerente – Revisado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os níveis de coerência no Azure Cosmos DB, leia os artigos a seguir:

- [Escolher o nível certo de coerência para o seu aplicativo](consistency-levels-choosing.md)
- [Níveis de coerência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
- [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
- [Configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Substituir o nível de consistência padrão](how-to-manage-consistency.md#override-the-default-consistency-level)
