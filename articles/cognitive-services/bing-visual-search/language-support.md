---
title: Idiomas compatíveis – API da Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Uma lista de idiomas naturais, países e regiões compatíveis com a API da Pesquisa Visual do Bing. A API da Pesquisa Visual do Bing é compatível com mais de trinta países/regiões, muitos deles com mais de um idioma.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68883540"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>Idiomas e regiões compatíveis com a API da Pesquisa Visual do Bing

A API de Pesquisa Visual do Bing dá suporte a mais de trinta países/regiões, muitos com mais de um idioma. Cada solicitação deve incluir o país/região e idioma de preferência do usuário. O conhecimento do mercado do usuário ajuda o Bing a retornar resultados apropriados. Se você não especificar um país/região e um idioma, o Bing se esforçará para determinar o país/região e o idioma do usuário. Como os resultados podem conter links para o Bing, o conhecimento do país/região e do idioma poderá fornecer uma experiência do usuário preferencial localizada do Bing, caso o usuário clique nos links do Bing.

Para especificar o país/região e o idioma, defina o parâmetro de consulta `mkt` (mercado) com um código da tabela **Mercados** abaixo. O mercado especifica um país/região e um idioma. Se o usuário preferir ver e exibir o texto em outro idioma, defina o parâmetro de consulta `setLang` com o código de idioma apropriado.

Como alternativa, é possível especificar o país/região usando o parâmetro de consulta `cc`. Se você especificar um país/região, também será necessário especificar um ou mais códigos de idiomas usando o cabeçalho HTTP `Accept-Language`. Os idiomas com suporte variam por país/região e são fornecidos para cada país na tabela Mercados.



> [!NOTE]
> As seguintes restrições de mercado se aplicam:
>
> - As anotações de reconhecimento de imagem só estão disponíveis em inglês.
> - Insights de receitas, compras e páginas que incluem o termo de pesquisa só estão disponíveis no mercado en-US.


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
