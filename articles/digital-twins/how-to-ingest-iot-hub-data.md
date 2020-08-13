---
title: Ingerir telemetria do Hub IoT
titleSuffix: Azure Digital Twins
description: Consulte como ingerir mensagens de telemetria do dispositivo do Hub IoT.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5209ffb0328e90fb2ca9b91773cbf18dd4ed2916
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163587"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingerir telemetria do Hub IoT no gêmeos digital do Azure

O Azure digital gêmeos é orientado com dados de dispositivos IoT e outras fontes. Uma fonte comum de dados de dispositivo a ser usada no Azure digital gêmeos é o [Hub IOT](../iot-hub/about-iot-hub.md).

O processo para ingerir dados no Azure digital gêmeos é configurar um recurso de computação externo, como uma [função do Azure](../azure-functions/functions-overview.md), que recebe os dados e usa as [APIs do DigitalTwins](how-to-use-apis-sdks.md) para definir propriedades ou disparar eventos de telemetria em [gêmeos digital](concepts-twins-graph.md) adequadamente. 

Este documento de instruções orienta o processo de gravação de uma função do Azure que pode incluir telemetria do Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar com este exemplo, você precisará concluir os seguintes pré-requisitos.
* **Um hub IOT**. Consulte a seção *criar um hub IOT* deste [início rápido do Hub IOT](../iot-hub/quickstart-send-telemetry-cli.md) para obter instruções.
* **Uma função do Azure** com as permissões corretas para chamar sua instância de cópia digital. Consulte [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md) para obter instruções. 
* **Uma instância digital gêmeos** que receberá a telemetria do dispositivo. Consulte [ *como: configurar uma instância e autenticação do gêmeos digital do Azure*](./how-to-set-up-instance-portal.md) 

### <a name="example-telemetry-scenario"></a>Cenário de telemetria de exemplo

Este "como" descreve como enviar mensagens do Hub IoT para o Azure digital gêmeos usando uma função do Azure. Há muitas configurações possíveis e estratégias de correspondência que você pode usar para isso, mas o exemplo deste artigo contém as seguintes partes:
* Um dispositivo de termômetro no Hub IoT, com uma ID de dispositivo conhecida.
* Um teledigital para representar o dispositivo, com uma ID correspondente

> [!NOTE]
> Este exemplo usa uma correspondência de ID direta entre a ID do dispositivo e uma ID de mesa digital correspondente, mas é possível fornecer mapeamentos mais sofisticados do dispositivo para seu número de mesa (como com uma tabela de mapeamento).

Sempre que um evento de telemetria de temperatura é enviado pelo dispositivo de termômetro, a propriedade de *temperatura* da folha de atualização digital deve ser atualizada. Este cenário é descrito em um diagrama abaixo:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Um diagrama que mostra um gráfico de fluxo. No gráfico, um dispositivo de Hub IoT envia a telemetria de temperatura por meio do Hub IoT para uma função do Azure, que atualiza uma propriedade de temperatura em um gêmeos no Azure digital." border="false":::

## <a name="add-a-model-and-twin"></a>Adicionar um modelo e um "r"

Você precisará de um entrelaçamento para atualizar com as informações do Hub IoT.

O modelo tem a seguinte aparência:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Para **carregar esse modelo em sua instância do gêmeos**, abra o CLI do Azure e execute o seguinte comando:
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Em seguida, você precisará **criar um pressione 1 usando esse modelo**. Use o comando a seguir para criar um conjunto de entrelaçar e definir 0,0 como um valor de temperatura inicial.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

A saída de um comando MyCreate com êxito deve ser assim:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Esta seção usa as mesmas etapas de inicialização do Visual Studio e o esqueleto do Azure function de [*como: configurar uma função do Azure para processar dados*](how-to-create-azure-function.md). O esqueleto manipula a autenticação e cria um cliente de serviço, pronto para processar dados e chamar as APIs do Azure digital gêmeos em resposta. 

Nas etapas a seguir, você adicionará um código específico a ele para processar eventos de telemetria IoT do Hub IoT.  

### <a name="add-telemetry-processing"></a>Adicionar processamento de telemetria
    
Os eventos de telemetria vêm na forma de mensagens do dispositivo. A primeira etapa da adição do código de processamento de telemetria é a extração da parte relevante desta mensagem de dispositivo do evento de grade de eventos. 

Dispositivos diferentes podem estruturar suas mensagens de forma diferente, portanto, o código **dessa etapa depende do dispositivo conectado.** 

O código a seguir mostra um exemplo de um dispositivo simples que envia telemetria como JSON. Este exemplo é explorado completamente no [*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md). O código a seguir localiza a ID do dispositivo que enviou a mensagem, bem como o valor de temperatura.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

O exemplo de código a seguir usa a ID e o valor de temperatura e as usa para "patch" (fazer atualizações) que se ocupam.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Atualizar seu código de função do Azure

Agora que você entendeu o código dos exemplos anteriores, abra o Visual Studio e substitua o código da função do Azure por este código de exemplo.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Conectar sua função ao Hub IoT

1. Configure um destino de evento para dados de Hub. Na [portal do Azure](https://portal.azure.com/), navegue até a instância do Hub IOT. Em **eventos**, crie uma assinatura para sua função do Azure. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Captura de tela da portal do Azure que mostra a adição de uma assinatura de evento.":::

2. Na página **criar assinatura de evento** , preencha os campos da seguinte maneira:
    1. Em **nome**, nomeie a assinatura como você deseja.
    2. Em **esquema de evento**, escolha **esquema de grade de eventos**.
    3. Em **nome do tópico do sistema**, escolha um nome exclusivo.
    4. Em **tipos de evento**, escolha **telemetria do dispositivo** como o tipo de evento para filtrar.
    5. Em **detalhes do ponto de extremidade**, selecione sua função do Azure como um ponto de extremidade.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Captura de tela da portal do Azure que mostra os detalhes da assinatura do evento":::

## <a name="send-simulated-iot-data"></a>Enviar dados de IoT simulados

Para testar sua nova função de entrada, use o simulador de dispositivo do [*tutorial: conectar uma solução de ponta a ponta*](./tutorial-end-to-end.md). Esse tutorial é orientado por um projeto de exemplo escrito em C#. O código de exemplo está localizado aqui: [exemplos de gêmeos digitais do Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Você usará o projeto **DeviceSimulator** nesse repositório.

No tutorial de ponta a ponta, conclua as seguintes etapas:
1. [*Registrar o dispositivo simulado no Hub IoT*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurar e executar a simulação*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Validar seus resultados

Ao executar o simulador de dispositivo acima, o valor de temperatura de sua digital de los será alterado. No CLI do Azure, execute o comando a seguir para ver o valor de temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A saída deve conter um valor de temperatura como este:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Para ver a alteração do valor, execute repetidamente o comando de consulta acima.

## <a name="next-steps"></a>Próximas etapas

Leia sobre entrada e saída de dados com o Azure digital gêmeos:
* [*Conceitos: integração com outros serviços*](concepts-integration.md)
