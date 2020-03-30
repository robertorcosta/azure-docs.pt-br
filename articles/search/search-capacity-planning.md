---
title: Ajuste a capacidade para consultas e cargas de trabalho de índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos do computador de partição e réplica no Azure Cognitive Search, onde cada recurso é precificado em unidades de pesquisa faturadas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e2ba5301b81b1a6f5de696ab4587cd8ff43e3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462556"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Ajuste a capacidade na Pesquisa Cognitiva do Azure

Antes [de provisionar um serviço de pesquisa](search-create-service-portal.md) e bloquear em um nível específico de preços, leve alguns minutos para entender o papel das réplicas e partições em um serviço e como você pode ajustar um serviço para acomodar picos e quedas na demanda de recursos.

Capacidade é uma função do [nível escolhido](search-sku-tier.md) (níveis determinam características de hardware) e a combinação de réplica e partição necessária para cargas de trabalho projetadas. Dependendo do nível e do tamanho do ajuste, adicionar ou reduzir a capacidade pode levar de 15 minutos a várias horas. 

Ao modificar a alocação de réplicas e partições, recomendamos o uso do portal Azure. O portal impõe limites em combinações permitidas que permanecem abaixo dos limites máximos de um nível. No entanto, se você precisar de uma abordagem de provisionamento baseada em script ou baseada em código, o [Azure PowerShell](search-manage-powershell.md) ou a [API Management REST](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e partições

|||
|-|-|
|*Partições* | Fornecem armazenamento de índice e E/S para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice). Cada partição tem uma parte do índice total. Se você alocar três partições, seu índice será dividido em terços. |
|*Réplicas* | Instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica é uma cópia de um índice. Se você alocar três réplicas, você terá três cópias de um índice disponível para atender solicitações de consulta.|

## <a name="when-to-add-nodes"></a>Quando adicionar nódulos

Inicialmente, um serviço é alocado um nível mínimo de recursos consistindo de uma partição e uma réplica. 

Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Nenhuma carga de trabalho é executada em segundo plano. Você pode agendar a indexação para momentos em que as solicitações de consulta são naturalmente menos freqüentes, mas o serviço não priorizará uma tarefa em vez de outra. Além disso, uma certa quantidade de redundância suaviza o desempenho da consulta quando os serviços ou nós são atualizados internamente.

Como regra geral, os aplicativos de pesquisa tendem a precisar de mais réplicas do que partições, especialmente quando as operações de serviço são tendenciosas em relação às cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

