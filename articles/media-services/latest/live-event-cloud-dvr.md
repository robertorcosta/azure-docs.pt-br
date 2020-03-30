---
title: Use a mudança de tempo e saídas ao vivo para criar reprodução de vídeo demanda
titleSuffix: Azure Media Services
description: Este artigo descreve como usar a mudança de tempo e saídas ao vivo para gravar Live Streams e criar reprodução demanda.
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899788"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Use a mudança de tempo e saídas ao vivo para criar reprodução de vídeo demanda

No Azure Media Services, um objeto [live output](https://docs.microsoft.com/rest/api/media/liveoutputs) é como um gravador de vídeo digital que capturará e gravará sua transmissão ao vivo em um ativo em sua conta de Serviços de Mídia. O conteúdo gravado é persistido no contêiner definido pelo recurso [Asset](https://docs.microsoft.com/rest/api/media/assets) (o contêiner está na conta do Azure Storage anexada à sua conta). A saída ao vivo também permite controlar algumas propriedades da transmissão ao vivo de saída, como quanto do fluxo é mantido na gravação de arquivo (por exemplo, a capacidade do DVR em nuvem) ou quando os espectadores podem começar a assistir a transmissão ao vivo. O arquivo em disco é uma "janela" de arquivo circular que contém apenas a quantidade de conteúdo especificada na propriedade **archiveWindowLength** da Saída ao Vivo. O conteúdo que cai fora desta janela é automaticamente descartado do recipiente de armazenamento e não pode ser recuperável. O valor archiveWindowLength representa uma duração de tempo iso-8601 (por exemplo, PTHH:MM:SS), que especifica a capacidade do DVR. O valor pode ser definido de um mínimo de três minutos para um máximo de 25 horas.

A relação entre um Evento Ao Vivo e suas Saídas ao Vivo é semelhante à transmissão de TV tradicional, na época em que um canal (Evento Ao Vivo) representa um fluxo constante de vídeo e uma gravação (Saída ao Vivo) é escopo de um segmento de tempo específico (por exemplo, notícias noturnas de 18h30 às 19h). Uma vez que você tenha o fluxo fluindo para o Evento Ao Vivo, você pode iniciar o evento de streaming criando um ativo, saída ao vivo e localizador de streaming. A Saída ao vivo arquiva o fluxo e o disponibiliza para usuários por meio do [Ponto de extremidade de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Você pode criar várias Saídas ao vivo (até três no máximo) em um Evento ao vivo com diferentes comprimentos e configurações de arquivamento. Para obter informações sobre o fluxo de trabalho de transmissão ao vivo, consulte a seção [de etapas gerais.](live-streaming-overview.md#general-steps)

## <a name="using-a-dvr-during-an-event"></a>Usando um DVR durante um evento

Esta seção discute como usar um DVR durante um evento para controlar quais partes do fluxo estão disponíveis para 'rebobinar'.

O `archiveWindowLength` valor determina o quão longe no tempo um espectador pode ir da posição ao vivo atual. O `archiveWindowLength` valor também determina quanto tempo os manifestos do cliente podem crescer.

Suponha que você está transmitindo um `ArchiveWindowLength` jogo de futebol, e tem um de apenas 30 minutos. Um espectador que começa a assistir ao seu evento 45 minutos após o início do jogo pode buscar de volta no máximo a marca de 15 minutos. Suas saídas ao vivo para o jogo continuarão até que o Evento Ao Vivo seja interrompido. O conteúdo que está fora do arquivoWindowLength é continuamente descartado do armazenamento e não pode ser recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido eliminado do seu DVR e do contêiner em blob armazenamento para o ativo. O arquivo não é recuperável e é removido do contêiner no armazenamento blob do Azure.

Um evento ao vivo suporta até três saídas ao vivo simultâneas (você pode criar no máximo 3 gravações/arquivos de uma transmissão ao vivo ao mesmo tempo). Esse suporte permite que você publique e arquive diferentes partes de um evento conforme necessário. Suponha que você precise transmitir um feed linear ao vivo 24x7 e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo sob demanda para visualização aproximada. Para este cenário, primeiro você cria uma saída ao vivo primária com uma pequena janela de arquivo de 1 hora ou menos – esta é a transmissão ao vivo principal em que seus espectadores sintonizariam. Você criaria um localizador de streaming para esta saída ao vivo e publicaria-o no seu aplicativo ou site como o feed "Ao vivo". Enquanto o Evento ao Vivo estiver em execução, você poderá criar programaticamente uma segunda Saída ao Vivo simultânea no início de um programa (ou 5 minutos antes para fornecer algumas alças para aparar mais tarde). Essa segunda Saída ao Vivo pode ser excluída 5 minutos depois que o programa é finalizado. Com este segundo ativo, você pode criar um novo Localizador de Streaming para publicar este programa como um ativo demanda no catálogo do seu aplicativo. Você pode repetir esse processo várias vezes para outros limites do programa ou destaques que queira compartilhar como vídeos sob demanda, enquanto o feed "Ao vivo" da primeira Saída ao Vivo continua difundindo o feed linear.

## <a name="creating-an-archive-for-on-demand-playback"></a>Criando um arquivo para reprodução demanda

O ativo para o que a saída ao vivo está arquivando automaticamente se torna um ativo demanda quando a saída ao vivo é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeSaídasOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente saídas ao vivo em parada.

Mesmo depois de parar e excluir o evento, os usuários podem transmitir seu conteúdo arquivado como um vídeo demanda, desde que você não exclua o ativo. Um ativo não deve ser excluído se for usado por um evento; o evento deve ser excluído primeiro.

Se você publicou o recurso da sua Saída Ao Vivo usando um localizador de streaming, o Evento Ao Vivo (até o comprimento da janela DVR) continuará a ser visível até o vencimento ou exclusão do localizador de streaming, o que vier primeiro.

Para obter mais informações, consulte:

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quando você exclui a saída de vida, você não está excluindo o ativo e o conteúdo subjacentes no ativo.

## <a name="next-steps"></a>Próximas etapas

* [Subclipe seus vídeos](subclip-video-rest-howto.md).
* [Defina filtros para seus ativos](filters-dynamic-manifest-rest-howto.md).
