---
title: Estados do LiveEvent e faturamento no Azure Media Services|Microsoft Docs
description: Este tópico fornece uma visão geral dos estados e faturamento do LiveEvent dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: af3d4b51dadfaa99a166ca0ce475c5a110d8f6e8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933674"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de Evento ao Vivo

Nos Serviços de Mídia do Azure, um Evento ao Vivo iniciará a cobrança assim que o estado transitar para estado de **Execução**. Para interromper o Evento ao Vivo da cobrança é necessário parar o Evento ao Vivo.

Quando o **LiveEventEncodingType** em seu [evento ao vivo](https://docs.microsoft.com/rest/api/media/liveevents) é definido como Standard ou Premium1080p, os serviços de mídia desligam automaticamente qualquer evento ao vivo que ainda esteja no estado de **execução** 12 horas após o feed de entrada ser perdido e não há nenhuma **saída ao vivo** s em execução. No entanto, você ainda será cobrado pelo tempo que o Evento ao Vivo estava no estado de **Execução**.

> [!NOTE]
> Eventos ao vivo de passagem não são desligados automaticamente e devem ser explicitamente interrompidos por meio da API para evitar uma cobrança excessiva. 

## <a name="states"></a>Estados

O Evento ao Vivo pode estar em um dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Interrompido**| Esse é o estado inicial do evento ao vivo após a criação (a menos que AutoStart tenha sido definido como true). Nenhuma cobrança ocorre nesse estado. Nesse estado, as propriedades do evento ao vivo poderão ser atualizadas, mas streamings não serão permitidos.|
|**Iniciando**| O Evento ao Vivo está sendo iniciado e os recursos estão sendo alocados. Não há cobrança nesse estado. Atualizações ou streaming não são permitidos durante esse estado. Se ocorrer um erro, o Evento ao Vivo retornará ao estado Parado.|
|**Executando**| Os recursos do Evento ao Vivo foram alocados, as URLs de visualização e ingestão foram geradas e está compatível para receber transmissões por streaming. Neste ponto, o faturamento está ativo. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional.|
|**Parando**| O Evento ao Vivo está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|
|**Excluindo**| O evento ao vivo está sendo excluído. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|

## <a name="next-steps"></a>Próximos passos

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
