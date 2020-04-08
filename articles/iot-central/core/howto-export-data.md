---
title: Exporte seus dados da Central de IoT do Azure | Microsoft Docs
description: Como exportar dados do seu aplicativo Azure IoT Central para o Azure Event Hubs, Azure Service Bus e armazenamento Azure Blob
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/07/2020
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: c83c97aab43b6978922202cc96ff92e1e046a7e2
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811635"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Exportar dados de IoT para destinos no Azure

*Este tópico aplica-se aos administradores.*

Este artigo descreve como usar o recurso de exportação de dados no Azure IoT Central. Esse recurso permite exportar seus dados continuamente para os **hubs de eventos do Azure,** **barramento de serviço do Azure**ou instâncias **de armazenamento do Azure Blob.** A exportação de dados usa o formato JSON e pode incluir telemetria, informações do dispositivo e informações do modelo do dispositivo. Use os dados exportados para:

- Insights e análises de caminhos quentes. Essa opção inclui ativar regras personalizadas no Azure Stream Analytics, desencadear fluxos de trabalho personalizados em Aplicativos de Lógica do Azure ou passá-lo através de Funções Azure para ser transformado.
- Análises de caminho saem frios, como modelos de treinamento no Azure Machine Learning ou análise de tendências de longo prazo no Microsoft Power BI.

> [!Note]
> Quando você liga a exportação de dados, você recebe apenas os dados daquele momento em diante. Atualmente, os dados não podem ser recuperados por um tempo em que a exportação de dados foi cancelada. Para reter dados mais históricos, ative a exportação de dados mais cedo.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ser um administrador em seu aplicativo IoT Central ou ter permissões de exportação de dados.

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Seu destino de exportação deve existir antes de configurar sua exportação de dados.

### <a name="create-event-hubs-namespace"></a>Criar um namespace dos Hubs de Eventos

Se você não tiver um namespace de eventos para exportar, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura do seu aplicativo IoT Central. Você se conecta usando uma seqüência de conexão neste caso.

3. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Se você não tiver um espaço de nome de Service Bus existente para exportar, siga estas etapas:

1. Crie um [novo espaço de nome de Service Bus no portal Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura do seu aplicativo IoT Central. Você se conecta usando uma seqüência de conexão neste caso.

3. Para criar uma fila ou tópico para exportar, vá para o espaço de nome do seu Ônibus de Serviço e selecione **+ Fila** ou **+ Tópico**.

Quando você escolhe o Service Bus como destino de exportação, as filas e tópicos não devem ter sessões ou detecção duplicada ativadas. Se alguma dessas opções estiver habilitada, algumas mensagens não chegarão à sua fila ou tópico.

### <a name="create-storage-account"></a>Criar Conta de Armazenamento

Se você não tiver uma conta de armazenamento Azure existente para exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode aprender mais sobre a criação de novas [contas de armazenamento Azure Blob](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento Azure Data Lake Storage v2](../../storage/blobs/data-lake-storage-quickstart-create-account.md). A exportação de dados só pode gravar dados em contas de armazenamento que suportam blobs de bloco. A lista a seguir mostra os tipos de conta de armazenamento compatíveis conhecidos:

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Propósito Geral V2|
    |Standard|Propósito Geral V1|
    |Standard|Armazenamento de blob|
    |Premium|Armazenamento block blob|

2. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

## <a name="set-up-data-export"></a>Configurar a exportação de dados

Agora que você tem um destino para exportar dados, siga essas etapas para configurar a exportação de dados.

1. Entre no aplicativo IoT Central.

2. No painel esquerdo, selecione **Exportação de dados**.

    > [!Tip]
    > Se você não ver **a exportação de dados** no painel esquerdo, então você não tem permissões para configurar a exportação de dados em seu aplicativo. Contate o administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo** no canto superior direito. Escolha um dos Hubs de **Eventos Azure,** **Azure Service Bus**ou armazenamento **Azure Blob** como o destino da sua exportação. O número máximo de exportações por aplicação é de cinco.

    ![Criar novas exportações de dados](media/howto-export-data/new-export-definition.png)

4. Na caixa de lista baixada, selecione o **namespace do Event Hubs,** **o espaço de nome do Service Bus,** o **namespace da conta de armazenamento**ou **insira uma seqüência de conexões**.

    - Você só vê contas de armazenamento, espaços de nome do Event Hubs e namespaces de Service Bus na mesma assinatura do aplicativo IoT Central. Se você quiser exportar para um destino fora desta assinatura, escolha **Enter uma string de conexão** e veja o próximo passo.
    - Para aplicativos criados usando o plano de preços gratuitos, a única maneira de configurar a exportação de dados é através de uma cadeia de conexão. Os aplicativos no plano de preços gratuitos não têm uma assinatura associada do Azure.

    ![Criar um novo Hub de Eventos](media/howto-export-data/export-event-hub.png)

5. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:
    - Hubs de eventos ou Service Bus, vá para o namespace no portal azure:
        - Em **Configurações,** selecione **Políticas de acesso compartilhado**
        - Escolha a **RootManageSharedAccessKey** padrão ou crie uma
        - Copie a cadeia de conexão primária ou secundária
    - Conta de armazenamento, vá para a conta de armazenamento no portal do Azure:
        - Em **Configurações,** selecione **teclas de acesso**
        - Copie a seqüência de conexão key1 ou a seqüência de conexão key2

6. Escolha um hub de eventos, fila, tópico ou contêiner na caixa de lista baixa.

7. Em **Dados para exportação,** escolha os tipos de dados a serem exportados definindo o tipo para **On**.

8. Para ativar a exportação de dados, **certifique-se de que** o alternador ativado esteja **ativado**. Clique em **Salvar**.

9. Após alguns minutos, seus dados aparecem no destino escolhido.

## <a name="export-contents-and-format"></a>Conteúdo de exportação e formato

Os dados de telemetria exportados contêm a totalidade da mensagem enviada aos seus dispositivos para a IoT Central, não apenas os valores de telemetria em si. Os dados dos dispositivos exportados contêm alterações nas propriedades e metadados de todos os dispositivos e os modelos de dispositivos exportados contêm alterações em todos os modelos de dispositivos.

Para Hubs de Eventos e Ônibus de Serviço, os dados são exportados em tempo quase real. Os dados estão `body` na propriedade e estão no formato JSON. Veja abaixo exemplos.

Para armazenamento Blob, os dados são exportados uma vez por minuto, com cada arquivo contendo o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/telemetria/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Dispositivos: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modelos de dispositivo: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Para navegar pelos arquivos exportados no portal Azure, navegue até o arquivo e selecione a guia **Editar blob.**

## <a name="telemetry"></a>Telemetria

Para hubs de eventos e ônibus de serviço, a IoT Central exporta uma nova mensagem rapidamente depois de receber a mensagem de um dispositivo. Cada mensagem exportada contém a mensagem completa que o dispositivo enviou na propriedade do corpo no formato JSON.

Para armazenamento Blob, as mensagens são em loteadas e exportadas uma vez por minuto. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [roteamento de mensagens do IoT Hub](../../iot-hub/tutorial-routing.md) para o armazenamento blob.

> [!NOTE]
> Para armazenamento Blob, certifique-se de que `contentType: application/JSON` seus `contentEncoding:utf-8` dispositivos `utf-16` `utf-32`estão enviando mensagens que têm e (ou ). Veja a documentação do [IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) como exemplo.

O dispositivo que enviou a telemetria é representado pelo ID do dispositivo (veja as seções a seguir). Para obter os nomes dos dispositivos, exporte dados do dispositivo e correlaciona cada mensagem usando a **conexãoDeviceId** que corresponde ao **dispositivoId** da mensagem do dispositivo.

O exemplo a seguir mostra uma mensagem recebida de um hub de eventos ou fila de ônibus de serviço ou tópico:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Esta mensagem não inclui o ID do dispositivo do dispositivo de envio.

Para recuperar o ID do dispositivo dos dados da mensagem em uma consulta do Azure Stream Analytics, use a função [GetMetadataPropertyValue.](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) Por exemplo, consulte a consulta em [Extend Azure IoT Central com regras personalizadas usando Stream Analytics, Funções Azure e SendGrid](./howto-create-custom-rules.md).

Para recuperar o ID do dispositivo em um espaço de trabalho Azure Databricks ou Apache Spark, use [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Por exemplo, consulte o espaço de trabalho Databricks em [Extend Azure IoT Central com análises personalizadas usando o Azure Databricks](./howto-create-custom-analytics.md).

O exemplo a seguir mostra um registro exportado para o armazenamento blob:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Dispositivos

Cada mensagem ou registro em um snapshot representa uma ou mais alterações em um dispositivo e suas propriedades de dispositivo e nuvem desde a última mensagem exportada. A mensagem inclui:

- `id` do dispositivo na IoT Central
- `displayName` do dispositivo
- ID do modelo do dispositivo em`instanceOf`
- `simulated`bandeira, verdade se o dispositivo é um dispositivo simulado
- `provisioned`bandeira, verdade se o dispositivo foi provisionado
- `approved`bandeira, verdade se o dispositivo foi aprovado para enviar dados
- Valores de propriedade
- `properties`incluindo valores de propriedades de dispositivos e nuvem

Os dispositivos excluídos não são exportados. Atualmente, não há indicadores em mensagens exportadas para dispositivos excluídos.

Para hubs de eventos e ônibus de serviço, a IoT Central envia mensagens contendo dados do dispositivo para o seu hub de eventos ou fila de ônibus de serviço ou tópico em quase tempo real.

Para armazenamento Blob, um novo instantâneo contendo todas as alterações desde o último escrito é exportado uma vez por minuto.

O exemplo a seguir mostra informações sobre dispositivos e dados de propriedades em um hub de eventos ou fila de ônibus de serviço ou tópico:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Este instantâneo é uma mensagem de exemplo que mostra dispositivos e propriedades de dados no armazenamento Blob. Os arquivos exportados contêm uma única linha por registro.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Modelos de dispositivo

Cada registro de mensagem ou snapshot representa uma ou mais alterações em um modelo de dispositivo publicado desde a última mensagem exportada. As informações enviadas em cada mensagem ou registro incluem:

- `id`do modelo do dispositivo `instanceOf` que corresponde ao fluxo dos dispositivos acima
- `displayName` do modelo de dispositivo
- O `capabilityModel` dispositivo, `interfaces`incluindo suas definições de telemetria, propriedades e comandos
- `cloudProperties`Definições
- Substituições e valores iniciais, em linha com o`capabilityModel`

Modelos de dispositivos excluídos não são exportados. Atualmente, não há indicadores em mensagens exportadas para modelos de dispositivos excluídos.

Para hubs de eventos e ônibus de serviço, a IoT Central envia mensagens contendo dados de modelo de dispositivo para o seu hub de eventos ou fila de ônibus de serviço ou tópico em quase tempo real.

Para armazenamento Blob, um novo instantâneo contendo todas as alterações desde o último escrito é exportado uma vez por minuto.

Este exemplo mostra uma mensagem sobre os dados dos modelos de dispositivos no hub de eventos ou na fila ou tópico do Service Bus:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Este instantâneo de exemplo mostra uma mensagem que contém dados de dispositivos e propriedades no armazenamento Blob. Os arquivos exportados contêm uma única linha por registro.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Aviso de alteração do formato de dados

> [!Note]
> O formato de dados de fluxo de telemetria não é afetado por essa alteração. Apenas os fluxos de dados de dispositivos e modelos de dispositivos são afetados.

Se você tiver uma exportação de dados existente em seu aplicativo de visualização com os fluxos de *modelos de dispositivos* e dispositivos *ligados,* atualize sua exportação até **30 de junho de 2020**. Essa exigência se aplica às exportações para o armazenamento Azure Blob, Azure Event Hubs e Azure Service Bus.

A partir de 3 de fevereiro de 2020, todas as novas exportações em aplicativos com modelos de dispositivos e dispositivos habilitados terão o formato de dados descrito acima. Todas as exportações criadas antes desta data permanecem no formato de dados antigos até 30 de junho de 2020, período em que essas exportações serão automaticamente migradas para o novo formato de dados. O novo formato de dados corresponde ao [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/get)propriedade do [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [propriedade na nuvem do dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)e objetos de modelo do [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) na API pública IoT Central.

Para **dispositivos,** diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:
- `@id`para que o `deviceId` dispositivo seja removido, é renomeado para`id` 
- `provisioned`bandeira é adicionada para descrever o status de provisionamento do dispositivo
- `approved`bandeira é adicionada para descrever o estado de aprovação do dispositivo
- `properties`incluindo propriedades de dispositivos e nuvem, combina entidades na API pública

Para **modelos de dispositivos,** diferenças notáveis entre o formato de dados antigo e o novo formato de dados incluem:

- `@id`para modelo de dispositivo é renomeado para`id`
- `@type`para o modelo do `types`dispositivo é renomeado para , e agora é uma matriz

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Dispositivos (formato preterido a partir de 3 de fevereiro de 2020)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Modelos de dispositivos (formato preterido a partir de 3 de fevereiro de 2020)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como exportar seus dados para o Azure Event Hubs, Azure Service Bus e armazenamento Azure Blob, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como criar webhooks](./howto-create-webhooks.md)
