---
title: Logs de diagnóstico para Conexões Híbridas
description: Este artigo fornece uma visão geral de todos os logs de atividade e de diagnóstico que estão disponíveis para a retransmissão do Azure.
services: service-bus-messaging
author: spelluru
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: b7ac5f1da70352115bf05df1a61120f46a85ec5d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783455"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Habilitar logs de diagnóstico para retransmissão do Azure Conexões Híbridas
Ao começar a usar o Conexões Híbridas de retransmissão do Azure, convém monitorar como e quando seus ouvintes e remetentes são abertos e fechados e como suas Conexões Híbridas são criadas e as mensagens são enviadas. Este artigo fornece uma visão geral dos logs de atividade e de diagnóstico fornecidos pelo serviço de retransmissão do Azure. 

Você pode exibir dois tipos de logs para a retransmissão do Azure:

- [Logs de atividade](../azure-monitor/platform/platform-logs-overview.md): esses logs têm informações sobre as operações executadas em relação ao namespace no portal do Azure ou por meio do modelo de Azure Resource Manager. Esses logs estão sempre habilitados. Por exemplo: "criar ou atualizar namespace", "criar ou atualizar conexão híbrida". 
- [Logs de diagnóstico](../azure-monitor/platform/platform-logs-overview.md): você pode configurar os logs de diagnóstico para uma exibição mais rica de tudo o que acontece com operações e ações que são realizadas em seu namespace usando a API ou o SDK de linguagem.

## <a name="view-activity-logs"></a>Exibir logs de atividade
Para exibir os logs de atividade do namespace de retransmissão do Azure, alterne para a página **log de atividades** no portal do Azure.

![Retransmissão do Azure-log de atividades](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico

> [!NOTE]
> Os logs de diagnóstico estão disponíveis apenas para Conexões Híbridas, não para a retransmissão do Windows Communication Foundation (WCF).

Para habilitar os logs de diagnóstico, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com), acesse o namespace de retransmissão do Azure e, em **monitoramento**, selecione **configurações de diagnóstico**.
1. Na página **configurações de diagnóstico** , selecione **Adicionar configuração de diagnóstico**.  

   ![O link "adicionar configuração de diagnóstico"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Defina as configurações de diagnóstico seguindo estas etapas:
    1. Na caixa **nome** , insira um nome para as configurações de diagnóstico.  
    2. Selecione **HybridConnectionsEvent** para o tipo de log. 
    3. Selecione um dos três **destinos** a seguir para seus logs de diagnóstico:  
        1. Se você selecionar **arquivar em uma conta de armazenamento**, configure a conta de armazenamento na qual os logs de diagnóstico serão armazenados.  
        2. Se você selecionar **fluxo para um hub de eventos**, configure o Hub de eventos para o qual você deseja transmitir os logs de diagnóstico.
        3. Se você selecionar **Enviar para log Analytics**, especifique a qual instância do log Analytics o diagnóstico será enviado.  

        ![Configurações de diagnóstico de exemplo](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selecione **salvar** na barra de ferramentas para salvar as configurações.

As novas configurações entram em vigor em cerca de 10 minutos. Os logs são exibidos no destino de arquivamento configurado, no painel **logs de diagnóstico** . Para obter mais informações sobre como definir as configurações de diagnóstico, consulte a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Esquema para eventos de conexões híbridas
As cadeias de caracteres JSON de log de eventos de conexões híbridas incluem os elementos listados na tabela a seguir:

| Nome | Descrição |
| ------- | ------- |
| ResourceId | ID de recurso do Azure Resource Manager |
| ActivityId | ID interna, usada para identificar a operação especificada. Também pode ser conhecido como "TrackingID" |
| Ponto de extremidade | O endereço do recurso de retransmissão |
| OperationName | O tipo da operação de Conexões Híbridas que está sendo registrada |
| EventTimeString | O carimbo de data/hora UTC do registro de log |
| Mensagem | A mensagem detalhada do evento |
| Categoria | Categoria do evento. Atualmente, há apenas `HybridConnectionsEvents`. 


## <a name="sample-hybrid-connections-event"></a>Evento de conexões híbridas de exemplo
Veja um exemplo de evento de conexões híbridas no formato JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Eventos e operações capturados nos logs de diagnóstico

| Operação | Descrição | 
| --------- | ----------- | 
| AuthorizationFailed | Falha na autorização.|
| InvalidSasToken | Token SAS inválido. | 
| ListenerAcceptingConnection | O ouvinte está aceitando a conexão. |
| ListenerAcceptingConnectionTimeout | O tempo limite da conexão do ouvinte foi atingido. |
| ListenerAcceptingHttpRequestFailed | Falha na solicitação HTTP de aceitação de ouvinte devido a uma exceção. |
| ListenerAcceptingRequestTimeout | O tempo limite da solicitação de aceitação do ouvinte foi atingido. |  
| ListenerClosingFromExpiredToken | O ouvinte está fechando porque o token de segurança expirou. | 
| ListenerRejectedConnection | O ouvinte rejeitou a conexão. |
| ListenerReturningHttpResponse | O ouvinte está retornando uma resposta HTTP. |  
| ListenerReturningHttpResponseFailed | O ouvinte está retornando uma resposta HTTP com um código de falha. | 
 ListenerSentHttpResponse | O serviço de retransmissão recebeu uma resposta HTTP do ouvinte. | 
| ListenerUnregistered | O registro do ouvinte foi cancelado. | 
| ListenerUnresponsive | O ouvinte não está respondendo ao retornar uma resposta. | 
| MessageSendingToListener | A mensagem está sendo enviada para o ouvinte. |
| MessageSentToListener | A mensagem é enviada ao ouvinte. | 
| NewListenerRegistered | Novo ouvinte registrado. |
| NewSenderRegistering | O novo remetente está se registrando. | 
| ProcessingRequestFailed | Falha no processamento de uma operação de conexão híbrida. | 
| SenderConnectionClosed | A conexão do remetente está fechada. |
| SenderListenerConnectionEstablished | O remetente e o ouvinte estabeleceram a conexão com êxito. |
| SenderSentHttpRequest | O remetente enviou uma solicitação HTTP. | 


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a retransmissão do Azure, consulte:

* [Introdução à retransmissão do Azure](relay-what-is-it.md)
* [Introdução às Conexões Híbridas de Retransmissão](relay-hybrid-connections-dotnet-get-started.md)
