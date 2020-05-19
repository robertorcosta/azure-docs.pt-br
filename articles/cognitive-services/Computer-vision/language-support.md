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
ms.openlocfilehash: a207118af0b07be79c934a9665f47e73c5eecde0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589186"
---
# <a name="language-support-for-computer-vision"></a>Suporte a idiomas para Pesquisa Visual Computacional

Alguns recursos do Pesquisa Visual Computacional dão suporte a vários idiomas; todos os recursos não mencionados aqui só dão suporte ao inglês.

## <a name="text-recognition"></a>Reconhecimento de texto

Pesquisa Visual Computacional pode reconhecer texto em vários idiomas. As APIs de leitura e OCR não exigem que você especifique um código de idioma. Consulte [reconhecer texto impresso e manuscrito](concept-recognizing-text.md) para obter mais informações sobre essa funcionalidade e as vantagens de cada API.

|Idioma| Código de idioma | API DE OCR | API de leitura |
|:-----|:----:|:-----:|:---:|
|Árabe | `ar`|✔ | |
|Chinês (Simplificado) | `zh-Hans`|✔ | |
|Chinês (Tradicional) | `zh-Hant`|✔ | |
|Tcheco | `cs` |✔ | |
|Dinamarquês | `da` |✔ | |
|Holandês | `nl` |✔ |✔ |
|Inglês | `en` |✔ |✔ |
|Finlandês | `fi` |✔ | |
|Francês | `fr` |✔ |✔ |
|Alemão | `de` |✔ |✔ |
|Grego | `el` |✔ | |
|Húngaro | `hu` |✔ | |
|Italiano | `it` |✔ |✔ |
|Japonês | `ja` |✔ | |
|Coreano | `ko` |✔ | |
|Norueguês | `nb` |✔ | |
|Polonês | `pl` |✔ | |
|Português | `pt` |✔ |✔ |
|Romeno | `ro` |✔ | |
|Russo | `ru` |✔ | |
|Sérvio (cirílico) | `sr-Cyrl` |✔ | |
|Sérvio (latino) | `sr-Latn` |✔ | |
|Eslovaco | `sk` |✔ | |
|Espanhol | `es` |✔ |✔ |
|Sueco | `sw` |✔ | |
|Turco | `tr` |✔ | |

## <a name="image-analysis"></a>Análise de imagem

Algumas ações da API de [análise de imagem](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) podem retornar resultados em outras linguagens, especificadas com o parâmetro de `language` consulta. Outras ações retornam resultados em inglês, independentemente do idioma especificado, e outras geram uma exceção para idiomas sem suporte. As ações são especificadas com `visualFeatures` os `details` parâmetros de consulta e. consulte a [visão geral](home.md) para obter uma lista de todas as ações que você pode fazer com a análise de imagem.

|Idioma | Código de idioma | Categorias | Marcações | Descrição | Adulto | Marcas | Cor | Faces | ImageType | Objetos | Celebridades | Pontos de referência |
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
