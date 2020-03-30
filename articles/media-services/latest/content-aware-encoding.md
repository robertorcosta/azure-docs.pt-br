---
title: Uma preconfiguração para codificação com reconhecimento de conteúdo - Azure Media Services
description: Este artigo discute a codificação com reconhecimento de conteúdo no Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772060"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Use a preconfiguração de codificação com reconhecimento de conteúdo para encontrar o valor ideal de bitrate para uma determinada resolução

Para preparar o conteúdo para entrega por [streaming de bitrate adaptativo,](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)o vídeo precisa ser codificado a várias taxas de bits (de alta a baixa). Isso garante uma degradação graciosa da qualidade, pois a taxa de bits é reduzida assim como a resolução do vídeo. Essa codificação de taxa de bitmúltipla faz uso da chamada escada de codificação – uma tabela de resoluções e bitrates, consulte as [predefinições de codificação incorporadas](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)dos Serviços de Mídia .

Você deve estar ciente do conteúdo que está processando e personalizar/sintonizar a escada de codificação para a complexidade do vídeo individual. A cada resolução, há um bitrate além do qual qualquer aumento de qualidade não é perceptivo – o codificador opera com esse valor de bitrate ideal. O próximo nível de otimização é selecionar as resoluções com base no conteúdo – por exemplo, um vídeo de uma apresentação do PowerPoint não se beneficia de ir abaixo de 720p. Indo mais longe, o codificador pode ser encarregado de otimizar as configurações de cada tomada dentro do vídeo. 

A [preconfiguração adaptive streaming](autogen-bitrate-ladder.md) da Microsoft aborda parcialmente o problema da variabilidade na qualidade e resolução dos vídeos de origem. Nossos clientes têm um mix variado de conteúdo, alguns em 1080p, outros em 720p, e alguns em SD e resoluções mais baixas. Além disso, nem todo o conteúdo de origem é mezanino de alta qualidade de estúdios de cinema ou TV. A preconfiguração adaptive streaming resolve esses problemas, garantindo que a escada bitrate nunca exceda a resolução ou a taxa média de bits do mezanino de entrada. No entanto, essa preconfiguração não examina propriedades de origem que não seja resolução e bitrate.

## <a name="the-content-aware-encoding"></a>A codificação com reconhecimento de conteúdo 

A predefinição de codificação com reconhecimento de conteúdo amplia o mecanismo de "streaming de bitrate adaptativo", incorporando uma lógica personalizada que permite ao codificador buscar o valor ideal de bitrate para uma determinada resolução, mas sem exigir uma análise computacional extensiva. Esta preconfiguração produz um conjunto de MP4s alinhados ao GOP. Dado qualquer conteúdo de entrada, o serviço realiza uma análise leve inicial do conteúdo de entrada, e usa os resultados para determinar o número ideal de camadas, bitrate apropriado e configurações de resolução para entrega por streaming adaptativo. Esta preconfiguração é particularmente eficaz para vídeos de baixa e média complexidade, onde os arquivos de saída serão em bits mais baixos do que o Adaptive Streaming predefinido, mas em uma qualidade que ainda entrega uma boa experiência aos espectadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados

Os gráficos de amostra a seguir mostram a comparação usando métricas de qualidade como [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) e [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A fonte foi criada concatenando clipes curtos de imagens de alta complexidade de filmes e programas de TV, com a intenção de enfatizar o codificador. Por definição, essa predefinição produz resultados que variam de conteúdo para conteúdo – também significa que, para algum conteúdo, pode não haver redução significativa na taxa de bits ou melhoria na qualidade.

![Curva de distorção de taxa (RD) usando PSNR](media/content-aware-encoding/msrv1.png)

**Figura 1: Curva de distorção de taxa (RD) usando métrica PSNR para fonte de alta complexidade**

![Curva de distorção de taxa (RD) usando VMAF](media/content-aware-encoding/msrv2.png)

**Figura 2: Curva de distorção de taxa (RD) usando métrica VMAF para fonte de alta complexidade**

Abaixo estão os resultados de outra categoria de conteúdo de origem, onde o codificador foi capaz de determinar que a entrada era de má qualidade (muitos artefatos de compressão por causa da taxa de bits baixa). Observe que, com a preconfiguração de reconhecimento de conteúdo, o codificador decidiu produzir apenas uma camada de saída – a um bitrate baixo o suficiente para que a maioria dos clientes pudesse reproduzir o fluxo sem empatar.

![Curva RD usando PSNR](media/content-aware-encoding/msrv3.png)

**Figura 3: Curva RD usando PSNR para entrada de baixa qualidade (a 1080p)**

![Curva RD usando VMAF](media/content-aware-encoding/msrv4.png)

**Figura 4: Curva RD usando VMAF para entrada de baixa qualidade (a 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Como usar a predefinido de codificação com reconhecimento de conteúdo 

Você pode criar transformações que usam esta predefinida da seguinte forma. 

> [!TIP]
> Consulte a seção [Próximapassos](#next-steps) para tutoriais que usam saídas de transformes. O ativo de saída pode ser entregue a partir de pontos finais de streaming do Media Services em protocolos como MPEG-DASH e HLS (como mostrado nos tutoriais).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Trabalhos de `ContentAwareEncoding` codificação usando a preset estão sendo cobrados com base nos minutos de saída. 

## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Enviar, codificar e transmitir vídeos com media services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo - REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo - CLI](stream-files-cli-quickstart.md)
* [Tutorial: Encodifique um arquivo remoto baseado em URL e transmita o vídeo - .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Encodie um arquivo remoto com base na URL e transmita o vídeo - Node.js](stream-files-nodejs-quickstart.md)
