---
title: Dimensionar partições e réplicas para consulta e indexação-Azure Search
description: Ajuste os recursos de computador da partição e da réplica em Azure Search, em que cada recurso é cobrado nas unidades de pesquisa cobráveis.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c048dcf31d8f434f742d2da9351ef9b46f0a71d4
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69650075"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Dimensionar partições e réplicas para cargas de trabalho de consulta e indexação no Azure Search
Depois que você [escolhe um tipo de preço](search-sku-tier.md) e [provisiona um serviço de pesquisa](search-create-service-portal.md), a próxima etapa é, como opção, aumentar o número de réplicas ou partições usadas pelo serviço. Cada camada oferece um número fixo de unidades de cobrança. Este artigo explica como alocar essas unidades para obter uma configuração ideal que equilibra os requisitos para execução da consulta, indexação e armazenamento.

A configuração de recursos está disponível quando você configura um serviço na [camada básica](https://aka.ms/azuresearchbasic) ou em uma das [camadas padrão ou de armazenamento otimizado](search-limits-quotas-capacity.md). Para serviços nessas camadas, a capacidade é comprada em incrementos de *unidades de pesquisa* (SUS), em que cada partição e réplica contam como uma su. 

Usar menos SUs resulta em uma lista menor proporcionalmente. A cobrança estará em vigor por enquanto o serviço estiver configurado. Se, no momento, você não estiver usando um serviço, a única maneira de evitar a cobrança será excluindo o serviço e o recriando quando precisar dele.

> [!Note]
> A exclusão de um serviço exclui tudo nele. Não há nenhum recurso no Azure Search para fazer backup e restaurar dados de pesquisa persistentes. Para reimplantar um índice existente em um novo serviço, você deve executar o programa usado para criá-lo e carregá-lo originalmente. 

## <a name="terminology-replicas-and-partitions"></a>Terminologia: réplicas e partições
Réplicas e partições são os recursos principais que retornam um serviço de pesquisa.

| Grupos | Definição |
|----------|------------|
|*Partições* | Fornece armazenamento de índice e e/s para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice).|
|*Réplicas* | Instâncias do serviço de pesquisa, usadas principalmente para balancear a carga de operações de consulta. Cada réplica sempre hospeda uma cópia de um índice. Se você tiver 12 réplicas, terá 12 cópias de cada índice carregado no serviço.|

> [!NOTE]
> Não é possível manipular ou gerenciar diretamente os índices executados em uma réplica. Uma cópia de cada índice em cada réplica faz parte da arquitetura do serviço.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições
No Azure Search, um serviço recebe inicialmente um nível mínimo de recursos compostos por uma partição e uma réplica. Para as camadas que dão suporte a ela, você pode ajustar incrementalmente os recursos computacionais aumentando as partições se precisar de mais armazenamento e e/s ou adicionar mais réplicas para volumes de consulta maiores ou melhor desempenho. Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Você não pode subdividir cargas de trabalho entre vários serviços.

Para aumentar ou alterar a alocação de réplicas e partições, é recomendável usar o portal do Azure. O portal impõe limites para combinações permitidas que ficam abaixo dos limites máximos. Se você precisar de uma abordagem de provisionamento baseada em script ou em código, o [Azure PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/services) são soluções alternativas.

Geralmente, os aplicativos de pesquisa precisam de mais réplicas do que as partições, especialmente quando as operações de serviço são tendenciosas em relação a cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

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
> Depois que um serviço é provisionado, ele não pode ser atualizado para um SKU superior. Você deve criar um serviço de pesquisa na nova camada e recarregar seus índices. Confira [Criar um serviço de Azure Search no portal](search-create-service-portal.md) para obter ajuda com o provisionamento do serviço.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço básico pode ter exatamente uma partição e até três réplicas, para um limite máximo de três SUs. O único recurso ajustável são as réplicas. Você precisa de um mínimo de duas réplicas para alta disponibilidade em consultas.

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

