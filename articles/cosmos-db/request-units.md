---
title: Unidades de Solicitação e taxa de transferência no Azure Cosmos DB
description: Saiba mais sobre como especificar e estimar os requisitos da Unidade de Solicitação no Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394243"
---
# <a name="request-units-in-azure-cosmos-db"></a>Unidades de Solicitação no Azure Cosmos DB

Com o Azure Cosmos DB, você paga pela taxa de transferência provisionada e pelo armazenamento que consome por hora. A taxa de transferência deverá ser provisionada para garantir que recursos do sistema suficientes sempre estejam disponíveis para o banco de dados do Azure Cosmos. Você precisa de recursos suficientes para atender ou exceder os [SLAs de Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação. 

O custo de todas as operações de banco de dados é normalizado por Azure Cosmos DB e é expresso por *unidades de solicitação* (ou RUs, para curto). É possível pensar em RUs por segundo como a moeda para a taxa de transferência. RUs por segundo é uma moeda baseada em taxa. Elas extraem os recursos do sistema como CPU, IOPS e memória que são necessários para executar as operações do banco de dados com suporte no Azure Cosmos DB. 

O custo para leitura de um item de 1 KB é 1 Unidade de Solicitação (ou 1 RU). Um mínimo de 10 RU/s é necessário para armazenar cada 1 GB de dados. Todas as outras operações do banco de dados são atribuídas de maneira semelhante a um custo usando RUs. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Independentemente da operação do banco de dados ser uma gravação, leitura ou consulta, os custos sempre serão medidos em RUs.

A imagem a seguir mostra a ideia de alto nível de RUs:

![Operações do banco de dados consomem Unidades de Solicitação](./media/request-units/request-units.png)

Para gerenciar e planejar a capacidade, o Azure Cosmos DB garante que o número de RUs para uma determinada operação de banco de dados em um determinado conjunto de dados seja determinístico. Você pode examinar o cabeçalho de resposta para rastrear o número de RUs consumidas por qualquer operação de banco de dados. Ao entender os [fatores que afetam os encargos de ru](request-units.md#request-unit-considerations) e os requisitos de taxa de transferência do aplicativo, você pode executar o aplicativo de forma econômica.

Você provisiona o número de RUs para o aplicativo em uma base por segundo em incrementos de 100 RUs por segundo. Para dimensionar a taxa de transferência provisionada ao aplicativo, você poderá aumentar ou diminuir o número de RUs a qualquer momento. Você pode dimensionar em incrementos ou decrementos de 100 RUs. É possível fazer as alterações programaticamente ou usando o portal do Azure. Você é cobrado por hora.

Você pode provisionar a taxa de transferência em duas granularidades distintas: 

* **Contêineres**: para obter mais informações, consulte [provisionar taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).
* **Bancos**de dados: para obter mais informações, consulte [provisionar taxa de transferência em um Azure Cosmos](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>Considerações sobre Unidade de Solicitação

Enquanto você estima o número de RUs por segundo para provisionar, considere os seguintes fatores:

* **Tamanho do item**: à medida que o tamanho de um item aumenta, o número de RUs consumidas para ler ou gravar o item também aumenta.

* **Indexação de itens**: por padrão, cada item é indexado automaticamente. Menos RUs serão consumidas se você optar por não indexar alguns dos itens em um contêiner.

* **Contagem de propriedades do item**: supondo que a indexação padrão esteja em todas as propriedades, o número de RUs consumidas para gravar um item aumenta conforme a contagem de propriedades do item aumenta.

* **Propriedades indexadas**: uma política de índice em cada contêiner determina quais propriedades são indexadas por padrão. Para reduzir o consumo de RU para operações de gravação, limite o número de propriedades indexadas.

* **Consistência de dados**: os níveis de consistência forte e limitado de desatualização consomem aproximadamente duas vezes mais RUs durante a execução de operações de leitura em comparação com a de outros níveis de consistência relaxados.

* **Padrões de consulta**: a complexidade de uma consulta afeta o número de RUs consumidas para uma operação. Fatores que afetam o custo das operações de consulta incluem: 
    
    - O número de resultados da consulta
    - O número de predicados
    - A natureza dos predicados
    - O número de funções definidas pelo usuário
    - O tamanho dos dados de origem
    - O tamanho do conjunto de resultados
    - Projeções

  O Azure Cosmos DB garante que a mesma consulta nos mesmos dados sempre custa o mesmo número de RUs em execuções repetidas.

* **Uso de script**: assim como acontece com consultas, procedimentos armazenados e gatilhos consomem RUs com base na complexidade das operações que são executadas. Ao desenvolver seu aplicativo, inspecione o [cabeçalho de encargo de solicitação](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) para entender melhor a quantidade de capacidade de ru consumida por cada operação.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Saiba mais sobre como [provisionar taxa de transferência para contêineres e bancos de dados do Azure Cosmos DB](set-throughput.md).
* Saiba mais sobre [partições lógicas](partition-data.md).
* Saiba mais sobre como [dimensionar taxa de transferência provisionada globalmente](scaling-throughput.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
* Saiba como [localizar o encargo de unidade de solicitação para uma operação](find-request-unit-charge.md).
* Saiba como [otimizar o custo de taxa de transferência provisionada em Azure Cosmos DB](optimize-cost-throughput.md).
* Saiba como [otimizar o custo de leituras e gravações no Azure Cosmos DB](optimize-cost-reads-writes.md).
* Saiba como [otimizar o custo da consulta no Azure Cosmos DB](optimize-cost-queries.md).
* Saiba como [usar métricas para monitorar a taxa de transferência](use-metrics.md).
