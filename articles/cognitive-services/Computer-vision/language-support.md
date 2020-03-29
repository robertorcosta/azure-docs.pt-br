---
title: Suporte ao idioma - Visão computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de linguagens naturais suportadas pelos recursos da Visão computacional; OCR, Reconhecer Texto e Ler.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220133"
---
# <a name="language-support-for-computer-vision"></a>Suporte ao idioma para visão computacional

Alguns recursos do Computer Vision suportam vários idiomas; quaisquer recursos não mencionados aqui apenas suportam inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

A Visão computacional pode reconhecer texto em muitos idiomas. Especificamente, a API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) suporta uma variedade de idiomas, enquanto a API [Ler](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e reconhecer a API [de texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) só suporta inglês. Consulte [Reconhecer texto impresso e manuscrita](concept-recognizing-text.md) para obter mais informações sobre essa funcionalidade e as vantagens de cada API.

O OCR detecta automaticamente a linguagem do material de entrada, portanto não há necessidade de especificar um código de idioma na chamada aPI. No entanto, os códigos de idioma `"language"` são sempre devolvidos como o valor do nó na resposta JSON.

|Idioma| Código de idioma | OCR API |
|:-----|:----:|:-----:|
|Árabe | `ar`|✔ |
|Chinês (Simplificado) | `zh-Hans`|✔ |
|Chinês (Tradicional) | `zh-Hant`|✔ |
|Tcheco | `cs` |✔ |
|Dinamarquês | `da` |✔ |
|Holandês | `nl` |✔ |
|Inglês | `en` |✔ |
|Finlandês | `fi` |✔ |
|Francês | `fr` |✔ |
|Alemão | `de` |✔ |
|Grego | `el` |✔ |
|Húngaro | `hu` |✔ |
|Italiano | `it` |✔ |
|Japonês | `ja` |✔ |
|Coreano | `ko` |✔ |
|Norueguês | `nb` |✔ |
|Polonês | `pl` |✔ |
|Português | `pt` |✔ |
|Romeno | `ro` |✔ |
|Russo | `ru` |✔ |
|Sérvio (cirílico) | `sr-Cyrl` |✔ |
|Sérvio (latino) | `sr-Latn` |✔ |
|Eslovaco | `sk` |✔ |
|Espanhol | `es` |✔ |
|Sueco | `sw` |✔ |
|Turco | `tr` |✔ |

## <a name="image-analysis"></a>Análise de imagens

Algumas ações da [API Analisar - Imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podem retornar resultados `language` em outros idiomas, especificados com o parâmetro de consulta. Outras ações retornam resultados em inglês, independentemente do idioma especificado, e outras lançam uma exceção para idiomas não suportados. As ações são `visualFeatures` especificadas com os parâmetros e `details` consulta; consulte a [visão geral](home.md) de uma lista de todas as ações que você pode fazer com a análise de imagem.

|Idioma | Código de idioma | Categorias | Marcas | Descrição | Adulto | Marcas | Color | Faces | ImageType | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Próximas etapas

Comece a usar os recursos de Visão computacional mencionados neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)