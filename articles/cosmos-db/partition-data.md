---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba mais sobre como o particionamento funciona no Azure Cosmos DB, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 57417a80ea83005c01b6f2a17206d46e6c049719
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112771"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB

Este artigo explica a relação entre partições lógicas e físicas. Ele também aborda as práticas recomendadas para particionamento e fornece uma visão detalhada de como funciona o dimensionamento horizontal em Azure Cosmos DB. Não é necessário entender esses detalhes internos para [selecionar sua chave de partição](partitioning-overview.md#choose-partitionkey) , mas nós as cobrimos para que você tenha uma clareza de como Azure Cosmos DB funciona.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste em um conjunto de itens que têm a mesma chave de partição. Por exemplo, em um contêiner que contém dados sobre alimentos nutrição, todos os itens contêm uma `foodGroup` propriedade. Você pode usar `foodGroup` como a chave de partição para o contêiner. Grupos de itens que têm valores específicos para `foodGroup` , como, `Beef Products` `Baked Products` e `Sausages and Luncheon Meats` , formam partições lógicas distintas. Você não precisa se preocupar em excluir uma partição lógica quando os dados subjacentes são excluídos.

Uma partição lógica também define o escopo das transações de banco de dados. Você pode atualizar itens em uma partição lógica usando uma [transação com isolamento de instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contêiner, novas partições lógicas são criadas de forma transparente pelo sistema.

Não há nenhum limite para o número de partições lógicas em seu contêiner. Cada partição lógica pode armazenar até 20 GB de dados. Boas opções de chave de partição têm uma ampla variedade de valores possíveis. Por exemplo, em um contêiner em que todos os itens contêm uma `foodGroup` propriedade, os dados dentro da `Beef Products` partição lógica podem crescer até 20 GB. A [seleção de uma chave de partição](partitioning-overview.md#choose-partitionkey) com uma ampla variedade de valores possíveis garante que o contêiner seja capaz de dimensionar.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner Cosmos do Azure é dimensionado pela distribuição de dados e taxa de transferência entre partições físicas. Internamente, uma ou mais partições lógicas são mapeadas para uma única partição física. A maioria dos contêineres de Cosmos pequenos tem muitas partições lógicas, mas exigem apenas uma única partição física. Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema e são totalmente gerenciadas pelo Azure Cosmos DB.

O número de partições físicas no contêiner Cosmos depende do seguinte:

- Quantidade de taxa de transferência provisionada (cada partição física individual pode fornecer uma taxa de transferência de até 10.000 unidades de solicitação por segundo)
- Armazenamento de dados total (cada partição física individual pode armazenar até 50 GB)

Não há nenhum limite para o número total de partições físicas em seu contêiner. À medida que a produtividade provisionada ou o tamanho dos dados crescer, Azure Cosmos DB criará automaticamente novas partições físicas, dividindo as existentes. As divisões de partição física não afetam a disponibilidade do aplicativo. Após a divisão da partição física, todos os dados em uma única partição lógica ainda serão armazenados na mesma partição física. Uma divisão de partição física simplesmente cria um novo mapeamento de partições lógicas para partições físicas.

A taxa de transferência provisionada para um contêiner é dividida uniformemente entre as partições físicas. Um design de chave de partição que não distribui as solicitações de taxa de transferência pode, uniformemente, criar partições "ativas". As partições quentes podem resultar em limitação de taxa e em uso ineficiente da taxa de transferência provisionada e em custos mais altos.

Você pode ver as partições físicas do contêiner na seção **armazenamento** da **folha métricas** do portal do Azure:

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="Exibindo o número de partições físicas" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

Neste contêiner de exemplo em que escolhemos `/foodGroup` a nossa chave de partição, cada um dos três retângulos representa uma partição física. Na imagem, o **intervalo de chaves de partição** é o mesmo que uma partição física. A partição física selecionada contém três partições lógicas: `Beef Products` , `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` .

Se provisionar uma taxa de transferência de 18.000 unidades de solicitação por segundo (RU/s), cada uma das três partições físicas poderá utilizar 1/3 da taxa de transferência total provisionada. Na partição física selecionada, as chaves de partição lógica `Beef Products` , `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` podem, coletivamente, utilizar as 6.000 de ru/s provisionadas da partição física. Como a taxa de transferência provisionada é dividida uniformemente nas partições físicas do contêiner, é importante escolher uma chave de partição que distribua uniformemente o consumo da taxa de transferência, [escolhendo a chave de partição lógica correta](partitioning-overview.md#choose-partitionkey). Se você escolher uma chave de partição que distribua uniformemente o consumo de taxa de transferência entre partições lógicas, você garantirá que o consumo de taxa de transferência entre partições físicas seja balanceado.

## <a name="replica-sets"></a>Conjuntos de réplicas

Cada partição física consiste em um conjunto de réplicas, também conhecido como um [*conjunto de réplicas*](global-dist-under-the-hood.md). Cada conjunto de réplicas hospeda uma instância do mecanismo de banco de dados Cosmos do Azure. Um conjunto de réplicas torna os dados armazenados na partição física durável, altamente disponível e consistente. Cada réplica que compõe a partição física herda a cota de armazenamento da partição. Todas as réplicas de uma partição física dão suporte coletiva à taxa de transferência alocada para a partição física. Azure Cosmos DB gerencia automaticamente os conjuntos de réplicas.

A maioria dos contêineres de Cosmos pequenos requer apenas uma única partição física, mas ainda terá pelo menos 4 réplicas.

A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="Uma imagem que demonstra Azure Cosmos DB particionamento" border="false":::

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [escolher uma chave de partição](partitioning-overview.md#choose-partitionkey).
* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre a [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