Adicionar mais réplicas ou partições aumenta o custo de execução do serviço. Certifique-se de verificar a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para entender as implicações de faturamento de adicionar mais nós. O [gráfico abaixo](#chart) pode ajudá-lo a cruzar o número de unidades de pesquisa necessárias para uma configuração específica.

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **Configurações,** abra a página **Escala** para modificar réplicas e partições. 

   A captura de tela a seguir mostra um serviço padrão provisionado com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas (1). Se o preço unitário fosse de US$ 100 (não um preço real), o custo mensal de execução deste serviço seria de US$ 100 em média.

   ![Página de escala mostrando valores atuais](media/search-capacity-planning/1-initial-values.png "Página de escala mostrando valores atuais")

1. Use o controle deslizante para aumentar ou diminuir o número de partições. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas.

   Este exemplo dobra a capacidade, com duas réplicas e partições cada. Observe a contagem da unidade de busca; agora são quatro porque a fórmula de faturamento é multiplicada por partições (2 x 2). Dobrar a capacidade mais do que dobra o custo de execução do serviço. Se o custo da unidade de busca fosse de US$ 100, a nova conta mensal seria agora de US$ 400.

   Para obter os custos atuais por unidade de cada nível, visite a [página Preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicionar réplicas e partições](media/search-capacity-planning/2-add-2-each.png "Adicionar réplicas e partições")

1. Clique **em Salvar** para confirmar as alterações.

   ![Confirme alterações na escala e faturamento](media/search-capacity-planning/3-save-confirm.png "Confirme alterações na escala e faturamento")

   Mudanças na capacidade levam várias horas para serem concluídas. Você não pode cancelar uma vez que o processo tenha iniciado e não há monitoramento em tempo real para ajustes de réplica e partição. No entanto, a seguinte mensagem permanece visível enquanto as alterações estão em andamento.

   ![Mensagem de status no portal](media/search-capacity-planning/4-updating.png "Mensagem de status no portal")

> [!NOTE]
> Depois que um serviço é provisionado, ele não pode ser atualizado para um nível mais alto. Você precisará criar um serviço de pesquisa no novo tipo e recarregar os índices. Consulte [Criar um serviço de Busca Cognitiva Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento de serviços.
>
> Além disso, partições e réplicas são gerenciadas exclusivamente e internamente pelo serviço. Não há nenhum conceito de afinidade do processador, ou atribuir uma carga de trabalho a um nó específico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço Básico pode ter exatamente uma partição e até três réplicas, para o limite máximo de três SUs. O único recurso ajustável são as réplicas. É necessário um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa otimizados padrão e de armazenamento podem assumir as seguintes combinações de réplicas e partições, sujeitas ao limite de 36 SU. 

|   | **1 partição** | **2 partições** | **3 partições** | **4 partições** | **6 partições** | **12 partições** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

SUs, preço e capacidade são explicados detalhadamente no site do Azure. Para obter mais informações, consulte [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e partições divide de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso porque o Azure Cognitive Search pré-divide cada índice em 12 fragmentos para que ele possa ser espalhado em porções iguais em todas as partições. Por exemplo, se o serviço tiver três partições e você criar um índice, cada partição conterá quatro fragmentos do índice. Como o Azure Cognitive Search fragmentos um índice é um detalhe de implementação, sujeito a alterações em lançamentos futuros. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidade

Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. As cargas de trabalho de consulta são executadas principalmente em réplicas. Se precisar de mais taxa de transferência ou alta disponibilidade, provavelmente, você precisará de mais réplicas.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

* Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

Os contratos de nível de serviço (SLA) para a Pesquisa Cognitiva do Azure são direcionados para operações de consulta e para atualizações de índices que consistem em adicionar, atualizar ou excluir documentos.

Camada Básico alcança o topo em uma partição e três réplicas. Se você quiser flexibilidade para responder imediatamente a flutuações na demanda por taxa de transferência de indexação e consulta, considere uma das camadas Standard.  Se você descobrir que seus requisitos de armazenamento estão crescendo muito mais rapidamente do que o seu throughput de consulta, considere um dos níveis otimizados de armazenamento.

## <a name="disaster-recovery"></a>Recuperação de desastre

Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço configurando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir de seu código.

## <a name="estimate-replicas"></a>Estimar réplicas

Em um serviço de produção, você deve alocar três réplicas para fins de SLA. Se você experimentar o desempenho lento da consulta, você pode adicionar réplicas para que cópias adicionais do índice sejam trazidas on-line para suportar cargas de trabalho de consulta maiores e para carregar equilibrar as solicitações sobre as múltiplas réplicas.

Não fornecemos orientações sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas definitivamente adicione escala e desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante o triplo da taxa de transferência.

Para obter orientação na estimativa de QPS para sua solução, consulte [Escala para consultas de desempenho](search-performance-optimization.md)e [Monitor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Estimativa de partições

O [nível escolhido](search-sku-tier.md) determina o tamanho e a velocidade da partição, e cada camada é otimizada em torno de um conjunto de características que se encaixam em vários cenários. Se você escolher um nível superior, você pode precisar de menos partições do que se você for com o S1. Uma das perguntas que você precisará responder através de testes autodirecionados é se uma partição maior e mais cara produz melhor desempenho do que duas partições mais baratas em um serviço provisionado em um nível mais baixo.

Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições distribui as operações de leitura/gravação em uma quantidade maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. A complexidade de suas consultas e seu volume influenciarão a rapidez com que a execução da consulta é retornada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha um nível de preço para a Pesquisa Cognitiva do Azure](search-sku-tier.md)