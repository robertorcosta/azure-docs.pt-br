---
title: Níveis de coerência no Azure Cosmos DB
description: O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aa09b1ec1e3f73547d211fab0907c9e3388c008b
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445325"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>O que são os níveis de consistência no Azure Cosmos DB?

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis solicita que os desenvolvedores escolham entre os dois modelos de consistência extremos: consistência *forte* e consistência *eventual* . O transação atômica do modelo de consistência forte é o padrão ouro de programação de dados. Mas ele adiciona um preço de latência de gravação maior (em estado estável) e disponibilidade reduzida (durante falhas). Por outro lado, a consistência eventual oferece maior disponibilidade e melhor desempenho, mas torna difícil programar aplicativos.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos. Os desenvolvedores podem usar essas opções para fazer compensações granulares e completas em relação à alta disponibilidade e ao desempenho.

Com o Azure Cosmos DB, os desenvolvedores podem escolher entre cinco níveis de consistência bem definidos no espectro de consistência. Esses níveis incluem *forte*, desatualização *limitada*, *sessão*, *prefixo consistente*e consistência *eventual* . Os níveis são bem definidos e intuitivos e podem ser usados para cenários do mundo real específicos. Cada nível fornece [compensações de desempenho e disponibilidade](consistency-levels-tradeoffs.md) e é apoiado por SLAs. A imagem a seguir mostra os diferentes níveis de consistência como um espectro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistência como um espectro" border="false" :::

Os níveis de consistência são independentes de região e são garantidos para todas as operações, independentemente da região da qual as leituras e gravações são atendidas, o número de regiões associadas à sua conta do Azure Cosmos ou se sua conta está configurada com uma ou várias regiões de gravação.

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A consistência de leitura se aplica a uma única operação de leitura com escopo definido em uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

