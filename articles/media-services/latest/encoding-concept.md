---
title: Codificação de vídeo e áudio com Serviços de Mídia
titleSuffix: Azure Media Services
description: Este artigo explica sobre a codificação de vídeo e áudio com o Azure Media Services.
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
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366570"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Codificação de vídeo e áudio com Serviços de Mídia

O termo codificação em Serviços de Mídia aplica-se ao processo de conversão de arquivos contendo vídeo digital e/ou áudio de um formato padrão para outro, com o objetivo de (a) reduzir o tamanho dos arquivos e/ou (b) produzir um formato compatível com uma ampla gama de dispositivos e aplicativos. Esse processo também é chamado de compressão de vídeo, ou transcodificação. Veja a [compactação de dados](https://en.wikipedia.org/wiki/Data_compression) e o [que é codificação e transcodificação?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Os vídeos são normalmente entregues a dispositivos e aplicativos por [download progressivo](https://en.wikipedia.org/wiki/Progressive_download) ou por streaming [adaptativo de bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Os Serviços de Mídia não faturam por trabalhos cancelados ou errados. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% das atas de trabalho. Você só é cobrado por trabalhos acabados.

* Para entregar por download progressivo, você pode usar o Azure Media Services para converter um arquivo de mídia digital (mezanino) em um arquivo [MP4,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) que contém vídeo codificado com o codec [H.264,](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) e áudio que foi codificado com o codec [AAC.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Este arquivo MP4 está escrito para um Ativo em sua conta de armazenamento. Você pode usar as APIs de armazenamento do Azure ou SDKs (por exemplo, [API de armazenamento REST](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)para baixar o arquivo diretamente. Se você criou o Ativo de saída com um nome de contêiner específico no armazenamento, use esse local. Caso contrário, você pode usar os Serviços de Mídia para [listar os URLs do contêiner de ativos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Para preparar o conteúdo para entrega por streaming de bitrate adaptativo, o arquivo mezanino precisa ser codificado em várias bits (de alta a baixa). Para garantir uma transição graciosa de qualidade, a resolução do vídeo é reduzida à medida que a taxa de bits é reduzida. Isso resulta na chamada escada de codificação – uma tabela de resoluções e bitrates (ver [escada de bitrate adaptativa gerada automaticamente](autogen-bitrate-ladder.md)). Você pode usar os Serviços de Mídia para codificar seus arquivos do mezanino em vários bitrates. Ao fazer isso, você receberá um conjunto de arquivos MP4 e arquivos de configuração de streaming associados gravados em um Ativo em sua conta de armazenamento. Em seguida, você pode usar o recurso [de Embalagem Dinâmica](dynamic-packaging-overview.md) em Serviços de Mídia para fornecer o vídeo através de protocolos de streaming como [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) e [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Isso exige que você crie um [Localizador de Streaming](streaming-locators-concept.md) e crie URLs de streaming correspondentes aos protocolos suportados, que podem ser entregues a dispositivos/aplicativos com base em seus recursos.

O diagrama a seguir mostra o fluxo de trabalho para codificação demanda com embalagem dinâmica.

![Fluxo de trabalho para codificação demanda com embalagem dinâmica](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Este tópico fornece orientação sobre como codificar seu conteúdo com o Serviços de Mídia do Microsoft Azure v3.

## <a name="transforms-and-jobs"></a>Transformações e trabalhos

Para codificar com os Serviços de Mídia v3 é necessário criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms) e um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs). A transformação define uma receita para suas configurações e saídas de codificação; o trabalho é um exemplo da receita. Para obter mais informações, consulte [Transformações e Empregos](transforms-jobs-concept.md).

Ao codificar com o Serviços de Mídia do Microsoft Azure, você usa predefinições para informar ao codificador como os arquivos de mídia de entrada devem ser processados. No Media Services v3, você usa o Standard Encoder para codificar seus arquivos. Por exemplo, é possível especificar a resolução de vídeo e/ou o número de canais de áudio que desejar no conteúdo codificado.

Você pode iniciar de forma rápida com uma das predefinições internas recomendadas com base nas melhores práticas do setor ou pode optar por criar uma predefinição personalizada para segmentar os requisitos de dispositivo ou cenário específico. Para obter mais informações, consulte [Codificar com uma transformação personalizada](customize-encoder-presets-how-to.md).

A partir de janeiro de 2019, ao codificar com o Codificador Padrão para produzir arquivos MP4, um novo arquivo .mpi é gerado e adicionado ao Ativo de saída. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

> [!NOTE]
> Você não deve modificar ou remover o arquivo MPI, ou tomar qualquer dependência em seu serviço sobre a existência (ou não) de tal arquivo.

### <a name="creating-job-input-from-an-https-url"></a>Criando entrada de trabalho a partir de uma URL HTTPS

Quando você envia Jobs para processar seus vídeos, você tem que dizer aos Serviços de Mídia onde encontrar o vídeo de entrada. Uma das opções é especificar uma URL HTTPS como uma entrada de trabalho. Atualmente, o Media Services v3 não suporta codificação de transferência em pedaços sobre URLs HTTPS.

#### <a name="examples"></a>Exemplos

* [Codificar a partir de uma URL HTTPS com .NET](stream-files-dotnet-quickstart.md)
* [Codificar a partir de uma URL HTTPS com REST](stream-files-tutorial-with-rest.md)
* [Codificar a partir de uma URL HTTPS com CLI](stream-files-cli-quickstart.md)
* [Codificar a partir de uma URL HTTPS com Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Criando entrada de trabalho a partir de um arquivo local

O vídeo de entrada pode ser armazenado como um Ativo de Serviço de Mídia. Nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no Armazenamento de Blobs do Azure).

#### <a name="examples"></a>Exemplos

[Codificar um arquivo local usando predefinições incorporadas](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Criação de entrada de trabalho com subclipping

Ao codificar um vídeo, você pode especificar também cortar ou cortar o arquivo de origem e produzir uma saída que tenha apenas uma parte desejada do vídeo de entrada. Essa funcionalidade funciona com qualquer [Transform](https://docs.microsoft.com/rest/api/media/transforms) que seja construída usando as predefinições [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou as predefinições [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Você pode especificar para criar um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs/create) com um único clipe de um vídeo sob demanda ou arquivo ao vivo (um evento gravado). A entrada do trabalho pode ser um Ativo ou uma URL HTTPS.

> [!TIP]
> Se você quiser transmitir um sublip do seu vídeo sem recodificar o vídeo, considere usar [manifestos de pré-filtragem com o Dynamic Packager](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exemplos

Veja exemplos:

* [Subclipe um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Subclipe um vídeo com REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Predefinições internas

Os Serviços de Mídia suportam as seguintes predefinições de codificação incorporada:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

O [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) é usado para definir uma predefinição interna para codificar o vídeo de entrada com o Codificador Standard.

As predefinições a seguir são atualmente têm suporte:

- **EncoderNamedPreset.AACGoodQualityAudio**: produz um único arquivo MP4 contendo apenas áudio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado): Para obter mais informações, consulte [a geração automática de uma escada bitrate](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: expõe uma preconfiguração experimental para codificação com reconhecimento de conteúdo. Dado qualquer conteúdo de entrada, o serviço tenta determinar automaticamente o número ideal de camadas e configurações apropriadas de bitrate e resolução para entrega por streaming adaptativo. Os algoritmos subjacentes continuarão a evoluir com o tempo. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, consulte [Predefinido experimental para codificação com reconhecimento de conteúdo](content-aware-encoding.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: produz um conjunto de oito arquivos MP4 alinhados ao GOP, variando de 6000 kbps a 400 kbps, e áudio AAC estéreo. A resolução inicia em 1080p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: produz um conjunto de seis arquivos MP4 alinhados ao GOP, variando de 3400 kbps a 400 kbps, e áudio AAC estéreo. A resolução inicia em 720p e diminui para 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: produz um conjunto de cinco arquivos MP4 alinhados ao GOP, variando de 1600 kbps a 400 kbps, e áudio AAC estéreo. A resolução inicia em 480p e diminui para 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: produz um arquivo MP4 onde o vídeo é codificado com codec H.264 a 6750 kbps e uma altura de imagem de 1080 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: produz um arquivo MP4 onde o vídeo é codificado com codec H.264 a 4500 kbps e uma altura de imagem de 720 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: produz um arquivo MP4 onde o vídeo é codificado com codec H.264 a 2200 kbps e uma altura de imagem de 480 pixels, e o áudio estéreo é codificado com codec AAC-LC a 64 kbps.

Para ver a lista de predefinições mais atualizada, consulte [predefinições incorporadas a serem usadas para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver como as predefinições são usadas, consulte [Uploading, codificação e streaming de arquivos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

O [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) descreve as configurações a serem usadas ao codificar o vídeo de entrada com o Codificador Standard. Use essa predefinição ao personalizar as predefinições de Transformação.

#### <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

- Todos os valores para altura e largura no conteúdo AVC devem ser um múltiplo de quatro.
- No Azure Media Services v3, todas as bitrates de codificação estão em bits por segundo. Isto é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 foi especificada como 128 (kilobits/segundo), em v3 seria definida como 128000 (bits/segundo).

### <a name="customizing-presets"></a>Personalização de predefinições

Os Serviços de Mídia oferece suporte completo para a personalização de todos os valores em predefinições, a fim de atender às suas necessidades e requisitos de codificação. Para exemplos que mostram como personalizar predefinições de codificadores, consulte a lista abaixo:

#### <a name="examples"></a>Exemplos

- [Personalizar predefinições com .NET](customize-encoder-presets-how-to.md)
- [Personalizar predefinições com a CLI](custom-preset-cli-howto.md)
- [Personalizar predefinições com REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Esquema predefinido

No Media Services v3, as predefinições são entidades fortemente digitadas na própria API. Você pode encontrar a definição de "esquema" para esses objetos em [Especificação de API Aberta (ou Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Você também pode visualizar as definições predefinidas (como **StandardEncoderPreset)** na [API REST,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (ou outra documentação de referência do Media Services v3 SDK).

## <a name="scaling-encoding-in-v3"></a>Codificação de escala na v3

Para dimensionar o processamento de mídia, consulte [Escala com CLI](media-reserved-units-cli-how-to.md).

## <a name="billing"></a>Cobrança

Os Serviços de Mídia não faturam por trabalhos cancelados ou errados. Por exemplo, um trabalho que atingiu 50% de progresso e é cancelado não é cobrado em 50% das atas de trabalho. Você só é cobrado por trabalhos acabados.

Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximas etapas

* [Faça upload, codificação e transmissão usando serviços de mídia](stream-files-tutorial-with-api.md).
* [Codificar a partir de uma URL HTTPS usando predefinições incorporadas](job-input-from-http-how-to.md).
* [Codifique um arquivo local usando predefinições incorporadas](job-input-from-local-file-how-to.md).
* [Crie uma predefinição personalizada para atingir seus requisitos específicos de cenário ou dispositivo.](customize-encoder-presets-how-to.md)
