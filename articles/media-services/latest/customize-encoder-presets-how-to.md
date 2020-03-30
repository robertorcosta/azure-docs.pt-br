---
title: Encode custom transform usando Media Services v3 .NET - Azure | Microsoft Docs
description: Este tópico mostra como usar o Azure Media Services v3 para codificar uma transformação personalizada usando .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068042"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Como codificar com uma transformação personalizada - .NET

Ao codificar com o Azure Media Services, você pode começar rapidamente com uma das predefinições incorporadas recomendadas com base nas práticas recomendadas do setor, como demonstrado no tutorial [de arquivos de Streaming.](stream-files-tutorial-with-api.md) Você também pode construir uma predefinição personalizada para atingir seus requisitos específicos de cenário ou dispositivo.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores para altura e largura no conteúdo AVC devem ser um múltiplo de 4.
* No Azure Media Services v3, todas as bitrates de codificação estão em bits por segundo. Isto é diferente das predefinições com nossas APIs v2, que usavam kilobits/segundo como a unidade. Por exemplo, se a taxa de bits em v2 foi especificada como 128 (kilobits/segundo), em v3 seria definida como 128000 (bits/segundo).

## <a name="prerequisites"></a>Pré-requisitos 

[Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET Core completo em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo de predefinição personalizado está localizado na pasta [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma predefinição personalizada 

Ao criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisará especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. A **Preset** descreve as instruções passo-a-passo das operações de processamento de vídeo e/ou áudio que devem ser usadas para gerar o **TransformOutput**desejado . O seguinte **TransformOutput** cria configurações personalizadas de saída de codec e camada.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Nos Serviços de Mídia v3, **obter** métodos sobre entidades retornam **nulos** se a entidade não existir (uma verificação insensível ao caso no nome).

### <a name="example"></a>Exemplo

O exemplo a seguir define um conjunto de saídas que queremos gerar quando este Transform for usado. Primeiro adicionamos uma camada AacAudio para a codificação de áudio e duas camadas H264Video para a codificação de vídeo. Nas camadas de vídeo, atribuímos rótulos para que possam ser usados nos nomes dos arquivos de saída. Em seguida, queremos que a saída também inclua miniaturas. No exemplo abaixo especificamos imagens em formato PNG, geradas em 50% da resolução do vídeo de entrada, e em três carimbos de tempo - {25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, especificamos o formato para os arquivos de saída - um para vídeo + áudio e outro para as miniaturas. Como temos várias Camadas H264, temos que usar macros que produzem nomes únicos por camada. Podemos usar um `{Label}` `{Bitrate}` ou macro, o exemplo mostra o primeiro.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Próximas etapas

[Arquivos de streaming](stream-files-tutorial-with-api.md) 
