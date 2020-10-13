---
title: Estados do LiveEvent e faturamento no Azure Media Services|Microsoft Docs
description: Este tópico fornece uma visão geral dos estados e faturamento do LiveEvent dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1b058eefe22238b60c3482c55b5ae340f4e597f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296729"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de Evento ao Vivo

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Mídia do Azure, um Evento ao Vivo iniciará a cobrança assim que o estado transitar para estado de **Execução**. Você será cobrado mesmo se não houver vídeo fluindo pelo serviço. Para interromper o Evento ao Vivo da cobrança é necessário parar o Evento ao Vivo. A transcrição ao vivo é cobrada da mesma maneira que o evento ao vivo.

Quando o **LiveEventEncodingType** em seu [evento ao vivo](/rest/api/media/liveevents) é definido como Standard ou Premium1080p, os serviços de mídia desligam automaticamente qualquer evento ao vivo que ainda esteja no estado de **execução** 12 horas após o feed de entrada ser perdido e não há nenhum s de **saída ao vivo**em execução. No entanto, você ainda será cobrado pelo tempo que o Evento ao Vivo estava no estado de **Execução**.

> [!NOTE]
> Eventos ao vivo de passagem não são desligados automaticamente e devem ser explicitamente interrompidos por meio da API para evitar uma cobrança excessiva. 

## <a name="states"></a>Estados

O Evento ao Vivo pode estar em um dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Parado**| Esse é o estado inicial do evento ao vivo após a criação (a menos que AutoStart tenha sido definido como true). Nenhuma cobrança ocorre nesse estado. Nesse estado, as propriedades do Evento ao Vivo poderão ser atualizadas, mas streaming não será permitido.|
|**Iniciando**| O Evento ao Vivo está sendo iniciado e os recursos estão sendo alocados. Não há cobrança nesse estado. Atualizações ou streaming não são permitidos durante esse estado. Se ocorrer um erro, o Evento ao Vivo retornará ao estado Parado.|
|**Executando**| Os recursos do Evento ao Vivo foram alocados, as URLs de visualização e ingestão foram geradas e está compatível para receber transmissões por streaming. Neste ponto, o faturamento está ativo. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional.|
|**Parando**| O Evento ao Vivo está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|
|**Excluir**| O Evento ao Vivo está sendo excluído. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|

Você pode optar por habilitar transcrições dinâmicas ao criar o evento ao vivo. Se você fizer isso, será cobrado pelas transcrições dinâmicas sempre que o evento ao vivo estiver no estado **executando** . Observe que você será cobrado mesmo se não houver áudio fluindo pelo evento ao vivo.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