O SUs, os preços e a capacidade são explicados em detalhes no site do Azure. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e partições divide-se uniformemente em 12 (especificamente, 1, 2, 3, 4, 6, 12). Isso ocorre porque o Azure Search divide previamente cada índice em 12 fragmentos, para que possam ser distribuídos em partes iguais entre todas as partições. Por exemplo, se o serviço tiver três partições e você criar um índice, cada partição conterá quatro fragmentos do índice. Como Azure Search fragmentos um índice é um detalhe de implementação, sujeito a alterações em versões futuras. Embora o número seja 12 hoje, você não deve esperar que esse número seja sempre 12 no futuro.
>


<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidade
Como é fácil e relativamente rápido escalar verticalmente, geralmente recomendamos que você inicie com uma partição e uma ou duas réplicas e, em seguida, aumente verticalmente conforme os volumes de consulta são criados. As cargas de trabalho de consulta são executadas principalmente em réplicas. Se você precisar de mais taxa de transferência ou alta disponibilidade, provavelmente precisará de réplicas adicionais.

Recomendações gerais para alta disponibilidade são:

* Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

* Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas mais indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

Os contratos de nível de serviço (SLA) para Azure Search são direcionados a operações de consulta e em atualizações de índice que consistem em Adicionar, atualizar ou excluir documentos.

Camada básica tops out em uma partição e três réplicas. Se você quiser que a flexibilidade responda imediatamente às flutuações na demanda para a indexação e a taxa de transferência de consulta, considere uma das camadas padrão.  Se você achar que seus requisitos de armazenamento estão crescendo muito mais rapidamente do que a taxa de transferência de consulta, considere uma das camadas de armazenamento otimizado.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidade de índice durante uma recompilação

A alta disponibilidade para Azure Search pertence a consultas e atualizações de índice que não envolvem a recriação de um índice. Se você excluir um campo, alterar um tipo de dados ou renomear um campo, será necessário recriar o índice. Para recompilar o índice, é necessário excluir o índice, recriar o índice e recarregar os dados.

> [!NOTE]
> Você pode adicionar novos campos a um índice de Azure Search sem recompilar o índice. O valor do novo campo será nulo para todos os documentos que já estão no índice.

Para manter a disponibilidade do índice durante uma recompilação, é necessário ter uma cópia do índice com um nome diferente no mesmo serviço, ou uma cópia do índice com o mesmo nome em um serviço diferente e fornecer a lógica de redirecionamento ou de failover no código.

## <a name="disaster-recovery"></a>Recuperação de desastre
Atualmente, não há nenhum mecanismo interno para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atender aos objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância ao nível de serviço Configurando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir do seu código.

## <a name="increase-query-performance-with-replicas"></a>Aumentar o desempenho da consulta com réplicas
A latência de consulta é um indicador de que réplicas adicionais são necessárias. Em geral, uma primeira etapa para melhorar o desempenho da consulta é adicionar mais desse recurso. À medida que você adiciona réplicas, cópias adicionais do índice são colocadas online para dar suporte a cargas de trabalho de consulta maiores e balancear a carga das solicitações em várias réplicas.

Não podemos fornecer estimativas rígidas em consultas por segundo (QPS): o desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas claramente resulte em um melhor desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante uma taxa de transferência tripla.

Para obter orientações sobre como estimar QPS para suas cargas de trabalho, consulte [Azure Search considerações sobre desempenho e otimização](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumentar o desempenho de indexação com partições
Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições espalha as operações de leitura/gravação em um número maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para a consulta. Dessa forma, você pode descobrir que cada aumento incremental nas partições requer um aumento menor, mas proporcional em réplicas. A complexidade de suas consultas e do volume de consulta determinará a rapidez com que a execução da consulta é reativada.


## <a name="next-steps"></a>Próximos passos

[Escolha um tipo de preço para Azure Search](search-sku-tier.md)
