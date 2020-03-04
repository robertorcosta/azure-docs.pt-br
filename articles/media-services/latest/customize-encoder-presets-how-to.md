---
title: Codificar transformação personalizada usando os serviços de mídia v3 .NET – Azure | Microsoft Docs
description: Este tópico mostra como usar os serviços de mídia do Azure v3 para codificar uma transformação personalizada usando o .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148328"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Como codificar com uma transformação personalizada - .NET

Quando estiver codificando com os serviços de mídia do Azure, você pode começar a usar rapidamente com uma das predefinições internas recomendadas com base em práticas recomendadas do setor, como demonstrado na [arquivos de Streaming](stream-files-tutorial-with-api.md) tutorial. Você também pode criar um personalizado predefinido para seus requisitos específicos de cenário ou dispositivo de destino.

## <a name="considerations"></a>Considerações

Ao criar predefinições personalizadas, as seguintes considerações se aplicam:

* Todos os valores de altura e largura no conteúdo de AVC devem ser um múltiplo de 4.
* Serviços de mídia do Azure v3, todas as taxas de bits de codifica são em bits por segundo. Isso é diferente das predefinições com nossas APIs v2, que usado quilobits por segundo, como a unidade. Por exemplo, se a taxa de bits na versão 2 foi especificada como 128 (quilobits/segundo), na v3 ele deve ser definido como 128000 (bits/segundo).

## <a name="prerequisites"></a>Prerequisites 

[Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>Baixar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET Core completo em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
O exemplo de predefinição personalizado está localizado na pasta [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Criar uma transformação com uma predefinição personalizada 

Ao criar uma nova [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você precisará especificar o que deseja produzir como uma saída. O parâmetro necessário é um objeto [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), como mostrado no código a seguir. Cada **TransformOutput** contém um **Predefinição**. O **predefinição** descreve as instruções passo a passo das operações de processamento de áudio e/ou vídeo que devem ser usados para gerar o desejado **TransformOutput**. O seguinte **TransformOutput** cria configurações personalizadas de saída de codec e camada.

Ao criar uma [Transformação](https://docs.microsoft.com/rest/api/media/transforms), você deverá verificar primeiro se já existe uma usando o método **Get**, conforme mostrado no código a seguir. Em Serviços de Mídia v3, os métodos **Get** em entidades retornarão **nulo** se a entidade não existir (uma verificação de maiúsculas e minúsculas no nome).

### <a name="example"></a>Exemplo

O exemplo a seguir define um conjunto de saídas que queremos ser gerado quando essa transformação é usada. Primeiro, adicionamos uma camada AacAudio para a codificação de áudio e duas camadas do H264Video para a codificação de vídeo. Nas camadas de vídeo, vamos atribuir rótulos para que eles podem ser usados nos nomes de arquivo de saída. Em seguida, queremos que a saída para incluir também as miniaturas. No exemplo a seguir, podemos especificar imagens no formato PNG, gerados em 50% da resolução de vídeo de entrada e em três carimbos de hora - {25%, 50%, 75} do comprimento do vídeo de entrada. Por fim, podemos especificar o formato para os arquivos de saída - um para vídeo + áudio e outro para as miniaturas. Como temos vários H264Layers, precisamos usar macros que geram nomes exclusivos por camada. Podemos pode usar um `{Label}` ou `{Bitrate}` macro, o exemplo mostra o antigo.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Próximas etapas

[Arquivos por streaming](stream-files-tutorial-with-api.md) 
