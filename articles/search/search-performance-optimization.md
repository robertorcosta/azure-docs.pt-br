---
title: Dimensionamento em razão de desempenho
titleSuffix: Azure Cognitive Search
description: Aprenda técnicas e práticas recomendadas para ajustar o desempenho da Pesquisa Cognitiva do Azure e configurar a escala ideal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212379"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Escala para desempenho na Pesquisa Cognitiva do Azure

Este artigo descreve as melhores práticas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade.

## <a name="start-with-baseline-numbers"></a>Comece com números de linha de base

Antes de realizar um esforço de implantação maior, certifique-se de saber como é uma carga típica de consulta. As seguintes diretrizes podem ajudá-lo a chegar aos números de consulta de linha de base.

1. Escolher uma latência de destino (ou a quantidade máxima de tempo) que uma solicitação de pesquisa normal deve demorar para ser concluída.

1. Crie e teste uma carga de trabalho real contra seu serviço de pesquisa com um conjunto de dados realista para medir essas taxas de latência.

1. Comece com um número baixo de consultas por segundo (QPS) e, em seguida, aumente gradualmente o número executado no teste até que a latência da consulta caia abaixo do alvo predefinido. Este é um parâmetro de comparação importante para ajudar você a planejar a escala à medida que o uso de seu aplicativo cresce.

1. Sempre que possível, reutilize as conexões HTTP. Se você estiver usando o Azure Cognitive Search .NET SDK, isso significa que você deve reutilizar uma instância ou a instância [SearchIndexClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) e se você estiver usando a API REST, você deve reutilizar um único HttpClient.

1. Varie a substância das solicitações de consulta para que a pesquisa ocorra em diferentes partes do seu índice. A variação é importante porque se você executar continuamente as mesmas solicitações de pesquisa, o cache de dados começará a fazer com que o desempenho pareça melhor do que poderia com um conjunto de consultas mais díspare.

1. Varie a estrutura das solicitações de consulta para que você obtenha diferentes tipos de consultas. Nem todas as consultas de pesquisa são executados no mesmo nível. Por exemplo, uma pesquisa de documento ou sugestão de pesquisa é tipicamente mais rápida do que uma consulta com um número significativo de facetas e filtros. A composição do teste deve incluir várias consultas, aproximadamente nas mesmas proporções que você esperaria na produção.  

Ao criar essas cargas de trabalho de teste, existem algumas características da Pesquisa Cognitiva do Azure para ter em mente:

+ É possível sobrecarregar seu serviço empurrando muitas consultas de pesquisa ao mesmo tempo. Quando isso acontecer, você verá códigos de resposta HTTP 503. Para evitar um 503 durante os testes, comece com várias faixas de solicitações de pesquisa para ver as diferenças nas taxas de latência à medida que você adiciona mais solicitações de pesquisa.

+ O Azure Cognitive Search não executa tarefas de indexação em segundo plano. Se o seu serviço lida com consultas e indexação de cargas de trabalho simultaneamente, leve isso em conta, introduzindo trabalhos de indexação em seus testes de consulta, ou explorando opções para executar trabalhos de indexação durante os horários de pico.

