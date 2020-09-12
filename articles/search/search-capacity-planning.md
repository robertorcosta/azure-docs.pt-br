---
title: Ajustar a capacidade de cargas de trabalho de consulta e índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos de computador da partição e da réplica no Azure Pesquisa Cognitiva, em que cada recurso é cobrado nas unidades de pesquisa cobráveis.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 76084a9ddd6842194bb4c6b25d62e62c2ed2d4a8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660316"
---
# <a name="adjust-the-capacity-of-an-azure-cognitive-search-service"></a>Ajustar a capacidade de um serviço de Pesquisa Cognitiva do Azure

Antes de [provisionar um serviço de pesquisa](search-create-service-portal.md) e bloqueá-lo em um tipo de preço específico, reserve alguns minutos para entender como a capacidade funciona e como você pode ajustar réplicas e partições para acomodar a flutuação de carga de trabalho.

A capacidade é uma função da [camada escolhida](search-sku-tier.md) (camadas determinam as características de hardware) e a combinação de réplica e partição necessária para cargas de trabalho projetadas. Você pode aumentar ou diminuir o número de réplicas ou partições individualmente. Dependendo da camada e do tamanho do ajuste, adicionar ou reduzir a capacidade pode levar de 15 minutos a várias horas.

Ao modificar a alocação de réplicas e partições, é recomendável usar o portal do Azure. O portal impõe limites para combinações permitidas que ficam abaixo dos limites máximos de uma camada. No entanto, se você precisar de uma abordagem de provisionamento baseada em script ou em código, o [Azure PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](/rest/api/searchmanagement/services) são soluções alternativas.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Conceitos: unidades de pesquisa, réplicas, partições, fragmentos

A capacidade é expressa *em unidades de pesquisa* que podem ser alocadas em combinações de *partições* e *réplicas*, usando um mecanismo de *fragmentação* subjacente para dar suporte a configurações flexíveis:

| Conceito  | Definição|
|----------|-----------|
|*Unidade de pesquisa* | Um único incremento da capacidade total disponível (36 unidades). Também é a unidade de cobrança para um serviço de Pesquisa Cognitiva do Azure. É necessário um mínimo de uma unidade para executar o serviço.|
|*Réplica* | Instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica hospeda uma cópia de um índice. Se você alocar três réplicas, terá três cópias de um índice disponível para atender às solicitações de consulta.|
|*Partição* | Armazenamento físico e e/s para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice). Cada partição tem uma fatia do índice total. Se você alocar três partições, o índice será dividido em terços. |
|*Fragmentos* | Um bloco de um índice. O Azure Pesquisa Cognitiva divide cada índice em fragmentos para tornar o processo de adição de partições mais rápido (movendo fragmentos para novas unidades de pesquisa).|

O diagrama a seguir mostra a relação entre réplicas, partições, fragmentos e unidades de pesquisa. Ele mostra um exemplo de como um único índice é estendido em quatro unidades de pesquisa em um serviço com duas réplicas e duas partições. Cada uma das quatro unidades de pesquisa armazena apenas metade dos fragmentos do índice. As unidades de pesquisa na coluna esquerda armazenam a primeira metade dos fragmentos, que compõem a primeira partição, enquanto aquelas na coluna direita armazenam a segunda metade dos fragmentos, que compõem a segunda partição. Como há duas réplicas, há duas cópias de cada fragmento de índice. As unidades de pesquisa na linha superior armazenam uma cópia, que inclui a primeira réplica, enquanto aquelas na linha inferior armazenam outra cópia, compostando a segunda réplica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Os índices de pesquisa são fragmentados entre partições.":::

O diagrama acima é apenas um exemplo. Muitas combinações de partições e réplicas são possíveis, até um máximo de 36 unidades de pesquisa de total.

No Pesquisa Cognitiva, o gerenciamento de fragmentos é um detalhe de implementação e não configurável, mas saber que um índice é fragmentado ajuda a entender as anomalias ocasionais em classificação e comportamentos de preenchimento automático:

+ A classificação de anomalias: as pontuações de pesquisa são calculadas primeiro no nível do fragmento e, em seguida, agregadas em um único conjunto de resultados. Dependendo das características do conteúdo do fragmento, as correspondências de um fragmento podem ter uma classificação maior do que as correspondências em outra. Se você observar classificações não intuitivas nos resultados da pesquisa, isso provavelmente ocorrerá devido aos efeitos da fragmentação, especialmente se os índices forem pequenos. Você pode evitar essas anomalias de classificação escolhendo [calcular pontuações globalmente em todo o índice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), mas isso incorrerá em uma penalidade de desempenho.

+ Anomalias de preenchimento automático: consultas de preenchimento automático, em que as correspondências são feitas nos primeiros vários caracteres de um termo parcialmente inserido, aceitam um parâmetro difuso que Forgives pequenos desvios na grafia. Para preenchimento automático, a correspondência difusa é restrita a termos dentro do fragmento atual. Por exemplo, se um fragmento contiver "Microsoft" e um termo parcial de "Micor" for inserido, o mecanismo de pesquisa corresponderá em "Microsoft" nesse fragmento, mas não em outros fragmentos que contenham as partes restantes do índice.

## <a name="when-to-add-nodes"></a>Quando adicionar nós

