---
title: Estimando os custos do plano de consumo em funções do Azure
description: Saiba como estimar melhor os custos que você pode incorrer ao executar seu aplicativo de função em um plano de consumo no Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963981"
---
# <a name="estimating-consumption-plan-costs"></a>Estimando os custos do plano de consumo

Existem atualmente três tipos de planos de hospedagem para um aplicativo que é executado em Funções Azure, com cada plano tendo seu próprio modelo de preços: 

| Plano | Descrição |
| ---- | ----------- |
| [**Consumo**](functions-scale.md#consumption-plan) | Você só é cobrado pelo tempo que seu aplicativo de função é executado. Este plano inclui uma página de[preços] [de subvenção gratuita]em uma base por assinatura.|
| [**Premium**](functions-scale.md#premium-plan) | Fornece os mesmos recursos e mecanismo de dimensionamento do plano de consumo, mas com desempenho aprimorado e acesso ao VNET. O custo é baseado no nível de preços escolhido. Para saber mais, consulte [o plano Azure Functions Premium](functions-premium-plan.md). |
| [**Dedicado (Serviço de Aplicativo)**](functions-scale.md#app-service-plan) <br/>(nível básico ou superior) | Quando você precisar executar em VMs dedicadas ou isoladamente, use imagens personalizadas ou queira usar sua capacidade de plano de serviço de aplicativo em excesso. Usa [o faturamento regular do plano app service](https://azure.microsoft.com/pricing/details/app-service/). O custo é baseado no nível de preços escolhido.|

Você escolheu o plano que melhor suporta o desempenho da função e os requisitos de custo. Para saber mais, confira [Escala e hospedagem do Azure Functions](functions-scale.md).

Este artigo trata apenas do plano de Consumo, uma vez que este plano resulta em custos variáveis. Este artigo substitui o plano de consumo que custa o artigo [faq de cobrança.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Funções duráveis também podem ser executadas em um plano de consumo. Para saber mais sobre as considerações de custo ao usar funções duráveis, consulte [O faturamento de Funções Duráveis](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Custos de plano de consumo

O *custo* de execução de uma única execução de função é medido em *GB-seconds*. O custo de execução é calculado combinando seu uso de memória com seu tempo de execução. Uma função que funciona por mais tempo custa mais, assim como uma função que consome mais memória. 

Considere um caso em que a quantidade de memória usada pela função permanece constante. Neste caso, calcular o custo é uma multiplicação simples. Por exemplo, digamos que sua função consumiu 0,5 GB por 3 segundos. Então o custo `0.5GB * 3s = 1.5 GB-seconds`da execução é . 

Uma vez que o uso da memória muda ao longo do tempo, o cálculo é essencialmente a parte integrante do uso da memória ao longo do tempo.  O sistema faz esse cálculo por amostragem do uso da memória do processo (juntamente com os processos infantis) em intervalos regulares. Como mencionado na [página de preços,]o uso da memória é arredondado até o balde de 128 MB mais próximo. Quando o seu processo está usando 160 MB, você é cobrado por 256 MB. O cálculo leva em conta a simultânea, que é múltiplas execuções de função simultâneas no mesmo processo.

> [!NOTE]
> Embora o uso da CPU não seja diretamente considerado no custo de execução, ele pode ter um impacto no custo quando afeta o tempo de execução da função.

## <a name="other-related-costs"></a>Outros custos relacionados

Ao estimar o custo total de execução de suas funções em qualquer plano, lembre-se de que o tempo de execução functions usa vários outros serviços do Azure, que são cobrados separadamente. Ao calcular os preços dos aplicativos de função, quaisquer gatilhos e vinculações que você tenha que se integram com outros serviços do Azure exigem que você crie e pague por esses serviços adicionais. 

Para funções em execução em um plano de consumo, o custo total é o custo de execução de suas funções, além do custo de largura de banda e serviços adicionais. 

Ao estimar os custos gerais do seu aplicativo de função e serviços relacionados, use a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Custo relacionado | Descrição |
| ------------ | ----------- |
| **Conta de armazenamento** | Cada aplicativo de função requer que você tenha uma conta de [armazenamento Azure](../storage/common/storage-introduction.md#types-of-storage-accounts)de propósito geral associada, que é [cobrada separadamente](https://azure.microsoft.com/pricing/details/storage/). Esta conta é usada internamente pelo tempo de execução Functions, mas você também pode usá-la para gatilhos e vinculações de armazenamento. Se você não tiver uma conta de armazenamento, uma será criada para você quando o aplicativo de função for criado. Para saber mais, consulte [os requisitos da conta de armazenamento](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | As funções contam com [insights de aplicativos](../azure-monitor/app/app-insights-overview.md) para fornecer uma experiência de monitoramento de alto desempenho para seus aplicativos de função. Embora não seja necessário, você deve [habilitar a integração do Application Insights](functions-monitoring.md#enable-application-insights-integration). Uma concessão gratuita de dados de telemetria é incluída todos os meses. Para saber mais, consulte [a página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Largura de banda da rede** | Você não paga pela transferência de dados entre os serviços do Azure na mesma região. No entanto, você pode incorrer em custos para transferências de dados de saída para outra região ou fora do Azure. Para saber mais, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamentos que afetam o tempo de execução

Os seguintes comportamentos de suas funções podem afetar o tempo de execução:

+ **Gatilhos e vinculações**: O tempo de leitura da entrada e da gravação da saída para as [vinculações da função](functions-triggers-bindings.md) é contado como tempo de execução. Por exemplo, quando sua função usa uma vinculação de saída para gravar uma mensagem em uma fila de armazenamento do Azure, seu tempo de execução inclui o tempo de execução para escrever a mensagem na fila, que está incluída no cálculo do custo da função. 

+ **Execução assíncrona**: O tempo que sua função espera`await` pelos resultados de uma solicitação assíncrona (em C#) é contado como tempo de execução. O cálculo do segundo GB baseia-se no tempo de início e fim da função e no uso da memória durante esse período. O que está acontecendo ao longo desse tempo em termos de atividade da CPU não é levado em conta no cálculo. Você pode ser capaz de reduzir custos durante operações assíncronas usando [funções duráveis](durable/durable-functions-overview.md). Você não é cobrado pelo tempo gasto em funções orquestrais.

## <a name="view-execution-data"></a>Exibir dados de execução

Em [sua fatura,](/azure/billing/billing-download-azure-invoice)você pode visualizar os dados relacionados ao custo de **Execuções Totais - Funções** e Tempo de **Execução - Funções**, juntamente com os custos reais faturados. No entanto, esses dados da fatura são um agregado mensal para um período de fatura anterior. 

Para entender melhor o impacto de custo de suas funções, você pode usar o Azure Monitor para visualizar métricas relacionadas a custos atualmente sendo geradas por seus aplicativos de função. Você pode usar o [explorador de métricas do Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) no portal do [Azure] ou apIs REST para obter esses dados.

### <a name="monitor-metrics-explorer"></a>Monitore o explorador de métricas

Use [o explorador de métricas do Azure Monitor](../azure-monitor/platform/metrics-getting-started.md) para visualizar dados relacionados a custos para os aplicativos de função do plano de consumo em um formato gráfico. 

1. No topo do [portal Azure] em **Serviços de Pesquisa, recursos e docs** procurem `monitor` e selecionem **Monitor** em **Serviços**.

1. À esquerda, **selecione Métricas** > **Selecione um recurso**e use as configurações abaixo da imagem para escolher seu aplicativo de função.

    ![Selecione o recurso do aplicativo de função](media/functions-consumption-costing/select-a-resource.png)

      
    |Configuração  |Valor sugerido  |Descrição  |
    |---------|---------|---------|
    | Subscription    |  Sua assinatura  | A assinatura com seu aplicativo de função.  |
    | Resource group     | Seu grupo de recursos  | O grupo de recursos que contém seu aplicativo de função.   |
    | Tipo de recurso     |  Serviços de Aplicativos | Os aplicativos de função são mostrados como instâncias do App Services no Monitor. |
    | Recurso     |  Seu aplicativo de função  | O aplicativo de função para monitorar.        |

1. Selecione **Aplicar** para escolher seu aplicativo de função como o recurso a ser monitorado.

1. Em **Métrica,** escolha **Contagem de execução de funções** e **soma** para **agregação**. Isso adiciona a soma das contagens de execução durante o período escolhido ao gráfico.

    ![Defina uma métrica de aplicativo de funções para adicionar ao gráfico](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selecione **Adicionar etapas métricas** e repetir 2-4 para adicionar **unidades de execução** de função ao gráfico. 

O gráfico resultante contém os totais para ambas as métricas de execução no intervalo de tempo escolhido, que neste caso é de duas horas.

![Gráfico de contagens de execução de funções e unidades de execução](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Como o número de unidades de execução é muito maior do que a contagem de execução, o gráfico mostra apenas unidades de execução.

Este gráfico mostra um total de `Function Execution Units` 1,11 bilhão consumidos em um período de duas horas, medido em MB-milissegundos. Para converter em GB-seconds, divida por 1024000. Neste exemplo, o aplicativo `1110000000 / 1024000 = 1083.98` de função consumiu GB-seconds. Você pode pegar esse valor e multiplicar pelo preço atual do tempo de execução na página de[preços]de [preços de Funções], o que lhe dá o custo dessas duas horas, assumindo que você já usou quaisquer concessões gratuitas de tempo de execução. 

### <a name="azure-cli"></a>CLI do Azure

O [Azure CLI](/cli/azure/) tem comandos para recuperar métricas. Você pode usar a CLI a partir de um ambiente de comando local ou diretamente do portal usando [o Azure Cloud Shell](../cloud-shell/overview.md). Por exemplo, o comando [a seguir a z monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) retorna dados de hora em hora durante o mesmo período de tempo usado anteriormente.

Certifique-se `<AZURE_SUBSCRIPTON_ID>` de substituir com seu ID de assinatura do Azure executando o comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Este comando retorna uma carga útil JSON que se parece com o seguinte exemplo:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Esta resposta em `2019-09-11T21:46` particular `2019-09-11T23:18`mostra que de para , o aplicativo consumiu 11100000000 MB-milissegundos (1083.98 GB-seconds).

## <a name="determine-memory-usage"></a>Determine o uso da memória

As unidades de execução de funções são uma combinação de tempo de execução e uso da memória, o que torna uma métrica difícil para entender o uso da memória. Os dados de memória não são uma métrica disponível atualmente através do Azure Monitor. No entanto, se você quiser otimizar o uso da memória do seu aplicativo, pode usar os dados do contador de desempenho coletados pelo Application Insights.  

Se você ainda não fez isso, [habilite o Application Insights em seu aplicativo de função](functions-monitoring.md#enable-application-insights-integration). Com essa integração ativada, você pode [consultar esses dados de telemetria no portal](functions-monitoring.md#query-telemetry-data).  

Em **Monitoramento,** **selecione Logs (Analytics)** e copie a seguinte consulta de telemetria e cole-a na janela de consulta e selecione **Executar**. Esta consulta retorna o uso total da memória em cada tempo amostrado.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Os resultados parecem o seguinte exemplo:

| Carimbo \[de data e hora UTC\]          | name          | value       |
|----------------------------|---------------|-------------|
| 12/09/2019, 1:05:14\.947 AM | Bytes Particulares | 209,932,288 |
| 12/09/2019, 1:06:14\.994 | Bytes Particulares | 212,189,184 |
| 12/09/2019, 1:06:30\.010 | Bytes Particulares | 231,714,816 |
| 12/09/2019, 1:07:15\.040 | Bytes Particulares | 210,591,744 |
| 12/09/2019, 1:12:16\.285 AM | Bytes Particulares | 216,285,184 |
| 12/09/2019, 1:12:31\.376 AM | Bytes Particulares | 235,806,720 |

## <a name="function-level-metrics"></a>Métricas em nível de função

O Azure Monitor rastreia métricas no nível de recurso, que para Funções é o aplicativo de função. A integração do Application Insights emite métricas por função. Aqui está uma consulta de análise de exemplo para obter a duração média de uma função:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | médiaDuração Milissegundos |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre aplicativos de funções de monitoramento](functions-monitoring.md)

[página de preços]:https://azure.microsoft.com/pricing/details/functions/
[Portal Azure]: https://portal.azure.com
