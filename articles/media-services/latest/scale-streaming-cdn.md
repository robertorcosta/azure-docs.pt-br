---
title: Transmitir conteúdo com integração CDN
titleSuffix: Azure Media Services
description: Saiba mais sobre streaming de conteúdo com integração de CDN, bem como pré-busca e Origin-Assist CDN-Prefetch.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128065"
---
# <a name="stream-content-with-cdn-integration"></a>Transmitir conteúdo com integração CDN

A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo.  

O CDN armazena o conteúdo transmitido a partir de um Media Services [Streaming Endpoint (origem)](streaming-endpoint-concept.md) por codec, por protocolo de streaming, por bitrate, por formato de contêiner e por criptografia/DRM. Para cada combinação de codec-streaming protocol-container formato-bitrate-encryption, haverá um cache cdn separado.

O conteúdo popular será servido diretamente do cache de CDN, desde que o fragmento de vídeo seja armazenado em cache. É provável que o conteúdo ao vivo seja armazenado em cache, porque normalmente há muitas pessoas assistindo exatamente o mesmo conteúdo. O conteúdo demanda pode ser um pouco mais complicado porque você pode ter algum conteúdo que seja popular e outros que não sejam. Se você tem milhões de ativos de vídeo onde nenhum deles é popular (apenas um ou dois espectadores por semana), mas você tem milhares de pessoas assistindo todos os vídeos diferentes, o CDN se torna muito menos eficaz.

Também é necessário considerar como o streaming adaptável funciona. Cada fragmento de vídeo individual é armazenado como sua própria entidade. Por exemplo, imagine a primeira vez que um determinado vídeo é assistido. Se o espectador pular por aí assistindo apenas alguns segundos aqui e ali, apenas os fragmentos de vídeo associados ao que a pessoa assistiu ficam armazenados em cache em CDN. Com o streaming adaptável, você normalmente tem de 5 a 7 taxas de bits diferentes de vídeo. Se uma pessoa está assistindo um bitrate e outra pessoa está assistindo um bitrate diferente, então cada um deles está armazenado separadamente no CDN. Mesmo que duas pessoas estejam assistindo o mesmo bitrate, eles podem estar transmitindo sobre protocolos diferentes. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é armazenado em cache separadamente. Portanto, cada taxa de bits e protocolo são armazenados em cache separadamente e apenas os fragmentos de vídeo que foram solicitados são armazenados em cache.

Ao decidir se habilita ou não o CDN no [ponto final](streaming-endpoint-concept.md)de streaming do Media Services, considere o número de espectadores antecipados. O CDN só ajuda se você estiver esperando muitos espectadores para o seu conteúdo. Se a concorrência máxima dos espectadores for inferior a 500, é recomendável desativar o CDN, já que o CDN é melhor com a concorrência.

