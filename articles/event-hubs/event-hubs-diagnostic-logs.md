---
title: Configurar logs de diagnóstico – Hub de Eventos do Azure | Microsoft Docs
description: Saiba como configurar logs de atividade e de diagnóstico para Hubs de Eventos no Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691850"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar logs de diagnóstico para um hub de eventos do Azure

É possível exibir dois tipos de logs para os Hubs de Eventos do Azure:

* **[Logs de atividade](../azure-monitor/platform/platform-logs-overview.md)**: esses logs têm informações sobre as operações realizadas em um trabalho. Os logs estão sempre habilitados. Você pode ver as entradas do log de atividades selecionando **log de atividades** no painel esquerdo do seu namespace do hub de eventos na portal do Azure. Por exemplo: "criar ou atualizar o namespace", "criar ou atualizar o Hub de eventos".

    ![Log de atividades para um namespace de hubs de eventos](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md)**: os logs de diagnóstico fornecem informações mais sofisticadas sobre operações e ações que são realizadas em seu namespace usando a API ou por meio de clientes de gerenciamento no SDK do idioma. 
    
    A seção a seguir mostra como habilitar os logs de diagnóstico para um namespace de hubs de eventos.

## <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico
Os logs de diagnóstico estão desabilitados por padrão. Para habilitar logs de diagnóstico, siga estas etapas:

