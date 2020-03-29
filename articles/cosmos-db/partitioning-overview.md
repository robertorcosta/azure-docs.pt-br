---
title: Particionamento no BD Cosmos do Azure
description: Aprenda sobre particionamento no Azure Cosmos DB, práticas recomendadas ao escolher uma chave de partição e como gerenciar partições lógicas
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251863"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure

O Azure Cosmos DB usa particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho de sua aplicação. Na partição, os itens em um contêiner são divididos em subconjuntos distintos chamados *partições lógicas*. As partições lógicas são formadas com base no valor de uma chave de *partição* que está associada a cada item em um contêiner. Todos os itens em uma partição lógica têm o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um `UserID` valor único para a propriedade. Se `UserID` serve como chave de partição para os itens no contêiner `UserID` e há 1.000 valores únicos, 1.000 partições lógicas são criadas para o contêiner.

Além de uma chave de partição que determina a partição lógica do item, cada item em um contêiner tem um *ID de item* (único dentro de uma partição lógica). Combinando a chave de partição e o iD do item cria o *índice*do item, que identifica exclusivamente o item.

[Escolher uma chave de partição](partitioning-overview.md#choose-partitionkey) é uma decisão importante que afetará o desempenho do seu aplicativo.

## <a name="managing-logical-partitions"></a>Gerenciamento de partições lógicas

O Azure Cosmos DB gerencia de forma transparente e automática a colocação de partições lógicas em partições físicas para satisfazer eficientemente as necessidades de escalabilidade e desempenho do contêiner. À medida que os requisitos de throughput e armazenamento de um aplicativo aumentam, o Azure Cosmos DB move partições lógicas para espalhar automaticamente a carga em um número maior de servidores. 

O Azure Cosmos DB usa particionamento baseado em hash para espalhar partições lógicas através de partições físicas. O Azure Cosmos DB hashes o valor chave de partição de um item. O resultado hashed determina a partição física. Em seguida, o Azure Cosmos DB aloca o espaço-chave da chave de partição hashes uniformemente através das partições físicas.

Consultas que acessam dados dentro de uma única partição lógica são mais econômicas do que consultas que acessam várias partições. Transações (em procedimentos ou gatilhos armazenados) são permitidas apenas contra itens em uma única partição lógica.

Para saber mais sobre como o Azure Cosmos DB gerencia partições, consulte [Partições lógicas](partition-data.md). (Não é necessário entender os detalhes internos para construir ou executar seus aplicativos, mas adicionado aqui para um leitor curioso.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Escolhendo uma chave de partição

A seguir, uma boa orientação para escolher uma chave de partição:

* Uma única partição lógica tem um limite superior de 20 GB de armazenamento.  

* Os contêineres Azure Cosmos têm um rendimento mínimo de 400 unidades de solicitação por segundo (RU/s). Quando o throughput é provisionado em um banco de dados, o mínimo de RUs por contêiner é de 100 unidades de solicitação por segundo (RU/s). As solicitações para a mesma chave de partição não podem exceder o throughput alocado a uma partição. Se as solicitações excederem o throughput alocado, as solicitações serão limitadas à taxa. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" em seu aplicativo.

* Escolha uma chave de partição que tenha uma ampla gama de valores e padrões de acesso distribuídos uniformemente em partições lógicas. Isso ajuda a espalhar os dados e a atividade em seu contêiner através do conjunto de partições lógicas, para que os recursos para armazenamento e throughput de dados possam ser distribuídos entre as partições lógicas.

* Escolha uma chave de partição que espalhe a carga de trabalho uniformemente em todas as partições e uniformemente ao longo do tempo. Sua escolha de chave de partição deve equilibrar a necessidade de consultas e transações de partição eficientes contra o objetivo de distribuir itens em várias partições para alcançar a escalabilidade.

* Os candidatos às chaves de partição podem incluir propriedades que aparecem frequentemente como um filtro em suas consultas. Consultas podem ser roteadas com eficiência, incluindo a chave de partição no predicado de filtro.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [particionamento e dimensionamento horizontal no Azure Cosmos DB](partition-data.md).
* Saiba mais sobre [o throughput provisionado no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [a distribuição global no Azure Cosmos DB](distribute-data-globally.md).
