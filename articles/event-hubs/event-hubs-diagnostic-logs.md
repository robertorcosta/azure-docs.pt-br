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
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509747"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar logs de diagnóstico para um hub de eventos do Azure

É possível exibir dois tipos de logs para os Hubs de Eventos do Azure:

* **[Logs de atividade](../azure-monitor/platform/platform-logs-overview.md)**: esses logs têm informações sobre as operações realizadas em um trabalho. Os logs estão sempre habilitados. Você pode ver as entradas do log de atividades selecionando **log de atividades** no painel esquerdo do seu namespace do hub de eventos na portal do Azure. Por exemplo: "criar ou atualizar o namespace", "criar ou atualizar o Hub de eventos".

    ![Log de atividades para um namespace de hubs de eventos](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Logs diagnóstico](../azure-monitor/platform/platform-logs-overview.md)**: é possível configurar logs de diagnóstico para ter uma visão mais detalhada de tudo o que acontece com um trabalho. Os logs de diagnóstico abrangem atividades desde o momento em que o trabalho é criado até sua exclusão, incluindo atualizações e atividades que ocorrem durante a execução do trabalho.

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

- **Logs de arquivo morto**: logs relacionados aos arquivos mortos dos Hubs de Eventos, especificamente os logs relacionados a erros de arquivo morto.
- **Logs operacionais**: informações sobre o que está acontecendo durante a operação dos Hubs de Eventos, especificamente o tipo de operação, incluindo a criação do hub de eventos, os recursos usados e o status da operação.
- **Logs de escala automática**: informações sobre operações de dimensionamento automático feitas em um namespace de hubs de eventos. 
- **Logs do coordenador de Kafka** – informações sobre operações de coordenador de Kafka relacionadas aos hubs de eventos. 
- **Kafka logs de usuário**: informações sobre as operações de usuário do Kafka relacionadas aos hubs de eventos. 
- **Evento de conexão de rede virtual (VNet) dos hubs de**eventos: informações sobre eventos de conexão de rede virtual dos hubs de eventos. 
- **Logs de usuário de chave gerenciado pelo cliente**: informações sobre operações relacionadas à chave gerenciada pelo cliente. 


    Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito nas seções a seguir.

## <a name="archive-logs-schema"></a>Esquema dos logs de arquivo morto

As cadeias de caracteres JSON do log de arquivo morto incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
TaskName | Descrição da tarefa que falhou.
ActivityId | ID interna, usada para acompanhamento.
trackingId | ID interna, usada para acompanhamento.
resourceId | ID do Recurso do Azure Resource Manager.
eventHub | Nome completo do hub de eventos (inclui o nome do namespace).
partitionId | Partição do Hub de Eventos usada para gravação.
archiveStep | ArchiveFlushWriter
startTime | Hora de início da falha.
falhas | Número de vezes que a falha ocorreu.
durationInSeconds | Duração da falha.
mensagem | Mensagem de erro.
category | ArchiveLogs

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log de arquivo morto:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
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
ActivityId | ID interna, usada para fins de rastreamento.
EventName | Nome da operação.  
resourceId | ID do Recurso do Azure Resource Manager.
SubscriptionId | ID da assinatura.
EventTimeString | Tempo da operação.
EventProperties | Propriedades da operação.
Status | Status da operação.
Chamador | Chamador da operação (Portal do Azure ou cliente de gerenciamento).
category | OperationalLogs

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Esquema de logs de dimensionamento automático
O JSON de log de dimensionamento automático inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| trackingId | ID interna, que é usada para fins de rastreamento |
| resourceId | ID interna, que contém a ID da assinatura do Azure e o nome do namespace |
| mensagem | Mensagem informativa, que fornece detalhes sobre a ação de inflar automaticamente. A mensagem contém o valor anterior e o atual da unidade de taxa de transferência para um namespace específico e o que disparou o inflado da TU. |

## <a name="kafka-coordinator-logs-schema"></a>Esquema de logs do coordenador de Kafka
O JSON do log do coordenador de Kafka inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| requestId | ID da solicitação, que é usada para fins de rastreamento |
| resourceId | ID interna, que contém a ID da assinatura do Azure e o nome do namespace |
| operationName | Nome da operação que é feita durante a coordenação do grupo |
| clientId | ID do cliente |
| namespaceName | Nome do Namespace | 
| subscriptionId | ID de assinatura do Azure |
| mensagem | Mensagem informativa, que fornece detalhes sobre as ações feitas durante a coordenação do grupo de consumidores. |

## <a name="kafka-user-error-logs-schema"></a>Esquema de logs de erros do usuário Kafka
O JSON do log de erros do usuário Kafka inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- |
| trackingId | ID de rastreamento, que é usada para fins de rastreamento. |
| namespaceName | Nome do Namespace |
| eventhub | Nome do Hub de Eventos |
| partitionId | Partition ID |
| groupId | ID do Grupo |
| ClientId | ID do cliente |
| resourceId | ID interna, que contém a ID da assinatura do Azure e o nome do namespace |
| mensagem | Mensagem informativa, que fornece detalhes sobre um erro |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Esquema de evento de conexão de rede virtual dos hubs de eventos

O evento JSON de conexão de VNet (rede virtual) dos hubs de eventos inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---  | ----------- | 
| subscriptionId | ID de assinatura do Azure |
| namespaceName | Nome do Namespace |
| ipAddress | Endereço IP de um cliente que se conecta ao serviço de hubs de eventos |
| ação | Ação feita pelo serviço de hubs de eventos ao avaliar solicitações de conexão. As ações com suporte são **AcceptConnection** e **RejectConnection**. |
| reason | Fornece um motivo pelo qual a ação foi feita |
| count | Número de ocorrências para a ação especificada |
| resourceId | ID de recurso interno, que contém a ID da assinatura e o nome do namespace. |

## <a name="customer-managed-key-user-logs"></a>Logs de usuário da chave gerenciada pelo cliente
O JSON do log de usuário de chave gerenciada pelo cliente inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---- | ----------- | 
| category | Tipo de categoria para uma mensagem. É um dos seguintes valores: **erro** e **informações** |
| resourceId | ID de recurso interno, que inclui a ID da assinatura do Azure e o nome do namespace |
| keyVault | Nome do recurso de Key Vault |
| chave | Nome da chave de Key Vault. |
| Versão | Versão da chave de Key Vault |
| operação | O nome de uma operação feita para atender a solicitações |
| code | Código de status |
| mensagem | Mensagem, que fornece detalhes sobre um erro ou uma mensagem informativa |



## <a name="next-steps"></a>Próximas etapas
- [Introdução aos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Visão geral de API de Hubs de Eventos](event-hubs-api-overview.md)
- Introdução aos Hubs de Eventos
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
