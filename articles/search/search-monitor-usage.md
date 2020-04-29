---
title: Monitorar operações e atividades
titleSuffix: Azure Cognitive Search
description: Habilite o registro em log, obtenha métricas de atividade de consulta, uso de recursos e outros dados do sistema de um serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77462319"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Monitorar operações e atividades do Azure Pesquisa Cognitiva

Este artigo apresenta o monitoramento no nível de serviço (recurso), no nível de carga de trabalho (consultas e indexação) e sugere uma estrutura para monitorar o acesso do usuário.

Em todo o espectro, você usará uma combinação de infraestrutura interna e serviços fundamentais como Azure Monitor, bem como APIs de serviço que retornam estatísticas, contagens e status. Entender a variedade de recursos pode ajudá-lo a construir um loop de comentários para que você possa resolver problemas à medida que eles surgirem.

## <a name="use-azure-monitor"></a>Usar o Azure Monitor

Muitos serviços, incluindo o Pesquisa Cognitiva do Azure, aproveitam [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) para alertas, métricas e registrar dados de diagnóstico. Para o Azure Pesquisa Cognitiva, a infraestrutura de monitoramento interna é usada principalmente para o monitoramento em nível de recurso (integridade do serviço) e o [monitoramento de consultas](search-monitor-queries.md).

A captura de tela a seguir ajuda você a localizar Azure Monitor recursos no Portal.

+ A guia **monitoramento** , localizada na página Visão geral principal, mostra as principais métricas em um relance.
+ **Log de atividades**, apenas abaixo da visão geral, relatórios sobre ações em nível de recurso: notificações de solicitação de integridade de serviço e de chave de API.
+ O **monitoramento**, além da lista, fornece alertas configuráveis, métricas e logs de diagnóstico. Crie-os quando precisar deles. Depois que os dados são coletados e armazenados, você pode consultar ou visualizar as informações de insights.

![Integração de Azure Monitor em um serviço de pesquisa](./media/search-monitor-usage/azure-monitor-search.png
 "Integração de Azure Monitor em um serviço de pesquisa")

### <a name="precision-of-reported-numbers"></a>Precisão dos números relatados

As páginas do portal são atualizadas a cada poucos minutos. Dessa forma, os números relatados no portal são aproximados, destinados a fornecer uma noção geral de como o sistema está atendendo às solicitações. As métricas reais, como consultas por segundo (QPS), podem ser maiores ou menores do que o número mostrado na página.

## <a name="activity-logs-and-service-health"></a>Logs de atividade e integridade do serviço

O [**log de atividades**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) coleta informações de Azure Resource Manager e relata sobre alterações na integridade do serviço. Você pode monitorar o log de atividades em busca de condições críticas, de erro e de aviso relacionadas à integridade do serviço.

Para tarefas em serviço – como consultas, indexação ou criação de objetos – você verá notificações informativas genéricas, como *obter chave de administração* e *obter chaves de consulta* para cada solicitação, mas não a própria ação específica. Para obter informações sobre esse detalhamento, você deve configurar o log de diagnósticos.

Você pode acessar o **Log de atividades** no painel de navegação à esquerda, em Notificações na barra de comando da janela superior ou na página **Diagnosticar e solucionar problemas**.

## <a name="monitor-storage"></a>Monitorar o armazenamento

As páginas com guias criadas na página Visão geral relatam sobre o uso de recursos. Essas informações ficam disponíveis assim que você começa a usar o serviço, sem nenhuma configuração necessária, e a página é atualizada a cada poucos minutos. 

Se estiver decidindo sobre [qual camada usar para cargas de trabalho de produção](search-sku-tier.md), ou se quiser [ajustar o número de partições e réplicas ativas](search-capacity-planning.md), essas métricas podem ajudá-lo nessas decisões ao mostrar o quão rápido os recursos são consumidos e o quão bem a configuração atual lida com a carga existente.

