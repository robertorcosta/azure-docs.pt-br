---
title: Exibir métricas com o Monitor Azure
description: Este artigo mostra como monitorar métricas com os gráficos do portal Azure e o Azure CLI.
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382929"
---
# <a name="monitor-media-services-metrics"></a>Monitorar as métricas dos Serviços de Mídia do Microsoft Azure

[O Azure Monitor](../../azure-monitor/overview.md) permite monitorar métricas e registros de diagnóstico que ajudam a entender como seus aplicativos estão se saindo. Para obter uma descrição detalhada deste recurso e para ver por que você gostaria de usar os registros de diagnóstico e métricas do Azure Media Services, consulte [métricas do Monitor Media Services e registros de diagnóstico](media-services-metrics-diagnostic-logs.md).

O Azure Monitor fornece várias maneiras de interagir com métricas, incluindo mapeá-las no portal, acessá-las através da API REST ou consulta-las usando o Azure CLI. Este artigo mostra como monitorar métricas com os gráficos do portal Azure e o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md)
- Revisar [métricas do Monitor Media Services e registros de diagnóstico](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Ver métricas no portal Azure

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até sua conta do Azure Media Services e selecione **Métricas**.
1. Clique na caixa **RECURSO** e selecione o recurso para o qual deseja monitorar métricas.

    A **janela Selecionar uma** janela de recursos aparece à direita com a lista de recursos disponíveis para você. Neste caso você vê:

    * &lt;Nome da conta do Serviço de Mídia&gt;
    * &lt;Nome da&gt;/&lt;conta do Serviço de Mídia nome de ponto final de streaming&gt;
    * &lt;nome da conta de armazenamento&gt;

    Selecione o recurso e **pressione Aplicar**. Para obter detalhes sobre recursos e métricas suportadas, consulte [as métricas do Monitor Media Services](media-services-metrics-diagnostic-logs.md).

    ![Métricas](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Para alternar entre os recursos para os quais deseja monitorar métricas, clique na caixa **RESOURCE** novamente e repita esta etapa.
1. (Opcionalmente) dê um nome ao seu gráfico (edite o nome pressionando o lápis na parte superior).
1. Adicione métricas que você deseja visualizar.

    ![Métricas](media/media-services-metrics/metrics03.png)
1. Você pode fixar seu gráfico no painel.

## <a name="view-metrics-with-azure-cli"></a>Ver métricas com o Azure CLI

Para obter métricas de "Egress" com o Azure CLI, você executaria o seguinte `az monitor metrics` comando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Egress" pelo nome métrico que você está interessado.

## <a name="see-also"></a>Confira também

* [Métricas do Monitor Azure](../../azure-monitor/platform/data-platform.md)
* [Crie, visualize e gerencie alertas métricos usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Próximas etapas

[Registros de diagnóstico](media-services-diagnostic-logs-howto.md)
