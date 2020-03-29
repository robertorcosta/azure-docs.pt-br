---
title: Benefícios multimestres do Azure Cosmos DB
description: Entenda os benefícios do multi-master no Azure Cosmos DB, comparação dos requisitos de latência e SLA em locais de gravação únicos e múltiplos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872002"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Entenda os benefícios multi-master no Azure Cosmos DB

Os operadores de conta cosmos DB devem escolher a configuração de distribuição global apropriada para garantir os requisitos de latência, disponibilidade e RTO para seus aplicativos. As contas do Azure Cosmos configuradas com vários locais de gravação oferecem benefícios significativos sobre contas com localização de gravação única, incluindo, 99,999% de disponibilidade de gravação SLA, <SLA de latência de 10 ms de gravação no percentil 99 e RTO = 0 em um desastre regional.

## <a name="comparison-of-features"></a>Comparação de recursos

|Requisito de aplicativo|Vários locais de gravação|Local de gravação único|Observação|
|---|---|---|---|
|Escrever SLA de latência de <10ms em P99|**Sim**|Não|Contas com local de gravação único incorrem em latência adicional de rede entre regiões para cada gravação.|
|Leia Latião SLA de <10ms em P99|**Sim**|Sim| |
|Escrever SLA de 99,999%|**Sim**|Não|Contas com Localização de Gravação Única garantem SLA de 99,99%|
|RTO = 0|**Sim**|Não|Tempo de inatividade zero para escreve em caso de desastres regionais. Contas com localização de gravação única têm RTO de 15 min.|

## <a name="next-steps"></a>Próximas etapas

Se você ainda quiser desativar EnableMultipleWriteLocations em sua conta do Azure Cosmos, [abra um ticket de suporte](https://azure.microsoft.com/support/create-ticket/).
