---
title: Suporte ao idioma-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo fornece uma lista de idiomas naturais com suporte pelos recursos do Pesquisa Visual Computacional; OCR, Reconhecimento de Texto e ler.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220133"
---
# <a name="language-support-for-computer-vision"></a>Suporte a idiomas para Pesquisa Visual Computacional

Alguns recursos do Pesquisa Visual Computacional dão suporte a vários idiomas; todos os recursos não mencionados aqui só dão suporte ao inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

Pesquisa Visual Computacional pode reconhecer texto em vários idiomas. Especificamente, a API de [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) dá suporte a uma variedade de linguagens, enquanto a API de [leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e a API de [reconhecimento de texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) dão suporte apenas ao inglês. Consulte [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre essa funcionalidade e as vantagens de cada API.

O OCR detecta automaticamente o idioma do material de entrada, portanto, não é necessário especificar um código de idioma na chamada à API. No entanto, os códigos de idioma sempre são retornados como `"language"` o valor do nó na resposta JSON.

|Linguagem| Código de idioma | API DE OCR |
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

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da API de [análise de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podem retornar resultados em outras linguagens, especificadas com `language` o parâmetro de consulta. Outras ações retornam resultados em inglês, independentemente do idioma especificado, e outras geram uma exceção para idiomas sem suporte. As ações são especificadas com `visualFeatures` os `details` parâmetros de consulta e; consulte a [visão geral](home.md) para obter uma lista de todas as ações que você pode fazer com a análise de imagem.

|Linguagem | Código de idioma | Categorias | Marcas | Descrição | Adulto | Marcas | Cor | Faces | ImageType | Objetos | Celebridades | Pontos de referência |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Chinês | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglês | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonês | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Português | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Espanhol | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Próximas etapas

Comece a usar os recursos de Pesquisa Visual Computacional mencionados neste guia.

* [Analisar uma imagem local (REST)](./quickstarts/csharp-analyze.md)
* [Extrair texto impresso (REST)](./quickstarts/csharp-print-text.md)