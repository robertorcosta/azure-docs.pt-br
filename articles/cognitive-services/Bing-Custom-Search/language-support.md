---
title: Suporte de idioma – API de Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas e regiões compatíveis com a API de Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 2579d24e1a1754e873c6591b6d86ec12dd0dfa60
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367311"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Suporte de idioma e região para a API de Pesquisa Personalizada do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020** , todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

A API de Pesquisa Personalizada do Bing dá suporte a mais de trinta países/regiões, muitos com mais de um idioma.

Embora seja opcional, a solicitação deve especificar o parâmetro de consulta [mkt](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt), que identifica o mercado de onde você deseja que venham os resultados. Para uma lista de parâmetros de consulta opcionais, consulte [Parâmetros de consulta](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

É possível especificar um país/região usando o parâmetro de consulta `cc`. Se você especificar um país/região, também será necessário especificar um ou mais códigos de idiomas usando o cabeçalho HTTP `Accept-Language`. Os idiomas com suporte variam por país/região e são fornecidos para cada país/região na tabela **Mercados**.

O cabeçalho `Accept-Language` e o parâmetro de consulta `setLang` são mutuamente exclusivos — não especifique ambos. Para obter detalhes, consulte [Accept-Language](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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

|País/Região|Linguagem|Código de mercado|
|-------|--------|-----------|
|Argentina|Espanhol|es-AR|
|Austrália|Inglês|en-AU|
|Áustria|Alemão|de-AT|
|Bélgica|Holandês|nl-BE|
|Bélgica|Francês|fr-BE|
|Brasil|Português|pt-BR|
|Canadá|Inglês|en-CA|
|Canadá|Francês|fr-CA|
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
|Reino Unido|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Estados Unidos|Espanhol|es-US|