Você pode configurar o nível de coerência padrão em sua conta do Azure Cosmos a qualquer momento. O nível de consistência padrão configurado em sua conta se aplica a todos os bancos de dados e contêineres do Azure Cosmos sob essa conta. Todas as leituras e consultas emitidas em um contêiner ou banco de dados usam o nível de consistência especificado por padrão. Para obter mais informações, consulte [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level). Você também pode substituir o nível de consistência padrão para uma solicitação específica, para saber mais, consulte como [substituir o artigo de nível de consistência padrão](how-to-manage-consistency.md?#override-the-default-consistency-level) .

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Os SLAs abrangentes fornecidos pelo Azure Cosmos DB garantem que 100% de solicitações de leitura atendam a garantia de consistência para qualquer nível de consistência que você escolher. Uma solicitação de leitura atende ao SLA de consistência, se todas as garantias de consistência associadas ao nível de consistência forem atendidas. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB usando a linguagem de especificação TLA + são fornecidas no repositório GitHub [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) .

A semântica dos cinco níveis de coerência é descrita aqui:

- **Forte**: a consistência forte oferece uma garantia transação atômica. Transação atômica refere-se ao fornecimento de solicitações simultaneamente. As leituras são garantidas para retornar a versão mais recente de um item. Um cliente nunca vê uma gravação não comprometida ou parcial. Os usuários sempre terão a garantia de ler a última gravação confirmada.

  O gráfico a seguir ilustra a forte consistência com notas musicais. Depois que os dados são gravados na região "oeste dos EUA 2", ao ler os dados de outras regiões, você obtém o valor mais recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Consistência como um espectro" pode ser configurada de duas maneiras:

- O número de versões (*K*) do item
- O intervalo de tempo (*T*) pelo qual as leituras podem atrasar por trás das gravações

A desatualização limitada oferece a ordem global total fora da "janela de desatualização". Quando um cliente executa operações de leitura em uma região que aceita gravações, as garantias fornecidas pela consistência de desatualização limitada são idênticas às garantias pela consistência forte.

Dentro da janela de desatualização, a desatualização limitada fornece as seguintes garantias de consistência:

- Consistência para clientes na mesma região para uma conta mestre único = Strong
- Consistência para clientes em regiões diferentes para uma conta mestre única = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta de vários mestres = prefixo consistente
- Consistência para clientes que gravam em regiões diferentes para uma conta de vários mestres = eventual

  A desatualização limitada é frequentemente escolhida por aplicativos distribuídos globalmente que esperam latências de baixa gravação, mas exigem garantia de ordem global total. A desatualização limitada é excelente para aplicativos que apresentam colaboração e compartilhamento de grupos, cotação de ações, publicação/assinatura/enfileiramento, etc. O gráfico a seguir ilustra a consistência de desatualização limitada com notas musicais. Depois que os dados são gravados na região "oeste dos EUA 2", as regiões "leste dos EUA 2" e "leste da Austrália" lêem o valor escrito com base no tempo de retardo máximo configurado ou no máximo de operações:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Consistência como um espectro" ou o compartilhamento do token de sessão para vários gravadores.

Os clientes fora da sessão que executam gravações verão as seguintes garantias:

- Consistência para clientes na mesma região para uma conta mestre único = prefixo consistente
- Consistência para clientes em regiões diferentes para uma conta mestre única = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta de vários mestres = prefixo consistente
- Consistência para clientes que gravam em várias regiões para uma conta de vários mestres = eventual

  A consistência da sessão é o nível de consistência mais amplamente usado para a região única, bem como para aplicativos distribuídos globalmente. Ele fornece latências de gravação, disponibilidade e taxa de transferência de leitura comparável à de consistência eventual, mas também fornece as garantias de consistência que atendem às necessidades dos aplicativos escritos para operar no contexto de um usuário. O gráfico a seguir ilustra a consistência da sessão com notas musicais. O "gravador oeste dos EUA 2" e o "leitor oeste dos EUA 2" estão usando a mesma sessão (sessão A) para que ambos leiam os mesmos dados ao mesmo tempo. Enquanto a região "leste da Austrália" está usando a "sessão B", ela recebe dados mais tarde, mas na mesma ordem que as gravações.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Consistência como um espectro":::

- **Prefixo consistente**: as atualizações que são retornadas contêm algum prefixo de todas as atualizações, sem intervalos. O nível de consistência de prefixo consistente garante que as leituras nunca vejam gravações fora de ordem.

Se as gravações foram executadas na ordem `A, B, C` , um cliente vê `A` , `A,B` ou `A,B,C` , mas nunca permutações fora de ordem, como `A,C` ou `B,A,C` . O prefixo consistente fornece latências de gravação, disponibilidade e taxa de transferência de leitura comparáveis à de consistência eventual, mas também fornece as garantias de ordem que atendem às necessidades dos cenários em que a ordem é importante. 

Abaixo estão as garantias de consistência para o prefixo consistente:

- Consistência para clientes na mesma região para uma conta mestre único = prefixo consistente
- Consistência para clientes em regiões diferentes para uma conta mestre única = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta de vários mestres = prefixo consistente
- Consistência para clientes que gravam em várias regiões para uma conta de vários mestres = eventual

O gráfico a seguir ilustra a consistência do prefixo de consistência com notas musicais. Em todas as regiões, as leituras nunca veem gravações fora de ordem:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Consistência como um espectro":::

- **Eventual**: não há garantia de classificação para leituras. Na ausência de qualquer gravação adicional, as réplicas eventualmente convergem.  
A consistência eventual é a forma mais fraca de consistência, pois um cliente pode ler os valores mais antigos do que aqueles que tinha lido anteriormente. A consistência eventual é ideal quando o aplicativo não requer nenhuma garantia de ordenação. Os exemplos incluem a contagem de retweets, curtidos ou comentários não-threaded. O gráfico a seguir ilustra a consistência eventual com notas musicais.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="Consistência como um espectro":::

## <a name="additional-reading"></a>Leituras adicionais

Para saber mais sobre conceitos de coerência, leia os artigos a seguir:

- [Especificações de TLA+ de alto nível para os cinco níveis de coerência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistência de dados replicada explicada por meio de beisebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistência de dados replicada explicada por meio de beisebol (White Paper) por Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
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