> [!Tip]
> Você pode simular uma carga de consulta realista usando ferramentas de teste de carga. Tente [testar a carga com DevOps Do Zure](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) ou use uma dessas [alternativas](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Escala para alto volume de consulta

Um serviço é sobrecarregado quando as consultas demoram muito ou quando o serviço começa a soltar solicitações. Se isso acontecer, você pode resolver o problema de duas maneiras:

+ **Adicionar réplicas**  

  Cada réplica é uma cópia de seus dados, permitindo que o serviço carregue solicitações de equilíbrio contra várias cópias.  Todo o balanceamento de carga e replicação de dados é gerenciado pelo Azure Cognitive Search e você pode alterar o número de réplicas alocadas para o seu serviço a qualquer momento. É possível alocar até 12 réplicas em um serviço de pesquisa Standard e três réplicas em um serviço de pesquisa Básico. As réplicas podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

+ **Crie um novo serviço em um nível mais alto**  

  O Azure Cognitive Search vem em vários níveis e cada um oferece [diferentes](https://azure.microsoft.com/pricing/details/search/) níveis de desempenho. Em alguns casos, você pode ter tantas consultas que o nível em que você está não pode fornecer reviravolta suficiente, mesmo quando as réplicas são maximizadas. Neste caso, considere mudar para um nível de desempenho mais alto, como o nível Standard S3, projetado para cenários com grande número de documentos e cargas de trabalho de consulta extremamente altas.

## <a name="scale-for-slow-individual-queries"></a>Escala para consultas individuais lentas

Outra razão para altas taxas de latência é uma única consulta que demora muito para ser concluída. Neste caso, adicionar réplicas não ajudará. Duas opções possíveis que podem ajudar incluem as seguintes:

+ **Aumentar partições**

  Uma partição divide dados entre recursos extras de computação. Duas partições dividem dados ao meio, uma terceira partição divide-os em terços, e assim por diante. Um efeito colateral positivo é que consultas mais lentas às vezes funcionam mais rápido devido à computação paralela. Notamos paralelota em consultas de baixa seletividade, como consultas que correspondem a muitos documentos ou facetas que fornecem contagens sobre um grande número de documentos. Uma vez que o cálculo significativo é necessário para marcar a relevância dos documentos, ou para contar os números de documentos, adicionar partições extras ajuda a concluir consultas mais rapidamente.  
   
  Pode haver um máximo de 12 partições no serviço de pesquisa Padrão e 1 partição no serviço de pesquisa Basic. As partições podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

+ **Limitar campos de cardinalidade alta**

  Um campo de alta cardinalidade consiste em um campo facetado ou filtrante que tem um número significativo de valores únicos e, como resultado, consome recursos significativos ao computar resultados. Por exemplo, definir um campo de Identificação de Produto ou Descrição como tabela facial/filtrante contaria como alta cardinalidade porque a maioria dos valores de documento para documento são únicos. Sempre que for possível, limite o número de campos de alta cardinalidade.

+ **Aumentar o nível de pesquisa**  

  Mudar-se para um nível mais alto de Pesquisa Cognitiva do Azure pode ser outra maneira de melhorar o desempenho de consultas lentas. Cada nível mais alto fornece CPUs mais rápidas e mais memória, ambas com impacto positivo no desempenho da consulta.

## <a name="scale-for-availability"></a>Escala para disponibilidade

As réplicas não só ajudam a reduzir a latência da consulta, mas também podem permitir alta disponibilidade. Com uma única réplica, você deve esperar um tempo de inatividade periódico devido à reinicialização do servidor após atualizações de software ou a outros eventos de manutenção. Como resultado, é importante considerar se seu aplicativo exige alta disponibilidade de pesquisas (consultas), bem como gravações (eventos de indexação). O Azure Cognitive Search oferece opções de SLA em todas as ofertas de pesquisa paga com os seguintes atributos:

+ Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

+ Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [Azure Cognitive Search Service Level Agreement](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como as réplicas são cópias de seus dados, ter várias réplicas permite que o Azure Cognitive Search faça reinicializações e manutenção da máquina contra uma réplica, enquanto a execução de consultas continua em outras réplicas. Por outro lado, se você tirar as réplicas, você incorrerá na degradação do desempenho da consulta, assumindo que essas réplicas eram um recurso subutilizado.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Escala para cargas de trabalho geodistribuídas e geo-redundância

Para cargas de trabalho geodistribuídas, os usuários que estão localizados longe do data center host terão taxas de latência mais altas. Uma atenuação é a prestação de múltiplos serviços de busca em regiões com maior proximidade com esses usuários.

A azure Cognitive Search atualmente não fornece um método automatizado de geo-replicação de índices de pesquisa cognitiva do Azure em todas as regiões, mas existem algumas técnicas que podem ser usadas que podem tornar esse processo simples de implementar e gerenciar. Elas serão descritas nas próximas seções.

O objetivo de um conjunto geodistribuído de serviços de pesquisa é ter dois ou mais índices disponíveis em duas ou mais regiões, onde um usuário é encaminhado para o serviço de Pesquisa Cognitiva Do Azure que fornece a menor latência como visto neste exemplo:

   ![Tabela de referência cruzada de serviços por região][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Mantenha os dados sincronizados em vários serviços

Existem duas opções para manter seus serviços de pesquisa distribuída em sincronia, que consistem em usar o [Azure Cognitive Search Indexer](search-indexer-overview.md) ou a Push API (também conhecida como [API de Pesquisa Cognitiva Do Azure](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Use indexadores para atualizar conteúdo em vários serviços

Se você já estiver usando o indexador em um serviço, você pode configurar um segundo indexador em um segundo serviço para usar o mesmo objeto de origem de dados, puxando dados do mesmo local. Cada serviço em cada região tem seu próprio indexador e um índice-alvo (seu índice de pesquisa não é compartilhado, o que significa que os dados são duplicados), mas cada indexador faz referência à mesma fonte de dados.

Aqui está um visual de alto nível de como seria essa arquitetura.

   ![Única fonte de dados com o indexador distribuído e combinações de serviço][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Use APIs REST para empurrar atualizações de conteúdo em vários serviços

Se você estiver usando a API Azure Cognitive Search REST para [empurrar conteúdo em seu índice de Pesquisa Cognitiva Do Azure,](https://docs.microsoft.com/rest/api/searchservice/update-index)você pode manter seus vários serviços de pesquisa em sincronia, pressionando alterações em todos os serviços de pesquisa sempre que uma atualização for necessária. Em seu código, certifique-se de lidar com casos em que uma atualização para um serviço de pesquisa falha, mas tem sucesso para outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Alavancagem do gerenciador de tráfego do Azure

[O Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) permite que você encaminhe solicitações para vários sites geolocalizados que são então apoiados por vários serviços de pesquisa. Uma vantagem do Gerenciador de Tráfego é que ele pode sondar o Azure Cognitive Search para garantir que ele esteja disponível e direcionar os usuários para serviços de pesquisa alternativos em caso de tempo de inatividade. Além disso, se você estiver roteando solicitações de pesquisa através de Sites da Web do Azure, o Azure Traffic Manager permite que você carregue os casos de equilíbrio em que o Site está em atividade, mas não no Azure Cognitive Search. Veja um exemplo de uma arquitetura que aproveita o Gerenciador de Tráfego.

   ![Tabela de referência cruzada de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os níveis de preços e os limites de serviços para cada um, consulte [limites de serviço](search-limits-quotas-capacity.md). Consulte [O plano de capacidade](search-capacity-planning.md) para saber mais sobre combinações de partição e réplicas.

Para uma discussão sobre desempenho e demonstrações das técnicas discutidas neste artigo, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