Os alertas relacionados ao armazenamento não estão disponíveis no momento; o consumo de armazenamento não é agregado ou conectado à tabela **AzureMetrics** em Azure monitor. Você precisaria [criar uma solução personalizada](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) que emita notificações relacionadas a recursos, em que seu código verifica o tamanho do armazenamento e manipula a resposta. Para obter mais informações sobre métricas de armazenamento, consulte [obter estatísticas de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

Para o monitoramento visual no portal, a guia **uso** mostra a disponibilidade de recursos em relação aos [limites](search-limits-quotas-capacity.md) atuais impostos pela camada de serviço. 

A ilustração a seguir aplica-se ao serviço gratuito, que está limitado a três objetos de cada tipo e 50 MB de armazenamento. Um serviço Basic ou Standard tem limites maiores e, se você aumentar a quantidade de partições, o armazenamento máximo aumenta proporcionalmente.

![Status de uso relativo aos limites de camada](./media/search-monitor-usage/usage-tab.png
 "Status de uso relativo aos limites de camada")

## <a name="monitor-workloads"></a>Monitorar cargas de trabalho

Os eventos registrados em log incluem aqueles relacionados à indexação e às consultas. A tabela **AzureDiagnostics** no log Analytics coleta dados operacionais relacionados a consultas e indexação.

A maioria dos dados registrados em log é para operações somente leitura. Para outras operações de criação-atualização-exclusão não capturadas no log, você pode consultar o serviço de pesquisa para obter informações do sistema.

| OperationName | Descrição |
|---------------|-------------|
| Perstats | Essa operação é uma chamada de rotina para [obter estatísticas de serviço](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), chamadas direta ou implicitamente para preencher uma página de visão geral do portal quando ela é carregada ou atualizada. |
| Consulta. Search |  Solicitações de consulta em um índice consulte [monitorar consultas](search-monitor-queries.md) para obter informações sobre consultas registradas.|
| Indexação. index  | Esta operação é uma chamada para [Adicionar, atualizar ou excluir documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| índices. Funções | Este é um índice criado pelo assistente de importação de dados. |
| Indexadores. Create | Crie um indexador explicitamente ou implicitamente por meio do assistente de importação de dados. |
| Indexadores. Get | Retorna o nome de um indexador sempre que o indexador é executado. |
| Indexadores. status | Retorna o status de um indexador sempre que o indexador é executado. |
| Fontes de fonte. Get | Retorna o nome da fonte de dados sempre que um indexador é executado.|
| Indexes. Get | Retorna o nome de um índice sempre que um indexador é executado. |

### <a name="kusto-queries-about-workloads"></a>Kusto consultas sobre cargas de trabalho

Se você habilitou o registro em log, poderá consultar **AzureDiagnostics** para obter uma lista de operações que foram executadas em seu serviço e quando. Você também pode correlacionar a atividade para investigar alterações no desempenho.

#### <a name="example-list-operations"></a>Exemplo: listar operações 

Retornar uma lista de operações e uma contagem de cada uma delas.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exemplo: correlacionar operações

Correlacione a solicitação de consulta com operações de indexação e processe os pontos de dados em um gráfico de tempo para ver as operações coincidirem.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Usar APIs de pesquisa

A API REST do Azure Pesquisa Cognitiva e o SDK do .NET fornecem acesso programático às métricas de serviço, informações de índice e indexador e contagens de documentos.

+ [OBTER estatísticas de serviço](/rest/api/searchservice/get-service-statistics)
+ [OBTER estatísticas de índice](/rest/api/searchservice/get-index-statistics)
+ [OBTER contagens de documentos](/rest/api/searchservice/count-documents)
+ [OBTER o status do indexador](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Monitorar o acesso do usuário

Como os índices de pesquisa são um componente de um aplicativo cliente maior, não há nenhuma metodologia interna para controlar ou monitorar o acesso por usuário a um índice. As solicitações são consideradas provenientes de um aplicativo cliente, para solicitações de administrador ou de consulta. As operações de leitura/gravação do administrador incluem a criação, a atualização e a exclusão de objetos em todo o serviço. Operações somente leitura são consultas em relação à coleção de documentos, com escopo para um único índice. 

Assim, o que você verá nos logs de atividade são referências a chamadas usando chaves de administração ou chaves de consulta. A chave apropriada é incluída em solicitações originadas do código do cliente. O serviço não está equipado para manipular tokens de identidade ou representação.

Quando os requisitos de negócios existem para autorização por usuário, a recomendação é a integração com o Azure Active Directory. Você pode usar $filter e identidades de usuário para [aparar os resultados da pesquisa](search-security-trimming-for-azure-search-with-aad.md) de documentos que um usuário não deve ver. 

Não é possível registrar essas informações separadamente da cadeia de caracteres de consulta que inclui o parâmetro $filter. Consulte [monitorar consultas](search-monitor-queries.md) para obter detalhes sobre cadeias de caracteres de consulta de relatórios.

## <a name="next-steps"></a>Próximas etapas

O fluência com Azure Monitor é essencial para supervisão de qualquer serviço do Azure, incluindo recursos como o Pesquisa Cognitiva do Azure. Se você não estiver familiarizado com Azure Monitor, Reserve um tempo para revisar os artigos relacionados aos recursos. Além dos tutoriais, o artigo a seguir é um bom ponto de partida.

> [!div class="nextstepaction"]
> [Monitorando recursos do Azure com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
