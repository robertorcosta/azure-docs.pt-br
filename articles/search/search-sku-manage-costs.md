---
title: Estimar a capacidade e os custos
titleSuffix: Azure Cognitive Search
description: Examine as diretrizes para estimar a capacidade e gerenciar os custos do serviço de pesquisa, incluindo infraestrutura e ferramentas no Azure, bem como as práticas recomendadas para o uso de recursos com mais eficiência.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.openlocfilehash: d48ae71a979a2d0f1457b0cefa8a98a02710dd96
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577598"
---
# <a name="how-to-estimate-capacity-and-costs-of-an-azure-cognitive-search-service"></a>Como estimar a capacidade e os custos de um serviço de Pesquisa Cognitiva do Azure

Como parte do planejamento de capacidade para o Azure Pesquisa Cognitiva, as diretrizes a seguir podem ajudá-lo a reduzir os custos ou gerenciar custos com mais eficiência:

+ Crie todos os recursos na mesma região, ou no menor número de regiões possível, para minimizar ou eliminar encargos de largura de banda.

+ Consolide todos os serviços em um grupo de recursos, como o Azure Pesquisa Cognitiva, serviços cognitivas e quaisquer outros serviços do Azure usados em sua solução. No portal do Azure, encontre o grupo de recursos e use os comandos de **Gerenciamento de custos** para obter informações sobre gastos reais e projetados.

+ Considere o aplicativo Web do Azure para seu aplicativo front-end para que as solicitações e respostas permaneçam dentro do limite de data center.

+ Escalar verticalmente para operações com uso intensivo de recursos, como indexação, e reajustar verticalmente para cargas de trabalho de consulta regular. Comece com a configuração mínima do Azure Pesquisa Cognitiva (uma SU composta de uma partição e uma réplica) e, em seguida, monitore a atividade do usuário para identificar os padrões de uso que indicariam a necessidade de mais capacidade. Se houver um padrão previsível, você poderá sincronizar a escala com a atividade (você precisaria escrever código para automatizar isso).

Os eventos faturáveis, a fórmula de cobrança e a taxa Faturável são explicados em [escolher um tipo de preço](search-sku-tier.md). Além disso, você pode visitar o [Gerenciamento de custos e cobrança](../cost-management-billing/cost-management-billing-overview.md) para ferramentas e recursos internos relacionados a gastos.

Não é possível desligar um serviço de pesquisa em uma base temporária. Os recursos dedicados estão sempre operacionais, alocados para seu uso exclusivo durante o tempo de vida do seu serviço. Excluir um serviço é permanente e também exclui seus dados associados.

Em termos do próprio serviço, a única maneira de reduzir sua conta é reduzir as réplicas e partições para um nível que ainda fornece desempenho aceitável e [conformidade de SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), ou criar um serviço em uma camada mais baixa (as tarifas de S1 por hora são menores do que as taxas S2 ou S3). Supondo que você provisione seu serviço na extremidade inferior de suas projeções de carga, se você ultrapassar o serviço, poderá criar um segundo serviço em camadas maior, recriar os índices no segundo serviço e, em seguida, excluir o primeiro.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

No Azure Pesquisa Cognitiva, a capacidade é estruturada como *réplicas* e *partições*.

+ Réplicas são instâncias do serviço de pesquisa. Cada réplica hospeda uma cópia com balanceamento de carga de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregado no serviço.

+ As partições armazenam índices e dividem automaticamente os dados pesquisáveis. Duas partições dividem o índice na metade, três partições as dividem em terços e assim por diante. Em termos de capacidade, o *tamanho da partição* é o principal recurso de diferenciação entre camadas.

