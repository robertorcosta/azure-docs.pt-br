---
title: Métricas de Serviços de Mídia e registros de diagnóstico com o Azure Monitor
titleSuffix: Azure Media Services
description: Saiba como monitorar as métricas e registros de diagnóstico do Azure Media Services via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585282"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Monitore métricas e registros de diagnóstico do Media Services via Azure Monitor

[O Azure Monitor](../../azure-monitor/overview.md) permite monitorar métricas e registros de diagnóstico que ajudam a entender como seus aplicativos estão se saindo. Todos os dados coletados pelo Azure Monitor se encaixam em um dos dois tipos fundamentais: métricas e registros. Você pode monitorar registros de diagnóstico do Media Services e criar alertas e notificações para as métricas e registros coletados. Você pode visualizar e analisar os dados de métricas usando [o metrics explorer](../../azure-monitor/platform/metrics-getting-started.md). Você pode enviar logs para [o Azure Storage,](https://azure.microsoft.com/services/storage/)transmiti-los para [o Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)exportá-los para [o Log Analytics](https://azure.microsoft.com/services/log-analytics/)ou usar serviços de terceiros.

Para obter uma visão geral detalhada, consulte [Azure Monitor Metrics](../../azure-monitor/platform/data-platform.md) e [azure Monitor Diagnostics logs](../../azure-monitor/platform/platform-logs-overview.md).

Este tópico discute [métricas](#media-services-metrics) de serviços de mídia suportadas e registros de diagnóstico de [serviços de mídia](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métricas dos Serviços de Mídia

As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Elas são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples. Para obter informações sobre como criar alertas métricos, consulte [Criar, visualizar e gerenciar alertas métricos usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Os Serviços de Mídia suportam métricas de monitoramento para os seguintes recursos:

* Conta
* Ponto de Extremidade de Streaming

### <a name="account"></a>Conta

Você pode monitorar as seguintes métricas da conta.

|Nome da métrica|Nome de exibição|Descrição|
|---|---|---|
|Contagem de ativos|Contagem de ativos|Ativos em sua conta.|
|Cotação patrimonial|Cota de ativos|Cota de ativos em sua conta.|
|AssetQuotaUsedPercentage|Cota de ativos utilizada|O percentual da cota de ativos já utilizada.|
|Contagem de políticas de conteúdo|Contagem de políticas de chave de conteúdo|Políticas-chave de conteúdo em sua conta.|
|Cota de política de conteúdo|Cota de política de chave de conteúdo|Cota de Políticas-Chave de Conteúdo em sua conta.|
|ConteúdochavePolicyQuotaUsedPercentage|Cota de política de chave de conteúdo usada|A porcentagem da cota de Política de Chave de Conteúdo já utilizada.|
|StreamingPolicyCount|Contagem de políticas de streaming|Políticas de streaming em sua conta.|
|StreamingPolicyQuota|Cota de Política de Streaming|Cota de Políticas de Streaming em sua conta.|
|StreamingPolicyQuotaUsedPercentage|Cota de Política de Streaming usada|O percentual da cota de Política de Streaming já foi utilizado.|

Você também deve rever [as cotas e os limites da conta.](limits-quotas-constraints.md)

### <a name="streaming-endpoint"></a>Ponto de Extremidade de Streaming

As seguintes métricas [de pontos finais de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) de serviços de mídia são suportadas:

|Nome da métrica|Nome de exibição|Descrição|
|---|---|---|
|Requests|Requests|Fornece o número total de solicitações HTTP atendidas pelo Streaming Endpoint.|
|Saída|Saída|O número total de bytes de saída. Por exemplo, bytes transmitidos pelo Streaming Endpoint.|
|SuccessE2ELatency|Sucesso de ponta a ponta Latência|Duração de tempo a partir de quando o Streaming Endpoint recebeu a solicitação para quando o último byte da resposta foi enviado.|

### <a name="why-would-i-want-to-use-metrics"></a>Por que eu iria querer usar métricas?

Aqui estão exemplos de como o monitoramento de métricas do Media Services pode ajudá-lo a entender como seus aplicativos estão se saindo. Algumas perguntas que podem ser abordadas com métricas do Media Services são:

* Como monitorar meu Ponto Final de Streaming Padrão para saber quando excedi os limites?
* Como saberei se tenho unidades de escala endpoint premium suficientes?
* Como posso definir um alerta para saber quando escalar meus Pontos Finais de Streaming?
* Como faço para definir um alerta para saber quando o egresso máximo configurado na conta foi atingido?
* Como posso ver a quebra dos pedidos falhando e o que está causando o fracasso?
* Como posso ver quantas solicitações HLS ou DASH estão sendo retiradas do empacotador?
* Como definir um alerta para saber quando o valor limite de # de solicitações com falha foi atingido?

### <a name="example"></a>Exemplo

Veja [como monitorar as métricas dos Serviços de Mídia](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Registros de diagnóstico do Media Services

Os registros de diagnóstico fornecem dados ricos e frequentes sobre o funcionamento de um recurso do Azure. Para obter mais informações, consulte [Como coletar e consumir dados de log de seus recursos do Azure](../../azure-monitor/platform/platform-logs-overview.md).

Os Serviços de Mídia suportam os seguintes registros de diagnóstico:

* Entrega de chaves

### <a name="key-delivery"></a>Entrega de chaves

|Nome|Descrição|
|---|---|
|Solicitação de serviço de entrega chave|Logs que mostram as informações de solicitação do serviço de entrega chave. Para obter mais informações, consulte [esquemas](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Por que eu iria querer usar registros de diagnósticos?

Algumas coisas que você pode examinar com os principais registros de diagnóstico de entrega são:

* Veja o número de licenças entregues pelo tipo DRM.
* Veja o número de licenças entregues pela política.
* Veja erros por DRM ou tipo de política.
* Veja o número de pedidos de licença não autorizados dos clientes.

### <a name="example"></a>Exemplo

Veja [como monitorar os registros de diagnóstico do Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Próximas etapas

* [Como coletar e consumir dados de log de seus recursos do Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Crie, visualize e gerencie alertas métricos usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Como monitorar métricas do Media Services](media-services-metrics-howto.md)
* [Como monitorar registros de diagnóstico do Media Service](media-services-diagnostic-logs-howto.md)
