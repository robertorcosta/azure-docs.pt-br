---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba como funciona o particionamento no Azure Cosmos DB, como configurar as teclas de particionamento e partição e como escolher a chave de partição certa para o seu aplicativo.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246611"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB

Este artigo explica as partições físicas e lógicas no Azure Cosmos DB. Ele também aborda as práticas recomendadas para o dimensionamento e o particionamento. 

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste em um conjunto de itens que têm a mesma chave de partição. Por exemplo, em um recipiente onde `City` todos os itens `City` contêm uma propriedade, você pode usar como chave de partição para o contêiner. Grupos de itens que possuem `City`valores `London`específicos `Paris`para, tais como, e `NYC`, formar partições lógicas distintas. Você não precisa se preocupar em excluir uma partição quando os dados subjacentes são excluídos.

No Azure Cosmos DB, um contêiner é a unidade fundamental de escalabilidade. Os dados adicionados ao contêiner e ao throughput que você provisão no contêiner são automaticamente (horizontalmente) particionados através de um conjunto de partições lógicas. Os dados e o throughput são particionados com base na chave de partição especificada para o contêiner Azure Cosmos. Para obter mais informações, consulte [Criar um contêiner Azure Cosmos](how-to-create-container.md).

Uma partição lógica também define o escopo das transações de banco de dados. Você pode atualizar itens dentro de uma partição lógica usando uma [transação com isolamento de instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contêiner, novas partições lógicas são criadas de forma transparente pelo sistema.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner Azure Cosmos é dimensionado distribuindo dados e throughput em um grande número de partições lógicas. Internamente, uma ou mais partições lógicas são mapeadas para uma partição física que consiste em um conjunto de réplicas, também referida como um conjunto de [*réplicas*](global-dist-under-the-hood.md). Cada conjunto de réplicas hospeda uma instância do mecanismo de banco de dados Do Azure Cosmos. Um conjunto de réplicas torna os dados armazenados dentro da partição física duráveis, altamente disponíveis e consistentes. Uma partição física suporta a quantidade máxima de unidades de armazenamento e solicitação (RUs). Cada réplica que compõe a partição física herda a cota de armazenamento da partição. Todas as réplicas de uma partição física suportam coletivamente o throughput que é alocado na partição física. 

A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

![Uma imagem que demonstra a particionamento do Azure Cosmos DB](./media/partition-data/logical-partitions.png)

O throughput provisionado para um contêiner é dividido uniformemente entre partições físicas. Um design de chave de partição que não distribua as solicitações de throughput uniformemente pode criar partições "quentes". Partições quentes podem resultar em limitação de taxa e uso ineficiente do throughput provisionado, e custos mais altos.

Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema. Você não pode controlar o tamanho, a colocação ou a contagem de partições físicas, e não pode controlar o mapeamento entre partições lógicas e partições físicas. No entanto, você pode controlar o número de partições lógicas e a distribuição de dados, carga de trabalho e throughput [escolhendo a chave de partição lógica certa](partitioning-overview.md#choose-partitionkey).

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [escolher uma chave de partição](partitioning-overview.md#choose-partitionkey).
* Saiba mais sobre [o throughput provisionado no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [a distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
