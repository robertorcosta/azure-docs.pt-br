---
title: Solução Azure SQL Analytics no Azure Monitor | Microsoft Docs
description: Solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados SQL do Azure
ms.subservice: logs
ms.topic: conceptual
author: danimir
ms.author: danil
ms.date: 02/21/2020
ms.reviewer: carlrab
ms.openlocfilehash: 921a05c4dc6c1d5cfa663ac71b469573b8f1925b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275458"
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview"></a>Monitorar o Banco de Dados SQL do Azure usando a Análise de SQL do Azure (Versão Prévia)

![Símbolo da Análise de SQL do Azure](./media/azure-sql/azure-sql-symbol.png)

O Azure SQL Analytics é uma solução avançada de monitoramento em nuvem para monitorar o desempenho de todos os seus bancos de dados SQL do Azure em escala e em várias assinaturas em uma única exibição. O Azure SQL Analytics coleta e visualiza as principais métricas de desempenho com inteligência incorporada para solução de problemas de desempenho.

Usando essas métricas coletadas, você pode criar regras e alertas personalizados de monitoramento. O Azure SQL Analytics ajuda você a identificar problemas em cada camada da pilha de aplicativos. Ele usa métricas do Azure Diagnostic juntamente com as visualizações do Azure Monitor para apresentar dados sobre todos os seus bancos de dados SQL do Azure em um único espaço de trabalho do Log Analytics. O Azure Monitor ajuda você a coletar, correlacionar e visualizar dados estruturados e não estruturados.

Para uma visão geral prática sobre o uso da solução da Análise de SQL do Azure e para cenários de uso típicos, assista o vídeo inserido:

>[!VIDEO https://www.youtube.com/embed/j-NDkN4GIzg]
>

## <a name="connected-sources"></a>Fontes conectadas

O Azure SQL Analytics é uma solução de monitoramento somente em nuvem que suporta o streaming de telemetria de diagnósticos para todos os seus bancos de dados Azure SQL. Como o Azure SQL Analytics não usa agentes para se conectar ao Azure Monitor, ele não suporta o monitoramento do SQL Server hospedado no local ou em máquinas virtuais.

| Fonte Conectada | Com suporte | Descrição |
| --- | --- | --- |
| [Configurações de diagnóstico](../platform/diagnostic-settings.md) | **Sim** | Os dados métricos e de log do Azure são enviados diretamente ao Azure Monitor Logs pelo Azure. |
| [Conta de armazenamento do Azure](../platform/collect-azure-metrics-logs.md) | Não | O Azure Monitor não lê os dados de uma conta de armazenamento. |
| [Agentes do Windows](../platform/agent-windows.md) | Não | Os agentes direct windows não são usados pelo Azure SQL Analytics. |
| [Agentes Linux](../learn/quick-collect-linux-computer.md) | Não | Os agentes Linux diretos não são usados pelo Azure SQL Analytics. |
| [Grupo de gerenciamento do System Center Operations Manager](../platform/om-agents.md) | Não | Uma conexão direta do agente do Gerente de Operações com o Monitor Do Azure não é usada pelo Azure SQL Analytics. |

## <a name="azure-sql-analytics-options"></a>Opções do Azure SQL Analytics

A tabela abaixo descreve opções suportadas para duas versões do painel Azure SQL Analytics, uma para bancos de dados únicos e agrupados e pools elásticos e outra para instâncias gerenciadas e bancos de dados de instâncias.

| Opção Azure SQL Analytics | Descrição | Banco de dados único e agrupado e suporte a piscinas elásticas | Instâncias gerenciadas e suporte ao banco de dados de instâncias |
| --- | ------- | ----- | ----- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. | Sim | Sim |
| Insights | Fornece uma busca detalhada hierárquica no Intelligent Insights sobre o desempenho. | Sim | Sim |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. | Sim | Sim |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. | Sim | Não |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. | Sim | Não |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |Sim | Não |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. | Sim | Sim |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. | Sim | Sim |

## <a name="configuration"></a>Configuração

Use o processo descrito no [Add Azure Monitor solutions da Galeria de Soluções](../../azure-monitor/insights/solutions.md) para adicionar o Azure SQL Analytics (Preview) ao seu espaço de trabalho log analytics.

### <a name="configure-azure-sql-databases-to-stream-diagnostics-telemetry"></a>Configure bancos de dados Azure SQL para transmitir telemetria de diagnóstico

Depois de criar a solução Azure SQL Analytics em seu espaço de trabalho, você precisa **configurar cada** recurso que deseja monitorar para transmitir sua telemetria de diagnóstico para o Azure SQL Analytics. Siga as instruções detalhadas nesta página:

- Habilite o Diagnóstico do Azure para o Banco de Dados SQL do Azure para [telemetria de diagnóstico de fluxo para a Análise de SQL do Azure](../../sql-database/sql-database-metrics-diag-logging.md).

A página acima também fornece instruções sobre como habilitar o suporte para monitorar várias assinaturas do Azure em um único workspace da Análise de SQL do Azure como um único painel de controle.

## <a name="using-azure-sql-analytics"></a>Usando o Azure SQL Analytics

Quando você adiciona o Azure SQL Analytics ao seu espaço de trabalho, o azulejo Azure SQL Analytics é adicionado ao seu espaço de trabalho e ele aparece em Visão Geral. Selecione Exibir link Resumo para carregar o conteúdo do bloco.

![Bloco de resumo do Azure SQL Analytics](./media/azure-sql/azure-sql-sol-tile-01.png)

Uma vez carregado, o azulejo mostra o número de bancos de dados únicos e agrupados, piscinas elásticas, instâncias gerenciadas e bancos de dados de instâncias gerenciadas dos quais o Azure SQL Analytics está recebendo telemetria de diagnóstico.

![Bloco de Análise do SQL Azure](./media/azure-sql/azure-sql-sol-tile-02.png)

O Azure SQL Analytics fornece duas visualizações separadas - uma para monitorar bancos de dados únicos e bancos de dados agrupados e pools elásticos, e outra para monitorar instâncias gerenciadas e bancos de dados de instâncias.

Para ver o painel de monitoramento do Azure SQL Analytics para bancos de dados únicos e agrupados e piscinas elásticas, clique na parte superior do azulejo. Para visualizar o painel de monitoramento do Azure SQL Analytics para obter instâncias gerenciadas e bancos de dados de instâncias, clique na parte inferior do bloco.

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que diferentes perspectivas funcionem, você deve habilitar métricas ou logs adequados em seus recursos SQL para serem transmitidos para o espaço de trabalho do Log Analytics.

Se algumas métricas ou logs não forem transmitidos para o Azure Monitor, as telhas do Azure SQL Analytics não serão preenchidas com informações de monitoramento.

### <a name="single-and-pooled-databases-and-elastic-pools-view"></a>Bancos de dados únicos e agrupados e pools elásticos visualizam

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics.png)

