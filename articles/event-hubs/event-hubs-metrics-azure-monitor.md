---
title: 'Métricas no Azure Monitor: Hubs de Eventos do Azure | Microsoft Docs'
description: Este artigo fornece informações sobre como usar o Monitoramento do Azure com Hubs de Eventos do Azure
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7ad570a41fd9dfff01e3a1da6b2d309a7a8464cc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931141"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Métricas de Hubs de Eventos do Azure no Azure Monitor

As métricas dos hubs de eventos fornecem o estado dos recursos dos hubs de eventos em sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus hubs de eventos não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado de seus hubs de eventos. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../azure-monitor/overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Log Analytics e os Hubs de Eventos. Para mais informações, consulte [Monitoramento de dados coletados por Azure Monitor](../azure-monitor/platform/data-platform.md).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../azure-monitor/platform/diagnostic-settings.md) no Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![Exibir métricas bem-sucedidas][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas**. Para exibir as métricas filtradas para o escopo do hub de eventos, selecione o hub de eventos e clique em **Métricas**.

Para métricas com suporte a dimensões, será necessário filtrar o valor da dimensão desejado, como mostrado no exemplo a seguir:

![Filtrar com valor de dimensão][2]

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor atualmente é gratuito. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelo Azure se você transmitir dados de métricas para Azure Monitor logs para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas do Hubs de Eventos é 1 minuto.

## <a name="request-metrics"></a>Métricas de solicitação

Conta o número de solicitações de gerenciamento de dados e de operações.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
| Solicitações de entrada  | O número de solicitações feitas ao serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Solicitações bem sucedidas    | O número de solicitações bem sucedidas feitas ao serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
| Erros do Servidor  | O número de solicitações não processadas devido a um erro no serviço de Hubs de Eventos do Azure durante um período específico. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName |
|Erros do usuário |O número de solicitações não processadas devido a erros do usuário durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Erros de Cota excedida |O número de solicitações que excedeu a cota disponível. Consulte [este artigo](event-hubs-quotas.md) para ver mais informações sobre as cotas dos Hubs de Eventos.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="throughput-metrics"></a>Métricas de taxa de transferência

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Solicitações Limitadas |O número de solicitações que foram restringidas porque o uso de unidades da taxa de transferência foi excedido.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="message-metrics"></a>Métricas de mensagens

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Mensagens de entrada |O número de eventos ou mensagens enviadas para os Hubs de Eventos durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens de saída |O número de eventos ou mensagens recuperadas dos Hubs de Eventos durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de entrada |O número de bytes enviado ao serviço de Hubs de Eventos do Azure durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes de saída |O número de bytes recuperado do serviço de Hubs de Eventos do Azure durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|ActiveConnections |O número de conexões ativas em um namespace, bem como em uma entidade.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões Abertas |O número de conexões abertas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Conexões Fechadas |O número de conexões fechadas.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="event-hubs-capture-metrics"></a>Métricas de captura de Hubs de Eventos

Monitore as métricas de captura de Hubs de Eventos habilitando o recurso de Captura para seus Hubs de Eventos. As métricas a seguir descrevem o que você pode monitorar com a Captura habilitada.

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|Lista de pendências de captura |O número de bytes que ainda estão pendentes de captura para o destino escolhido.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Mensagens capturadas |O número de mensagens ou eventos que são capturados para o destino escolhido durante um período específico.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|Bytes capturados |O número de bytes que são capturados para o destino escolhido durante um período específico.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões das métricas

Os Hubs de Evento do Azure dão suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

| Nome da métrica | Descrição |
| ------------------- | ----------------- |
|EntityName| Os Hubs de Eventos dão suporte às entidades de hub de evento sob o namespace.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integração do Azure Monitor com as ferramentas SIEM
O roteamento dos dados de monitoramento (logs de atividades, logs de diagnóstico, etc.) para um hub de eventos com o Azure Monitor permite que você integre facilmente as ferramentas SIEM (gerenciamento de eventos e informações de segurança). Para obter mais informações, consulte os seguintes artigos/Postagens de blog:

- [Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Introdução à integração de log do Azure](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Usar o Monitor do Azure para integrar às ferramentas de SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

No cenário em que uma ferramenta SIEM consome dados de log de um hub de eventos, se você não vir mensagens de entrada ou se vir mensagens de entrada, mas nenhuma mensagem de saída no grafo de métricas, siga estas etapas:

- Se não houver **nenhuma mensagem de entrada**, significa que o serviço de Azure monitor não está movendo os logs de auditoria/diagnóstico para o Hub de eventos. Abra um tíquete de suporte com a equipe de Azure Monitor neste cenário. 
- Se houver mensagens de entrada, mas **nenhuma mensagem de saída**, isso significa que o aplicativo Siem não está lendo as mensagens. Entre em contato com o provedor SIEM para determinar se a configuração do hub de eventos desses aplicativos está correta.


## <a name="next-steps"></a>Próximas etapas

* Consulte [Visão geral do Monitoramento do Azure](../azure-monitor/overview.md).
* Exemplo de como [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub. 

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

- Introdução com um Tutorial de Hubs de Eventos
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
