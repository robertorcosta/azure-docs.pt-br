---
title: Filtre seus manifestos usando o Dynamic Packager
titleSuffix: Azure Media Services
description: Aprenda a criar filtros usando o Dynamic Packager para filtrar e transmitir seletivamente seus manifestos.
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
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186207"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtre seus manifestos usando o Dynamic Packager

Quando você está fornecendo conteúdo de streaming de bitrate adaptativo para dispositivos, às vezes você precisa publicar várias versões de um manifesto para direcionar recursos específicos do dispositivo ou largura de banda de rede disponível. O [Dynamic Packager](dynamic-packaging-overview.md) permite especificar filtros que podem filtrar codecs específicos, resoluções, bitrates e combinações de faixas de áudio em tempo real. Esta filtragem remove a necessidade de criar várias cópias. Você simplesmente precisa publicar uma nova URL com um conjunto específico de filtros configurados para seus dispositivos-alvo (iOS, Android, SmartTV ou navegadores) e os recursos de rede (cenários de alta largura de banda, celular ou de baixa largura de banda). Neste caso, os clientes podem manipular o streaming de seu conteúdo através da seqüência de consultas (especificando [filtros de ativos ou filtros de conta](filters-concept.md)disponíveis) e usar filtros para transmitir seções específicas de um fluxo.

Alguns cenários de entrega exigem que você certifique-se de que um cliente não pode acessar faixas específicas. Por exemplo, talvez você não queira publicar um manifesto que contenha faixas HD para um nível específico de assinante. Ou, talvez você queira remover faixas específicas de bitrate adaptativo (ABR) para reduzir o custo de entrega para um dispositivo específico que não se beneficiaria das faixas adicionais. Neste caso, você pode associar uma lista de filtros pré-criados com o [localizador de streaming](streaming-locators-concept.md) na criação. Os clientes, então, não podem manipular como o conteúdo é transmitido porque é definido pelo **Localizador de Streaming**.

