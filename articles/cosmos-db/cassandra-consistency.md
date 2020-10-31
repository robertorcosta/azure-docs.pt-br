---
title: Níveis de consistência do Apache Cassandra e Azure Cosmos DB
description: Os níveis de consistência do Apache Cassandra e Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: f640ad85fd34dd5a4803e7dd96f1c0283f0c859a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100417"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Níveis de consistência do Apache Cassandra e Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Ao contrário de Azure Cosmos DB, o Apache Cassandra não fornece nativamente garantias de consistência definidas com precisão. Em vez disso, o Apache Cassandra fornece um nível de consistência de gravação e um nível de consistência de leitura para habilitar a alta disponibilidade, consistência e compensações de latência. Ao usar o API do Cassandra do Azure Cosmos DB:

* O nível de consistência de gravação do Apache Cassandra é mapeado para o nível de consistência padrão configurado em sua conta do Azure Cosmos. A consistência de uma operação de gravação (CL) não pode ser alterada em uma base por solicitação.

* Azure Cosmos DB mapeará dinamicamente o nível de consistência de leitura especificado pelo driver de cliente Cassandra para um dos níveis de consistência Azure Cosmos DB configurados dinamicamente em uma solicitação de leitura.

## <a name="mapping-consistency-levels"></a>Mapeamento de níveis de consistência

A tabela a seguir ilustra como os níveis de consistência Cassandra nativos são mapeados para os níveis de consistência do Azure Cosmos DB ao usar o API do Cassandra:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Mapeamento de modelo de consistência Cassandra" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que seus clientes possam obter leituras fortes e consistentes para suas cargas de trabalho examinando a métrica do PBS (desatualização limitada) do *Probabilistic* . Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma percepção da frequência com que você pode obter uma consistência mais forte do que o nível de consistência configurado atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e os níveis de consistência para Azure Cosmos DB:

* [Visão geral da distribuição global](distribute-data-globally.md)
* [Visão geral do nível de consistência](consistency-levels.md)
* [Alta disponibilidade](high-availability.md)
