---
title: Como usar marcadores de decoração para realçar texto - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Saiba como usar as decorações de texto e realçar ocorrências nos resultados da pesquisa usando a API de Pesquisa na Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854053"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Usar marcadores de decoração para realçar texto

Bing suporta hit highlighting, que marca termos de consulta (ou outros termos que Bing acha relevantes) nas seqüências de exibição de algumas respostas. Por exemplo, um resultado `name`de `displayUrl`página `snippet` web e campos podem conter termos de consulta marcados. 

Por padrão, o Bing não inclui realce de marcadores em cadeias de exibição. Para habilitar os marcadores, inclua o `textDecorations` parâmetro de `true`consulta na sua solicitação e defina-o como .

## <a name="hit-highlighting-example"></a>Exemplo de destaque de hit

O exemplo a seguir `Sailing Dinghy`mostra um resultado da Web para . Bing marcou o início e o fim do termo de consulta usando os caracteres Unicode E000 e E001.
  
![Realce de ocorrência](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Antes de exibir o resultado na interface do usuário, substitua os caracteres Unicode por aqueles apropriados para o formato de exibição.

## <a name="marker-formatting"></a>Formatação de marcadores

Bing oferece a opção de usar caracteres Unicode ou tags HTML como marcadores. Para especificar quais marcadores usar, inclua o parâmetro de consulta [textFormat:](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| Valor             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caracteres Unicode (padrão) |
| `textFormat=HTML` | Caracteres HTML              |

## <a name="additional-text-decorations"></a>Decorações de texto adicionais

Bing pode devolver várias decorações de texto diferentes. Por exemplo, `Computation` uma resposta pode conter marcadores `log(2)` de `expression` subscritos para o termo de consulta no campo.

![marcadores de computação](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se a solicitação não especificasse decorações, o `expression` campo conteria `log10(2)`. 

Se `textDecorations` `true`for, Bing pode incluir os seguintes marcadores nas seqüências de respostas do display. Se não houver uma tag HTML equivalente, a célula de tabela está vazia.

|Unicode|HTML|Descrição
|-|-|-
|U+E000|\<b>|Marca o início do termo de consulta (realce de ocorrências)
|U+E001|\</b>|Marca o final do termo de consulta
|U+E002|\<i>|Marca o início do conteúdo em itálico 
|U+E003|\</i>|Marca o final do conteúdo em itálico
|U+E004|\<br/>|Marca uma quebra de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o final de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o final de um endereço
|U+E009|\&nbsp;|Marca um espaço rígido
|U+E00C|\<strong>|Marca o início do conteúdo em negrito
|U+E00D|\</strong>|Marca o fim do conteúdo em negrito
|U+E00E||Marca o início do conteúdo cujo plano de fundo deve ser mais claro do que seu plano de fundo ao redor
|U+E00F||Marca o final do conteúdo cujo plano de fundo deve ser mais claro do que seu plano de fundo ao redor
|U+E010||Marca o início do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E011||Marca o final do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E012|\<del>|Marca o início do conteúdo que deve ser ignorado
|U+E013|\</del>|Marca o final do conteúdo que deve ser ignorado
|U+E016|\<sub>|Marca o início do conteúdo subscrito
|U+E017|\</sub>|Marca o final do conteúdo subscrito
|U+E018|\<sup>|Marca o início do conteúdo sobrescrito
|U+E019|\</sup>|Marca o final do conteúdo sobrescrito

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Pesquisa na Web do Bing?](overview.md) 
* [Miniaturas de redimensionamento e corte](resize-and-crop-thumbnails.md)