Este tópico discute a habilitação da [integração do CDN.](#enable-azure-cdn-integration) Também explica o pré-busca (cache ativo) e o conceito [Origin-Assist CDN-Prefetch.](#origin-assist-cdn-prefetch)

## <a name="considerations"></a>Considerações

* O [ponto](streaming-endpoint-concept.md) `hostname` final de streaming e a URL de streaming permanecem os mesmos se você habilita ou não o CDN.
* Se você precisar da capacidade de testar seu conteúdo com ou sem CDN, crie outro ponto final de streaming que não esteja habilitado para CDN.

## <a name="enable-azure-cdn-integration"></a>Habilitar a integração da CDN do Azure

> [!IMPORTANT]
> Você não pode habilitar cdn para contas de teste ou student Azure.
>
> A integração do CDN está habilitada em todos os data centers do Azure, exceto nas regiões do Governo Federal e da China.

Depois que um ponto final de streaming é provisionado com CDN ativado, há um tempo de espera definido no Media Services antes que a atualização do DNS seja feita para mapear o ponto final de streaming para o ponto final do CDN.

Se quiser desabilitar/habilitar a CDN depois, o ponto de extremidade de streaming deverá estar no estado **interrompido**. Para que a integração da CDN do Azure seja habilitada e as alterações estejam ativas em todos os POPs da CDN talvez sejam necessárias até duas horas. No entanto, você pode iniciar seu endpoint de streaming e transmitir sem interrupções do ponto final do streaming. Uma vez que a integração esteja completa, o fluxo é entregue a partir do CDN. Durante o período de provisionamento, seu ponto final de streaming estará no estado **inicial** e você poderá observar o desempenho degradado.

Quando o ponto final de streaming Padrão é criado, ele é configurado por padrão com o Standard Verizon. Você pode configurar provedores Premium Verizon ou Standard Akamai usando APIs REST.

A integração dos Serviços de Mídia do Azure à CDN do Azure é implementada da **Verizon na CDN do Azure** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preço e provedores da CDN do Azure**.

> [!NOTE]
> Para obter detalhes sobre o Azure CDN, consulte a visão geral do [CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Determinar se uma alteração de DNS foi feita

Você pode determinar se a alteração do DNS foi feita em um ponto final de <https://www.digwebinterface.com>streaming (o tráfego está sendo direcionado para o CDN do Azure) usando . Se você vir azureedge.net nomes de domínio nos resultados, o tráfego agora está sendo apontado para o CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

O cache de CDN é um processo reativo. Se o CDN puder prever qual será o próximo objeto solicitado, o CDN poderá solicitar e armazenar proativamente o próximo objeto. Com esse processo, você pode obter um cache-hit para todos (ou a maioria) dos objetos, o que melhora o desempenho.

O conceito de prefetching se esforça para posicionar objetos na "borda da internet" na expectativa de que estes serão solicitados pelo jogador iminentemente, reduzindo assim o tempo para entregar esse objeto ao jogador.

Para alcançar esse objetivo, um endpoint de streaming (origem) e CDN precisam trabalhar lado a lado de algumas maneiras:

- A origem dos Serviços de Mídia precisa ter a "inteligência" (Origin-Assist) para informar a CDN sobre o próximo objeto a ser prefetch.
- O CDN faz o pré-busca e o cache (parte cdn-prefetch). A CDN também precisa ter a "inteligência" para informar a origem se é uma busca prefetch ou uma busca regular, lidar com as respostas 404 e uma maneira de evitar loop de pré-busca sem fim.

### <a name="benefits"></a>Benefícios

Os benefícios do recurso *Origin-Assist CDN-Prefetch* incluem:

- O Prefetch melhora a qualidade de reprodução de vídeo, pré-posicionando segmentos de vídeo antecipados na borda durante a reprodução, reduzindo a latência ao espectador e melhorando os tempos de download do segmento de vídeo. Isso resulta em um tempo de inicialinicialção de vídeo mais rápido e menores ocorrências de rebuffering.
- Este conceito é aplicável ao cenário geral de origem CDN e não se limita à mídia.
- A Akamai adicionou este recurso ao [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Esse recurso ainda não é aplicável ao CDN Akamai integrado com o ponto final de streaming do Media Services. No entanto, está disponível para clientes da Media Services que têm um contrato Akamai pré-existente e exigem integração personalizada entre o CDN da Akamai e a origem dos Serviços de Mídia.

### <a name="how-it-works"></a>Como ele funciona

O suporte à `Origin-Assist CDN-Prefetch` CDN para os cabeçalhos (tanto para streaming ao vivo quanto em vídeo demanda) está disponível para clientes que têm contrato direto com a Akamai CDN. O recurso envolve as seguintes trocas de cabeçalho HTTP entre o Akamai CDN e a origem dos Serviços de Mídia:

|Cabeçalho HTTP|Valores|Remetente|Receptor|Finalidade|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (padrão) ou 0 |CDN|Origem|Para indicar o CDN está ativado o pré-fetch.|
|`CDN-Origin-Assist-Prefetch-Path`| Exemplo: <br/>Fragmentos(vídeo=14000000000,format=mpd-time-cmaf)|Origem|CDN|Para fornecer caminho de pré-busca para CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (solicitação de pré-busca) ou 0 (solicitação regular)|CDN|Origem|Indicar a solicitação do CDN é uma pré-busca.|

Para ver parte da troca de cabeçalho em ação, você pode tentar as seguintes etapas:

1. Use Carteiro ou cURL para emitir uma solicitação à origem dos Serviços de Mídia para um segmento ou fragmento de áudio ou vídeo. Certifique-se de `CDN-Origin-Assist-Prefetch-Enabled: 1` adicionar o cabeçalho na solicitação.
2. Na resposta, você deve `CDN-Origin-Assist-Prefetch-Path` ver o cabeçalho com um caminho relativo como seu valor.

### <a name="supported-streaming-protocols"></a>Protocolos de streaming suportados

O `Origin-Assist CDN-Prefetch` recurso suporta os seguintes protocolos de streaming para streaming ao vivo e demanda:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Streaming suave

### <a name="faqs"></a>Perguntas frequentes

* E se uma URL de caminho de pré-busca for inválida para que o prefetch do CDN receba um 404?

    O CDN só armazenará uma resposta 404 por 10 segundos (ou outro valor configurado).

* Suponha que você tenha um vídeo demanda. Se o Prefetch do CDN estiver habilitado, esse recurso implica que uma vez que um cliente solicite o primeiro segmento de vídeo, o prefetch iniciará um loop para prefetch todos os segmentos de vídeo subsequentes na mesma taxa de bits?

    Não, o prefetch de CDN é feito somente após uma solicitação/resposta iniciada pelo cliente. O cdn-prefetch nunca é acionado por uma pré-busca, para evitar um loop de pré-busca.

* O recurso Origin-Assist CDN-Prefetch está sempre ligado? Como pode ser ligado/desligado?

    Esse recurso está desativado por padrão. Os clientes precisam atilá-lo via AKAMAI API.

* Para transmissão ao vivo, o que aconteceria com o Origin-Assist se o próximo segmento ou fragmento ainda não estivesse disponível?

    Neste caso, a origem dos Serviços de Mídia não fornecerá `CDN-Origin-Assist-Prefetch-Path` cabeçalho e o prefetch de CDN não ocorrerá.

* Como `Origin-Assist CDN-Prefetch` funciona com filtros de manifesto dinâmicos?

    Este recurso funciona independentemente do filtro manifesto. Quando o próximo fragmento estiver fora de uma janela de filtro, sua URL ainda será localizada olhando para o manifesto do cliente bruto e, em seguida, retornada como cabeçalho de resposta pré-fetch cdN. Assim, o CDN obterá a URL de um fragmento filtrado do manifesto DASH/HLS/Smooth. No entanto, o jogador nunca fará uma solicitação get à CDN para buscar esse fragmento, porque esse fragmento não está incluído no manifesto DASH/HLS/Smooth mantido pelo jogador (o jogador não sabe a existência desse fragmento).

* A lista de reprodução DASH MPD/HLS/Smooth manifest pode ser pré-rebuscada?

    Não, DASH MPD, hls master playlist, lista de reprodução de variantes HLS ou URL manifesto suave não é adicionado ao cabeçalho de pré-busca.

* URLs pré-fetch são relativos ou absolutos?

    Embora o Akamai CDN permita ambos, a origem dos Serviços de Mídia só fornece URLs relativos para o caminho de pré-busca, porque não há nenhum benefício aparente no uso de URLs absolutas.

* Esse recurso funciona com conteúdo protegido por DRM?

    Sim, uma vez que este recurso funciona no nível HTTP, ele não decodifica ou analisa qualquer segmento/fragmento. Não importa se o conteúdo é criptografado ou não.

* Esse recurso funciona com o SSAI (Server Side Ad Insertion, inserção de anúncios laterais do servidor)?
    
    Ele funciona para conteúdo original/principal (o conteúdo original do vídeo antes da inserção do anúncio), uma vez que o SSAI não altera o carimbo de data e hora do conteúdo de origem dos Serviços de Mídia. Se esse recurso funciona com conteúdo de anúncios depende se a origem do anúncio suporta origin-assist. Por exemplo, se o conteúdo de anúncios também estiver hospedado no Azure Media Services (mesma origem ou origem separada), o conteúdo de anúncios também será pré-rebuscado.

* Esse recurso funciona com o conteúdo UHD/HEVC?

    Sim.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* Certifique-se de revisar o documento [Streaming Endpoint (origem).](streaming-endpoint-concept.md)
* O exemplo [neste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto de extremidade de streaming padrão com .NET.
