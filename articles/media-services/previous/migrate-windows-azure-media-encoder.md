---
title: Migrar do Windows Azure Media Encoder para o Media Encoder Standard | Microsoft Docs
description: Este tópico discute como migrar do Azure Media Encoder para o processador de mídia Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513194"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Windows Azure Media Encoder para o Media Encoder Standard

Este artigo discute as etapas para migrar do processador de mídia legado Do Windows Azure Media Encoder (WAME) (que está sendo aposentado) para o processador de mídia Media Encoder Standard. Para as datas de aposentadoria, consulte este tópico [de componentes legados.](legacy-components.md)

Ao codificar arquivos com WAME, os clientes normalmente usavam uma seqüência predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p`. Para migrar, seu código precisa ser atualizado para usar o processador de mídia **Media Encoder Standard** `H264 Multiple Bitrate 1080p`em vez de WAME, e uma das [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como . 

## <a name="migrating-to-media-encoder-standard"></a>Migrando para o Media Encoder Standard

Aqui está uma amostra típica de código C# que usa o componente legado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Aqui está a versão atualizada que usa media encoder standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Cenários avançados 

Se você tivesse criado sua própria preset de codificação para WAME usando seu esquema, há um [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Diferenças conhecidas 

O Media Encoder Standard é mais robusto, confiável, tem melhor desempenho e produz uma saída de melhor qualidade do que o codificador WAME legado. Além disso: 

* O Media Encoder Standard produz arquivos de saída com uma convenção de nomeação diferente da WAME.
* O Media Encoder Standard produz artefatos como arquivos contendo [os metadados](media-services-input-metadata-schema.md) do arquivo de entrada e os [metadados do arquivo de saída](media-services-output-metadata-schema.md).
* Como documentado na [página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding) (especialmente na seção FAQ), quando você codifica vídeos usando media encoder Standard, você é cobrado com base na duração dos arquivos produzidos como saída. Com o WAME, você seria cobrado com base nos tamanhos dos arquivos de vídeo de entrada e do arquivo de vídeo de saída.

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um ticket de suporte navegando para [nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Próximas etapas

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
