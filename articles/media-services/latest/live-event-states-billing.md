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
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543068"
---
# <a name="live-event-states-and-billing"></a>Estados e cobrança de Evento ao Vivo

Nos Serviços de Mídia do Azure, um Evento ao Vivo iniciará a cobrança assim que o estado transitar para estado de **Execução**. Você será cobrado mesmo que não haja nenhum vídeo fluindo através do serviço. Para interromper o Evento ao Vivo da cobrança é necessário parar o Evento ao Vivo. A Transcrição Ao Vivo é cobrada da mesma forma que o Evento Ao Vivo.

Quando **o LiveEventEncodingType** em seu [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) estiver definido como Padrão ou Premium1080p, o Media Services desliga automaticamente qualquer Evento Ao Vivo que ainda esteja no estado em **execução** 12 horas após a entrada ser perdida e não houver execução de **saída ao vivo.** No entanto, você ainda será cobrado pelo tempo que o Evento ao Vivo estava no estado de **Execução**.

> [!NOTE]
> Os Eventos ao Vivo de passagem não são automaticamente desligados e devem ser explicitamente interrompidos através da API para evitar faturamento excessivo. 

## <a name="states"></a>Estados

O Evento ao Vivo pode estar em um dos seguintes estados.

|Estado|Descrição|
|---|---|
|**Parou**| Este é o estado inicial do Evento Ao Vivo após a criação (a menos que a partida automática tenha sido definida como verdadeira.) Não há faturamento neste estado. Nesse estado, as propriedades do Evento ao Vivo poderão ser atualizadas, mas streaming não será permitido.|
|**Começando**| O Evento ao Vivo está sendo iniciado e os recursos estão sendo alocados. Não há cobrança nesse estado. Atualizações ou streaming não são permitidos durante esse estado. Se ocorrer um erro, o Evento ao Vivo retornará ao estado Parado.|
|**Em execução**| Os recursos do Evento ao Vivo foram alocados, as URLs de visualização e ingestão foram geradas e está compatível para receber transmissões por streaming. Neste ponto, o faturamento está ativo. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional.|
|**Parar**| O Evento ao Vivo está sendo interrompido e os recursos estão sendo desprovisionados. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|
|**Excluir**| O Evento ao Vivo está sendo excluído. Não haverá cobrança nesse estado transitório. Atualizações ou streaming não são permitidos durante esse estado.|

Você pode optar por ativar transcrições ao vivo ao criar o Evento ao Vivo. Se você fizer isso, você será cobrado por Transcrições ao Vivo sempre que o Evento Ao Vivo estiver no estado **de execução.** Observe que você será cobrado mesmo que não haja áudio fluindo através do Evento Ao Vivo.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)
