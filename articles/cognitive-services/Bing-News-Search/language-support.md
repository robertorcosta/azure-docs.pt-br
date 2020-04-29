---
title: Idiomas compatíveis – API de Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais, países e regiões compatíveis com a API de Pesquisa de Notícias do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220188"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>Idiomas e regiões compatíveis com a API de Pesquisa de Notícias do Bing

A API de Pesquisa de Notícias do Bing dá suporte a vários países/regiões, muitos deles com mais de um idioma. Especificar um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base nos interesses nesse país/região. Além disso, os resultados podem conter links para o Bing e esses links podem localizar a experiência do usuário do Bing, de acordo com o país/região ou idioma especificado.

É possível especificar um país/região usando o parâmetro de consulta `cc`. Se você especificar um país/região, também será necessário especificar um ou mais códigos de idiomas usando o cabeçalho HTTP `Accept-Language`. Os idiomas com suporte variam por país/região e são fornecidos para cada país/região na tabela Mercados.

Como alternativa, você pode especificar o mercado usando o parâmetro de consulta `mkt` e um código da tabela **Mercados**. Especificar um mercado, especifica simultaneamente um país/região e um idioma preferido. O parâmetro de consulta `setLang` pode ser definido para um código de idioma nesse caso; geralmente, é o mesmo idioma especificado pelo `mkt`, a menos que o usuário prefira consultar o Bing em outro idioma.

## <a name="supported-markets-for-news-search-endpoint"></a>Mercados com suporte para ponto de extremidade de pesquisa de notícias

Para o ponto de extremidade `/news/search`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que podem ser especificados no parâmetro de consulta `cc`, consulte [Códigos do País](#countrycodes).  

|País/região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Áustria|Alemão|de-AT|
|Suíça|Alemão|de-CH|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|Canada|Inglês|en-CA|
|United Kingdom|Inglês|en-GB|
|Indonésia|Inglês|en-ID|
|Irlanda|Inglês|en-IE|
|Índia|Inglês|en-IN|
|Malásia|Inglês|en-MY|
|Nova Zelândia|Inglês|en-NZ|
|República das Filipinas|Inglês|en-PH|
|Singapura|Inglês|en-SG|
|Estados Unidos|Inglês|pt-BR|
|Inglês|geral|en-WW|
|Inglês|geral|en-XA|
|África do Sul|Inglês|en-ZA|
|Argentina|Espanhol|es-AR|
|Chile|Espanhol|es-CL|
|Espanha|Espanhol|es-ES|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US|
|Espanhol|geral|es-XL|
|Finlândia|Finlandês|fi-FI|  
|França|Francês|fr-BE|
|Canada|Francês|fr-CA|
|Bélgica|Holandês|nl-BE|
|Suíça|Francês|fr-CH|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT|
|RAE de Hong Kong|Chinês tradicional|zh-HK|  
|Taiwan|Chinês tradicional|zh-TW|
|Japão|Japonês|ja-JP|  
|Coreia do Sul|Coreano|ko-KR|  
|Países Baixos|Holandês|nl-NL|  
|República Popular da China|Chinês|zh-CN|  
|Brasil|Português|pt-BR|
|Rússia|Russo|ru-RU|  
|Suécia|Sueco|sv-SE|  
|Turquia|Turco|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Mercados com suporte para ponto de extremidade de notícias
Para o ponto de extremidade `/news`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que podem ser especificados no parâmetro de consulta `cc`, consulte [Códigos do País](#countrycodes).  

|País/região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------|
|Dinamarca|Dinamarquês|da-DK|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|United Kingdom|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Inglês|geral|en-WW|
|Chile|Espanhol|es-CL|
|México|Espanhol|es-MX|
|Estados Unidos|Espanhol|es-US|
|Finlândia|Finlandês|fi-FI|  
|Canada|Francês|fr-CA|
|França|Francês|fr-FR|  
|Itália|Italiano|it-IT|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Mercados com suporte para ponto de extremidade de tendências de notícias
Para o ponto de extremidade `/news/trendingtopics`, a tabela a seguir lista os valores de código de mercado que você pode usar para especificar o parâmetro de consulta `mkt`. O Bing retorna o conteúdo somente para esses mercados. A lista está sujeita a alterações.  

Para obter uma lista de códigos de país/região que podem ser especificados no parâmetro de consulta `cc`, consulte [Códigos do País](#countrycodes).  

|País/região|Linguagem|Código de mercado|  
|---------------------|--------------|-----------------|
|Alemanha|Alemão|de-DE|
|Austrália|Inglês|en-AU|
|United Kingdom|Inglês|en-GB|
|Estados Unidos|Inglês|pt-BR|
|Canada|Inglês|en-CA|
|Índia|Inglês|en-IN|
|França|Francês|fr-FR|
|Canada|Francês|fr-CA|
|Brasil|Português|pt-BR|
|República Popular da China|Chinês|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Códigos de país/região  

A seguir, são apresentados os códigos de país/região que podem ser especificados no parâmetro de consulta `cc`. A lista está sujeita a alterações.  

|País/região|Código do país|  
|---------------------|------------------|  
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
|Itália|TI|  
|Japão|JP|  
|Coreia do Sul|KR|  
|Malásia|MY|  
|México|MX|  
|Países Baixos|NL|  
|Nova Zelândia|NZ|  
|Noruega|Não|  
|República Popular da China|CN|  
|Polônia|PL|  
|Portugal|PT|  
|República das Filipinas|PH|  
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

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os pontos de extremidade da Pesquisa de Notícias do Bing, confira [Referência da API de Pesquisa de Notícias v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).
