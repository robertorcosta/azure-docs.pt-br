---
title: Deslocamentos de texto na API de análise de texto
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre compensações causadas por codificações multilínturas e emojis.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219231"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Deslocamentos de texto na saída da API do Text Analytics

O suporte multilíngue e emoji levou a codificações Unicode que usam mais de um [ponto de código](https://wikipedia.org/wiki/Code_point) para representar um único caractere exibido, chamado grafeme. Por exemplo, emojis 👍 como 🌷 e podem usar vários caracteres para compor a forma com caracteres adicionais para atributos visuais, como o tom de pele. Da mesma forma, `अनुच्छेद` a palavra hindi é codificada como cinco letras e três marcas combinadas.

Devido aos diferentes comprimentos de possíveis codificações multilíndüárias e emojis, a API do Text Analytics pode retornar compensações na resposta.

## <a name="offsets-in-the-api-response"></a>Compensações na resposta da API. 

Sempre que as compensações forem retornadas, a resposta da API, como [reconhecimento de entidade nomeada](../how-tos/text-analytics-how-to-entity-linking.md) ou análise de [sentimento,](../how-tos/text-analytics-how-to-sentiment-analysis.md)lembre-se do seguinte:

* Os elementos na resposta podem ser específicos para o ponto final que foi chamado. 
* As cargas HTTP POST/GET estão codificadas no [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), que pode ou não ser a codificação padrão de caracteres no seu compilador ou sistema operacional do lado do cliente.
* As compensações referem-se à contagem de graféns com base no padrão [Unicode 8.0.0,](https://unicode.org/versions/Unicode8.0.0) não na contagem de caracteres.

## <a name="extracting-substrings-from-text-with-offsets"></a>Extraindo substrings de texto com deslocamentos

Deslocamentos podem causar problemas ao usar métodos de substring baseados em caracteres, por exemplo, o método .NET [substring().](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) Um problema é que um deslocamento pode fazer com que um método de substrings termine no meio de uma codificação de grafeme de vários caracteres em vez do final.

Em .NET, considere usar a classe [StringInfo,](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) que permite trabalhar com uma string como uma série de elementos textuais, em vez de objetos de caracteres individuais. Você também pode procurar bibliotecas de divisores de grafeme em seu ambiente de software preferido. 

A API text analytics também retorna esses elementos texulos, por conveniência.

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar o idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
