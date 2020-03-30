---
title: Monitorar operações e atividades
titleSuffix: Azure Cognitive Search
description: Habilite o registro, obtenha métricas de atividade de consulta, uso de recursos e outros dados do sistema de um serviço de pesquisa cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462319"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorar operações e atividades da Pesquisa Cognitiva do Azure

Este artigo introduz o monitoramento no nível de serviço (recurso), no nível de carga de trabalho (consultas e indexação), e sugere uma estrutura para monitorar o acesso do usuário.

Em todo o espectro, você usará uma combinação de infra-estrutura incorporada e serviços fundamentais como o Azure Monitor, bem como APIs de serviço que retornam estatísticas, contagens e status. Entender a gama de recursos pode ajudá-lo a construir um loop de feedback para que você possa resolver problemas à medida que eles emergem.

## <a name="use-azure-monitor"></a>Usar o Azure Monitor

Muitos serviços, incluindo o Azure Cognitive Search, aproveitam [o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) para alertas, métricas e dados de diagnóstico de registro. Para o Azure Cognitive Search, a infra-estrutura de monitoramento incorporada é usada principalmente para monitoramento em nível de recurso (saúde do serviço) e [monitoramento de consultas.](search-monitor-queries.md)

A captura de tela a seguir ajuda a localizar os recursos do Azure Monitor no portal.

+ A guia **de monitoramento,** localizada na página de visão geral principal, mostra as principais métricas rapidamente.
+ **Registro de atividades**, logo abaixo Visão geral, relatórios sobre ações em nível de recurso: saúde do serviço e notificações de solicitação de aPI.
+ **O monitoramento**, mais abaixo da lista, fornece alertas configuráveis, métricas e registros de diagnóstico. Crie isso quando precisar. Uma vez que os dados são coletados e armazenados, você pode consultar ou visualizar as informações para obter insights.

![Integração do Azure Monitor em um serviço de pesquisa](./media/search-monitor-usage/azure-monitor-search.png
 "Integração do Azure Monitor em um serviço de pesquisa")

### <a name="precision-of-reported-numbers"></a>Precisão dos números relatados

As páginas do portal são atualizadas a cada poucos minutos. Como tal, os números relatados no portal são aproximados, com o objetivo de lhe dar uma noção geral de quão bem seu sistema está atendendo solicitações. As métricas reais, como consultas por segundo (QPS), podem ser maiores ou menores do que o número mostrado na página.

## <a name="activity-logs-and-service-health"></a>Registros de atividades e serviços de saúde

O [**registro atividade**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) coleta informações do Azure Resource Manager e relata alterações na saúde do serviço. Você pode monitorar o registro de atividades para condições críticas, erros e de alerta relacionadas à saúde do serviço.

Para tarefas em serviço - como consultas, indexação ou criação de objetos - você verá notificações informárias genéricas como *Obter chave de anúncio* e obter chaves de *consulta* para cada solicitação, mas não a ação específica em si. Para obter informações sobre este grão, você deve configurar o registro de diagnóstico.

Você pode acessar o **Log de atividades** no painel de navegação à esquerda, em Notificações na barra de comando da janela superior ou na página **Diagnosticar e solucionar problemas**.

## <a name="monitor-storage"></a>Monitore o armazenamento

Páginas com guias incorporadas ao relatório da página Visão Geral sobre o uso de recursos. Essas informações ficam disponíveis assim que você começa a usar o serviço, sem necessidade de configuração, e a página é atualizada a cada poucos minutos. 

Se estiver decidindo sobre [qual camada usar para cargas de trabalho de produção](search-sku-tier.md), ou se quiser [ajustar o número de partições e réplicas ativas](search-capacity-planning.md), essas métricas podem ajudá-lo nessas decisões ao mostrar o quão rápido os recursos são consumidos e o quão bem a configuração atual lida com a carga existente.

Os alertas relacionados ao armazenamento não estão disponíveis no momento; o consumo de armazenamento não é agregado ou registrado na tabela **AzureMetrics** no Azure Monitor. Você precisaria [construir uma solução personalizada](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) que emita notificações relacionadas a recursos, onde seu código verifica o tamanho do armazenamento e lida com a resposta. Para obter mais informações sobre métricas de armazenamento, consulte [Obter estatísticas de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Para monitoramento visual no portal, a guia **Uso** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md) atuais impostos pelo nível de serviço. 

A ilustração a seguir aplica-se ao serviço gratuito, que está limitado a três objetos de cada tipo e 50 MB de armazenamento. Um serviço Basic ou Standard tem limites maiores e, se você aumentar a quantidade de partições, o armazenamento máximo aumenta proporcionalmente.

