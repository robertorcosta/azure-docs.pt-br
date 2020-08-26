---
title: Consultas de auditoria em consultas de log de Azure Monitor
description: Detalhes de logs de auditoria de consulta de log que fornecem telemetria sobre consultas de log executadas no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/25/2020
ms.openlocfilehash: cb38dcba2f61a432decb56164b816688ad3192d8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893661"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Auditar consultas em logs de Azure Monitor (versão prévia)
Logs de auditoria de consulta de log fornecem telemetria sobre as consultas de log executadas no Azure Monitor. Isso inclui informações como, por exemplo, quando uma consulta foi executada, quem a executou, qual ferramenta foi usada, o texto da consulta e as estatísticas de desempenho que descrevem a execução da consulta.

## <a name="current-limitations"></a>Limitações atuais
As seguintes limitações se aplicam durante a visualização pública:

- Somente consultas centradas no espaço de trabalho serão registradas em log. As consultas são executadas no modo centrado em recursos ou executadas em um Application Insights não configurado, pois o espaço de trabalho não será registrado em log.


## <a name="configure-query-auditing"></a>Configurar a auditoria de consulta
A auditoria de consulta é habilitada com uma [configuração de diagnóstico](../platform/diagnostic-settings.md) no espaço de trabalho log Analytics. Isso permite que você envie dados de auditoria para o espaço de trabalho atual ou qualquer outro espaço de trabalho em sua assinatura, para os hubs de eventos do Azure para envio fora do Azure ou para o armazenamento do Azure para arquivamento. 

### <a name="azure-portal"></a>Portal do Azure
Acesse a configuração de diagnóstico para um espaço de trabalho Log Analytics no portal do Azure em qualquer um dos seguintes locais:

- No menu **Azure monitor** , selecione **configurações de diagnóstico**e, em seguida, localize e selecione o espaço de trabalho.

    [![Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png) de configurações de diagnóstico ](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- No menu **log Analytics espaços de trabalho** , selecione o espaço de trabalho e, em seguida, selecione **configurações de diagnóstico**.

    [![Configurações de diagnóstico Log Analytics espaço de trabalho ](media/log-query-audit/diagnostic-setting-workspace.png)](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Você pode obter um exemplo de modelo do Resource Manager na [configuração de diagnóstico para log Analytics espaço de trabalho](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Dados de auditoria
Um registro de auditoria é criado cada vez que uma consulta é executada. Se você enviar os dados para um espaço de trabalho Log Analytics, ele será armazenado em uma tabela chamada *LAQueryLogs*. A tabela a seguir descreve as propriedades em cada registro dos dados de auditoria.

| Campo | Descrição |
|:---|:---|
| TimeGenerated         | Hora UTC quando a consulta foi enviada. |
| CorrelationId         | ID exclusiva para identificar a consulta. Pode ser usado em cenários de solução de problemas ao entrar em contato com a Microsoft para obter assistência. |
| AADObjectId           | ID de Azure Active Directory da conta de usuário que iniciou a consulta.  |
| AADTenantId           | ID do locatário da conta de usuário que iniciou a consulta.  |
| AADEmail              | Email do locatário da conta de usuário que iniciou a consulta.  |
| AADClientId           | ID e nome resolvido do aplicativo usado para iniciar a consulta. |
| RequestClientApp      | Nome resolvido do aplicativo usado para iniciar a consulta. |
| QueryTimeRangeStart   | Início do intervalo de tempo selecionado para a consulta. Isso pode não ser preenchido em determinados cenários, como quando a consulta é iniciada a partir de Log Analytics, e o intervalo de tempo é especificado dentro da consulta em vez do seletor de hora. |
| QueryTimeRangeEnd     | Fim do intervalo de tempo selecionado para a consulta. Isso pode não ser preenchido em determinados cenários, como quando a consulta é iniciada a partir de Log Analytics, e o intervalo de tempo é especificado dentro da consulta em vez do seletor de hora.  |
| QueryText             | Texto da consulta que foi executada. |
| RequestTarget         | A URL da API foi usada para enviar a consulta.  |
| RequestContext        | Lista de recursos nos quais a consulta foi solicitada a execução. Contém até três matrizes de cadeias de caracteres: espaços de trabalho, aplicativos e recursos. As consultas de assinatura ou grupo de recursos direcionadas serão mostradas como *recursos*. Inclui o destino implícito por RequestTarget. |
| RequestContextFilters | Conjunto de filtros especificado como parte da invocação de consulta. Inclui até três matrizes de cadeias de caracteres possíveis:<br>-ResourceTypes-tipo de recurso para limitar o escopo da consulta<br>-Espaços de trabalho-lista de espaços de trabalho para limitar a consulta a<br>-WorkspaceRegions-lista de regiões de espaço de trabalho para limitar a consulta |
| ResponseCode          | Código de resposta HTTP retornado quando a consulta foi enviada. |
| ResponseDurationMs    | Hora da resposta a ser retornada.  |
| StatsCPUTimeMs       | Tempo total de computação usado para computação, análise e busca de dados. Populado apenas se a consulta retornar com o código de status 200. |
| StatsDataProcessedKB | Quantidade de dados que foi acessada para processar a consulta. Influenciado pelo tamanho da tabela de destino, pelo período de tempo usado, pelos filtros aplicados e pelo número de colunas referenciadas. Populado apenas se a consulta retornar com o código de status 200. |
| StatsDataProcessedStart | Hora dos dados mais antigos que foram acessados para processar a consulta. Influenciado pelo intervalo de tempo explícito da consulta e filtros aplicados. Isso pode ser maior do que o intervalo de tempo explícito devido ao particionamento de dados. Populado apenas se a consulta retornar com o código de status 200. |
| StatsDataProcessedEnd  |Hora dos dados mais recentes que foram acessados para processar a consulta. Influenciado pelo intervalo de tempo explícito da consulta e filtros aplicados. Isso pode ser maior do que o intervalo de tempo explícito devido ao particionamento de dados. Populado apenas se a consulta retornar com o código de status 200. |
| StatsWorkspaceCount | Número de espaços de trabalho acessados pela consulta. Populado apenas se a consulta retornar com o código de status 200. |
| StatsRegionCount | Número de regiões acessadas pela consulta. Populado apenas se a consulta retornar com o código de status 200. |



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [as configurações de diagnóstico](../platform/diagnostic-settings.md).
- Saiba mais sobre como [otimizar consultas de log](query-optimization.md).