---
title: Entenda o ponto de extremidade interno Azure Hub IoT | Microsoft Docs
description: Guia do Desenvolvedor – descreve como usar o ponto de extremidade compatível com hub de eventos interno para ler mensagens de dispositivo para nuvem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: e7b8f8a33b741a8dcf2d1a68ae3cf86d6e3687eb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392539"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partição** | Defina essa propriedade no momento da criação para determinar o número de [partições](../event-hubs/event-hubs-features.md#partitions) para inclusão do evento de dispositivo para nuvem. |
| **Período de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O Hub IoT permite a retenção de dados nos hubs de eventos internos por um máximo de 7 dias. Você pode definir o tempo de retenção durante a criação do Hub IoT. O tempo de retenção de dados no Hub IoT depende da camada e do tipo de unidade do Hub IoT. Em termos de tamanho, os hubs de eventos internos podem reter as mensagens do tamanho máximo da mensagem até pelo menos 24 horas de cota. Por exemplo, para um hub IoT de unidade S1 fornece armazenamento suficiente para reter pelo menos mensagens 400 mil de tamanho de 4K cada. Se seus dispositivos estiverem enviando mensagens menores, eles poderão ser retidos por mais tempo (até 7 dias), dependendo da quantidade de armazenamento consumida. Garantimos manter os dados para o tempo de retenção especificado como um mínimo.

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno. Você pode ter até 20 grupos de consumidores para cada Hub IoT.

Se você estiver usando o [Roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e a rota de [fallback](iot-hub-devguide-messages-d2c.md#fallback-route) estiver habilitada, todas as mensagens que não corresponderem a uma consulta em qualquer rota vão para o ponto de extremidade interno. Se você desabilitar essa rota de fallback, as mensagens que não corresponderem a nenhuma consulta serão descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática usando as [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Algumas integrações de produtos e SDKs de hubs de eventos estão cientes do Hub IoT e permitem que você use a cadeia de conexão de serviço do Hub IoT para se conectar ao ponto de extremidade interno.

Ao usar SDKs de hubs de eventos ou integrações de produtos que não reconhecem o Hub IoT, você precisa de um ponto de extremidade compatível com o Hub de eventos e um nome compatível com o Hub de eventos. Você pode recuperar esses valores do portal da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Clique em **Pontos de extremidade internos**.

3. A seção de **eventos** contém os seguintes valores: **partições**, **nome compatível**com o Hub de eventos, **ponto de extremidade compatível com o Hub de eventos**, tempo de **retenção**e **grupos de consumidores**.

    ![Configurações de dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo ponto de extremidade compatível com o Hub de eventos contém uma cadeia de conexão completa de hubs de eventos semelhante a: **Endpoint = SB://abcd1234namespace.ServiceBus.Windows.net/; SharedAccessKeyName = iothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = iothub-eHub-ABCD-1234-123456**. Se o SDK que você está usando exigir outros valores, eles serão:

| {1&gt;Nome&lt;1} | {1&gt;Valor&lt;1} |
| ---- | ----- |
| Ponto de extremidade | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome do host | abcd1234namespace.servicebus.windows.net |
| {1&gt;Namespace&lt;1} | abcd1234namespace |

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Os SDKs que você pode usar para se conectar ao ponto de extremidade compatível com o Hub de eventos interno que o Hub IoT expõe incluem:

| Linguagem | . | {1&gt;Exemplo&lt;1} | {1&gt;Observações&lt;1} |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Início rápido](quickstart-send-telemetry-dotnet.md) | Usa informações compatíveis com os hubs de eventos |
 Java | https://github.com/Azure/azure-event-hubs-java | [Início rápido](quickstart-send-telemetry-java.md) | Usa informações compatíveis com os hubs de eventos |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Início rápido](quickstart-send-telemetry-node.md) | Usa a cadeia de conexão do Hub IoT |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa a cadeia de conexão do Hub IoT |

As integrações de produto que você pode usar com o ponto de extremidade compatível com o Hub de eventos interno que o Hub IoT expõe incluem:

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Consulte [processando dados do Hub IOT com Azure Functions](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Consulte [transmitir dados como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series insights](https://docs.microsoft.com/azure/time-series-insights/). Consulte [Adicionar uma origem de evento do Hub IOT ao seu ambiente de time Series insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para saber mais sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Inícios Rápidos](quickstart-send-telemetry-node.md) mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para saber mais, confira o tutorial [Processar as mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md).

* Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, confira [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem](iot-hub-devguide-messages-read-custom.md).
