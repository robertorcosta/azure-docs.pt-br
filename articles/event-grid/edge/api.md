---
title: REST API - Azure Event Grid IoT Edge | Microsoft Docs
description: API REST na Grade de Eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841822"
---
# <a name="rest-api"></a>API REST
Este artigo descreve as APIs REST do Azure Event Grid on IoT Edge

## <a name="common-api-behavior"></a>Comportamento comum da API

### <a name="base-url"></a>URL base
Event Grid on IoT Edge tem as seguintes APIs expostas sobre HTTP (porta 5888) e HTTPS (porta 4438).

* URL base para HTTP:http://eventgridmodule:5888
* URL base para HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Cadeia de consulta de solicitação
Todas as solicitações de API requerem o seguinte parâmetro de seqüência de consulta:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Solicitar tipo de conteúdo
Todas as solicitações de API devem ter um **tipo de conteúdo**.

No caso de **EventGridSchema** ou **CustomSchema,** o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

No caso de **CloudEventSchemaV1_0** no modo estruturado, o valor do Tipo de Conteúdo pode ser um dos seguintes valores:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Em caso de **CloudEventSchemaV1_0** no modo binário, consulte a [documentação](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) para obter detalhes.

### <a name="error-response"></a>Resposta de erro
Todas as APIs retornam um erro com a seguinte carga útil:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Gerenciar tópicos

### <a name="put-topic-create--update"></a>Colocar tópico (criar/ atualizar)

**Solicitação:**``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Carga útil:**

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Resposta**: HTTP 200

**Carga útil:**

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Obter tópico

**Solicitação:**``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Obter todos os tópicos

