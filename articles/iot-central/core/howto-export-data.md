---
title: Exporte seus dados da Central de IoT do Azure | Microsoft Docs
description: Como exportar dados do seu aplicativo Azure IoT Central para o Azure Event Hubs, Azure Service Bus e Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: how-to
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 725c5acf961fffb1fd4cf9bc17e37a5940f871cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157901"
---
# <a name="export-iot-data-to-destinations-in-azure"></a>Exportar dados de IoT para destinos no Azure

*Este tópico aplica-se aos administradores.*

Este artigo descreve como usar o recurso de exportação contínua de dados no Azure IoT Central para exportar seus dados para **os hubs de eventos do Azure,** **azure Service Bus**ou para as instâncias de armazenamento Do **Azure Blob.** Os dados são exportados no formato JSON e podem incluir telemetria, informações do dispositivo e informações do modelo do dispositivo. Use os dados exportados para:

- Insights e análises de caminhos quentes. Essa opção inclui ativar regras personalizadas no Azure Stream Analytics, desencadear fluxos de trabalho personalizados em Aplicativos de Lógica do Azure ou passá-lo através de Funções Azure para ser transformado.
- Análises de caminho saem frios, como modelos de treinamento no Azure Machine Learning ou análise de tendências de longo prazo no Microsoft Power BI.

> [!Note]
> Ao ativar a exportação contínua de dados, você obtém apenas os dados desse momento em diante. Atualmente, não é possível recuperar dados por um tempo quando a exportação contínua de dados for desativada. Para reter dados mais históricos, ative a exportação contínua de dados no início.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ser um administrador em seu aplicativo IoT Central ou ter permissões de exportação de dados.

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Seu destino de exportação deve existir antes de configurar sua exportação contínua de dados.

### <a name="create-event-hubs-namespace"></a>Criar um namespace dos Hubs de Eventos

Se você não tiver um namespace de eventos para exportar, siga estas etapas:

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](../../event-hubs/event-hubs-create.md).

2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura do seu aplicativo IoT Central. Você se conecta usando uma seqüência de conexão neste caso.

3. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

Se você não tiver um espaço de nome de Service Bus existente para exportar, siga estas etapas:

1. Crie um [novo espaço de nome de Service Bus no portal Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Escolha uma assinatura. Você pode exportar dados para outras assinaturas que não estão na mesma assinatura do seu aplicativo IoT Central. Você se conecta usando uma seqüência de conexão neste caso.

3. Acesse o namespace do Barramento de Serviço e selecione **+ Fila** ou **+ Tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.

Quando você escolhe o Service Bus como destino de exportação, as filas e tópicos não devem ter sessões ou detecção duplicada ativadas. Se alguma dessas opções estiver habilitada, algumas mensagens não chegarão à sua fila ou tópico.

### <a name="create-storage-account"></a>Criar Conta de Armazenamento

Se você não tiver uma conta de armazenamento Azure existente para exportar, siga estas etapas:

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Você pode aprender mais sobre a criação de novas [contas de armazenamento Azure Blob](https://aka.ms/blobdocscreatestorageaccount) ou [contas de armazenamento V2 do Lago de Dados Do Azure.](../../storage/blobs/data-lake-storage-quickstart-create-account.md) A exportação de dados só pode gravar dados em contas de armazenamento que suportam blobs de bloco. A seguir está uma lista de tipos conhecidos compatíveis de contas de armazenamento: 

    |Nível de desempenho|Tipo de Conta|
    |-|-|
    |Standard|Propósito Geral V2|
    |Standard|Propósito Geral V1|
    |Standard|Armazenamento de Blobs|
    |Premium|Armazenamento de blob de bloco|

2. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

## <a name="set-up-continuous-data-export"></a>Configuração da exportação contínua de dados

Agora que você tem um destino para exportar dados, siga essas etapas para configurar a exportação contínua de dados.

1. Entre no aplicativo IoT Central.

2. No painel esquerdo, selecione **Exportação de dados**.

    > [!Note]
    > Se você não ver Exportação de Dados no painel esquerdo, então você não terá permissões para configurar a exportação de dados em seu aplicativo. Contate o administrador para configurar a exportação de dados.

3. Selecione o botão **+ Novo** no canto superior direito. Escolha um dos Hubs de **Eventos Azure,** **Azure Service Bus**ou armazenamento **Azure Blob** como o destino da sua exportação. O número máximo de exportações por aplicação é de cinco.

    ![Criar exportação contínua de dados](media/howto-export-data/new-export-definition.png)

4. Na caixa de lista baixada, selecione o **namespace do Event Hubs,** **o espaço de nome do Service Bus,** o **namespace da conta de armazenamento**ou **insira uma seqüência de conexões**.

    - Você só vê contas de armazenamento, espaços de nome do Event Hubs e namespaces de Service Bus na mesma assinatura do aplicativo IoT Central. Caso deseje exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e veja a etapa 5.
    - Para aplicativos criados usando o plano de preços gratuitos, a única maneira de configurar a exportação contínua de dados é através de uma cadeia de conexão. Os aplicativos no plano de preços gratuitos não têm uma assinatura associada do Azure.

    ![Criar um novo Hub de Eventos](media/howto-export-data/export-event-hub.png)

5. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:
    - Event Hubs ou Service Bus, vá para o namespace no portal Azure.
        - Em **Configurações,** selecione **Políticas de acesso compartilhado**
        - Escolha a **RootManageSharedAccessKey** padrão ou crie uma
        - Copie a cadeia de conexão primária ou secundária
    - Conta de armazenamento, vá para a conta armazenamento no portal do Azure:
        - Em **Configurações,** selecione **teclas de acesso**
        - Copie a seqüência de conexão key1 ou a seqüência de conexão key2

6. Escolha um hub de eventos, fila, tópico ou contêiner na caixa de lista baixa.

7. Em **Dados para exportação,** escolha os tipos de dados a serem exportados definindo o tipo para **On**.

8. Para ativar a exportação contínua de dados, **certifique-se de que a alterdeativa ativada** está **ativada**. Selecione **Salvar**.

9. Após alguns minutos, seus dados aparecem no destino escolhido.

## <a name="export-contents-and-format"></a>Conteúdo de exportação e formato

Os dados de telemetria exportados contêm a totalidade da mensagem enviada aos seus dispositivos para a IoT Central, não apenas os valores de telemetria em si. Os dados dos dispositivos exportados contêm alterações nas propriedades e metadados de todos os dispositivos e os modelos de dispositivos exportados contêm alterações em todos os modelos de dispositivos.

Para Hubs de Eventos e Ônibus de Serviço, os dados são exportados em tempo quase real. Os dados estão na propriedade do corpo e estão no formato JSON (veja abaixo, por exemplo).

Para o Blob Storage, os dados são exportados uma vez por minuto, com cada arquivo contendo o lote de alterações desde o último arquivo exportado. Os dados exportados são colocados em três pastas no formato JSON. Os caminhos padrão em sua conta de armazenamento são:

- Telemetria: _{container}/{app-id}/telemetria/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Dispositivos: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Modelos de dispositivo: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Você pode navegar pelos arquivos exportados no portal Azure navegando até o arquivo e escolhendo a guia **Editar blob.**


## <a name="telemetry"></a>Telemetria

Para Hubs de Eventos e Ônibus de Serviço, uma nova mensagem é exportada rapidamente depois que a IoT Central recebe a mensagem de um dispositivo, e cada mensagem exportada contém a mensagem completa que o dispositivo enviou na propriedade do corpo no formato JSON.

Para o Blob Storage, as mensagens são em loteadas e exportadas uma vez por minuto. Os arquivos exportados usam o mesmo formato que os arquivos de mensagem exportados pelo [roteamento de mensagens do IoT Hub](../../iot-hub/tutorial-routing.md) para o armazenamento blob. 

> [!NOTE]
> Para o Blob Storage, certifique-se de `contentType: application/JSON` que `contentEncoding:utf-8` seus `utf-16` `utf-32`dispositivos estão enviando mensagens que têm e (ou ). Veja a documentação do [IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) como exemplo.

O dispositivo que enviou a telemetria é representado pelo ID do dispositivo (veja as seções a seguir). Para obter os nomes dos dispositivos, exporte dados do dispositivo e correlaciona cada mensagem usando a **conexãoDeviceId** que corresponde ao **dispositivoId** da mensagem do dispositivo.

Esta é uma mensagem de exemplo recebida em um hub de eventos ou fila de ônibus de serviço ou tópico.

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

Este é um exemplo de registro exportado para armazenamento blob:

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

Cada mensagem ou registro em um snapshot representa uma ou mais alterações em um dispositivo e suas propriedades de dispositivo e nuvem desde a última mensagem exportada. Isso inclui:

- `id` do dispositivo na IoT Central
- `displayName` do dispositivo
- ID do modelo do dispositivo em`instanceOf`
- `simulated`bandeira, verdade se o dispositivo é um dispositivo simulado
- `provisioned`bandeira, verdade se o dispositivo foi provisionado
- `approved`bandeira, verdade se o dispositivo foi aprovado para enviar dados
- Valores de propriedade
- `properties`incluindo valores de propriedades de dispositivos e nuvem

Os dispositivos excluídos não são exportados. Atualmente, não há indicadores em mensagens exportadas para dispositivos excluídos.

Para hubs de eventos e ônibus de serviço, as mensagens que contêm dados do dispositivo são enviadas para o seu hub de eventos ou fila de ônibus de serviço ou tópico em quase tempo real, como aparece na IoT Central. 

Para blob storage, um novo instantâneo contendo todas as alterações desde o último escrito é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre dispositivos e dados de propriedades no hub de eventos ou na fila ou tópico do Service Bus:

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

Este é um exemplo de instantâneo contendo dispositivos e dados de propriedades no Blob Storage. Os arquivos exportados contêm uma única linha por registro.

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

Para hubs de eventos e ônibus de serviço, mensagens contendo dados de modelo de dispositivo são enviadas para o seu hub de eventos ou fila de ônibus de serviço ou tópico em quase tempo real, como aparece na IoT Central. 

Para blob storage, um novo instantâneo contendo todas as alterações desde o último escrito é exportado uma vez por minuto.

Esta é uma mensagem de exemplo sobre dados de modelos de dispositivos no hub de eventos ou fila ou tópico do Service Bus:

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

Este é um exemplo de instantâneo contendo dispositivos e dados de propriedades no Blob Storage. Os arquivos exportados contêm uma única linha por registro.

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

Se você tiver uma exportação de dados existente em seu aplicativo de visualização com os fluxos de *modelos de dispositivos* e *dispositivos ligados,* você precisará atualizar sua exportação até **30 de junho de 2020**. Isso se aplica às exportações para o Azure Blob Storage, Azure Event Hubs e Azure Service Bus.

A partir de 3 de fevereiro de 2020, todas as novas exportações em aplicativos com modelos de dispositivos e dispositivos habilitados terão o formato de dados descrito acima. Todas as exportações criadas antes disso permanecerão no formato de dados antigos até 30 de junho de 2020, após o qual essas exportações serão automaticamente migradas para o novo formato de dados. O novo formato de dados corresponde ao [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/get)propriedade do [dispositivo,](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties) [propriedade da nuvem do dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties) e objetos de modelo do [dispositivo](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) na API pública IoT Central. 
 
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

Agora que você sabe como exportar seus dados para o Azure Event Hubs, Azure Service Bus e Azure Blob Storage, continue para o próximo passo:

> [!div class="nextstepaction"]
> [Como criar webhooks](./howto-create-webhooks.md)
