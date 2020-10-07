---
title: Inscrever-se no Video Indexer e carregar seu primeiro vídeo – Azure
titleSuffix: Azure Media Services
description: Saiba como se inscrever e carregar seu primeiro vídeo usando o portal do Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: f6c3953947e6f7e84f4cf9b565d2f66648b177f7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85130748"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Início Rápido: Como inscrever-se e atualizar seu primeiro vídeo

Este guia de início rápido de introdução mostra como entrar no site do Video Indexer e como carregar seu primeiro vídeo.

Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Video Indexer que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga pelos minutos indexados, bem como os encargos relacionados à conta dos Serviços de Mídia do Azure. 

## <a name="sign-up-for-video-indexer"></a>Inscrever-se no Video Indexer

Para começar a desenvolver com o Video Indexer, navegue para o site do [Video Indexer](https://www.videoindexer.ai/) e inscreva-se.

> [!NOTE]
> Depois de começar a usar o Video Indexer, todos os dados armazenados e o conteúdo carregado serão criptografados em repouso com uma chave gerenciada da Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Carregar um vídeo usando o site do Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Formatos de arquivo compatíveis com o Video Indexer

Confira o artigo [contêiner de entrada/formatos de arquivo](../latest/media-encoder-standard-formats.md#input-containerfile-formats) para obter uma lista de formatos de arquivo que você pode usar com o Video Indexer.

### <a name="upload-a-video"></a>Carregar um vídeo

1. Conecte-se ao site do [Video Indexer](https://www.videoindexer.ai/).
2. Para carregar um vídeo, pressione o botão ou o link **Carregar**.

    > [!NOTE]
    > O nome do vídeo não deve ter mais de 80 caracteres.

    ![Carregar](./media/video-indexer-get-started/video-indexer-upload.png)

    Depois que o seu vídeo tiver sido carregado, o Video Indexer iniciará a indexação e a análise do vídeo.

    ![Carregado](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Depois que o Video Indexer terminar de analisar, você receberá uma notificação com um link para seu vídeo e uma breve descrição do que foi encontrado nele. Por exemplo: people (pessoas), topics (tópicos), OCRs.

## <a name="see-also"></a>Confira também

Confira [Carregar e indexar vídeos](upload-index-videos.md) para obter mais detalhes.

Depois de carregar e indexar um vídeo, você pode começar a usar o site do [Video Indexer](video-indexer-view-edit.md) ou o [Portal do Desenvolvedor do Video Indexer](video-indexer-use-apis.md) para ver as informações do vídeo. 

[Começar a usar APIs](video-indexer-use-apis.md)

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução detalhada, visite nosso [laboratório de introdução](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Ao final do workshop, você terá um bom entendimento dos tipos de informações que podem ser extraídas do conteúdo de vídeo e áudio, estará mais preparado para identificar oportunidades relacionadas à inteligência de conteúdo e fará uma demonstração da IA de vídeo no Azure e de vários cenários no Video Indexer.

