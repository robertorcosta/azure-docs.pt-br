---
title: Usar o PowerShell para configurar alertas no Application Insights | Microsoft Docs
description: Automatize a configuração do Application Insights para receber emails sobre alterações de métricas.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: ea33ecfbc02bfed75a66e751ce1788474a6d0e8f
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111296"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Usar o PowerShell para configurar alertas no Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode automatizar a configuração de [alertas](../../azure-monitor/platform/alerts-log.md) no [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Além disso, você pode [definir webhooks para automatizar sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Se você quiser criar alertas e recursos ao mesmo tempo, considere [usar um modelo do Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Configuração única
Se você nunca usou o PowerShell com sua assinatura do Azure:

Instale o módulo do Azure Powershell no computador em que você deseja executar os scripts.

* Instale o [Microsoft Web Platform Installer (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
* Use-o para instalar o Microsoft Azure PowerShell

## <a name="connect-to-azure"></a>Conectar-se ao Azure
Inicie o Azure PowerShell e [conecte-se à sua assinatura](/powershell/azure/overview):

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Obter alertas

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Adicionar alerta

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>Exemplo 1
Enviar email se a resposta do servidor às solicitações HTTP, em média no prazo de 5 minutos, demorar mais de 1 segundo. Meu recurso Application Insights é chamado IceCreamWebApp e está no grupo de recursos Fabrikam. Sou o proprietário da assinatura do Azure.

O GUID é a ID da assinatura (não a chave de instrumentação do aplicativo).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>Exemplo 2
Tenho um aplicativo em que uso o [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) para relatar uma métrica chamada "salesPerHour". Envie um email para meus colegas se "salesPerHour" cair abaixo de 100, em média no prazo de 24 horas.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

A mesma regra pode ser usada para a métrica relatada usando o [parâmetro de medida](../../azure-monitor/app/api-custom-events-metrics.md#properties) de outra chamada de controle, como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Nomes de métrica
| Nome da métrica | Nome da tela | Descrição |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Exceções de navegador |Contagem de exceções não identificadas lançadas no navegador. |
| `basicExceptionServer.count` |Exceções do servidor |Contagem de exceções sem tratamento lançadas pelo aplicativo |
| `clientPerformance.clientProcess.value` |Tempo de processamento do cliente |Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas. |
| `clientPerformance.networkConnection.value` |Tempo de conexão de rede de carregamento de página |Tempo que leva para o navegador se conectar à rede. Pode ser 0 se armazenado em cache. |
| `clientPerformance.receiveRequest.value` |Tempo de resposta de recebimento |Tempo entre o envio da solicitação do navegador e o início do recebimento de resposta. |
| `clientPerformance.sendRequest.value` |Tempo de solicitação de envio |Tempo gasto pelo navegador para enviar a solicitação. |
| `clientPerformance.total.value` |Tempo de carregamento de página do navegador |Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados. |
| `performanceCounter.available_bytes.value` |Memória disponível |Memória física disponível imediatamente para um processo ou para uso do sistema. |
| `performanceCounter.io_data_bytes_per_sec.value` |Taxa de processamento de IO |Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |taxa de exceção |Exceções geradas por segundo. |
| `performanceCounter.percentage_processor_time.value` |CPU do processo |A porcentagem de tempo decorrido em todos os threads do processo usados pelo processador para executar instruções do processo dos aplicativos. |
| `performanceCounter.percentage_processor_total.value` |Tempo do processador |A porcentagem de tempo que o processador gasta em threads ocupados. |
| `performanceCounter.process_private_bytes.value` |Processar bytes particulares |Memória atribuída exclusivamente aos processos do aplicativo monitorado. |
| `performanceCounter.request_execution_time.value` |Tempo de execução de solicitação do ASP.NET |Tempo de execução da solicitação mais recente. |
| `performanceCounter.requests_in_application_queue.value` |Solicitações do ASP.NET na fila de execução |Comprimento da fila de solicitação de aplicativo. |
| `performanceCounter.requests_per_sec.value` |Taxa de solicitação do ASP.NET |Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Falhas de dependência |Contagem de falhas de chamadas feitas pelo aplicativo servidor para recursos externos. |
| `request.duration` |Tempo de resposta do servidor |Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta. |
| `request.rate` |Taxa de solicitação |Taxa de todas as solicitações para o aplicativo por segundo. |
| `requestFailed.count` |Solicitações falhas |Contagem de solicitações HTTP que resultaram em um código de resposta >= 400 |
| `view.count` |Visualizações de página |Contagem de solicitações de usuário  cliente para uma página da Web. O tráfego sintético é filtrado. |
| {o nome de métrica personalizada} |{O nome da métrica} |O valor de métrica relatado pelo [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) ou o [parâmetro de medidas de uma chamada de controle](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

As métricas são enviadas por diferentes módulos de telemetria:

| Grupo de métricas | Módulo de coletor |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>exibição |[JavaScript do navegador](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Desempenho](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Dependência](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| request,<br/>requestFailed |[Solicitação do servidor](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
Você pode [automatizar sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md). O Azure ligará para um endereço web de sua escolha quando um alerta for gerado.

## <a name="see-also"></a>Confira também
* [Script para configurar o Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Criar recursos de teste da Web e do Application Insights por meio de modelos](powershell.md)
* [Automatizar o acoplamento do Diagnóstico do Microsoft Azure ao Application Insights](powershell-azure-diagnostics.md)
* [Automatizar sua resposta a um alerta](../../azure-monitor/platform/alerts-webhooks.md)
