---
title: Escalar verticalmente partições e réplicas para adicionar capacidade para cargas de trabalho de consulta e índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos de computador da partição e da réplica no Azure Pesquisa Cognitiva, em que cada recurso é cobrado nas unidades de pesquisa cobráveis.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8613ddc668df338c4f96a9d37f32120718513925
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792496"
---
# <a name="scale-up-partitions-and-replicas-to-add-capacity-for-query-and-index-workloads-in-azure-cognitive-search"></a>Escalar verticalmente partições e réplicas para adicionar capacidade para cargas de trabalho de consulta e índice no Azure Pesquisa Cognitiva

Depois que você [escolhe um tipo de preço](search-sku-tier.md) e [provisiona um serviço de pesquisa](search-create-service-portal.md), a próxima etapa é, como opção, aumentar o número de réplicas ou partições usadas pelo serviço. Cada camada oferece um número fixo de unidades de cobrança. Este artigo explica como alocar essas unidades para obter uma configuração ideal que equilibra os requisitos para execução da consulta, indexação e armazenamento.

A configuração de recursos está disponível quando você configura um serviço na [camada básica](https://aka.ms/azuresearchbasic) ou em uma das [camadas padrão ou de armazenamento otimizado](search-limits-quotas-capacity.md). Para os serviços nessas camadas, a capacidade é comprada em incrementos de *unidades de pesquisa* (SUs), em que cada partição e réplica conta como uma SU. 

Usar menos SUs resulta em uma lista menor proporcionalmente. A cobrança fica em vigor durante o tempo de configuração do serviço. Se, no momento, você não estiver usando um serviço, a única maneira de evitar a cobrança será excluindo o serviço e o recriando quando precisar dele.

> [!Note]
> Excluir um serviço exclui tudo nele. Não há nenhum recurso no Azure Pesquisa Cognitiva para fazer backup e restaurar dados de pesquisa persistentes. Para reimplantar um índice existente em um novo serviço, você deverá executar o programa usado para criar e carregá-lo originalmente. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e partições
Réplicas e partições são os recursos principais que retornam um serviço de pesquisa.

| Grupos | Definição |
|----------|------------|
|*Partições* | Fornecem armazenamento de índice e E/S para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice).|
|*Réplicas* | Instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica sempre hospeda uma cópia de um índice. Se você tiver 12 réplicas, terá 12 cópias de cada índice carregadas no serviço.|

> [!NOTE]
> Não há uma maneira de manipular ou gerenciar diretamente quais índices são executados em uma réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura do serviço.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições
No Azure Pesquisa Cognitiva, um serviço é inicialmente alocado um nível mínimo de recursos consistindo em uma partição e uma réplica. Para tipos que dão suporte a isso, você poderá ajustar de forma incremental a capacidade de recursos computacionais aumentando as partições, se precisar de mais armazenamento e E/S ou adicionar mais réplicas para volumes maiores de consulta ou melhor desempenho. Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Você não pode subdividir cargas de trabalho entre vários serviços.

