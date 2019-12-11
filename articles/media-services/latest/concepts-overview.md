---
title: Conceitos e terminologia dos serviços de mídia do Azure – Azure | Microsoft Docs
description: Este tópico fornece uma breve visão geral da terminologia e dos conceitos dos serviços de mídia do Azure e fornece links para obter mais detalhes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 39bdcc94b785371044b5d49fd844a06a176a8fba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970029"
---
# <a name="media-services-concepts"></a>Conceitos dos serviços de mídia

Este tópico fornece uma breve visão geral da terminologia e dos conceitos dos serviços de mídia do Azure. O artigo também fornece links para artigos com uma explicação detalhada dos conceitos e da funcionalidade dos serviços de mídia v3. 

Os conceitos fundamentais descritos nestes tópicos devem ser examinados antes do início do desenvolvimento.

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) compatíveis.

## <a name="terminology"></a>Terminologia

Esta seção mostra como alguns termos comuns do setor são mapeados para a API dos serviços de mídia v3.

### <a name="live-event"></a>Evento ao Vivo

Um **evento ao vivo** representa um pipeline para ingestão, transcodificação (opcionalmente) e empacotamento de fluxos ao vivo de vídeo, áudio e metadados em tempo real.

Para clientes que migram de APIs do Media Services v2, o **evento ao vivo** substitui a entidade de **canal** na v2. Para obter mais informações, consulte [migrando de v2 para v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Ponto de extremidade de streaming (empacotamento e origem)

Um **ponto de extremidade de streaming** representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente, usando um dos protocolos de mídia de streaming comuns (HLS ou Dash). Além disso, o **ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para a DRMs líder do setor.

No setor de streaming de mídia, esse serviço é comumente conhecido como um **empacotador** ou **origem**.  Outros termos comuns do setor para esse recurso incluem JITP (just-in-time-Packager) ou JITE (codificação just-in-time). 
 
## <a name="cloud-upload-and-storage"></a>Upload e armazenamento na nuvem

Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia e carregar seus arquivos digitais em **ativos**.

- [Upload e armazenamento na nuvem](storage-account-concept.md)
- [Conceito de ativos](assets-concept.md)

## <a name="encoding"></a>Codificação

Depois de carregar seus arquivos de mídia digital de alta qualidade em ativos, você pode codificá-los em formatos que podem ser reproduzidos em uma ampla variedade de navegadores e dispositivos. 

Para codificar com os serviços de mídia v3, você precisa criar **transformações** e **trabalhos**.

![Transformações](./media/encoding/transforms-jobs.png)

- [Transformações e Trabalhos](transforms-jobs-concept.md)
- [Codificação com os serviços de mídia](encoding-concept.md)

## <a name="media-analytics"></a>Análise de mídia

Para analisar os arquivos de vídeo e áudio, você também precisa criar **transformações** e **trabalhos**.

- [Analisando arquivos de áudio e vídeo](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empacotamento, entrega, proteção

Depois que o conteúdo é codificado, você pode tirar proveito do **empacotamento dinâmico**. Nos serviços de mídia, um **ponto de extremidade de streaming**/Origin é o serviço de empacotamento dinâmico usado para fornecer conteúdo de mídia aos players cliente. Para disponibilizar vídeos no ativo de saída para os clientes para reprodução, você precisa criar um **localizador de streaming** e, em seguida, criar URLs de streaming. 

Ao criar o **localizador de streaming**, além do nome do ativo, você precisa especificar a **política de streaming**. **As políticas de streaming** permitem definir protocolos de streaming e opções de criptografia (se houver) para seus **localizadores de streaming**.

O empacotamento dinâmico é usado se você transmite seu conteúdo ao vivo ou sob demanda. O diagrama a seguir mostra o streaming sob demanda com fluxo de trabalho de empacotamento dinâmico.

![Empacotamento Dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Com os serviços de mídia, você pode entregar seu conteúdo ao vivo e sob demanda criptografado dinamicamente com criptografia AES (AES-128) ou/e qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados.

Se especificar as opções de criptografia em seu fluxo, crie a **política de chave de conteúdo** e associe-a ao seu **localizador de streaming**. A **política de chave de conteúdo** permite que você configure como a chave de conteúdo é entregue aos clientes finais.

A seguinte imagem ilustra o fluxo de trabalho de proteção de conteúdo dos Serviços de Mídia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;a criptografia dinâmica dá suporte ao AES-128 "Clear Key", CBCS e CENC. 

Você pode usar **manifestos dinâmicos** dos serviços de mídia para transmitir apenas uma rendição ou subclipes específicos do seu vídeo. No exemplo a seguir, o codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo ISO MP4s (de 180p para 1080p). O recurso codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, é mostrado o manifesto HLS para o ativo sem filtros (ele contém todas as sete representações).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro chamado "ott". O filtro "ott" especifica a remoção de todas as taxas de bits abaixo de 1 Mbps, o que resultou na remoção dos dois níveis de qualidade inferiores na resposta. Na parte inferior direita, é mostrado o manifesto HLS, ao qual foi aplicado um filtro chamado "mobile". O filtro "mobile" especifica a remoção de representações em que a resolução é maior do que 720p, resultando na remoção de duas representações de 1080p.

![Filtragem de representação](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Empacotamento dinâmico](dynamic-packaging-overview.md)
- [Pontos de Extremidade de Streaming](streaming-endpoint-concept.md)
- [Localizadores de Streaming](streaming-locators-concept.md)
- [Políticas de Streaming](streaming-policy-concept.md)
- [Políticas de Chave de Conteúdo](content-key-policy-concept.md)
- [Proteção de conteúdo](content-protection-overview.md)
- [Manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Filtros](filters-concept.md)

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="live-streaming"></a>Transmissão ao vivo

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. **Eventos ao Vivo** são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Quando você cria um **evento ao vivo**, é criado um ponto de extremidade de entrada que pode ser usado para enviar um sinal ao vivo de um codificador remoto. Depois que o fluxo fluir para o **evento ao vivo**, você poderá iniciar o evento de streaming criando um **ativo**, uma **saída ao vivo**e um **localizador de streaming**. A **saída ao vivo** arquivará o fluxo no **ativo** e o tornará disponível para os visualizadores por meio do ponto de **extremidade de streaming**. Um **evento ao vivo** pode ser um dos dois tipos: **codificação ativa**e de **passagem** .

A imagem a seguir ilustra o fluxo de trabalho do tipo de passagem:

![passagem](./media/live-streaming/pass-through.svg)

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Eventos ao Vivo e Saídas Dinâmicas ](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitoramento

### <a name="event-grid"></a>Grade de Eventos

Para ver o progresso do trabalho, você deve usar a **grade de eventos**. Os serviços de mídia também emite os tipos de evento ao vivo. Com a Grade de Eventos, seus aplicativos podem escutar e reagir a eventos de praticamente todos os serviços do Azure, bem como de origens personalizadas. 

- [Manipulando eventos de grade de eventos](reacting-to-media-services-events.md)
- [Esquemas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitore métricas e logs de diagnóstico que ajudam você a entender como seus aplicativos estão sendo executados com o Azure Monitor.

- [Métricas e logs de diagnóstico](media-services-metrics-diagnostic-logs.md)
- [Esquemas de logs de diagnóstico](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clientes do Player

Você pode usar Player de Mídia do Azure para reproduzir conteúdo de mídia transmitido pelos serviços de mídia em uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure usa padrões do setor, como HTML5, MSE (Media Source Extensions) e EME (Encrypted Media Extensions) para fornecer uma experiência avançada de streaming adaptável. 

- [Visão geral do Player de Mídia do Azure](use-azure-media-player.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximos passos

* [Codificar o arquivo remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o arquivo carregado e o fluxo de vídeo – .NET](stream-files-tutorial-with-api.md)
* [Transmitir ao vivo – .NET](stream-live-tutorial-with-api.md)
* [Analisar vídeo – .NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128 – .NET](protect-with-aes128.md)
* [Criptografar dinamicamente com vários DRM – .NET](protect-with-drm.md) 
