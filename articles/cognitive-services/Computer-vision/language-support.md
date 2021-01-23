---
title: Suporte ao idioma-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de idiomas naturais com suporte pelos recursos do Pesquisa Visual Computacional; OCR, análise de imagem.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1abb857c9f03be502db02099383c6fe0b5110461
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736811"
---
# <a name="language-support-for-computer-vision"></a>Suporte a idiomas para Pesquisa Visual Computacional

Alguns recursos do Pesquisa Visual Computacional dão suporte a vários idiomas; todos os recursos não mencionados aqui só dão suporte ao inglês.

## <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

As APIs de OCR do Pesquisa Visual Computacional dão suporte a vários idiomas. Eles não exigem que você especifique um código de idioma. Consulte [OCR (reconhecimento óptico de caracteres)](concept-recognizing-text.md) para obter mais informações.

|Linguagem| Código de idioma | API DE OCR | Leia 3,0 e 3,1 | Leia v 3.2-Preview. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|Árabe | `ar`|✔ | | |
|Chinês (Simplificado) | `zh-Hans`|✔ | |✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ | | |
|Tcheco | `cs` |✔ | | |
|Dinamarquês | `da` |✔ | | |
|Holandês | `nl` |✔ |✔ |✔ |
|Inglês | `en` |✔ |✔ |✔ |
|Finlandês | `fi` |✔ | | |
|Francês | `fr` |✔ |✔ |✔ |
|Alemão | `de` |✔ |✔ |✔ |
|Grego | `el` |✔ | | |
|Húngaro | `hu` |✔ | | |
|Italiano | `it` |✔ |✔ |✔ |
|Japonês | `ja` |✔ | |✔ |
|Coreano | `ko` |✔ | | |
|Norueguês | `nb` |✔ | | |
|Polonês | `pl` |✔ | | |
|Português | `pt` |✔ |✔ |✔ |
|Romeno | `ro` |✔ | | |
|Russo | `ru` |✔ | | |
|Sérvio (cirílico) | `sr-Cyrl` |✔ | | |
|Sérvio (latino) | `sr-Latn` |✔ | | |
|Eslovaco | `sk` |✔ | | |
|Espanhol | `es` |✔ |✔ |✔ |
|Sueco | `sw` |✔ | | |
|Turco | `tr` |✔ | | |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da API de [análise de imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) podem retornar resultados em outras linguagens, especificadas com o parâmetro de `language` consulta. Outras ações retornam resultados em inglês, independentemente do idioma especificado, e outras geram uma exceção para idiomas sem suporte. As ações são especificadas com `visualFeatures` os `details` parâmetros de consulta e. consulte a [visão geral](overview.md) para obter uma lista de todas as ações que você pode fazer com a análise de imagem.

|Linguagem | Código de idioma | Categorias | Marcações | Descrição | Adulto | Marcas | Color | Faces | ImageType | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Próximas etapas

Comece a usar os recursos de Pesquisa Visual Computacional mencionados neste guia.

* [Analisar uma imagem local (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-analyze.md)
* [Extrair texto impresso (REST)](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-print-text.md)