**Solicitação:**``` GET /topics?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Excluir tópico

**Solicitação:**``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200, carga útil vazia

## <a name="manage-event-subscriptions"></a>Gerencie assinaturas de eventos
As amostras nesta `EndpointType=Webhook;`seção usam . As amostras de `EndpointType=EdgeHub / EndpointType=EventGrid` json estão na próxima seção. 

### <a name="put-event-subscription-create--update"></a>Coloque a assinatura do evento (criar/atualizar)

**Solicitação:**``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Carga útil:**
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Resposta**: HTTP 200

**Carga útil:**

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Obtenha assinatura do evento

**Solicitação:**``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Obtenha assinaturas de eventos

**Solicitação:**``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Resposta**: HTTP 200

**Carga útil:**
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Excluir assinatura do evento

**Solicitação:**``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Resposta:** HTTP 200, sem carga útil


## <a name="publish-events-api"></a>Publicar a API de eventos

### <a name="send-batch-of-events-in-event-grid-schema"></a>Enviar lote de eventos (em esquema de Grade de Eventos)

**Solicitação:**``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Resposta**: HTTP 200, carga útil vazia


**Descrições do campo de carga**
- ```Id```é obrigatório. Pode ser qualquer valor de string que é povoado pelo chamador. Event Grid NÃO faz qualquer detecção duplicada ou aplica qualquer semântica neste campo.
- ```Topic```é opcional, mas se especificado deve corresponder ao topic_name da URL de solicitação
- ```Subject```é obrigatório, pode ser qualquer valor de string
- ```EventType```é obrigatório, pode ser qualquer valor de string
- ```EventTime```é obrigatório, não é validado, mas deve ser um DateTime adequado.
- ```DataVersion``` é obrigatório
- ```MetadataVersion```é opcional, se especificado deve ser uma string com o valor```"1"```
- ```Data```é opcional, e pode ser qualquer token JSON (número, string, booleano, matriz, objeto)

### <a name="send-batch-of-events-in-custom-schema"></a>Enviar lote de eventos (em esquema personalizado)

**Solicitação:**``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Resposta**: HTTP 200, carga útil vazia


**Restrições de carga**
- Deve ser uma série de eventos.
- Cada entrada de matriz DEVE ser um objeto JSON.
- Nenhuma outra restrição (além do tamanho da carga).

## <a name="examples"></a>Exemplos

### <a name="set-up-topic-with-eventgrid-schema"></a>Configurar tópico com esquema EventGrid
Configura um tópico para exigir que os eventos sejam publicados em **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Configurar tópico com esquema personalizado
Configura um tópico para exigir que `customschema`os eventos sejam publicados em .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Configurar tópico com esquema de eventos na nuvem
Configura um tópico para exigir que `cloudeventschema`os eventos sejam publicados em .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Configure o WebHook como destino, eventos a serem entregues em eventgridschema
Use este tipo de destino para enviar eventos para qualquer outro módulo (que hospeda um ponto final HTTP) ou para qualquer ponto final endereçado HTTP na rede/internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Restrições ao `endpointUrl` atributo:
- Deve ser não nulo.
- Deve ser uma URL absoluta.
- Se outbound__webhook__httpsOnly for definido como verdadeiro nas configurações do EventGridModule, ele deve ser apenas HTTPS.
- Se outbound__webhook__httpsOnly definido como falso, pode ser HTTP ou HTTPS.

Restrições na `eventDeliverySchema` propriedade:
- Deve corresponder ao esquema de entrada do tópico de subscrição.
- Pode ser nula. Ele é padrão para o esquema de entrada do tópico.

### <a name="set-up-iot-edge-as-destination"></a>Configurar ioT Edge como destino

Use este destino para enviar eventos para o IoT Edge Hub e ser submetido ao subsistema de roteamento/filtragem/encaminhamento do Edge Hub.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Configure event grid cloud como destino

Use este destino para enviar eventos para Event Grid na nuvem (Azure). Você precisará primeiro configurar um tópico de usuário na nuvem para o qual os eventos devem ser enviados, antes de criar uma assinatura de evento na borda.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

Url endpoint:
- Deve ser não nulo.
- Deve ser uma URL absoluta.
- O `/api/events` caminho deve ser definido no caminho da URL de solicitação.
- Deve ter `api-version=2018-01-01` na seqüência de consultas.
- Se outbound__eventgrid__httpsOnly for definida como verdadeira nas configurações eventGridModule (verdade por padrão), ela deve ser apenas HTTPS.
- Se outbound__eventgrid__httpsOnly for definido como falso, pode ser HTTP ou HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames for definida como falsa (falsa por padrão), ela deve direcionar um dos seguintes pontos finais:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve ser nulo.

Nome do tópico:
- Se a Assinatura.EventDeliverySchema estiver definida como EventGridSchema, o valor deste campo será colocado no campo Tópico de cada evento antes de ser encaminhado para a Grade de Eventos na nuvem.
- Se a assinatura.EventDeliverySchema estiver definida como CustomEventSchema, essa propriedade será ignorada e a carga de evento personalizada será encaminhada exatamente como foi recebida.

## <a name="set-up-event-hubs-as-a-destination"></a>Configure hubs de eventos como destino

Para publicar em um Event `endpointType` `eventHub` Hub, defina o e forneça:

* conexãoString: seqüência de conexão para o Hub de eventos específico que você está direcionando gerado por meio de uma Diretiva de Acesso Compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Você pode gerar uma seqüência de conexão específica da entidade navegando para o Centro de Eventos específico que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configure filas de ônibus de serviço como destino

Para publicar em uma fila de `endpointType` `serviceBusQueue` ônibus de serviço, defina o e forneça:

* conexãoString: seqüência de conexão para a fila de barramento de serviço específica que você está direcionando gerada através de uma Diretiva de Acesso Compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Gere uma seqüência de conexão específica da entidade navegando para a fila de ônibus de serviço específica que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configure tópicos de barramento de serviço como destino

Para publicar em um tópico `endpointType` de `serviceBusTopic` barramento de serviço, defina o e forneça:

* conexãoString: seqüência de conexão para o tópico específico do barramento de serviço que você está direcionando gerado por meio de uma diretiva de acesso compartilhado.

    >[!NOTE]
    > A seqüência de conexões deve ser específica da entidade. Usar uma seqüência de conexão namespace não funcionará. Gere uma seqüência de conexão específica da entidade navegando para o tópico específico de barramento de serviço que você gostaria de publicar no Portal Azure e clicando em **políticas de acesso compartilhado** para gerar uma nova seqüência de connecção específica da entidade.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Configurar filas de armazenamento como destino

Para publicar em uma fila `endpointType` de `storageQueue` armazenamento, defina o e forneça:

* queueNome: Nome da fila de armazenamento para a que você está publicando.
* conexãoString: string de conexão para a conta de armazenamento em que a fila de armazenamento está.

    >[!NOTE]
    > Hubs de eventos desalinhados, filas de ônibus de serviço e tópicos de barramento de serviço, a seqüência de conexão usada para filas de armazenamento não é específica da entidade. Em vez disso, deve apenas a seqüência de conexão para a conta de armazenamento.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```