Para aumentar ou alterar a alocação de réplicas e partições, recomendamos o uso do portal do Azure. O portal impõe limites para combinações permitidas que ficam abaixo dos limites máximos. Se você precisar de uma abordagem de provisionamento baseada em script ou em código, o [Azure PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

Em geral, os aplicativos de pesquisa precisam de mais réplicas do que partições, especialmente quando as operações de serviço são polarizadas para cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

2. Em **configurações**, abra a página **escala** para modificar réplicas e partições. 

   A captura de tela a seguir mostra um serviço padrão provisionado com uma réplica e uma partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas (1). Se o preço unitário era $100 (não um preço real), o custo mensal da execução desse serviço seria de $100 em média.

   ![Página escala mostrando os valores atuais](media/search-capacity-planning/1-initial-values.png "Página escala mostrando os valores atuais")

3. Use o controle deslizante para aumentar ou diminuir o número de partições. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas.

   Este exemplo duplica a capacidade, com duas réplicas e partições cada. Observe a contagem de unidades de pesquisa; Agora, ele é quatro porque a fórmula de cobrança é réplicas multiplicada por partições (2 x 2). Dobrar a capacidade mais do que o dobro do custo da execução do serviço. Se o custo da unidade de pesquisa fosse $100, a nova fatura mensal agora será $400.

   Para os custos por unidade atuais de cada camada, visite a [página de preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicionar réplicas e partições](media/search-capacity-planning/2-add-2-each.png "Adicionar réplicas e partições")

3. Clique em **salvar** para confirmar as alterações.

   ![Confirmar alterações de escala e cobrança](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações de escala e cobrança")

   As alterações na capacidade levam várias horas para serem concluídas. Não é possível cancelar quando o processo foi iniciado e não há monitoramento em tempo real para os ajustes de réplica e partição. No entanto, a mensagem a seguir permanece visível enquanto as alterações estão em andamento.

   ![Mensagem de status no portal](media/search-capacity-planning/4-updating.png "Mensagem de status no portal")


> [!NOTE]
> Após o provisionamento de um serviço, ele não pode ser atualizado para um SKU superior. Você precisará criar um serviço de pesquisa no novo tipo e recarregar os índices. Consulte [criar um serviço de pesquisa cognitiva do Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento de serviço.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço Básico pode ter exatamente uma partição e até três réplicas, para o limite máximo de três SUs. O único recurso ajustável são as réplicas. É necessário um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa padrão e de armazenamento otimizados podem assumir as seguintes combinações de réplicas e partições, sujeito ao limite de 36-SU. 

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
> O número de réplicas e partições divide de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque o Azure Pesquisa Cognitiva divide cada índice em 12 fragmentos para que ele possa ser distribuído em partes iguais em todas as partições. Por exemplo, se o serviço tiver três partições e você criar um índice, cada partição conterá quatro fragmentos do índice. Como o Azure Pesquisa Cognitiva fragmenta um índice é um detalhe de implementação, sujeito a alterações em versões futuras. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.
>


<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidade
Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. As cargas de trabalho de consulta são executadas principalmente em réplicas. Se precisar de mais taxa de transferência ou alta disponibilidade, provavelmente, você precisará de mais réplicas.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

* Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

Os contratos de nível de serviço (SLA) para o Azure Pesquisa Cognitiva são direcionados em operações de consulta e em atualizações de índice que consistem em Adicionar, atualizar ou excluir documentos.

Camada Básico alcança o topo em uma partição e três réplicas. Se você quiser flexibilidade para responder imediatamente a flutuações na demanda por taxa de transferência de indexação e consulta, considere uma das camadas Standard.  Se você achar que seus requisitos de armazenamento estão crescendo muito mais rapidamente do que a taxa de transferência de consulta, considere uma das camadas de armazenamento otimizado.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidade de índice durante uma recompilação

A alta disponibilidade do Pesquisa Cognitiva do Azure pertence a consultas e atualizações de índice que não envolvem a recriação de um índice. Se você excluir um campo, alterar um tipo de dados ou renomear um campo, será necessário recompilar o índice. Para recompilar o índice, é necessário excluir o índice, recriar o índice e recarregar os dados.

> [!NOTE]
> Você pode adicionar novos campos a um índice de Pesquisa Cognitiva do Azure sem recompilar o índice. O valor do novo campo será nulo para todos os documentos já existentes no índice.

Para manter a disponibilidade do índice durante uma recompilação, é necessário ter uma cópia do índice com um nome diferente no mesmo serviço, ou uma cópia do índice com o mesmo nome em um serviço diferente e fornecer a lógica de redirecionamento ou de failover no código.

## <a name="disaster-recovery"></a>Recuperação de desastre
Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço configurando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir de seu código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho de consulta com réplicas
A latência da consulta é um indicador da necessidade de réplicas adicionais. Em geral, uma primeira etapa para melhorar o desempenho de consulta é adicionar mais desse recurso. Conforme você adiciona réplicas, cópias adicionais do índice são colocadas online para oferecer maior suporte a cargas de trabalho de consulta e balancear a carga das solicitações em várias réplicas.

Não podemos fornecer estimativas fixas para QPS (consultas por segundo): o desempenho da consulta depende da complexidade da consulta e de cargas de trabalho concorrentes. Embora a adição de réplicas definitivamente adicione escala e desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante o triplo da taxa de transferência.

Para obter orientação sobre como estimar o QPS para suas cargas de trabalho, consulte [Considerações sobre desempenho e otimização do Azure pesquisa cognitiva](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho de indexação com partições
Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições distribui as operações de leitura/gravação em uma quantidade maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. A complexidade de suas consultas e seu volume influenciarão a rapidez com que a execução da consulta é retornada.


## <a name="next-steps"></a>Próximos passos

[Escolha um tipo de preço para o Azure Pesquisa Cognitiva](search-sku-tier.md)
