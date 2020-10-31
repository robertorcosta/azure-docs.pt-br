---
title: Suporte de idioma – API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: Descubra quais países/regiões e idiomas têm suporte para API de Pesquisa de Imagem do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: b7037804f1a0984cee2d1756adf80bfe2eec3918
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095520"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>Suporte de idioma e região para a API de Pesquisa de Imagem do Bing

> [!WARNING]
> APIs de Pesquisa do Bing estão mudando de serviços cognitivas para serviços Pesquisa do Bings. A partir de **30 de outubro de 2020** , todas as novas instâncias do pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> APIs de Pesquisa do Bing provisionado usando serviços cognitivas terão suporte nos próximos três anos ou até o final do seu Enterprise Agreement, o que ocorrer primeiro.
> Para obter instruções de migração, consulte [serviços de pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

A API de Pesquisa de Imagem do Bing dá suporte a mais de trinta países/regiões, muitos com mais de um idioma. Especificar um país/região com uma consulta serve principalmente para refinar os resultados da pesquisa com base nos interesses nesse país/região. Além disso, os resultados podem conter links para o Bing e esses links podem localizar a experiência do usuário do Bing, de acordo com o país/região ou idioma especificado.

Para especificar o país/região e o idioma, defina o parâmetro de consulta `mkt` (mercado) com um código da tabela **Mercados** abaixo. O mercado especifica um país/região e um idioma. Se o usuário preferir ver e exibir o texto em outro idioma, defina o parâmetro de consulta `setLang` com o código de idioma apropriado.

Como alternativa, é possível especificar o país/região usando o parâmetro de consulta `cc`. Se você especificar um país/região, também será necessário especificar um ou mais códigos de idiomas usando o cabeçalho HTTP `Accept-Language`. Os idiomas com suporte variam por país/região e são fornecidos para cada país/região na tabela Mercados.

> [!NOTE]
> Atualmente, a API de Imagens Populares dá suporte apenas aos seguintes mercados:
> - en-US (inglês, Estados Unidos)
> - en-CA (inglês, Canadá)
> - en-AU (inglês, Austrália)
> - zh-CN (chinês, China)

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

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os pontos de extremidade da Pesquisa de Notícias do Bing, confira [Referência da API de Pesquisa de Imagem v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