Inicialmente, um serviço é alocado um nível mínimo de recursos que consistem em uma partição e uma réplica.

Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Nenhuma carga de trabalho é executada em segundo plano. Você pode agendar a indexação para tempos em que as solicitações de consulta são naturalmente menos frequentes, mas o serviço não priorizará uma tarefa em vez de outra. Além disso, uma certa quantidade de redundância simplifica o desempenho da consulta quando os serviços ou nós são atualizados internamente.

Como regra geral, os aplicativos de pesquisa tendem a precisar de mais réplicas do que as partições, especialmente quando as operações de serviço são tendenciosas em relação a cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

> [!NOTE]
> Adicionar mais réplicas ou partições aumenta o custo da execução do serviço e pode introduzir pequenas variações de como os resultados são ordenados. Certifique-se de verificar a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para entender as implicações de cobrança da adição de mais nós. O [gráfico abaixo](#chart) pode ajudá-lo a fazer referência cruzada do número de unidades de pesquisa necessárias para uma configuração específica. Para obter mais informações sobre como as réplicas adicionais afetam o processamento da consulta, consulte [Ordering Results](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **configurações**, abra a página **escala** para modificar réplicas e partições. 

   A captura de tela a seguir mostra um serviço padrão provisionado com uma réplica e uma partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas (1). Se o preço unitário era $100 (não um preço real), o custo mensal da execução desse serviço seria de $100 em média.

   ![Página escala mostrando os valores atuais](media/search-capacity-planning/1-initial-values.png "Página escala mostrando os valores atuais")

1. Use o controle deslizante para aumentar ou diminuir o número de partições. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas.

   Este exemplo duplica a capacidade, com duas réplicas e partições cada. Observe a contagem de unidades de pesquisa; Agora, ele é quatro porque a fórmula de cobrança é réplicas multiplicada por partições (2 x 2). Dobrar a capacidade mais do que o dobro do custo da execução do serviço. Se o custo da unidade de pesquisa fosse $100, a nova fatura mensal agora será $400.

   Para os custos por unidade atuais de cada camada, visite a [página de preços](https://azure.microsoft.com/pricing/details/search/).

   ![Adicionar réplicas e partições](media/search-capacity-planning/2-add-2-each.png "Adicionar réplicas e partições")

1. Selecione **salvar** para confirmar as alterações.

   ![Confirmar alterações de escala e cobrança](media/search-capacity-planning/3-save-confirm.png "Confirmar alterações de escala e cobrança")

   As alterações na capacidade levam várias horas para serem concluídas. Não é possível cancelar quando o processo foi iniciado e não há monitoramento em tempo real para os ajustes de réplica e partição. No entanto, a mensagem a seguir permanece visível enquanto as alterações estão em andamento.

   ![Mensagem de status no portal](media/search-capacity-planning/4-updating.png "Mensagem de status no portal")

> [!NOTE]
> Depois que um serviço é provisionado, ele não pode ser atualizado para uma camada superior. Você precisará criar um serviço de pesquisa no novo tipo e recarregar os índices. Consulte [criar um serviço de pesquisa cognitiva do Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento de serviço.
>
> Além disso, partições e réplicas são gerenciadas exclusivamente e internamente pelo serviço. Não há nenhum conceito de afinidade de processador ou atribuição de uma carga de trabalho a um nó específico.
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

SUs, preço e capacidade são explicados detalhadamente no site do Azure. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/search/).

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

## <a name="disaster-recovery"></a>Recuperação de desastre

Atualmente, não há mecanismo integrado para recuperação de desastres. Adicionar partições ou réplicas seria a estratégia incorreta para atingir os objetivos de recuperação de desastres. A abordagem mais comum é adicionar redundância no nível de serviço configurando um segundo serviço de pesquisa em outra região. Assim como acontece com a disponibilidade durante uma recompilação de índice, o redirecionamento ou a lógica de failover deve vir de seu código.

## <a name="estimate-replicas"></a>Estimar réplicas

Em um serviço de produção, você deve alocar três réplicas para fins de SLA. Se você tiver um desempenho de consulta lento, poderá adicionar réplicas para que cópias adicionais do índice sejam colocadas online para dar suporte a cargas de trabalho de consulta maiores e balancear a carga das solicitações em várias réplicas.

Não fornecemos diretrizes sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas definitivamente adicione escala e desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante o triplo da taxa de transferência.

Para obter orientação sobre como estimar o QPS para sua solução, consulte [escala para desempenho](search-performance-optimization.md)e [consultas de monitor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Estimar partições

A [camada escolhida](search-sku-tier.md) determina o tamanho e a velocidade da partição, e cada camada é otimizada em um conjunto de características que se ajustam a vários cenários. Se você escolher uma camada de extremidade superior, poderá precisar de menos partições do que se você estiver com S1. Uma das perguntas que você precisará responder por meio de testes autodirecionados é se uma partição maior e mais cara produz um desempenho melhor do que duas partições mais baratas em um serviço provisionado em uma camada mais baixa.

Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições distribui as operações de leitura/gravação em uma quantidade maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. A complexidade de suas consultas e seu volume influenciarão a rapidez com que a execução da consulta é retornada.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha um tipo de preço para o Azure Pesquisa Cognitiva](search-sku-tier.md)