![Status de uso relativo aos limites de nível](./media/search-monitor-usage/usage-tab.png
 "Status de uso relativo aos limites de nível")

## <a name="monitor-workloads"></a>Monitorar cargas de trabalho

Eventos registrados incluem aqueles relacionados à indexação e consultas. A tabela **AzureDiagnostics** no Log Analytics coleta dados operacionais relacionados a consultas e indexação.

A maioria dos dados registrados é para operações somente leitura. Para outras operações de create-update-delete não capturadas no log, você pode consultar o serviço de pesquisa para obter informações do sistema.

| OperationName | Descrição |
|---------------|-------------|
| ServiceStats | Esta operação é uma chamada de rotina para [obter estatísticas de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), chamada direta ou implicitamente para preencher uma página de visão geral do portal quando ela é carregada ou atualizada. |
| Consulta.Pesquisa |  Solicitações de consulta contra um índice Consulte [consultas do Monitor](search-monitor-queries.md) para obter informações sobre consultas registradas.|
| Indexação.Índice  | Esta operação é uma chamada para [Adicionar, Atualizar ou Excluir Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| Índices. Protótipo | Este é um índice criado pelo assistente de dados de importação. |
| Indexadores.Criar | Crie um indexador explicitamente ou implicitamente através do assistente de dados de importação. |
| Indexadores.Obter | Retorna o nome de um indexador sempre que o indexador é executado. |
| Indexadores.Status | Retorna o status de um indexador sempre que o indexador é executado. |
| DataSources.Get | Retorna o nome da fonte de dados sempre que um indexador é executado.|
| Índices.Obter | Retorna o nome de um índice sempre que um indexador é executado. |

### <a name="kusto-queries-about-workloads"></a>Perguntas de Kusto sobre cargas de trabalho

Se você habilitou o registro, poderá consultar **o AzureDiagnostics** para obter uma lista de operações que foram realizadas em seu serviço e quando. Você também pode correlacionar atividade para investigar mudanças no desempenho.

#### <a name="example-list-operations"></a>Exemplo: Operações de lista 

Retorne uma lista de operações e uma contagem de cada uma.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemplo: Correlacionar operações

Correlacionar a solicitação de consulta com as operações de indexação e renderizar os pontos de dados em um gráfico de tempo para ver as operações coincidirem.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Use APIs de pesquisa

Tanto a API Azure Cognitive Search REST quanto a .NET SDK fornecem acesso programático a métricas de serviço, informações de índice e indexador e contagem de documentos.

+ [Estatísticas de serviços GET](/rest/api/searchservice/get-service-statistics)
+ [Estatísticas do Índice GET](/rest/api/searchservice/get-index-statistics)
+ [CONTAGEM de documentos GET](/rest/api/searchservice/count-documents)
+ [Status do indexador GET](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorar o acesso do usuário

Como os índices de pesquisa são um componente de um aplicativo cliente maior, não há uma metodologia incorporada para controlar ou monitorar o acesso por usuário a um índice. Supõe-se que as solicitações sejam provenientes de um aplicativo cliente, tanto para solicitações de admin quanto de consulta. As operações de gravação de leitura do admin incluem a criação, atualização, exclusão de objetos em todo o serviço. Operações somente leitura são consultas contra a coleta de documentos, escopo para um único índice. 

Como tal, o que você verá nos registros de atividades são referências a chamadas usando chaves de admin ou chaves de consulta. A chave apropriada está incluída nas solicitações originárias do código do cliente. O serviço não está equipado para lidar com tokens de identidade ou personificação.

Quando os requisitos de negócios existem para autorização por usuário, a recomendação é a integração com o Azure Active Directory. Você pode usar $filter e identidades de usuário para [aparar resultados](search-security-trimming-for-azure-search-with-aad.md) de pesquisa de documentos que um usuário não deve ver. 

Não há como registrar essas informações separadamente da seqüência de consultas que inclui o parâmetro $filter. Consulte [As consultas do Monitor](search-monitor-queries.md) para obter detalhes sobre as seqüências de consultas de relatórios.

## <a name="next-steps"></a>Próximas etapas

A fluência com o Azure Monitor é essencial para a supervisão de qualquer serviço do Azure, incluindo recursos como o Azure Cognitive Search. Se você não estiver familiarizado com o Azure Monitor, aproveite para revisar artigos relacionados a recursos. Além dos tutoriais, o artigo a seguir é um bom lugar para começar.

> [!div class="nextstepaction"]
> [Monitorando os recursos do Azure com o Monitor Do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
