---
title: Encargos de unidade de solicitação para o Azure Cosmos DB como um repositório de valor de chave
description: Saiba mais sobre os encargos da unidade de solicitação do Azure Cosmos DB para operações simples de gravação e leitura quando ele é usado como um repositório de chaves/valores.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 5b2ee8b5bf19f16d7f7f04e9515fe591db7132f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647516"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB como uma loja de valores-chave – visão geral do custo

O Azure Cosmos DB é um serviço de multimodelo de banco de dados distribuído globalmente para a criação fácil de aplicativos altamente disponíveis de grande escala. Por padrão, o Azure Cosmos DB indexa automaticamente e eficientemente todos os dados que ingere. Isso permite consultas Rápidas e consistentes [de SQL](how-to-sql-query.md) (e [JavaScript)](stored-procedures-triggers-udfs.md)nos dados. 

Este artigo descreve o custo do Azure Cosmos DB para operações simples de gravação e leitura quando ele é usado como um repositório de chaves/valores. As operações de gravação incluem inserções, substituições, exclusões e upserts de itens de dados. Além de garantir um SLA de disponibilidade de 99,999% para todas as contas de várias regiões, o Azure Cosmos DB oferece <latência de 10 ms garantida para leituras e para as gravações (indexadas), no percentil 99. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos RUs (Unidades de Solicitação)

O desempenho do Azure Cosmos DB baseia-se na quantidade de throughput provisionado expressa em [Unidades de Solicitação](request-units.md) (RU/s). O provisionamento está em uma segunda granularidade e é adquirido em RU/s[(não confundir com o faturamento por hora).](https://azure.microsoft.com/pricing/details/cosmos-db/) As RUs devem ser consideradas como uma abstração lógica (uma moeda) que simplifica o provisionamento do throughput necessário para a aplicação. Os usuários não têm que pensar em diferenciar entre ler e escrever throughput. O modelo de moeda única de RUs cria eficiência no compartilhamento da capacidade provisionada entre leituras e gravações. Este modelo de capacidade provisionada permite que o serviço forneça um **throughput previsível e consistente, baixa latência garantida e alta disponibilidade.** Finalmente, enquanto o modelo RU é usado para descrever throughput, cada RU provisionado também tem uma quantidade definida de recursos (por exemplo, memória, núcleos/CPU e IOPS).

Como um sistema de banco de dados distribuído globalmente, o Cosmos DB é o único serviço Azure que fornece SLAs abrangentes que cobrem latência, throughput, consistência e alta disponibilidade. O throughput que você provisão é aplicado a cada uma das regiões associadas à sua conta Cosmos. Para leituras, o Cosmos DB oferece vários [níveis de consistência](consistency-levels.md) bem definidos para sua escolha. 

A tabela a seguir mostra o número de RUs necessários para executar operações de leitura e gravação com base em um item de dados de tamanho 1 KB e 100 KBs com indexação automática padrão desatualizada. 

|Tamanho do Item|1 Leitura|1 Gravação|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Custo de leituras e gravações

Se você prover 1.000 RU/s, isso equivale a 3,6 milhões de RU/hora e custará US$ 0,08 por hora (nos EUA e na Europa). Para um item de dados de tamanho de 1 KB, isso significa que você pode consumir 3,6 milhões de leituras ou 0,72 milhões de gravações (3,6 milhões de RU / 5) usando seu throughput provisionado. Normalizado para milhões de leituras e gravações, o custo seria de US$ 0,022 /milhão de leituras (US$ 0,08 / 3,6) e US$ 0,111/milhão de gravações (US$ 0,08 / 0,72). O custo por milhão se torna mínimo, conforme mostra a tabela a seguir.

|Tamanho do Item|Custo de 1 milhão de leituras|Custo de 1 milhão de gravações|
|-------------|-------|--------|
|1 KB|$ 0,022|$ 0,111|
|100 KB|$ 0,222|$ 1,111|


A maioria dos repositórios de blob ou de objeto básicos cobra $ 0,40 por um milhão de transações de leitura, e $ 5 por um milhão de transações de gravação. Se usado de forma ideal, o Cosmos DB pode ser até 98% mais barato do que essas outras soluções (para transações de 1 KB).

## <a name="next-steps"></a>Próximas etapas

* Use a [calculadora RU](https://cosmos.azure.com/capacitycalculator/) para estimar o throughput de suas cargas de trabalho.

