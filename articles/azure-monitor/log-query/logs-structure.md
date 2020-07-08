---
title: Estrutura dos logs do Azure Monitor | Microsoft Docs
description: Você precisa de uma consulta de log para recuperar dados de log do Azure Monitor.  Este artigo descreve como novas consultas de log são utilizadas no Azure Monitor e fornece conceitos necessários para serem compreendidos antes de criar uma.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 58724656dd407f09687b57d0ab034f3a1f808b76
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196281"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura de logs do Azure Monitor
A capacidade de obter informações rapidamente sobre seus dados usando uma [consulta de log](log-query-overview.md) é um recurso poderoso do Azure Monitor. Para criar consultas eficientes e úteis, você deve entender alguns conceitos básicos, como onde estão localizados os dados desejados e como eles são estruturados. Este artigo fornece os conceitos básicos de que você precisa para começar.

## <a name="overview"></a>Visão geral
Os dados dos logs de Azure Monitor são armazenados em um workspace do Log Analytics ou em um aplicativo do Application Insights. Ambos são da plataforma [Azure Data Explorer](/azure/data-explorer/) o que significa que eles aproveitam seus poderosos mecanismos de dados e linguagem de consulta.

> [!IMPORTANT]
> Se você estiver usando um [recurso do Application Insights baseado em workspace](../app/create-workspace-resource.md), a telemetria é armazenada no workspace do Log Analytics com todos os outros dados do log. As tabelas foram renomeadas e reestruturadas, mas têm as mesmas informações que as tabelas no aplicativo do Application Insights.

Os dados em workspaces e aplicativos são organizados em tabelas, cada um dos quais armazena diferentes tipos de dados e tem seu próprio conjunto exclusivo de propriedades. A maioria das [fontes de dados](../platform/data-sources.md) será gravada em suas próprias tabelas em um workspace do Log Analytics, enquanto o Application Insights gravará em um conjunto predefinido de tabelas em um aplicativo do Application Insights. As consultas de log são muito flexíveis, permitindo que você combine facilmente os dados de várias tabelas e até mesmo use uma consulta entre recursos para combinar dados de tabelas em vários workspaces ou para escrever consultas que combinam dados de workspace e de aplicativo.

A imagem a seguir mostra exemplos de fontes de dados que gravam em tabelas diferentes que são usadas em consultas de exemplo.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
Todos os dados coletados pelo Azure Monitor são armazenados em um [workspace do Log Analytics](../platform/manage-access.md), exceto para o Application Insights. Você pode criar um ou mais workspaces dependendo de seus requisitos específicos. [Fontes de dados](../platform/data-sources.md) como logs de atividades e logs de recursos do Azure, agentes em máquinas virtuais e dados de insights e soluções de monitoramento gravarão dados em um ou mais workspaces que você configurar como parte de sua integração. Outros serviços, como a [Central de Segurança do Azure](/azure/security-center/) e o [Azure Sentinel](/azure/sentinel/) também usam um workspace do Log Analytics para armazenar seus dados para que eles possam ser analisados usando consultas de log, juntamente com dados de monitoramento de outras fontes.

Tipos diferentes de dados são armazenados em tabelas diferentes no workspace e cada tabela tem um conjunto exclusivo de propriedades. Um conjunto padrão de tabelas é adicionado a um workspace quando ele é criado, e novas tabelas são adicionadas para diferentes fontes de dados, soluções e serviços à medida que são estes são integrados. Você também pode criar tabelas personalizadas usando a [API do coletor de dados](../platform/data-collector-api.md).

Você pode procurar as tabelas em um workspace e seu esquema na guia **Esquema** em Log Analytics para o workspace.

![Esquema do workspace](media/scope/workspace-schema.png)

Use a consulta a seguir para listar as tabelas no workspace e o número de registros coletados em cada uma no dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação para cada fonte de dados para obter detalhes das tabelas que elas criam. Os exemplos incluem artigos para [fontes de dados do agente](../platform/agent-data-sources.md), [logs de recursos](../platform/diagnostic-logs-schema.md) e [soluções de monitoramento](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Permissões de workspace
Consulte [Criar uma implantação de logs do Azure Monitor](../platform/design-logs-deployment.md) para entender a estratégia de controle de acesso e as recomendações para fornecer acesso aos dados em um workspace. Além de conceder acesso ao próprio workspace, você pode limitar o acesso a tabelas individuais usando [RBAC de nível de tabela](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicativo do Application Insights

> [!IMPORTANT]
> Se você estiver usando um [recurso do Application Insights baseado em workspace](../app/create-workspace-resource.md), a telemetria é armazenada no workspace do Log Analytics com todos os outros dados do log. As tabelas foram renomeadas e reestruturadas, mas têm as mesmas informações que as tabelas no recurso clássico de Application Insights.

Quando você cria um aplicativo no Application Insights, um aplicativo correspondente é criado automaticamente nos logs do Azure Monitor. Nenhuma configuração é necessária para coletar dados e o aplicativo irá gravar dados de monitoramento automaticamente, como exibições de página, solicitações e exceções.

Ao contrário de um workspace do Log Analytics, um aplicativo do Application Insights tem um conjunto fixo de tabelas. Você não pode configurar outras fontes de dados para gravar no aplicativo, portanto, nenhuma tabela adicional pode ser criada. 

| Tabela | Descrição | 
|:---|:---|
| availabilityResults | Dados de resumo dos testes de disponibilidade. |
| browserTimings      | Dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada. |
| customEvents        | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics       | Métricas personalizadas criadas pelo seu aplicativo. |
| dependencies        | Chamadas do aplicativo para outros componentes (incluindo componentes externos) registrados por meio de TrackDependency() – por exemplo, chamadas para API REST, banco de dados ou sistema de arquivos. |
| exceptions          | As exceções geradas pelo tempo de execução do aplicativo capturam as exceções do lado do servidor e do lado do cliente (navegadores).|
| pageViews           | Dados sobre cada exibição do site com informações do navegador. |
| performanceCounters | Medições de desempenho dos recursos de computação que dão suporte ao aplicativo, por exemplo, contadores de desempenho do Windows. |
| solicitações            | Solicitações recebidas pelo seu aplicativo. Por exemplo, um registro de solicitação separado é registrado para cada solicitação HTTP que o aplicativo Web recebe.  |
| traces              | Logs detalhados (rastreamentos) emitidos por meio de estruturas de log/código do aplicativo gravados por meio de TrackTrace(). |

Você pode exibir o esquema para cada tabela na guia **Esquema** no Log Analytics para o aplicativo.

![Esquema do aplicativo](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrões
Embora cada tabela nos logs do Azure Monitor tenha seu próprio esquema, há propriedades padrão compartilhadas por todas as tabelas. Consulte [Propriedades padrão nos logs do Azure Monitor](../platform/log-standard-properties.md) para saber detalhes de cada uma delas.

| Espaço de trabalho do Log Analytics | Aplicativo do Application Insights | Descrição |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e hora de criação do registro. |
| Type          | itemType   | Nome da tabela da qual o registro foi recuperado. |
| _ResourceId   |            | Identificador exclusivo do recurso associado ao registro. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturáveis. |
| _BilledSize   |            | Especifica o tamanho em bytes de dados que serão cobrados. |

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como usar o [Log Analytics para criar e editar pesquisas de log](../log-query/portals.md).
- Confira um [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) utilizando a nova linguagem de consulta.