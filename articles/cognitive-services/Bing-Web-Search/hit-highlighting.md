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
ms.openlocfilehash: ea9cd9cf064b9c07016bc5e166cc4ec4c041b146
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078536"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Usar marcadores de decoração para realçar texto

> [!WARNING]
> APIs de Pesquisa do Bing estão mudando de serviços cognitivas para serviços Pesquisa do Bings. A partir de **30 de outubro de 2020** , todas as novas instâncias do pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> APIs de Pesquisa do Bing provisionado usando serviços cognitivas terão suporte nos próximos três anos ou até o final do seu Enterprise Agreement, o que ocorrer primeiro.
> Para obter instruções de migração, consulte [serviços de pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

O Bing dá suporte ao realce de visita, que marca os termos de consulta (ou outros termos que o Bing encontra relevante) nas cadeias de caracteres de exibição de algumas respostas. Por exemplo, os campos, e dos resultados de uma página da Web `name` `displayUrl` `snippet` podem conter os termos de consulta marcados. 

Por padrão, o Bing não inclui realce de marcadores em cadeias de exibição. Para habilitar os marcadores, inclua o `textDecorations` parâmetro de consulta em sua solicitação e defina-o como `true` .

## <a name="hit-highlighting-example"></a>Exemplo de realce de clique

O exemplo a seguir mostra um resultado da Web para `Sailing Dinghy` . Bing marcou o início e o final do termo de consulta usando os caracteres Unicode E000 e E001.
  
![Realce de ocorrência](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Antes de exibir o resultado na interface do usuário, substitua os caracteres Unicode pelos que são apropriados para seu formato de exibição.

## <a name="marker-formatting"></a>Formatação do marcador

O Bing fornece a opção de usar caracteres Unicode ou marcas HTML como marcadores. Para especificar quais marcadores usar, inclua o parâmetro de consulta [TextFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) : 

| Valor             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caracteres Unicode (padrão) |
| `textFormat=HTML` | Caracteres HTML              |

## <a name="additional-text-decorations"></a>Decorações de texto adicionais

O Bing pode retornar várias decorações de texto diferentes. Por exemplo, uma `Computation` resposta pode conter marcadores de subscrito para o termo de consulta `log(2)` no `expression` campo.

![marcadores de computação](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se a solicitação não especificou decorações, o `expression` campo conterá `log10(2)` . 

Se `textDecorations` for `true` , o Bing pode incluir os marcadores a seguir nas cadeias de caracteres de exibição de respostas. Se não houver nenhuma marca HTML equivalente, a célula da tabela estará vazia.

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