Cada perspectiva nesta visão fornece resumos nos níveis de assinatura, servidor, pool elástico e banco de dados. Além disso, cada perspectiva mostrada é específica ao relatório à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista dá continuidade ao drill down.

### <a name="managed-instance-and-instances-databases-view"></a>Bancos de dados de instâncias gerenciadas e instâncias visualizam

Depois que o bloco da Análise de SQL do Azure para o banco de dados é selecionado, o painel de monitoramento será mostrado.

![Visão geral da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-overview-mi.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica. Quando a perspectiva for selecionada, o relatório de dril down será aberto.

Selecionando a exibição de instância gerenciada, mostra detalhes sobre a utilização da instância gerenciada, bancos de dados que ele contém e telemetria nas consultas executadas em toda a instância.

![Tempos limite da Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-metrics-mi.png)

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

O [Intelligent Insights](../../sql-database/sql-database-intelligent-insights.md) do Banco de dados SQL do Azure permite que você saiba o que está acontecendo com o desempenho do Banco de Dados SQL do Microsoft Azure. Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights.

![Insights de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pools-and-database-reports"></a>Piscinas elásticas e relatórios de banco de dados

Tanto os pools elásticos quanto os bancos de dados possuem seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no tempo especificado.

![Banco de dados de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-database.png)

![Pool elástico SQL do Azure](./media/azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Através da duração da consulta e a consulta aguarda perspectivas, você pode correlacionar o desempenho de qualquer consulta através do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/azure-sql/azure-sql-sol-queries.png)

## <a name="permissions"></a>Permissões

Para usar a Análise de SQL do Azure, os usuários precisam receber uma permissão mínima da função de leitor no Azure. No entanto, essa função não permite que os usuários vejam o texto de consulta ou realizem quaisquer ações de ajuste automático. Funções mais permissivas no Azure que permitem usar o Azure SQL Analytics na medida do possível são Proprietário, Contribuinte, Contribuinte SQL DB ou Contribuinte do Servidor SQL. Também convém considerar a criação de uma função personalizada no portal, com permissões específicas necessárias apenas para usar a Análise de SQL do Azure e sem acesso ao gerenciamento de outros recursos.

### <a name="creating-a-custom-role-in-portal"></a>Criar uma função personalizada no portal

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Reconhecendo que algumas organizações impõem controles de permissão estrito no Azure, localize o seguinte script PowerShell, possibilitando a criação de uma função personalizada "Operador de monitoramento da Análise de SQL" no portal do Azure, com, pelo menos, permissões de leitura e gravação necessárias para usar a Análise de SQL do Azure até a extensão máxima.

Substitua "{SubscriptionId}" no script abaixo por sua ID da assinatura do Azure e execute o script conectado como uma função de Proprietário ou Colaborador no Azure.

   ```powershell
    Connect-AzAccount
    Select-AzSubscription {SubscriptionId}
    $role = Get-AzRoleDefinition -Name Reader
    $role.Name = "SQL Analytics Monitoring Operator"
    $role.Description = "Lets you monitor database performance with Azure SQL Analytics as a reader. Does not allow change of resources."
    $role.IsCustom = $true
    $role.Actions.Add("Microsoft.SQL/servers/databases/read");
    $role.Actions.Add("Microsoft.SQL/servers/databases/topQueries/queryText/*");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/read");
    $role.Actions.Add("Microsoft.Sql/servers/databases/automaticTuning/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/write");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/read");
    $role.Actions.Add("Microsoft.Sql/servers/advisors/recommendedActions/write");
    $role.Actions.Add("Microsoft.Resources/deployments/write");
    $role.AssignableScopes = "/subscriptions/{SubscriptionId}"
    New-AzRoleDefinition $role
   ```

Depois de criar a nova função, atribua essa função para cada usuário ao qual você precisa conceder permissões personalizadas para usar a Análise de SQL do Azure.

## <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

A análise de dados na Análise de SQL do Azure baseia-se na [linguagem de programação do Log Analytics](../log-query/get-started-queries.md) para suas consultas personalizadas e relatórios. Leia a descrição dos dados disponíveis coletados do recurso de banco de dados para a consulta personalizada [métricas e logs disponíveis](../../sql-database/sql-database-metrics-diag-logging.md#metrics-and-logs-available).

O alerta automatizado no Azure SQL Analytics é baseado na gravação de uma consulta do Log Analytics que dispara um alerta sobre uma condição atendida. Veja abaixo vários exemplos sobre as consultas do Log Analytics sobre as quais o alerta pode ser configurado no Azure SQL Analytics.

### <a name="creating-alerts-for-azure-sql-database"></a>Criando alertas para o Banco de Dados SQL do Azure

Você pode [criar alertas](../platform/alerts-metric.md) facilmente com os dados provenientes de recursos de Banco de Dados SQL do Azure. Aqui estão algumas consultas de [consultas de logs](../log-query/log-query-overview.md) que podem ser usadas com um alerta do log:

#### <a name="high-cpu-on-azure-sql-database"></a>CPU alta no Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/DATABASES/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A pré-exigência de configurar esse alerta é que os bancos de dados monitorados transmitam métricas básicas para o Azure SQL Analytics.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="high-cpu-on-azure-sql-database-elastic-pools"></a>CPU alta em pools elásticos do Banco de Dados SQL do Azure

```
AzureMetrics
| where ResourceProvider=="MICROSOFT.SQL"
| where ResourceId contains "/ELASTICPOOLS/"
| where MetricName=="cpu_percent"
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

> [!NOTE]
>
> - A pré-exigência de configurar esse alerta é que os bancos de dados monitorados transmitam métricas básicas para o Azure SQL Analytics.
> - Substitua o valor cpu_percent de MetricName por dtu_consumption_percent para obter os resultados de DTU alta em vez disso.

#### <a name="azure-sql-database-storage-in-average-above-95-in-the-last-1-hr"></a>Banco de Dados SQL do Azure em média acima de 95% na última 1 hora

```
let time_range = 1h;
let storage_threshold = 95;
AzureMetrics
| where ResourceId contains "/DATABASES/"
| where MetricName == "storage_percent"
| summarize max_storage = max(Average) by ResourceId, bin(TimeGenerated, time_range)
| where max_storage > storage_threshold
| distinct ResourceId
```

> [!NOTE]
>
> - A pré-exigência de configurar esse alerta é que os bancos de dados monitorados transmitam métricas básicas para o Azure SQL Analytics.
> - Essa consulta exige que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (resultados > 0) da consulta, indicando que a condição existe em alguns bancos de dados. A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.
> - A saída é uma lista de recursos de banco de dados que estão acima de storage_threshold dentro do time_range definido.

#### <a name="alert-on-intelligent-insights"></a>Alerta no Intelligent Insights

```
let alert_run_interval = 1h;
let insights_string = "hitting its CPU limits";
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| where TimeGenerated > ago(alert_run_interval)
| where rootCauseAnalysis_s contains insights_string
| distinct ResourceId
```

> [!NOTE]
>
> - A pré-exigência de configuração desse alerta é que os bancos de dados monitorados transmitam o registro de diagnósticos do SQLInsights para o Azure SQL Analytics.
> - Essa consulta exige que uma regra de alerta seja configurada para ser executada com a mesma frequência que alert_run_interval a fim de evitar resultados duplicados. A regra deve ser configurada para disparar o alerta quando houver resultados (resultados > 0) da consulta.
> - Personalize o alert_run_interval para especificar o intervalo de tempo para verificar se a condição ocorreu em bancos de dados configurados para transmitir o log do SQLInsights para o Azure SQL Analytics.
> - Personalize o insights_string para capturar a saída do texto da análise de causa de raiz do Insights. Este é o mesmo texto exibido na UI do Azure SQL Analytics que você pode usar a partir dos insights existentes. Como alternativa, você pode usar a consulta a seguir para ver o texto de todos os Insights gerados em sua assinatura. Use a saída da consulta para coletar as cadeias de caracteres distintas para configurar alertas no Insights.

```
AzureDiagnostics
| where Category == "SQLInsights" and status_s == "Active"
| distinct rootCauseAnalysis_s
```

### <a name="creating-alerts-for-managed-instances"></a>Criação de alertas para instâncias gerenciadas

#### <a name="managed-instance-storage-is-above-90"></a>O armazenamento de instância gerenciada está acima de 90%

```
let storage_percentage_threshold = 90;
AzureDiagnostics
| where Category =="ResourceUsageStats"
| summarize (TimeGenerated, calculated_storage_percentage) = arg_max(TimeGenerated, todouble(storage_space_used_mb_s) *100 / todouble (reserved_storage_mb_s))
   by ResourceId
| where calculated_storage_percentage > storage_percentage_threshold
```

> [!NOTE]
>
> - A pré-exigência de configurar esse alerta é que a instância monitorada tenha o fluxo de registro ResourceUsageStats ativado para o Azure SQL Analytics.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerenciada. A saída é o consumo percentual de armazenamento na instância gerenciada.

#### <a name="managed-instance-cpu-average-consumption-is-above-95-in-the-last-1-hr"></a>O consumo médio da CPU de instância gerenciada está acima de 95% nos últimos 1 h

```
let cpu_percentage_threshold = 95;
let time_threshold = ago(1h);
AzureDiagnostics
| where Category == "ResourceUsageStats" and TimeGenerated > time_threshold
| summarize avg_cpu = max(todouble(avg_cpu_percent_s)) by ResourceId
| where avg_cpu > cpu_percentage_threshold
```

> [!NOTE]
>
> - A necessidade prévia de configurar esse alerta é que a instância gerenciada monitorada tenha o fluxo de registro ResourceUsageStats ativado para o Azure SQL Analytics.
> - Essa consulta requer que uma regra de alerta seja configurada para disparar um alerta quando houver resultados (> 0 resultados) da consulta, denotando que a condição existe na instância gerenciada. A saída é o consumo percentual de utilização média da CPU em período definido na instância gerenciada.

### <a name="pricing"></a>Preços

Embora o Azure SQL Analytics seja gratuito, o consumo de telemetria de diagnóstico acima das unidades livres de ingestão de dados alocadas a cada mês se aplica, veja [os preços do Log Analytics](https://azure.microsoft.com/pricing/details/monitor). As unidades gratuitas de ingestão de dados fornecidas habilitam o monitoramento gratuito de vários bancos de dados por mês. Bancos de dados mais ativos com cargas de trabalho mais pesadas ingerem mais dados versus bancos de dados ociosos. Você pode monitorar facilmente o consumo de ingestão de dados no Azure SQL Analytics selecionando o OMS Workspace no menu de navegação do Azure SQL Analytics e, em seguida, selecionando Uso e Custos Estimados.

## <a name="next-steps"></a>Próximas etapas

- Use [consultas de log](../log-query/log-query-overview.md) no Azure Monitor para visualizar dados detalhados do Azure SQL.
- [Criar seus próprios painéis](../learn/tutorial-logs-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](../platform/alerts-overview.md) quando ocorrerem eventos específicos do Azure SQL.
