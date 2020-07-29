---
title: Monitorar a integridade de seu Hub IoT do Azure | Microsoft Docs
description: Use o Azure Monitor e o Azure Resource Health para monitorar seu Hub IoT Hub e diagnosticar problemas rapidamente
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
ms.openlocfilehash: 140bbc698db773f851cf03d80e8e51be9c2a21c3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327541"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorar a integridade do Hub IoT do Azure e diagnosticar problemas rapidamente

As empresas que implementam o Hub IoT do Azure esperam um desempenho confiável de seus recursos. Para ajudá-lo a manter um monitoramento em suas operações, o Hub IoT é totalmente integrado ao [Azure Monitor](../azure-monitor/index.yml) e o [Azure Resource Health](../service-health/resource-health-overview.md). Esses dois serviços funcionam para fornecer os dados necessários para manter suas soluções de IoT em funcionamento em um estado íntegro.

O Azure Monitor é uma fonte única de monitoramento e registro em log para todos os serviços do Azure. Você pode enviar os logs de diagnóstico que Azure Monitor gera para Azure Monitor logs, hubs de eventos ou armazenamento do Azure para processamento personalizado. As configurações de diagnóstico e métricas do Azure Monitor oferecem a visibilidade sobre o desempenho de seus recursos. Continue lendo este artigo para saber como [Usar o Azure Monitor](#use-azure-monitor) com o Hub IoT. 

> [!IMPORTANT]
> Não há garantia de que os eventos emitidos pelo serviço Hub IoT usando logs de diagnóstico do Azure Monitor sejam confiáveis ou ordenados. Alguns eventos podem ser perdidos ou entregues fora de ordem. Os logs de diagnóstico também não devem ser em tempo real, e pode levar vários minutos para que os eventos sejam registrados na sua opção de destino.

O Azure Resource Health ajuda você a diagnosticar e a obter suporte quando um problema com o Azure afeta seus recursos. Um painel fornece o status de integridade atual e anterior para cada um dos seus hubs IoT. Continue na seção na parte inferior deste artigo para saber como [usar Azure Resource Health](#use-azure-resource-health) com o Hub IOT. 

O Hub IoT também fornece suas próprias métricas que você pode usar para entender o estado de seus recursos de IoT. Para obter mais informações, consulte [Entender as métricas do Hub IoT](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Usar o Azure Monitor

O Azure Monitor fornece informações de diagnóstico para recursos do Azure, o que significa que você pode monitorar as operações que são executadas em seu hub IoT.

Para saber mais sobre as métricas e os eventos que o Azure Monitor inspeciona, consulte [Métricas com suporte com o Azure Monitor](../azure-monitor/platform/metrics-supported.md) e [Serviços, esquemas e categorias com suporte para Logs de Diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Compreender os logs

O Azure Monitor controla diferentes operações que ocorrem no Hub IoT. Cada categoria tem um esquema que define como os eventos nessa categoria são relatados.

#### <a name="connections"></a>conexões

O dispositivo de faixas de categoria conexões conecta e desconecta eventos um hub IoT, bem como erros. Esta categoria é útil para identificar tentativas de conexão não autorizadas e/ou alertas ao perder a conexão aos dispositivos.

> [!NOTE]
> Para obter um status de conexão confiável dos dispositivos, marque [Pulsação do dispositivo](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Comandos da nuvem para o dispositivo

A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e são relacionados ao pipeline de mensagem da nuvem para o dispositivo. Essa categoria inclui erros que ocorrem ao:

* Enviar mensagens de nuvem para dispositivo (como erros de remetente não autorizado),
* Receber mensagens da nuvem para dispositivo (como erros de contagem de entrega excedida), e
* Receber comentários de mensagem da nuvem para dispositivo (como erros de comentário expirados).

Essa categoria não capturará erros quando a mensagem da nuvem para dispositivo for entregue com êxito, mas for manipulada incorretamente pelo dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Rotas

A categoria [Roteamento de mensagens](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) rastreia erros que ocorrem durante a avaliação de rota de mensagem e integridade do ponto de extremidade, conforme percebido pelo Hub IOT. Essa categoria inclui eventos, como:

* Uma regra é avaliada como "não definida";
* O Hub IoT marca um ponto de extremidade como inativo; ou
* Quaisquer erros recebidos de um ponto de extremidade.

Essa categoria não inclui erros específicos sobre as próprias mensagens (como erros de limitação de dispositivo), que são relatados na categoria "telemetria de dispositivo".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Aqui estão mais detalhes sobre o roteamento de logs de diagnóstico:

* [Lista de códigos de erro de log de diagnóstico de roteamento](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista de operaçõesnames dos logs de diagnóstico de roteamento](troubleshoot-message-routing.md#diagnostics-operation-names)

#### <a name="device-telemetry"></a>Telemetria de dispositivo

A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Operações de upload de arquivo

A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui:

* Erros que ocorrem com o URI de SAS, como quando ele expirar antes que um dispositivo notifique o hub de um carregamento concluído.

* Carregamentos com falha relatados pelo dispositivo.

* Erros que ocorrem quando um arquivo não for encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT.

Essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo está carregando um arquivo no armazenamento.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operações de dispositivo gêmeo para nuvem

A categoria de operações de gêmeos de nuvem para dispositivo rastreia eventos iniciados pelo serviço em dispositivos gêmeos. Essas operações podem incluir obter gêmeo, atualizar ou substituir marcas e atualizar ou substituir propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operações de nuvem gêmea para dispositivo

A categoria de operações de gêmeos de dispositivo para nuvem rastreia eventos iniciados pelo dispositivo em dispositivos gêmeos. Essas operações podem incluir gêmeos, atualizar as propriedades relatadas e assinar as propriedades desejadas.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Consultas de gêmeos

A categoria de consultas de gêmeos relata sobre solicitações de consulta para dispositivos gêmeos que são iniciados na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Operações de trabalhos

A categoria de operações de trabalhos relata sobre solicitações de trabalho para atualizar dispositivos gêmeos ou invocar métodos diretos em vários dispositivos. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Métodos diretos

A categoria de métodos diretos rastreia as interações solicitação-resposta enviadas a dispositivos individuais. Essas solicitações são iniciadas na nuvem.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Rastreamento Distribuído (Versão Prévia)

A categoria de rastreamento distribuído rastreia as IDs de correlação para mensagens que carregam o cabeçalho de contexto de rastreamento. Para habilitar esses logs totalmente, o código do lado do cliente deve ser atualizado ao seguir [analisar e diagnosticar aplicativos IOT de ponta a ponta com o rastreamento distribuído do Hub IOT (versão prévia)](iot-hub-distributed-tracing.md).

Observe que `correlationId` está em conformidade com a proposta de [contexto de rastreamento W3C](https://github.com/w3c/trace-context) , em que ele contém um, `trace-id` bem como um `span-id` .

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Logs D2C do Hub IoT (dispositivo para nuvem)

O Hub IoT registra esse log quando uma mensagem que contém propriedades de rastreamento válidas chega ao Hub IoT.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Aqui, `durationMs` não é calculado, uma vez que o relógio do Hub IoT não pode ser sincronizado com o relógio do dispositivo e, portanto, um cálculo de duração pode ser enganoso. Recomendamos gravar escrever lógica usando carimbos de data/hora na seção `properties` para capturar os picos na latência de dispositivo para nuvem.

| Propriedade | Type | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Inteiro | O tamanho da mensagem de dispositivo para nuvem em bytes |
| **deviceId** | Cadeia de caracteres alfanumérica ASCII de 7 bits | A identidade do dispositivo |
| **callerLocalTimeUtc** | Carimbo de data/hora UTC | A hora de criação da mensagem conforme relatada pelo relógio local do dispositivo |
| **calleeLocalTimeUtc** | Carimbo de data/hora UTC | A hora de chegada da mensagem no gateway do Hub IoT conforme relatado pelo relógio do lado do serviço de Hub IoT |

##### <a name="iot-hub-ingress-logs"></a>Logs de entrada do Hub IoT

O Hub IoT registra esse log quando a mensagem que contém as propriedades de rastreamento válido grava no Hub de Eventos interno ou integrado.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` seção, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Type | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Cadeia de caracteres | Verdadeiro ou falso, indica se o roteamento de mensagens está ou não habilitado no Hub IoT |
| **parentSpanId** | Cadeia de caracteres | A [ID do span](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria, neste caso, o rastreamento de mensagens D2C |

##### <a name="iot-hub-egress-logs"></a>Logs de saída do Hub IoT

O Hub IoT registra esse log quando [roteamento](iot-hub-devguide-messages-d2c.md) está habilitado e a mensagem é gravada para um [ponto de extremidade](iot-hub-devguide-endpoints.md). Se o roteamento não estiver habilitado, o Hub IoT não registrará esse log.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Na `properties` seção, esse log contém informações adicionais sobre a entrada da mensagem.

| Propriedade | Type | Descrição |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Cadeia de caracteres | O nome do ponto de extremidade de roteamento |
| **endpointType** | Cadeia de caracteres | O tipo de roteamento o ponto de extremidade |
| **parentSpanId** | Cadeia de caracteres | A [ID do span](https://w3c.github.io/trace-context/#parent-id) da mensagem pai, que seria, neste caso, o rastreamento de mensagens de entrada do Hub IoT |

#### <a name="configurations"></a>Configurações

Os logs de configuração do Hub IoT rastreia eventos e erros para o conjunto de recursos de gerenciamento automático de dispositivos.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Fluxos de dispositivo (visualização)

A categoria fluxos de dispositivo rastreia as interações de solicitação-resposta enviadas a dispositivos individuais.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Ler logs de Hubs de Eventos do Azure

Depois de configurar o log de eventos por meio das configurações de diagnóstico, é possível criar aplicativos que leem logs de forma que você possa tomar uma ação com base nas informações neles. Esse código de exemplo recupera os logs de um hub de eventos:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="use-azure-resource-health"></a>Usar o Azure Resource Health

Use o Azure Resource Health para monitorar se o Hub IoT está em execução. Você também pode saber se uma interrupção regional está afetando a integridade do seu Hub IoT. Para entender os detalhes específicos sobre o estado de integridade de seu Hub IoT do Azure, recomendamos que você [Use o Azure Monitor](#use-azure-monitor).

O Hub IoT do Azure indica a integridade em um nível regional. Se houver uma interrupção regional afetando seu hub IoT, o status de integridade será mostrado como **Desconhecido**. Para saber mais, consulte [Tipos de recursos e verificações de integridade no Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Para verificar a integridade de seus Hubs IoT, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até **serviço**integridade de  >  **recursos**integridade.

3. Nas caixas suspensas, selecione sua assinatura e selecione **Hub IOT** como o tipo de recurso.

Para saber mais sobre como interpretar os dados de integridade, confira [visão geral do Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Entender as métricas de Hub IoT](iot-hub-metrics.md)
* [Monitoramento remoto IoT e notificações com Aplicativos Lógicos do Azure conectando o Hub IoT e a caixa de correio](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
