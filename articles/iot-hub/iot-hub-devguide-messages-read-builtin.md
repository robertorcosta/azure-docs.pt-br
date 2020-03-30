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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284597"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos](https://azure.microsoft.com/documentation/services/event-hubs/). Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | Descrição |
| ------------------- | ----------- |
| **Contagem de partições** | Defina essa propriedade no momento da criação para determinar o número de [partições](../event-hubs/event-hubs-features.md#partitions) para inclusão do evento de dispositivo para nuvem. |
| **Tempo de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O IoT Hub permite a retenção de dados nos Hubs de Eventos incorporados por um máximo de 7 dias. Você pode definir o tempo de retenção durante a criação do seu IoT Hub. O tempo de retenção de dados no IoT Hub depende do seu nível de hub ioT e do tipo de unidade. Em termos de tamanho, os Hubs de Eventos incorporados podem reter mensagens do tamanho máximo da mensagem até pelo menos 24 horas de cota. Por exemplo, para 1 unidade S1 IoT Hub fornece armazenamento suficiente para reter pelo menos 400K mensagens de tamanho 4k cada. Se seus dispositivos estiverem enviando mensagens menores, elas podem ser retidas por mais tempo (até 7 dias), dependendo da quantidade de armazenamento consumido. Garantimos a retenção dos dados para o tempo de retenção especificado como mínimo.

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno. Você pode ter até 20 grupos de consumidores para cada IoT Hub.

Se você estiver usando [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e a [rota de recuo](iot-hub-devguide-messages-d2c.md#fallback-route) estiver ativada, todas as mensagens que não correspondem a uma consulta em qualquer rota vão para o ponto final incorporado. Se você desativar essa rota de recuo, as mensagens que não correspondem a nenhuma consulta serão retiradas.

Você pode modificar o tempo de retenção, seja de maneira programática usando as [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Algumas integrações de produtos e Hubs de Eventos Os SDKs estão cientes do IoT Hub e permitem que você use sua cadeia de conexão de serviço de hub IoT para se conectar ao ponto final incorporado.

Quando você usa SDKs do Event Hubs ou integrações de produtos que desconhecem o IoT Hub, você precisa de um endpoint compatível com Event Hub e nome compatível com Event Hub. Você pode recuperar esses valores do portal da seguinte forma:

1. Faça login no [portal Azure](https://portal.azure.com) e navegue até o seu hub de IoT.

2. Clique em **Pontos de extremidade internos**.

3. A seção **Eventos** contém os seguintes valores: **Partições,** **nome compatível com o Hub de eventos,** **ponto final compatível com o Hub de eventos,** **tempo de retenção**e **grupos de consumidores**.

    ![Configurações de dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

No portal, o campo de ponto final compatível com o Event Hub contém uma seqüência completa de conexão Event Hubs que se parece com: **Endpoint=sb://abcd1234namespace.servicebus.windows.net/; CompartilhadoAccessKeyName=iothubowner; SharedAccessKey=keykeykeykeykeykey=; EntityPath=iothub-ehub-abcd-1234-123456**. Se o SDK que você está usando requer outros valores, então eles seriam:

| Nome | Valor |
| ---- | ----- |
| Ponto de extremidade | sb://abcd1234namespace.servicebus.windows.net/ |
| Nome do host | abcd1234namespace.servicebus.windows.net |
| Namespace | abcd1234namespace |

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Os SDKs que você pode usar para se conectar ao ponto final incorporado compatível com o Hub de Eventos que o IoT Hub expõe incluem:

| Idioma | . | Exemplo | Observações |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [Quickstart](quickstart-send-telemetry-dotnet.md) | Usa informações compatíveis com hubs de eventos |
 Java | https://github.com/Azure/azure-event-hubs-java | [Quickstart](quickstart-send-telemetry-java.md) | Usa informações compatíveis com hubs de eventos |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [Quickstart](quickstart-send-telemetry-node.md) | Usa string de conexão IoT Hub |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | Usa string de conexão IoT Hub |

As integrações de produtos que você pode usar com o ponto final compatível com o Hub de eventos incorporado que o IoT Hub expõe incluem:

* [Funções Azure](https://docs.microsoft.com/azure/azure-functions/). Consulte [Os dados de processamento do IoT Hub com funções do Azure](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/). Veja [os dados do Stream como entrada no Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Insights da série temporal](https://docs.microsoft.com/azure/time-series-insights/). Consulte [Adicionar uma fonte de evento hub de IoT ao seu ambiente Time Series Insights](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, consulte [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Inícios Rápidos](quickstart-send-telemetry-node.md) mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para saber mais, confira o tutorial [Processar as mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md).

* Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, confira [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem](iot-hub-devguide-messages-read-custom.md).