> [!NOTE]
> Todas as camadas padrão e de armazenamento otimizadas dão suporte a [combinações flexíveis de réplicas e partições](search-capacity-planning.md#chart) para que você possa [otimizar seu sistema para velocidade ou armazenamento](search-performance-optimization.md) alterando o equilíbrio. A camada básica oferece até três réplicas para alta disponibilidade, mas tem apenas uma partição. As camadas gratuitas não fornecem recursos dedicados: os recursos de computação são compartilhados por vários assinantes.

### <a name="evaluating-capacity"></a>Avaliando a capacidade

A capacidade e os custos de executar o serviço são disponibilizados em mãos. As camadas impõem limites em dois níveis: armazenamento e conteúdo (número de índices, por exemplo). Você deve pensar em ambos porque qualquer limite que atingir primeiro é o limite efetivo.

Os requisitos de negócios normalmente ditam o número de índices que você precisará. Por exemplo, você pode precisar de um índice global para um repositório de documentos grande. Ou talvez você precise de vários índices com base na região, no aplicativo ou no nicho de negócios.

Para determinar o tamanho de um índice, você precisa [compilar um](search-what-is-an-index.md). Seu tamanho será baseado em dados importados e na configuração de índice, como se você habilitar sugestores, filtragem e classificação.

Para pesquisa de texto completo, a estrutura de dados primária é uma estrutura de [índice invertida](https://en.wikipedia.org/wiki/Inverted_index) , que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que você feedu nele. Uma fonte de dados grande com alta redundância pode resultar em um índice menor do que um conjunto de dado menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de datas original.

> [!NOTE] 
> Mesmo que a estimativa de necessidades futuras de índices e armazenamento possa parecer uma tarefa de adivinhação, vale a pena fazer isso. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço em uma camada mais alta e, em seguida, [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco de um serviço de uma camada para outra.
>

## <a name="estimate-with-the-free-tier"></a>Estimar com a camada gratuita

Uma abordagem para calcular a capacidade é iniciar com a camada Livre. Lembre-se de que o serviço gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser desafiador estimar um tamanho de índice projetado com essas restrições, mas estas são as etapas:

+ [Crie um serviço gratuito](search-create-service-portal.md).
+ Prepare um DataSet pequeno e representativo.
+ [Crie um índice inicial no portal](search-get-started-portal.md) e anote seu tamanho. Os recursos e atributos têm um impacto no armazenamento. Por exemplo, a adição de sugestores (consultas de pesquisa conforme o tipo) aumentará os requisitos de armazenamento. Usando o mesmo conjunto de dados, você pode tentar criar várias versões de um índice, com atributos diferentes em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["implicações de armazenamento" em criar um índice básico](search-what-is-an-index.md#index-size).

Com uma estimativa aproximada em mãos, você pode dobrar esse valor para o orçamento de dois índices (desenvolvimento e produção) e, em seguida, escolher a camada adequadamente.

## <a name="estimate-with-a-billable-tier"></a>Estimar com uma camada Faturável

Os recursos dedicados podem acomodar grandes períodos de amostragem e processamento para estimativas mais realistas de quantidade de índice, tamanho e volumes de consulta durante o desenvolvimento. Alguns clientes saltam diretamente com uma camada Faturável e, em seguida, reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Examine os limites de serviço em cada camada](./search-limits-quotas-capacity.md#index-limits) para determinar se as camadas inferiores podem dar suporte ao número de índices de que você precisa. Nas camadas básica, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. A camada de armazenamento otimizado tem um limite de 10 índices porque ele foi projetado para dar suporte a um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Comece de baixo, em básico ou S1, se você não tiver certeza sobre a carga projetada.
    + Comece de alto, em S2 ou mesmo S3, se você souber que vai ter carregamentos de consulta e indexação em larga escala.
    + Comece com o armazenamento otimizado, em L1 ou L2, se você estiver indexando uma grande quantidade de dados e a carga de consulta for relativamente baixa, assim como com um aplicativo de negócios interno.

1. [Criar um índice inicial](search-what-is-an-index.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra as consultas por segundo, as consultas limitadas e a latência de pesquisa. Todos esses valores podem ajudá-lo a decidir se selecionou a camada certa. 

O número de índice e o tamanho são igualmente importantes para sua análise. Isso ocorre porque os limites máximos são atingidos por meio da utilização total de armazenamento (partições) ou por limites máximos de recursos (índices, indexadores e assim por diante), o que vier primeiro. O portal ajuda a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser inalterados se os documentos contiverem dados estranhos. O ideal é que os documentos contenham apenas os dados de que você precisa para a experiência de pesquisa. Os dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de BLOBs ou de tabelas do Azure). Em seguida, um campo deve ser adicionado no índice para manter uma referência de URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos se você estiver carregando vários documentos em massa em uma solicitação). Para obter mais informações, consulte [limites de serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md).
>

**Considerações sobre volume de consultas**

Consultas por segundo (QPS) é uma métrica importante durante o ajuste de desempenho, mas geralmente é apenas uma consideração de camada se você espera um alto volume de consulta no início.

As camadas padrão podem fornecer um equilíbrio entre réplicas e partições. Você pode aumentar o retorno da consulta adicionando réplicas para balanceamento de carga ou adicionar partições para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

Se você espera grandes volumes de consulta sustentados desde o início, considere as camadas mais altas Standard, apoiadas por hardware mais potente. Você pode pegar partições e réplicas offline ou até mesmo alternar para um serviço de camada inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho e otimização do Azure pesquisa cognitiva](search-performance-optimization.md).

As camadas de armazenamento otimizadas são úteis para cargas de trabalho de dados grandes, dando suporte a armazenamento de índice mais geral disponível para quando os requisitos de latência de consulta são menos importantes. Você ainda deve usar réplicas adicionais para balanceamento de carga e partições adicionais para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

**Contratos de nível de serviço**

Os recursos de camada gratuita e visualização não fornecem [contratos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Você precisa ter duas ou mais réplicas para SLAs de consulta (leitura). Você precisa ter três ou mais réplicas para os SLAs de consulta e indexação (leitura/gravação). O número de partições não afeta os SLAs.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Permitir que as métricas sejam compiladas em consultas e coletar dados sobre padrões de uso (consultas durante o horário comercial, indexação fora do horário de pico). Use esses dados para informar as decisões de provisionamento do serviço. Embora não seja prático em uma cadência de hora ou diária, você pode ajustar dinamicamente as partições e os recursos para acomodar as alterações planejadas nos volumes de consulta. Você também pode acomodar alterações não planejadas, mas sustentadas, se os níveis tiverem tempo suficiente para garantir a tomada de ações.

+ Lembre-se de que a única desvantagem do no provisionamento é que você pode precisar subdividir um serviço se os requisitos reais forem maiores do que suas previsões. Para evitar a interrupção do serviço, você deve criar um novo serviço em uma camada superior e executá-lo lado a lado até que todos os aplicativos e solicitações tenham como destino o novo ponto de extremidade.

## <a name="next-steps"></a>Próximas etapas

Comece com uma camada gratuita e crie um índice inicial usando um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure Pesquisa Cognitiva é uma estrutura de índice invertida. O tamanho e a complexidade de um índice invertido são determinados pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor que o conteúdo altamente irregular. Portanto, as características de conteúdo, em vez do tamanho do conjunto de os, determinam os requisitos de armazenamento de índice

Depois de ter uma estimativa inicial do tamanho do índice, [provisione um serviço Faturável](search-create-service-portal.md) em uma das camadas discutidas neste artigo: básico, Standard ou armazenamento otimizado. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [recompile o índice](search-howto-reindex.md) para incluir todos os dados que você deseja que sejam pesquisáveis.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escalabilidade que você precisa.

Se o desempenho e a capacidade estiverem corretos, você estará pronto. Caso contrário, crie novamente um serviço de pesquisa em uma outra camada mais estreitamente alinhada a suas necessidades.

> [!NOTE]
> Se você tiver dúvidas, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).