1.  Na [portal do Azure](https://portal.azure.com), navegue até o namespace de seus hubs de eventos. 
2. Selecione **configurações de diagnóstico** em **monitoramento** no painel esquerdo e, em seguida, selecione **+ Adicionar configuração de diagnóstico**. 

    ![Página Configurações de diagnóstico – adicionar configuração de diagnóstico](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Na seção **detalhes da categoria** , selecione os **tipos de logs de diagnóstico** que você deseja habilitar. Você encontrará detalhes sobre essas categorias posteriormente neste artigo. 
5. Na seção **detalhes de destino** , defina o destino de arquivo morto (destino) que você deseja; por exemplo, uma conta de armazenamento, um hub de eventos ou um espaço de trabalho Log Analytics.

    ![Página Adicionar configurações de diagnóstico](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selecione **salvar** na barra de ferramentas para salvar as configurações de diagnóstico.

    As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**.

    Para saber mais sobre como configurar um diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias dos logs de diagnóstico

Os hubs de eventos capturam os logs de diagnóstico para as seguintes categorias:

| Categoria | Descrição | 
| -------- | ----------- | 
| Logs de arquivo | Captura informações sobre operações de [captura de hubs de eventos](event-hubs-capture-overview.md) , especificamente, logs relacionados a erros de captura. |
| Logs operacionais | Capture todas as operações de gerenciamento executadas no namespace dos hubs de eventos do Azure. As operações de dados não são capturadas devido ao alto volume de operações de dados que são realizadas nos hubs de eventos do Azure. |
| Logs de dimensionamento automático | Captura operações de inflar automaticamente realizadas em um namespace de hubs de eventos. |
| Logs do coordenador de Kafka | Captura operações de coordenador de Kafka relacionadas aos hubs de eventos. |
| Logs de erros do usuário Kafka | Captura informações sobre APIs Kafka chamadas em hubs de eventos. |
| Evento de conexão de rede virtual (VNet) dos hubs de eventos | Captura informações sobre endereços IP e redes virtuais enviando tráfego para os hubs de eventos. |
| Logs de usuário da chave gerenciada pelo cliente | Captura operações relacionadas à chave gerenciada pelo cliente. |


Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito nas seções a seguir.

## <a name="archive-logs-schema"></a>Esquema dos logs de arquivo morto

As cadeias de caracteres JSON do log de arquivo morto incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
TaskName | Descrição da tarefa que falhou
ActivityId | ID interna, usada para acompanhamento
trackingId | ID interna, usada para acompanhamento
resourceId | ID de recurso do Azure Resource Manager
eventHub | Nome completo do hub de eventos (inclui o nome do namespace)
partitionId | Partição do hub de eventos sendo gravada
archiveStep | valores possíveis: ArchiveFlushWriter, DestinationInit
startTime | Hora de início de falha
falhas | Número de vezes que a falha ocorreu
durationInSeconds | Duração de falha
mensagem | Mensagem de erro
category | ArchiveLogs

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log de arquivo morto:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Esquema de logs operacionais

As cadeias de caracteres JSON do log operacional incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
ActivityId | ID interna, usada para fins de acompanhamento |
EventName | Nome da operação |
resourceId | ID de recurso do Azure Resource Manager |
SubscriptionId | ID da assinatura |
EventTimeString | Tempo de operação |
EventProperties | Propriedades da operação |
Status | Status da operação |
Chamador | Chamador da operação (portal do Azure ou cliente de gerenciamento) |
Categoria | OperationalLogs |

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Esquema de logs de dimensionamento automático
O JSON de log de dimensionamento automático inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| TrackingId | ID interna, que é usada para fins de rastreamento |
| ResourceId | ID do Recurso do Azure Resource Manager. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre a ação de inflar automaticamente. A mensagem contém o valor anterior e o atual da unidade de taxa de transferência para um namespace específico e o que disparou o inflado da TU. |

## <a name="kafka-coordinator-logs-schema"></a>Esquema de logs do coordenador de Kafka
O JSON do log do coordenador de Kafka inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| RequestId | ID da solicitação, que é usada para fins de rastreamento |
| ResourceId | ID de recurso do Azure Resource Manager |
| Operação | Nome da operação que é feita durante a coordenação do grupo |
| ClientId | ID do cliente |
| NamespaceName | Nome do Namespace | 
| SubscriptionId | ID de assinatura do Azure |
| Mensagem | Mensagem informativa ou de aviso, que fornece detalhes sobre as ações feitas durante a coordenação do grupo. |

### <a name="example"></a>Exemplo

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Esquema de logs de erros do usuário Kafka
O JSON do log de erros do usuário Kafka inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- |
| TrackingId | ID de rastreamento, que é usada para fins de rastreamento. |
| NamespaceName | Nome do Namespace |
| Eventhub | Nome do Hub de Eventos |
| PartitionId | Partition ID |
| GroupId | ID do Grupo |
| ClientId | ID do cliente |
| ResourceId | ID do Recurso do Azure Resource Manager. |
| Mensagem | Mensagem informativa, que fornece detalhes sobre um erro |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Esquema de evento de conexão de rede virtual dos hubs de eventos

O evento JSON de conexão de VNet (rede virtual) dos hubs de eventos inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---  | ----------- | 
| SubscriptionId | ID de assinatura do Azure |
| NamespaceName | Nome do Namespace |
| IPAddress | Endereço IP de um cliente que se conecta ao serviço de hubs de eventos |
| Ação | Ação feita pelo serviço de hubs de eventos ao avaliar solicitações de conexão. As ações com suporte são **aceitar conexão** e **negar conexão**. |
| Motivo | Fornece um motivo pelo qual a ação foi feita |
| Contagem | Número de ocorrências para a ação especificada |
| ResourceId | ID do Recurso do Azure Resource Manager. |

### <a name="example"></a>Exemplo

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Logs de usuário da chave gerenciada pelo cliente
O JSON do log de usuário de chave gerenciada pelo cliente inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| Categoria | Tipo de categoria para uma mensagem. É um dos seguintes valores: **erro** e **informações** |
| ResourceId | ID de recurso interno, que inclui a ID da assinatura do Azure e o nome do namespace |
| KeyVault | Nome do recurso de Key Vault |
| Chave | Nome da chave de Key Vault. |
| Versão | Versão da chave de Key Vault |
| Operação | O nome de uma operação feita para atender a solicitações |
| Código | Código de status |
| Mensagem | Mensagem, que fornece detalhes sobre um erro ou uma mensagem informativa |



## <a name="next-steps"></a>Próximas etapas
- [Introdução aos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Visão geral de API de Hubs de Eventos](event-hubs-api-overview.md)
- Introdução aos Hubs de Eventos
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
