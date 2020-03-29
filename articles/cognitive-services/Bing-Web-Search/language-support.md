---
title: Suporte de idioma – API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais, países e regiões compatíveis com a API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882683"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Suporte de idioma e região para a API de Pesquisa na Web do Bing

A API de Pesquisa na Web do Bing dá suporte a mais de trinta países ou regiões, muitos com mais de um idioma. Especificar um país ou região com uma consulta ajuda a refinar os resultados da pesquisa com base nos interesses nesse país ou região. Os resultados podem incluir links para o Bing e esses links podem localizar a experiência do usuário do Bing, de acordo com o país/região ou idioma especificado.

É possível especificar um país ou região usando o parâmetro de consulta `cc`. Quando um país ou região é especificado, você deve especificar um ou mais códigos de idioma com o [ `Accept-Language` cabeçalho](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Use a [tabela de Mercados](#markets) para obter uma lista dos idiomas com suporte com cada mercado.

Como alternativa, você pode especificar o mercado usando o parâmetro de consulta `mkt` e um código da tabela **Mercados**. Especificar um mercado, especifica simultaneamente um país ou região e um idioma preferido. Você pode definir explicitamente o idioma com o parâmetro de consulta `setLang`.

## <a name="countriesregions"></a>Países/regiões

|País/Região|Código|
|-------|----|
|Argentina|AR|
|Austrália|AU|
|Áustria|AT|
|Bélgica|BE|
|Brasil|BR|
|Canada|CA|
|Chile|CL|
|Dinamarca|DK|
|Finlândia|FI|
|França|FR|
|Alemanha|DE|
|RAE de Hong Kong|HK|
|Índia|IN|
|Indonésia|ID|
|Itália|IT|
|Japão|JP|
|Coreia do Sul|KR|
|Malásia|MY|
|México|MX|
|Países Baixos|NL|
|Nova Zelândia|NZ|
|Noruega|Não|
|China|CN|
|Polônia|PL|
|Portugal|PT|
|Filipinas|PH|
|Rússia|RU|
|Arábia Saudita|SA|
|África do Sul|ZA|
|Espanha|ES|
|Suécia|SE|
|Suíça|CH|
|Taiwan|TW|
|Turquia|TR|
|United Kingdom|GB|
|Estados Unidos|EUA|

## <a name="markets"></a>Mercados

|País/Região|Idioma|Código de mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-AU|
|Áustria|Alemão|de-AT|
|Bélgica|Holandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canada|Inglês|en-CA|
|Canada|Francês|fr-CA|
|Chile|Espanhol|es-CL|
|Dinamarca|Dinamarquês|da-DK|
|Finlândia|Finlandês|fi-FI|
|França|Francês|fr-FR|
|Alemanha|Alemão|de-DE|
|RAE de Hong Kong|Chinês tradicional|zh-HK|
|Índia|Inglês|en-IN|
|Indonésia|Inglês|en-ID|
|Itália|Italiano|it-IT|
|Japão|Japonês|ja-JP|
|Coreia do Sul|Coreano|ko-KR|
|Malásia|Inglês|en-MY|
|México|Espanhol|es-MX|
|Países Baixos|Holandês|nl-NL|
|Nova Zelândia|Inglês|en-NZ|
|Noruega|Norueguês|no-NO|
|China|Chinês|zh-CN|
|Polônia|Polonês|pl-PL|
|Portugal|Português|pt-PT|
|Filipinas|Inglês|en-PH|
|Rússia|Russo|ru-RU|
|Arábia Saudita|Árabe|ar-SA|
|África do Sul|Inglês|en-ZA|
|Espanha|Espanhol|es-ES|
|Suécia|Sueco|sv-SE|
|Suíça|Francês|fr-CH|
|Suíça|Alemão|de-CH|
|Taiwan|Chinês tradicional|zh-TW|
|Turquia|Turco|tr-TR|
|United Kingdom|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Estados Unidos|Espanhol|es-US|

## <a name="next-steps"></a>Próximas etapas

* [Referência de API da Pesquisa de Imagem do Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