Você pode combinar filtragem através da especificação de [filtros no Streaming Locator](filters-concept.md#associating-filters-with-streaming-locator) + filtros adicionais específicos do dispositivo que seu cliente especifica na URL. Essa combinação é útil para restringir faixas adicionais, como metadados ou fluxos de eventos, linguagens de áudio ou faixas de áudio descritivas.

Essa capacidade de especificar diferentes filtros em seu fluxo fornece uma poderosa solução de manipulação **do Manifesto Dinâmico** para segmentar vários cenários de casos de uso para seus dispositivos de destino. Este tópico explica conceitos relacionados a **Manifestos Dinâmicos** e dá exemplos de cenários em que você pode usar esse recurso.

> [!NOTE]
> Os Manifestos Dinâmicos não alteram o ativo e o manifesto padrão para esse ativo.

## <a name="overview-of-manifests"></a>Visão geral dos manifestos

O Azure Media Services suporta protocolos HLS, MPEG DASH e Smooth Streaming. Como parte do [Dynamic Packaging,](dynamic-packaging-overview.md)os manifestos do cliente de streaming (HLS Master Playlist, DASH Media Presentation Description [MPD] e Smooth Streaming) são gerados dinamicamente com base no seletor de formato na URL. Para obter mais informações, consulte os protocolos de entrega no [fluxo de trabalho comum demanda](dynamic-packaging-overview.md#delivery-protocols).

### <a name="get-and-examine-manifest-files"></a>Obter e examinar arquivos de manifesto

Você especifica uma lista de condições de propriedade de filtragem com base em quais faixas do seu fluxo (ao vivo ou vídeo demanda [VOD]) devem ser incluídas em um manifesto criado dinamicamente. Para obter e examinar as propriedades das faixas, você precisa carregar o manifesto Smooth Streaming primeiro.

O [upload, codificação e fluxo de arquivos com](stream-files-tutorial-with-api.md#get-streaming-urls) o tutorial .NET mostra como construir as URLs de streaming com .NET. Se você executar o aplicativo, uma das URLs aponta para o manifesto do Smooth Streaming: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/> Copie e cole o URL na barra de endereços de um navegador. O arquivo será baixado. Você pode abri-lo em qualquer editor de texto.

Para um exemplo REST, consulte [Enviar, codificar e transmitir arquivos com REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Monitorar a taxa de bits de um fluxo de vídeo

Você pode usar a página de demonstração do [ Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para monitorar a taxa de bits de um fluxo de vídeo. A página de demonstração exibe informações de diagnóstico na guia **Diagnóstico:**

![Diagnóstico do Player de Mídia do Azure][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Exemplos: URLs com filtros na cadeia de consulta

Você pode aplicar filtros aos protocolos de streaming ABR: HLS, MPEG-DASH e Smooth Streaming. A tabela a seguir mostra alguns exemplos de URLs com filtros:

|Protocolo|Exemplo|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Filtragem de representação

Você pode optar por codificar seu ativo para vários perfis de codificação (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) e vários bitrates de qualidade. No entanto, nem todos os dispositivos de cliente oferecerão suporte a todos os seus perfis e taxas de bits de todos os seus ativos. Por exemplo, dispositivos Android mais antigos suportam apenas H.264 Baseline+AACL. Enviar bitrates mais altos para um dispositivo que não pode obter os benefícios desperdiça largura de banda e computação de dispositivos. Tal dispositivo deve decodificar todas as informações dadas, apenas para dimensioná-la para exibição.

Com o Dynamic Manifest, você pode criar perfis de dispositivos (como celular, console ou HD/SD) e incluir as faixas e qualidades que você deseja fazer parte de cada perfil. Isso se chama filtragem de rendição. O diagrama a seguir mostra um exemplo disso.

![Exemplo de filtragem de rendição com Manifesto Dinâmico][renditions2]

No exemplo a seguir, o codificador foi usado para codificar um ativo mezzanine em sete representações de vídeo ISO MP4s (de 180p para 1080p). O ativo codificado pode ser [embalado dinamicamente](dynamic-packaging-overview.md) em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.

A parte superior do diagrama a seguir mostra o manifesto HLS para o ativo sem filtros. (Contém todas as sete representações.)  No inferior esquerdo, o diagrama mostra um manifesto HLS ao qual um filtro chamado "ott" foi aplicado. O filtro "ott" especifica a remoção de todas as bits abaixo de 1 Mbps, de modo que os dois níveis de qualidade inferiores foram retirados na resposta. No lado inferior direito, o diagrama mostra o manifesto HLS ao qual foi aplicado um filtro chamado "móvel". O filtro "móvel" especifica a remoção de representações onde a resolução é maior que 720p, de modo que as duas rendições 1080p foram retiradas.

![Filtragem de rendição com Manifesto Dinâmico][renditions1]

## <a name="removing-language-tracks"></a>Remover faixas de idiomas
Seus ativos podem incluir vários idiomas de áudio, como inglês, espanhol, francês e assim por diante. Normalmente, o Player SDK gerencia a seleção padrão de faixas de áudio e faixas de áudio disponíveis por seleção do usuário.

O desenvolvimento desses SDKs do Jogador é desafiador porque requer implementações diferentes entre estruturas de jogadores específicas do dispositivo. Além disso, em algumas plataformas, as APIs do Jogador são limitadas e não incluem o recurso de seleção de áudio onde os usuários não podem selecionar ou alterar a faixa de áudio padrão. Com filtros de ativos, você pode controlar o comportamento criando filtros que incluem apenas linguagens de áudio desejadas.

![Filtragem de faixas de linguagem com Manifesto Dinâmico][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Aparando o início de um ativo

Na maioria dos eventos de transmissão ao vivo, os operadores executam alguns testes antes do evento real. Por exemplo, eles podem incluir uma lousa como esta antes do início do evento: "O programa começará momentaneamente".

Se o programa estiver sendo arquivado, o teste e os dados da imagem fixa também são arquivados e incluídos na apresentação. No entanto, essas informações não devem ser mostradas aos clientes. Com o manifesto dinâmico, é possível criar um filtro de hora de início e remover os dados indesejados do manifesto.

![Aparando o início de um ativo com Manifesto Dinâmico][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Criar subclipes (exibições) de um arquivo ao vivo

Muitos eventos ao vivo são de longa duração e o arquivamento dinâmico pode incluir vários eventos. Após o término do evento ao vivo, talvez os difusores queiram dividir o arquivo ao vivo em sequências lógicas de início e parada do programa.

Você pode publicar esses programas virtuais separadamente sem postar o processamento do arquivo ao vivo e não criar ativos separados (o que não recebe o benefício dos fragmentos armazenados em cache existentes nas CDNs). Exemplos desses programas virtuais são os tempos de um jogo de futebol ou de basquete, entradas no beisebol ou eventos individuais de qualquer programa de esportes.

Com o Dynamic Manifest, você pode criar filtros usando tempos de início/fim e criar visualizações virtuais sobre o topo do seu arquivo ao vivo.

![Filtro de subclipe com Manifesto Dinâmico][subclip_filter]

Aqui está o recurso filtrado:

![Ativo filtrado com Manifesto Dinâmico][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Ajustando a janela de apresentação (DVR)

Atualmente, o Azure Media Services oferece arquivamento circular onde a duração pode ser configurada entre 1 minuto e 25 horas. A filtragem de manifesto pode ser usada para criar uma janela DVR com rolagem que ultrapassa o arquivo, sem excluir a mídia. Há muitos cenários em que os difusores podem desejar fornecer uma janela DVR limitada que se move com a borda ao vivo e, ao mesmo tempo manter uma janela de arquivamento maior. Uma emissora pode querer usar os dados que estão fora da janela dvr para destacar clipes, ou eles podem querer fornecer diferentes janelas de DVR para diferentes dispositivos. Por exemplo, a maioria dos dispositivos móveis não suporta grandes janelas de DVR (você pode ter uma janela de DVR de 2 minutos para dispositivos móveis e uma hora para clientes de desktop).

![Janela DVR com Manifesto Dinâmico][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustar o LiveBackoff (posição ao vivo)

A filtragem de manifesto pode ser usada para remover vários segundos da borda ao vivo de um programa ao vivo. A filtragem permite que as emissoras assistam à apresentação no ponto de publicação de pré-visualização e criem pontos de inserção de anúncios antes que os espectadores recebam a transmissão (recuada por 30 segundos). Os radiodifusores podem então empurrar esses anúncios para suas estruturas de clientes a tempo de receber e processar as informações antes da oportunidade de anúncio.

Além do suporte ao anúncio, a configuração de back-off ao vivo pode ser usada para ajustar a posição dos espectadores para que, quando os clientes derrapem e atinjam a borda ao vivo, eles ainda possam obter fragmentos do servidor. Dessa forma, os clientes não receberão um erro HTTP 404 ou 412.

![Filtro para back-off ao vivo com Manifesto Dinâmico][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinar várias regras em um único filtro

É possível combinar várias regras de filtragem em um único filtro. Por exemplo, você pode definir uma "regra de alcance" para remover lousas de um arquivo ao vivo e também filtrar bitrates disponíveis. Quando você está aplicando várias regras de filtragem, o resultado final é a intersecção de todas as regras.

![Múltiplas regras de filtragem com Manifesto Dinâmico][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinando vários filtros (composição de filtros)

Você também pode combinar vários filtros em uma única URL. O seguinte cenário demonstra por que talvez seja conveniente combinar filtros:

1. Você precisa filtrar suas qualidades de vídeo para dispositivos móveis, como Android ou iPad (a fim de limitar as qualidades de vídeo). Para remover as qualidades indesejadas, você criará um filtro de conta adequado para os perfis do dispositivo. Você pode usar filtros de conta para todos os seus ativos a mesma conta do Media Services sem qualquer associação adicional.
1. Você também deseja cortar a hora de início e de término de um ativo. Para fazer o corte, você criará um filtro de ativos e definirá o tempo de início/término.
1. Você quer combinar ambos os filtros. Sem combinação, você precisaria adicionar filtragem de qualidade ao filtro de corte, o que dificultaria o uso do filtro.

Para combinar filtros, defina os nomes dos filtros para a URL manifesto/playlist em formato delimitado por ponto e vírgula. Vamos supor que você tenha um filtro chamado *MyMobileDevice* que filtra qualidades, e você tem outro chamado *MyStartTime* para definir uma hora de início específica. Você pode combinar até três filtros.

Para mais informações, consulte [este post no blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Considerações e limitações

- Os valores para **forceEndTimestamp,** **presentationWindowDuration**e **liveBackoffDuration** não devem ser definidos para um filtro VOD. São usados apenas para cenários de filtros ao vivo.
- Um manifesto dinâmico opera nos limites do GOP (quadros-chave), de modo que o corte tem precisão gop.
- Você pode usar o mesmo nome de filtro para filtros de conta e ativos. Os filtros de ativos têm maior precedência e substituirão os filtros da conta.
- Se você atualizar um filtro, pode levar até 2 minutos para que o ponto final de streaming atualize as regras. Se você usou filtros para servir o conteúdo (e fez cache do conteúdo em proxies e caches de CDN), atualizar esses filtros pode resultar em falhas do jogador. Recomendamos que você limpe o cache depois de atualizar o filtro. Se essa opção não for possível, considere usar um filtro diferente.
- Os clientes precisam baixar manualmente o manifesto e analisar o carimbo de horário exato de início e a escala de tempo.

    - Para determinar as propriedades das faixas em um ativo, [obtenha e examine o arquivo manifesto](#get-and-examine-manifest-files).
    - A fórmula para definir as propriedades de carimbo de tempo do filtro de ativos é: <br/>startTimestamp &lt;= tempo de&gt; +  &lt;início no tempo&gt; de início do filtro esperado em segundos * escala de tempo

## <a name="next-steps"></a>Próximas etapas

Os seguintes artigos mostram como criar filtros de forma programática:  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com o .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
