---
title: Estrutura dos Logs de Monitor do Azure | Microsoft Docs
description: Você precisa de uma consulta de log para recuperar dados de log do Azure Monitor.  Este artigo descreve como novas consultas de log são utilizadas no Azure Monitor e fornece conceitos necessários para serem compreendidos antes de criar uma.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662069"
---
# <a name="structure-of-azure-monitor-logs"></a>Estrutura dos logs do Monitor do Azure
A capacidade de obter insights rápidos sobre seus dados usando uma [consulta de log](log-query-overview.md) é um recurso poderoso do Azure Monitor. Para criar consultas eficientes e úteis, você deve entender alguns conceitos básicos, como onde os dados que você deseja estão localizados e como eles são estruturados. Este artigo fornece os conceitos básicos que você precisa para começar.

## <a name="overview"></a>Visão geral
Os dados no Azure Monitor Logs são armazenados em um espaço de trabalho do Log Analytics ou em um aplicativo Application Insights. Ambos são alimentados pelo [Azure Data Explorer,](/azure/data-explorer/) o que significa que eles aproveitam seu poderoso mecanismo de dados e linguagem de consulta.

Os dados em espaços de trabalho e aplicativos são organizados em tabelas, cada uma das quais armazena diferentes tipos de dados e tem seu próprio conjunto único de propriedades. A maioria das fontes de [dados](../platform/data-sources.md) gravará em suas próprias tabelas em um espaço de trabalho do Log Analytics, enquanto o Application Insights gravará para um conjunto predefinido de tabelas em um aplicativo Application Insights. As consultas de log são muito flexíveis, permitindo combinar facilmente dados de várias tabelas e até mesmo usar uma consulta entre recursos para combinar dados de tabelas em vários espaços de trabalho ou para escrever consultas que combinam espaço de trabalho e dados de aplicativos.

A imagem a seguir mostra exemplos de fontes de dados que escrevem para diferentes tabelas que são usadas em consultas de amostra.

![Tabelas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
Todos os dados coletados pelo Azure Monitor Logs, exceto os Insights do Aplicativo, são armazenados em um [espaço de trabalho do Log Analytics](../platform/manage-access.md). Você pode criar um ou mais espaços de trabalho, dependendo de seus requisitos específicos. [Fontes de dados,](../platform/data-sources.md) como registros de atividades e registros de recursos dos recursos do Azure, agentes em máquinas virtuais e dados de insights e soluções de monitoramento gravarão dados em um ou mais espaços de trabalho que você configurar como parte de seu onboarding. Outros serviços como [o Azure Security Center](/azure/security-center/) e o [Azure Sentinel](/azure/sentinel/) também usam um espaço de trabalho do Log Analytics para armazenar seus dados para que possam ser analisados usando consultas de log, juntamente com dados de monitoramento de outras fontes.

Diferentes tipos de dados são armazenados em diferentes tabelas no espaço de trabalho, e cada tabela tem um conjunto único de propriedades. Um conjunto padrão de tabelas é adicionado a um espaço de trabalho quando ele é criado, e novas tabelas são adicionadas para diferentes fontes de dados, soluções e serviços à medida que são a bordo. Você também pode criar tabelas personalizadas usando a [API de coleta de dados](../platform/data-collector-api.md).

Você pode navegar pelas tabelas em um espaço de trabalho e seu esquema na guia **Esquema** a no Log Analytics para o espaço de trabalho.

![Esquema do espaço de trabalho](media/scope/workspace-schema.png)

Use a consulta a seguir para listar as tabelas no espaço de trabalho e o número de registros coletados em cada um ao longo do dia anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte a documentação de cada fonte de dados para obter detalhes das tabelas que eles criam. Exemplos incluem artigos para [fontes de dados de agentes,](../platform/agent-data-sources.md)registros de [recursos](../platform/diagnostic-logs-schema.md)e [soluções de monitoramento.](../insights/solutions-inventory.md)

### <a name="workspace-permissions"></a>Permissões de espaço de trabalho
Consulte [Projetar uma implantação do Azure Monitor Logs](../platform/design-logs-deployment.md) para entender a estratégia de controle de acesso e recomendações para fornecer acesso a dados em um espaço de trabalho. Além de conceder acesso ao próprio espaço de trabalho, você pode limitar o acesso a tabelas individuais usando [o Nível de Tabela RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicativo Application Insights
Quando você cria um aplicativo no Application Insights, um aplicativo correspondente é criado automaticamente no Azure Monitor Logs. Nenhuma configuração é necessária para coletar dados, e o aplicativo gravará automaticamente dados de monitoramento, como visualizações de página, solicitações e exceções.

Ao contrário de um espaço de trabalho do Log Analytics, um aplicativo Application Insights tem um conjunto fixo de tabelas. Você não pode configurar outras fontes de dados para gravar no aplicativo para que nenhuma tabela adicional possa ser criada. 

| Tabela | Descrição | 
|:---|:---|
| disponibilidadeResultados | Dados resumidos de testes de disponibilidade. |
| navegadorTimings      | Dados sobre o desempenho do cliente, como o tempo demorado para processar os dados de entrada. |
| customEvents        | Eventos personalizados criados pelo seu aplicativo. |
| customMetrics       | Métricas personalizadas criadas pelo seu aplicativo. |
| dependências        | Chamadas do aplicativo para componentes externos. |
| exceções          | Exceções lançadas pelo tempo de execução do aplicativo. |
| Pageviews           | Dados sobre cada site visualizam com informações do navegador. |
| performanceCounters | Medições de desempenho dos recursos de computação que suportam a aplicação. |
| solicitações            | Detalhes de cada solicitação de solicitação.  |
| traces              | Resultados do rastreamento distribuído. |

Você pode visualizar o esquema de cada tabela na guia **Esquema** no Log Analytics para o aplicativo.

![Esquema de aplicação](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propriedades padrões
Embora cada tabela no Azure Monitor Logs tenha seu próprio esquema, existem propriedades padrão compartilhadas por todas as tabelas. Consulte [propriedades padrão no Azure Monitor Logs](../platform/log-standard-properties.md) para obter detalhes de cada um.

| Espaço de trabalho do Log Analytics | Aplicativo Application Insights | Descrição |
|:---|:---|:---|
| TimeGenerated |  timestamp  | Data e hora em que o disco foi criado. |
| Type          | itemType   | O nome da tabela do que o registro foi recuperado. |
| _ResourceId   |            | Identificador exclusivo para o recurso com o qual o registro está associado. |
| _IsBillable   |            | Especifica se os dados ingeridos são faturados. |
| _BilledSize   |            | Especifica o tamanho em bytes de dados que serão cobrados. |

## <a name="next-steps"></a>Próximas etapas
- Aprenda a usar [o Log Analytics para criar e editar pesquisas de log](../log-query/portals.md).
- Confira um [tutorial sobre como escrever consultas](../log-query/get-started-queries.md) utilizando a nova linguagem de consulta.
