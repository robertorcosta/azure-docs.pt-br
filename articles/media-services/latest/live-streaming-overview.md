---
title: Visão geral da transmissão ao vivo com o Azure Media Services v3 | Microsoft Docs
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure v3.
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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068014"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os Serviços de Mídia do Azure v3

O Azure Media Services permite entregar eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os Serviços de Mídia, você precisa do seguinte:  

- Uma câmera é usada para capturar o evento ao vivo.<br/>Para obter ideias de instalação, confira [Configuração da engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se você não tiver acesso a uma câmera, ferramentas como [o Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) podem ser usadas para gerar uma transmissão ao vivo a partir de um arquivo de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado para os Serviços de Mídia. O feed de contribuição pode incluir sinais relacionados à publicidade, como os marcadores SCTE-35.<br/>Para obter uma lista dos codificadores de transmissão ao vivo recomendados, confira [Codificadores de transmissão ao vivo](recommended-on-premises-live-encoders.md). Além disso, confira este blog: [Produção de streaming ao vivo com OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes nos Serviços de Mídia, que permitem a você ingerir, visualizar, empacotar, gravar, criptografar e transmitir o evento ao vivo para seus clientes ou para um CDN para distribuição posterior.

Este artigo fornece uma visão geral e orientação de transmissão ao vivo com serviços de mídia e links para outros artigos relevantes.
 
> [!NOTE]
> Você pode usar o [portal Azure](https://portal.azure.com/) para gerenciar v3 [Eventos ao Vivo,](live-events-outputs-concept.md)ver v3 [Assets,](assets-concept.md)obter informações sobre como acessar APIs. Para todas as outras tarefas de gerenciamento (por exemplo, Transformações e Empregos), use a [API REST,](https://docs.microsoft.com/rest/api/media/) [CLI](https://aka.ms/ams-v3-cli-ref)ou uma das [SDKs](media-services-apis-overview.md#sdks)suportadas.

## <a name="dynamic-packaging"></a>Empacotamento Dinâmico

Com os Serviços de Mídia, você pode aproveitar o [Dynamic Packaging](dynamic-packaging-overview.md), que permite visualizar e transmitir suas transmissões ao vivo nos [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a partir do feed de contribuição que está sendo enviado para o serviço. Seus espectadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar o [Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos da Web ou móveis para fornecer seu fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Criptografia Dinâmica

A criptografia dinâmica permite que você criptografe dinamicamente seu conteúdo ao vivo ou demanda com o AES-128 ou qualquer um dos três principais sistemas de gerenciamento de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os serviços de mídia também fornecem um serviço de distribuição de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) para os clientes autorizados. Para obter mais informações, consulte [criptografia Dinâmica](content-protection-overview.md).

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="dynamic-manifest"></a>Manifesto Dinâmico

A filtragem dinâmica é usada para controlar o número de faixas, formatos, bitrates e janelas de tempo de apresentação que são enviadas aos jogadores. Para obter mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de eventos ao vivo

[Eventos ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e pelo processamento dos feeds de vídeo ao vivo. Um evento ao vivo pode ser definido como um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). Para obter detalhes sobre transmissão ao vivo em Media Services v3, consulte [Eventos ao vivo e saídas ao vivo](live-events-outputs-concept.md).

### <a name="pass-through"></a>Passagem

![passagem](./media/live-streaming/pass-through.svg)

Ao usar o **evento ao vivo**de passagem, você conta com o codificador ao vivo no local para gerar uma transmissão de vídeo de taxa de bits múltipla e enviá-la como feed de contribuição para o Evento Ao Vivo (usando o PROTOCOLO DE ENTRADA RTMP ou fragmentado-MP4). O Evento Ao Vivo, em seguida, leva através das transmissões de vídeo recebidas para o empacotadora dinâmico (Streaming Endpoint) sem qualquer transcodificação adicional. Esse evento ao vivo é otimizado para eventos ao vivo de longa duração ou transmissão ao vivo linear 24x365. 

### <a name="live-encoding"></a>Codificação ativa  

![Codificação ativa](./media/live-streaming/live-encoding.svg)

Ao usar a codificação em nuvem com serviços de mídia, você configuraria seu codificador ao vivo no local para enviar um único vídeo bitrate como o feed de contribuição (até 32Mbps agregado) para o Evento Ao Vivo (usando o protocolo de entrada RTMP ou fragmentado-MP4). O Evento Ao Vivo transcodifica o fluxo de bits de entrada em [várias transmissões de vídeo bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) em resoluções variadas para melhorar a entrega e o torna disponível para entrega a dispositivos de reprodução através de protocolos padrão do setor, como MPEG-DASH, Apple HTTP Live Streaming (HLS) e Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

Transcrição ao vivo é um recurso que você pode usar com eventos ao vivo que são transmissores ou codificação ao vivo. Para obter mais informações, consulte [transcrição ao vivo](live-transcription.md). Quando esse recurso é ativado, o serviço usa o recurso [Fala-texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio recebido em texto. Este texto é então disponibilizado para entrega, juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como um recurso de pré-visualização em West US 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para entender o fluxo de trabalho de transmissão ao vivo no Media Services v3, você tem que primeiro revisar e entender os seguintes conceitos: 

- [Pontos finais de streaming](streaming-endpoint-concept.md)
- [Eventos ao Vivo e Saídas Dinâmicas](live-events-outputs-concept.md)
- [Localizadores de Streaming](streaming-locators-concept.md)

### <a name="general-steps"></a>Passos gerais

1. Em sua conta de Serviços de Mídia, certifique-se de que o **Ponto Final de Streaming** (Origin) está sendo executado. 
2. Crie um [evento ao vivo](live-events-outputs-concept.md). <br/>Ao criar o evento, é possível especificar sua inicialização automática. Como alternativa, você poderá iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando a inicialização automática é definida como true, o Evento ao Vivo é iniciado logo após a criação. A cobrança começa assim que o Evento ao vivo começa a ser transmitido. É necessário chamar explicitamente o recurso Parar no Evento ao vivo para parar a cobrança adicional. Para saber mais, confira [Estados e cobrança do Evento ao vivo](live-event-states-billing.md).
3. Obtenha as URL(s) ingestão e configure seu codificador no local para usar a URL para enviar o feed de contribuição.<br/>Confira [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está sendo realmente recebida.
5. Crie um novo objeto **Asset.** 

    Cada saída de vida está associada a um ativo, que ele usa para gravar o vídeo no contêiner de armazenamento azure associado. 
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou para que o fluxo possa ser arquivado no ativo.

    As Saídas ao Vivo começam na criação e terminam quando são excluídas. Quando você exclui a saída de vida, você não está excluindo o ativo e o conteúdo subjacentes no ativo.
7. Crie um **localizador de streaming** com os [tipos de política de streaming incorporadas](streaming-policy-concept.md).

    Para publicar a saída ao vivo, você deve criar um Localizador de Streaming para o ativo associado. 
8. Liste os caminhos no **Localizador de Streaming** para retornar as URLs a serem usadas (elas são determinísticas).
9. Obtenha o nome de host para **o Ponto Final** de Streaming (Origin) que deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Caso deseje que o **Evento ao Vivo** deixe de ser visível, você precisará interromper a transmissão do evento e excluir o **Localizador de Streaming**.
12. Se você tiver terminado o fluxo de eventos e deseja limpar os recursos provisionados anteriormente, siga o procedimento a seguir.

    * Pare de enviar o fluxo por push por meio do codificador.
    * Interrompa o Evento ao Vivo. Depois que o Evento ao Vivo estiver parado, ele não incorrerá em nenhum encargo. Quando for necessário iniciá-lo novamente ele terá a mesma URL de ingestão, portanto, você não precisará reconfigurar seu codificador.
    * Você pode parar seu ponto de extremidade de Streaming, a menos que você deseje continuar a fornecer o arquivo morto do evento ao vivo como um fluxo sob demanda. Se o Evento ao Vivo estiver no estado interrompido, ele não incorrerá em nenhum encargo.

O ativo para o que a saída ao vivo está arquivando, automaticamente se torna um ativo sob demanda quando a saída ao vivo é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeSaídasOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente saídas ao vivo na parada. 

> [!TIP]
> Veja [tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md), o artigo examina o código que implementa as etapas descritas acima.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores dinâmicos recomendados](recommended-on-premises-live-encoders.md)
- [Usando um DVR em nuvem](live-event-cloud-dvr.md)
- [Comparação de recursos de tipos de Evento ao Vivo](live-event-types-comparison.md)
- [Estados e cobrança](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Veja o artigo [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Transmissão ao vivo quickstart] (live-events-wirecast-quickstart.md.
* [Tutorial de live streaming](stream-live-tutorial-with-api.md)
* [Diretrizes de migração dos Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md)
