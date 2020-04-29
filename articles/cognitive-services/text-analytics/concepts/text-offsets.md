---
title: Deslocamentos de texto na API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre os deslocamentos causados por codificações multilíngues e emojis.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219231"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Deslocamentos de texto na saída de API de Análise de Texto

O suporte multilíngue e Emoji levou a codificações Unicode que usam mais de um [ponto de código](https://wikipedia.org/wiki/Code_point) para representar um único caractere exibido, chamado de grafemas. Por exemplo, emojis como 🌷 e 👍 podem usar vários caracteres para compor a forma com caracteres adicionais para atributos visuais, como o tom de capa. Da mesma forma, a `अनुच्छेद` palavra híndi é codificada como cinco letras e três marcas de combinação.

Devido aos diferentes comprimentos de codificações multilíngues e de emojis, o API de Análise de Texto pode retornar deslocamentos na resposta.

## <a name="offsets-in-the-api-response"></a>Deslocamentos na resposta da API. 

Sempre que os deslocamentos forem retornados à resposta da API, como [reconhecimento de entidade nomeada](../how-tos/text-analytics-how-to-entity-linking.md) ou [análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md), lembre-se do seguinte:

* Os elementos na resposta podem ser específicos para o ponto de extremidade que foi chamado. 
* As cargas HTTP POST/GET são codificadas em [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que podem ou não ser a codificação de caracteres padrão no seu compilador do lado do cliente ou sistema operacional.
* Os deslocamentos se referem a contagens de grafemas com base no padrão [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) , não em contagens de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extraindo subcadeias de texto com deslocamentos

Os deslocamentos podem causar problemas ao usar métodos de subcadeias de caracteres baseados em caractere, por exemplo, o método de [subcadeias](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) de caracteres .net (). Um problema é que um deslocamento pode fazer com que um método de subcadeia de caracteres termine no meio de uma codificação grafemas de vários caracteres, em vez do final.

No .NET, considere usar a classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) , que permite que você trabalhe com uma cadeia de caracteres como uma série de elementos textuais, em vez de objetos de caractere individuais. Você também pode procurar por bibliotecas de divisores grafemas em seu ambiente de software preferido. 

O API de Análise de Texto também retorna esses elementos textuais, por conveniência.

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar o idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
