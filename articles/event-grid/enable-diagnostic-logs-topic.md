---
title: Grade de eventos do Azure – Habilitar logs de diagnóstico para tópicos ou domínios
description: Este artigo fornece instruções passo a passo sobre como habilitar os logs de diagnóstico para um tópico da grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: 13a2168c854475b841b0ebc52bb678c7ca22a1bb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626455"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Habilitar os logs de diagnóstico para os tópicos ou domínios da grade de eventos do Azure
As configurações de diagnóstico permitem que os usuários da grade de eventos capturem e exibam logs de **falha de publicação e entrega** em uma conta de armazenamento, em um hub de eventos ou em um espaço de trabalho log Analytics. Este artigo fornece instruções passo a passo para habilitar essas configurações em um tópico da grade de eventos.

## <a name="prerequisites"></a>Pré-requisitos

- Um tópico de grade de eventos provisionado
- Um destino provisionado para capturar logs de diagnóstico. Ele pode ser um dos seguintes destinos no mesmo local que o tópico da grade de eventos:
    - Conta de Armazenamento do Azure
    - Hub de Eventos
    - Espaço de trabalho do Log Analytics

## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Etapas para habilitar os logs de diagnóstico para um tópico

> [!NOTE]
> O procedimento a seguir fornece instruções passo a passo para habilitar os logs de diagnóstico para um tópico. As etapas para habilitar os logs de diagnóstico para um domínio são muito semelhantes. Na etapa 2, navegue até o **domínio** da grade de eventos na portal do Azure.  

1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue até o tópico da grade de eventos para o qual você deseja habilitar as configurações do log de diagnóstico. 
3. Selecione **configurações de diagnóstico** em **monitoramento** no menu à esquerda.
4. Na página **configurações de diagnóstico** , selecione **Adicionar nova configuração de diagnóstico**. 
    
    ![Botão Adicionar configuração de diagnóstico](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Especifique um **nome** para a configuração de diagnóstico. 
7. Selecione as opções **DeliveryFailures** e **PublishFailures** na seção **log** . 
    ![Selecionar as falhas](./media/enable-diagnostic-logs-topic/log-failures.png)
6. Habilite um ou mais dos destinos de captura para os logs e, em seguida, configure-os selecionando um recurso de captura criado anteriormente. 
    - Se você selecionar **arquivar em uma conta de armazenamento**, selecione **conta de armazenamento-configurar**e, em seguida, selecione a conta de armazenamento em sua assinatura do Azure. 

        ![Arquivar em uma conta de armazenamento do Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se você selecionar **fluxo para um hub de eventos**, selecione **Hub de eventos-configurar**e, em seguida, selecione o namespace de hubs de eventos, o Hub de eventos e a política de acesso. 
        ![Transmitir para um hub de eventos](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se você selecionar **Enviar para log Analytics**, selecione o espaço de trabalho log Analytics.
        ![Enviar para o Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Clique em **Salvar**. Em seguida, selecione **X** no canto direito para fechar a página. 
9. Agora, de volta à página **configurações de diagnóstico** , confirme se você vê uma nova entrada na tabela configurações de **diagnóstico** . 
    ![Configuração de diagnóstico na lista](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Você também pode habilitar a coleta de todas as métricas para o tópico. 

## <a name="view-diagnostic-logs-in-azure-storage"></a>Exibir logs de diagnóstico no armazenamento do Azure 

1. Depois de habilitar uma conta de armazenamento como um destino de captura e a grade de eventos começar a emitir logs de diagnóstico, você deverá ver novos contêineres chamados **insights-logs-deliveryfailures** e **insights-logs-publishfailures** na conta de armazenamento. 

    ![Contêineres de armazenamento para logs de diagnóstico](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Ao navegar por um dos contêineres, você terminará em um blob no formato JSON. O arquivo contém entradas de log para uma falha de entrega ou uma falha de publicação. O caminho de navegação representa o **ResourceId** do tópico da grade de eventos e o carimbo de data/hora (nível de minuto) para quando as entradas de log foram emitidas. O arquivo blob/JSON, que pode ser baixado, no final adere ao esquema descrito na próxima seção. 

    [![Arquivo JSON no armazenamento](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Você deve ver o conteúdo no arquivo JSON semelhante ao exemplo a seguir: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>Próximas etapas
Para o esquema de log e outras informações conceituais sobre os logs de diagnóstico para tópicos ou domínios, consulte [logs de diagnóstico](diagnostic-logs.md).
