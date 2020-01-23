---
title: Métricas da Retransmissão do Azure no Azure Monitor (versão prévia) | Microsoft Docs
description: Este artigo fornece informações sobre como você pode usar Azure Monitor para monitorar o estado da retransmissão do Azure.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 5c548186ec51cf86f34942cb15d8f984afa60268
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514928"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas da Retransmissão do Azure no Azure Monitor (versão prévia)
As métricas da Retransmissão do Azure fornecem o estado dos recursos na sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus recursos de retransmissão não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado da Retransmissão do Azure. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

> [!IMPORTANT]
> Este artigo se aplica somente para o recurso de Conexões Híbridas de Retransmissão do Azure, não para a Retransmissão do WCF. 

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Operation Management Suite e os Hubs de Eventos. Para mais informações, consulte [Monitoramento de dados coletados por Azure Monitor](../azure-monitor/platform/data-platform.md).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../azure-monitor/platform/diagnostic-settings.md) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas (versão prévia)** . 

Para métricas com suporte para dimensões, você deve filtrar pelo valor da dimensão desejado.

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor atualmente é gratuito durante a versão prévia. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado por logs de Azure Monitor se transmitir dados de métricas para Azure Monitor logs para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas da Retransmissão do Azure é um minuto.

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success (versão prévia) | O número de conexões de ouvinte bem-sucedidas feitas para a Retransmissão do Azure durante o período especificado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError (versão prévia)|O número de erros de cliente em conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError (versão prévia)|O número de erros de servidor nas conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-Success (versão prévia)|O número de conexões de remetente bem-sucedidas feitas durante o período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError (versão prévia)|O número de erros de cliente em conexões de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError (versão prévia)|O número de erros de servidor em conexões de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests (versão prévia)|O número total de conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests (versão prévia)|As solicitações de conexão feitas por remetentes durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections (versão prévia)|O número de conexões ativas durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveListeners (versão prévia)|O número de ouvintes ativos durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects (versão prévia)|O número de ouvintes desconectados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects (versão prévia)|O número de remetentes desconectados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de uso de memória

| Nome da métrica | Description |
| ------------------- | ----------------- |
|BytesTransferred (versão prévia)|O número de bytes transferidos durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões das métricas

A Retransmissão do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

|Nome da dimensão|Description|
| ------------------- | ----------------- |
|EntityName| A Retransmissão do Azure dá suporte a entidades de mensagens no namespace.|

## <a name="next-steps"></a>Próximos passos

Consulte [Visão geral do Monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




