---
title: Migrar do Azure Media Encoder para o Media Encoder Standard | Microsoft Docs
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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: f8fe1b13db6473e80f0d7cdc638b775a0c8062c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513494"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrar do Azure Media Encoder para o Media Encoder Standard

Este artigo discute os passos para migrar do processador de mídia Azure Media Encoder (AME) legado (que está sendo aposentado) para o processador de mídia Media Encoder Standard. Para as datas de aposentadoria, consulte este tópico [de componentes legados.](legacy-components.md)

Ao codificar arquivos com AME, os clientes normalmente usavam uma seqüência predefinida nomeada, como `H264 Adaptive Bitrate MP4 Set 1080p`. Para migrar, seu código precisa ser atualizado para usar o processador de mídia **Media Encoder Standard** `H264 Multiple Bitrate 1080p`em vez de AME, e uma das [predefinições equivalentes](media-services-mes-presets-overview.md) do sistema como . 

## <a name="migrating-to-media-encoder-standard"></a>Migrando para o Media Encoder Standard

Aqui está uma amostra típica de código C# que usa o processador de mídia legado. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
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

Se você tivesse criado sua própria preset de codificação para AME usando seu esquema, há um [esquema equivalente para Media Encoder Standard](media-services-mes-schema.md). Se você tiver dúvidas sobre como mapear as configurações mais antigas para o novo codificador, entre em contato conosco atravésmailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Diferenças conhecidas 

O Media Encoder Standard é mais robusto, confiável, tem melhor desempenho e produz uma saída de melhor qualidade do que o codificador AME legado. Além disso: 

* O Media Encoder Standard produz arquivos de saída com uma convenção de nomeação diferente da AME.
* O Media Encoder Standard produz artefatos como arquivos contendo [os metadados](media-services-input-metadata-schema.md) do arquivo de entrada e os [metadados do arquivo de saída](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Próximas etapas

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
