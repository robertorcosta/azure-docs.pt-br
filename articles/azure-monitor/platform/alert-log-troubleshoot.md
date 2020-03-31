---
title: Alertas de log de solução de problemas no Azure Monitor | Microsoft Docs
description: Problemas, erros e resoluções comuns para regras de alerta de log no Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249029"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertas de log de solução de problemas no Azure Monitor  

Este artigo mostra como resolver problemas comuns que podem acontecer quando você está configurando alertas de log no Azure Monitor. Ele também fornece soluções para problemas comuns com funcionalidade ou configuração de alertas de log.

Os *alertas de log* de termo descrevem regras que são acionadas com base em uma consulta de log em um [espaço de trabalho do Azure Log Analytics](../learn/tutorial-viewdata.md) ou no [Azure Application Insights](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidade, terminologia e tipos em [alertas de log no Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera os casos em que o portal Azure mostra uma regra de alerta acionada e uma notificação não é realizada por um grupo de ação associado. Para esses casos, consulte os detalhes em [Criar e gerenciar grupos de ação no portal Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alerta de log não acionado

Aqui estão algumas razões comuns pelas quais o estado para uma regra de alerta de log configurada [no Azure Monitor](../platform/alerts-log.md) não aparece [como *disparado* quando esperado](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para logs

Um alerta de log executa periodicamente sua consulta com base no [Log Analytics](../learn/tutorial-viewdata.md) ou [no Application Insights](../../azure-monitor/app/analytics.md). Como o Azure Monitor processa muitos terabytes de dados de milhares de clientes de fontes variadas em todo o mundo, o serviço é suscetível a atrasos de tempo variados. Para obter mais informações, consulte [o tempo de ingestão de dados nos registros do Monitor do Azure](../platform/data-ingestion-time.md).

Para mitigar atrasos, o sistema espera e tenta a consulta de alerta várias vezes se descobrir que os dados necessários ainda não foram ingeridos. O sistema tem um tempo de espera de aumento exponencial. O alerta de log é acionado somente após a disponibilização dos dados, de modo que o atraso pode ser devido à lenta ingestão de dados de log.

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado

Conforme descrito no artigo sobre [terminologia para alertas de log,](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta retorna apenas registros que foram criados dentro deste intervalo.

O período de tempo restringe os dados buscados para uma consulta de log para evitar abusos, e contorna qualquer comando de tempo (como **há )** usado em uma consulta de log. Por exemplo, se o período de tempo estiver definido como 60 minutos e a consulta for executada às 13h15, somente registros criados entre 12h15 e 13h15 serão usados para a consulta de log. Se a consulta de log usar um comando de tempo como **há (1d)**, a consulta ainda usará apenas dados entre 12:15 PM e 1:15 PM porque o período de tempo está definido para esse intervalo.

Verifique se o período de tempo na configuração corresponde à sua consulta. Para o exemplo mostrado anteriormente, se a consulta de log usar **atrás (1d)** com o marcador verde, o período de tempo deve ser definido como 24 horas ou 1.440 minutos (indicado em vermelho). Essa configuração garante que a consulta seja executada conforme pretendido.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção Suprimir Alertas está definida

Conforme descrito na etapa 8 do artigo sobre a criação de uma regra de alerta de [log no portal Azure, os](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)alertas de log fornecem uma opção **Dessuprimir alertas** para suprimir ações de acionamento e notificação por um período de tempo configurado. Como resultado, você pode pensar que um alerta não disparou. Na verdade, ele disparou, mas foi suprimido.  

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra de alerta com medição métrica está incorreta

*Os alertas de registro* de medição métrica são um subtipo de alertas de log que têm recursos especiais e uma sintaxe de consulta de alerta restrita. Uma regra para um alerta de registro de medição métrica requer que a saída de consulta seja uma série de tempos métrica. Ou seja, a saída é uma tabela com períodos de tempo distintos e igualmente dimensionados, juntamente com valores agregados correspondentes.

Você pode optar por ter variáveis adicionais na tabela ao lado **do AggregatedValue**. Essas variáveis podem ser usadas para classificar a tabela.

Por exemplo, suponha que uma regra para um alerta de registro de medição métrica foi configurada como:

- Consulta de`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Período de tempo de 6 horas
- Limiar de 50
- Lógica de alerta de três violações consecutivas
- **Agregar Ao** ser escolhido como **$table**

Porque o comando inclui **resumir... por** e fornece duas variáveis (**carimbo de tempo** e **$table),** o sistema escolhe **$table** para **Agregado Sobre**. O sistema classifica a tabela de resultados pelo campo **$table,** como mostrado na captura de tela a seguir. Em seguida, ele analisa as várias **instâncias de AgregadosDe valor** para cada tipo de tabela (como **disponibilidadeResultados**) para ver se houve três ou mais violações consecutivas.

![Execução de consulta de medição métrica com múltiplos valores](media/alert-log-troubleshoot/LogMMQuery.png)

Como **Agregado Sobre** é definido em **$table,** os dados são classificados em uma **coluna $table** (indicada em vermelho). Em seguida, agrupamos e procuramos por tipos do campo **Agregado Sobre.**

Por exemplo, para **$table**, os valores para **disponibilidadeOs resultados** serão considerados como um lote/entidade (indicado em laranja). Nesta parcela/entidade de valor, o serviço de alerta verifica três violações consecutivas (indicadas em verde). As brechas disparam um alerta para a disponibilidade de valor da **tabelaResultados**.

Da mesma forma, se três violações consecutivas acontecerem por qualquer outro valor de **$table,** outra notificação de alerta é acionada para a mesma coisa. O serviço de alerta classifica automaticamente os valores em uma parcela/entidade (indicada em laranja) por tempo.

Agora suponha que a regra para o alerta `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`de registro de medição métrica foi modificada e a consulta foi . O resto da configuração permaneceu a mesma de antes, incluindo a lógica de alerta para três violações consecutivas. A opção **Agregado Sobre** neste caso é carimbo de data **e hora** por padrão. Apenas um valor é fornecido na consulta para **resumir ... por** (isto é, **carimbo de tempo**). Como o exemplo anterior, a saída no final da execução seria tão ilustrada quanto a seguir.

   ![Execução de consulta de medição métrica com valor singular](media/alert-log-troubleshoot/LogMMtimestamp.png)

Como **Agregado Sobre** é definido no carimbo de data e **hora,** os dados são classificados na coluna **carimbo de data e hora** (indicado em vermelho). Então agrupamos por **carimbo de data.** Por exemplo, `2018-10-17T06:00:00Z` os valores para serão considerados como uma parcela/entidade (indicada em laranja). Nesta parcela/entidade de valor, o serviço de alerta não encontrará violações consecutivas (porque cada valor **de carimbo de hora** tem apenas uma entrada). Então o alerta nunca é acionado. Nesse caso, o usuário deve:

- Adicione uma variável manequim ou uma variável existente (como **$table)** para classificar corretamente usando o campo **Agregado Sobre.**
- Reconfigure a regra de alerta para usar a lógica de alerta com base na **violação total.**

## <a name="log-alert-fired-unnecessarily"></a>Alerta de log disparado desnecessariamente

Uma regra de alerta de log configurada [no Azure Monitor](../platform/alerts-log.md) pode ser acionada inesperadamente quando você vê-la em [Alertas Azure](../platform/alerts-managing-alert-states.md). As seções a seguir descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta disparado por dados parciais

O Log Analytics e o Application Insights estão sujeitos a atrasos e processamento sinéramericano. Quando você executa uma consulta de alerta de log, você pode descobrir que nenhum dado está disponível ou apenas alguns dados estão disponíveis. Para obter mais informações, consulte [O tempo de ingestão de dados do Log no Azure Monitor](../platform/data-ingestion-time.md).

Dependendo de como você configurou a regra de alerta, o disparo errado pode acontecer se não houver dados ou dados parciais nos logs no momento da execução do alerta. Nesses casos, aconselhamos que você altere a consulta de alerta ou configuração.

Por exemplo, se você configurar a regra de alerta de log a ser acionada quando o número de resultados de uma consulta de análise for menor que 5, o alerta será acionado quando não há dados (registro zero) ou resultados parciais (um registro). Mas após o atraso na ingestão de dados, a mesma consulta com dados completos pode fornecer um resultado de 10 registros.

### <a name="alert-query-output-is-misunderstood"></a>A saída da consulta de alerta é mal compreendida

Você fornece a lógica para alertas de log em uma consulta de análise. A consulta de análise pode usar vários big data e funções matemáticas. O serviço de alerta executa sua consulta em intervalos especificados com dados para um período de tempo especificado. O serviço de alerta faz alterações sutis na consulta com base no tipo de alerta. Você pode visualizar essa alteração na **seção Consulta a ser executada** na tela lógica de sinal **configurar:**

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

A **caixa consulta a ser executada** é o que o serviço de alerta de log é executado. Se você quiser entender o que a saída de consulta de alerta pode ser antes de criar o alerta, você pode executar a consulta declarada e o tempo através do [portal Analytics](../log-query/portals.md) ou da [API de análise](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>O alerta de log foi desativado

As seções a seguir listam algumas razões pelas quais o Azure Monitor pode desativar a [regra de alerta de log](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Recurso onde o alerta foi criado não existe mais

As regras de alerta de log criadas no Azure Monitor têm como alvo um recurso específico, como um espaço de trabalho do Azure Log Analytics, um aplicativo Azure Application Insights e um recurso do Azure. Em seguida, o serviço de alerta de log executará uma consulta de análise fornecida na regra para o destino especificado. Mas após a criação de regras, os usuários geralmente passam a excluir do Azure - ou mover-se dentro do Azure - o alvo da regra de alerta de log. Como o alvo da regra de alerta não é mais válido, a execução da regra falha.

Nesses casos, o Azure Monitor desativa o alerta de log e garante que você não seja cobrado desnecessariamente quando a regra não pode ser executada continuamente por um período considerável (como uma semana). Você pode descobrir a hora exata em que o Azure Monitor desativou o alerta de log via [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md). No Azure Activity Log, um evento é adicionado quando o Azure Monitor desativa a regra de alerta de log.

O evento de amostra a seguir no Azure Activity Log é para uma regra de alerta que foi desativada devido a uma falha contínua.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>A consulta usada em um alerta de log não é válida

Cada regra de alerta de log criada no Azure Monitor como parte de sua configuração deve especificar uma consulta de análise que o serviço de alerta será executado periodicamente. A consulta de análise pode ter sintaxe correta no momento da criação ou atualização da regra. Mas, às vezes, durante um período de tempo, a consulta fornecida na regra de alerta de log pode desenvolver problemas de sintaxe e fazer com que a execução da regra falhe. Algumas razões comuns pelas quais uma consulta de análise fornecida em uma regra de alerta de log pode desenvolver erros são:

- A consulta é escrita para [executar vários recursos](../log-query/cross-workspace-query.md). E um ou mais dos recursos especificados não existem mais.
- [O alerta de log do tipo](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) de medição métrica configurado tem uma consulta de alerta que não está em conformidade com as normas de sintaxe
- Não houve fluxo de dados para a plataforma de análise. A [execução da consulta dá um erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não há dados para a consulta fornecida.
- As [alterações](https://docs.microsoft.com/azure/kusto/query/) no idioma de consulta incluem um formato revisado para comandos e funções. Assim, a consulta fornecida anteriormente em uma regra de alerta não é mais válida.

[O Azure Advisor](../../advisor/advisor-overview.md) avisa sobre esse comportamento. Uma recomendação é adicionada para a regra específica de alerta de log no Azure Advisor, a categoria de Alta Disponibilidade com impacto médio e uma descrição de Reparar sua regra de alerta de log para garantir o monitoramento. Se uma consulta de alerta na regra de alerta de log não for corrigida após o Azure Advisor fornecer uma recomendação por sete dias, o Azure Monitor desativará o alerta de log e garantirá que você não seja cobrado desnecessariamente quando a regra não puder ser executada continuamente por um período considerável ( Como uma semana).

Você pode encontrar a hora exata em que o Azure Monitor desativou a regra de alerta de log procurando um evento no [Azure Activity Log](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [alertas de log no Azure](../platform/alerts-unified-log.md).
- Saiba mais sobre [o Application Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).
