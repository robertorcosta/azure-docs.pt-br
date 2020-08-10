---
title: Integração com outros serviços
titleSuffix: Azure Digital Twins
description: Entenda os requisitos de entrada e saída ao implantar o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ca500401a6bff8a00dd9c51eecb29aa93fdbc82b
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042642"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrar o gêmeos digital do Azure a outros serviços

O Azure digital gêmeos normalmente é usado junto com outros serviços. Usando as [**rotas de eventos**](concepts-route-events.md), o Azure digital gêmeos recebe dados de serviços upstream, como o [Hub IOT](../iot-hub/about-iot-hub.md), que é usado para entregar telemetria e notificações. 

O Azure digital gêmeos também pode rotear dados para serviços downstream, como mapas do Azure ([*como usar o Azure digital gêmeos para atualizar um mapa interno do Azure Maps*](how-to-integrate-maps.md)) e Time Series insights ([*instruções: integrar com Time Series insights*](how-to-integrate-time-series-insights.md)), para armazenamento, integração de fluxo de trabalho, análise e muito mais. 

## <a name="data-ingress"></a>Entrada de dados

O Azure digital gêmeos pode ser orientado com dados e eventos de qualquer serviço — Hub IoT, aplicativos lógicos, seu próprio serviço personalizado e muito mais. Isso permite que você colete a telemetria de dispositivos físicos em seu ambiente e processe esses dados usando o grafo de gêmeos digital do Azure na nuvem.

O Azure digital gêmeos não tem um hub IoT interno. Você pode usar um hub IoT existente atualmente em produção ou implantar um novo. Isso lhe dá acesso completo a todos os recursos de gerenciamento de dispositivos do Hub IoT.

Para ingerir dados de qualquer fonte no Azure digital gêmeos, use uma [função do Azure](../azure-functions/functions-overview.md). Saiba mais sobre esse padrão em [*como: a telemetria de ingestão do Hub IOT*](how-to-ingest-iot-hub-data.md)ou experimente você mesmo no tutorial do Azure digital gêmeos [*: Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md).

## <a name="data-egress-services"></a>Serviços de saída de dados

O Azure digital gêmeos pode enviar dados para **pontos de extremidade**conectados. Os pontos de extremidade com suporte podem ser:
* [Hub de Evento](../event-hubs/event-hubs-about.md)
* [Grade de Eventos](../event-grid/overview.md)
* [Barramento de Serviço](../service-bus-messaging/service-bus-messaging-overview.md)

Os pontos de extremidade são anexados ao Azure digital gêmeos usando APIs de gerenciamento ou o portal do Azure. Saiba mais sobre como anexar um ponto de extremidade ao gêmeos digital do Azure em [*instruções: gerenciar pontos de extremidade e rotas*](how-to-manage-routes-apis-cli.md).

Há muitos outros serviços em que você pode querer direcionar, por fim, os dados, como o [armazenamento do Azure](../storage/common/storage-introduction.md) ou [Time Series insights](../time-series-insights/time-series-insights-update-overview.md). Para enviar seus dados a serviços como esses, anexe o serviço de destino a um ponto de extremidade.

Por exemplo, se você também estiver usando [mapas do Azure](../azure-maps/about-azure-maps.md) e quiser correlacionar o local com o grafo do Azure digital gêmeos [bigráfica](concepts-twins-graph.md), poderá usar Azure Functions com a grade de eventos para estabelecer a comunicação entre todos os serviços em sua implantação.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os pontos de extremidade e eventos de roteamento para serviços externos:
* [*Conceitos: roteamento de eventos de gêmeos digitais do Azure*](concepts-route-events.md)

Consulte como configurar o gêmeos digital do Azure para ingerir dados do Hub IoT:
* [*Como: telemetria de ingestão do Hub IoT*](how-to-ingest-iot-hub-data.md)
