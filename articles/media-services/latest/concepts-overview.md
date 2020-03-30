---
title: Terminologia e conceitos dos Serviços de Mídia
titleSuffix: Azure Media Services
description: Aprenda sobre terminologia e conceitos para a Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500097"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologia e conceitos dos Serviços de Mídia

Este tópico fornece uma breve visão geral da terminologia e conceitos do Azure Media Services. O artigo também fornece links para artigos com uma explicação detalhada dos conceitos e funcionalidades do Media Services v3.

Os conceitos fundamentais descritos nesses tópicos devem ser revistos antes de iniciar o desenvolvimento.

> [!NOTE]
> Atualmente, você pode usar o [portal Azure](https://portal.azure.com/) para: gerenciar os Serviços de Mídia v3 [Live Events,](live-events-outputs-concept.md)visualizar (não gerenciar) [ativos](assets-concept.md)v3 e obter informações sobre como [acessar APIs](access-api-portal.md).
> Para todas as [outras tarefas](transforms-jobs-concept.md) de gerenciamento (por exemplo, Transformações e Empregos e [Proteção de Conteúdo),](content-protection-overview.md)use a [API REST,](https://aka.ms/ams-v3-rest-ref) [CLI](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia de Serviços de Mídia v3

|Termo|Descrição|
|---|---|
|Evento ao vivo|Um **Evento Ao Vivo** representa um pipeline para ingerir, transcodificar (opcionalmente) e empacotar transmissões ao vivo de metadados de vídeo, áudio e em tempo real.<br/><br/>Para clientes que migram de APIs de Serviços de Mídia v2, o **Evento Ao Vivo** substitui a entidade do **Canal** em v2. Para obter mais informações, consulte [Migrando de v2 para v3](migrate-from-v2-to-v3.md).|
|Ponto final de streaming/embalagem/origem|Um **streaming Endpoint** representa um serviço dinâmico (just-in-time) de embalagem e origem que pode fornecer seu conteúdo ao vivo e demanda diretamente para um aplicativo de player cliente. Ele usa um dos protocolos comuns de mídia de streaming (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece criptografia dinâmica (just-in-time) para sistemas de gerenciamento de direitos digitais (DRMs) líderes do setor.<br/><br/>Na indústria de streaming de mídia, este serviço é comumente referido como um **Packager** ou **Origin**.  Outros termos comuns no setor para esse recurso incluem JITP (just-in-time-packager) ou JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>Media Services v3 conceitos

|Conceitos|Descrição|Links|
|---|---|---|
|Ativos e upload de conteúdo|Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de Serviços de Mídia e carregar seus arquivos digitais em **Ativos**.|[Upload e armazenamento na nuvem](storage-account-concept.md)<br/><br/>[Conceito de ativos](assets-concept.md)|
|Codificando o conteúdo|Uma vez que você carregue seus arquivos de mídia digital de alta qualidade em Ativos, você pode codificá-los em formatos que podem ser reproduzidos em uma grande variedade de navegadores e dispositivos. <br/><br/>Para codificar com os Serviços de Mídia v3, você precisa criar **Transformações** e **Empregos**.|[Transformações e Trabalhos](transforms-jobs-concept.md)<br/><br/>[Codificação com os Serviços de Mídia do Microsoft Azure](encoding-concept.md)|
|Análise de conteúdo (Video Indexer)|O Media Services v3 permite extrair insights de seus arquivos de vídeo e áudio usando predefinições de Media Services v3. Para analisar seu conteúdo usando predefinições de Media Services v3, você precisa criar **Transformações** e **Empregos**.<br/><br/>Se você quiser insights mais detalhados, use [o Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) diretamente.|[Analisando os arquivos de áudio e vídeos](analyzing-video-audio-files-concept.md)|
|Empacotamento e entrega|Uma vez que seu conteúdo esteja codificado, você pode tirar proveito da **Embalagem Dinâmica**. Em Serviços de Mídia, um **Streaming Endpoint** é o serviço de embalagem dinâmica usado para fornecer conteúdo de mídia aos jogadores clientes. Para disponibilizar vídeos no ativo de saída aos clientes para reprodução, você tem que criar um **Localizador de Streaming** e, em seguida, construir URLs de streaming. <br/><br/>Ao criar o **Localizador de Streaming,** além do nome do ativo, você precisa especificar **A Política de Streaming**. **As Políticas de Streaming** permitem definir protocolos de streaming e opções de criptografia (se houver) para seus **localizadores de streaming**. A Embalagem Dinâmica é usada se você transmitir seu conteúdo ao vivo ou demanda. <br/><br/>Você pode usar **os Manifestos Dinâmicos dos Serviços de** Mídia para transmitir apenas uma representação específica ou subclipes do seu vídeo.|[Empacotamento dinâmico](dynamic-packaging-overview.md)<br/><br/>[Pontos finais de streaming](streaming-endpoint-concept.md)<br/><br/>[Localizadores de Streaming](streaming-locators-concept.md)<br/><br/>[Políticas de Streaming](streaming-policy-concept.md)<br/><br/>[Manifestos dinâmicos](filters-dynamic-manifest-overview.md)<br/><br/>[Filtros](filters-concept.md)|
|Proteção de conteúdo|Com os Serviços de Mídia, você pode fornecer seu conteúdo ao vivo e demanda criptografado dinamicamente com o Advanced Encryption Standard (AES-128) ou/e qualquer um dos três principais sistemas DRM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. <br/><br/>Ao especificar opções de criptografia em seu fluxo, crie a **Política de Chave de Conteúdo** e associe-a ao **localizador de streaming**. A **Política de Chave de Conteúdo** permite configurar como a chave de conteúdo é entregue aos clientes finais.<br/><br/> Tente reutilizar políticas sempre que as mesmas opções forem necessárias.| [Políticas da Chave de Conteúdo](content-key-policy-concept.md)<br/><br/>[Proteção de conteúdo](content-protection-overview.md)|
|Transmissão ao vivo|Os Serviços de Mídia permitem que você entregue eventos ao vivo para seus clientes na nuvem do Azure. **Eventos ao Vivo** são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Quando você cria um **Evento Ao Vivo,** um ponto final de entrada é criado que você pode usar para enviar um sinal ao vivo de um codificador remoto. Uma vez que você tenha o fluxo fluindo para o **Evento Ao Vivo,** você pode iniciar o evento de streaming criando um **Ativo**, Saída **ao Vivo**e **Localizador de Streaming**. **A Saída Ao Vivo** arquivará o fluxo no **Ativo** e o disponibilizará aos espectadores através do **Streaming Endpoint**. Um evento ao vivo pode ser definido como um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). |[Visão geral da transmissão ao vivo](live-streaming-overview.md)<br/><br/>[Eventos ao Vivo e Saídas Dinâmicas](live-events-outputs-concept.md)|
|Monitoramento com grade de eventos|Para ver o andamento do trabalho, use **Event Grid**. Os Serviços de Mídia também emitem os tipos de eventos ao vivo. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. |[Lidando com os eventos da Grade de Eventos](reacting-to-media-services-events.md)<br/><br/>[Esquemas](media-services-event-schemas.md)|
|Monitoramento com monitor do Azure|Monitore métricas e registros de diagnóstico que ajudam você a entender como seus aplicativos estão se saindo com o Azure Monitor.|[Métrica e logs de diagnóstico](media-services-metrics-diagnostic-logs.md)<br/><br/>[Esquemas de logs de diagnóstico](media-services-diagnostic-logs-schema.md)|
|Clientes do Player|Você pode usar o Azure Media Player para reproduzir conteúdo de mídia transmitido pelos Serviços de Mídia em uma grande variedade de navegadores e dispositivos. O Azure Media Player usa padrões do setor, como HTML5, Media Source Extensions (MSE) e Encrypted Media Extensions (EME) para fornecer uma experiência de streaming adaptável enriquecida. |[Visão geral do Player de Mídia do Azure](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Codificar o arquivo remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o arquivo carregado e o fluxo de vídeo – .NET](stream-files-tutorial-with-api.md)
* [Transmitir ao vivo - .NET](stream-live-tutorial-with-api.md)
* [Analisar vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128 – .NET](protect-with-aes128.md)
* [Criptografar dinamicamente com vários DRM – .NET](protect-with-drm.md)
