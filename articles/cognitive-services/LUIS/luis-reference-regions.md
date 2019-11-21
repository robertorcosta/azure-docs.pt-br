---
title: Publishing regions & endpoints - LUIS
titleSuffix: Azure Cognitive Services
description: The region specified in the Azure portal is the same where you will publish the LUIS app and an endpoint URL is generated for this same region.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 4f8e5d1c5ac3b0383fee5a901d2bb34edab8dae2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225488"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Criação e publicação de regiões e as chaves associadas

Three authoring regions are supported by corresponding LUIS portals. Para publicar um aplicativo LUIS em mais de uma região, é necessário ter pelo menos uma chave por região. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS Authoring regions
There are three LUIS authoring portals, based on region. É necessário criar e publicar na mesma região. 

|LUIS|Região de criação|Azure region name|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|Territórios Insulares<br>não Europa<br>não Austrália| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|Austrália| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|Europa|`westeurope`|

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publishing regions and Azure resources
O aplicativo é publicado em todas as regiões associadas aos recursos do LUIS adicionados no portal do LUIS. For example, for an app created on [www.luis.ai][www.luis.ai], if you create a LUIS or Cognitive Service resource in **westus** and [add it to the app as a resource](luis-how-to-azure-subscription.md), the app is published in that region. 

## <a name="public-apps"></a>Aplicativos públicos
Um aplicativo público é publicado em todas as regiões para que um usuário com uma chave de recurso do LUIS baseada em região possa acessar o aplicativo em qualquer região associada com a chave de recurso.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publishing regions are tied to authoring regions

O aplicativo da região de criação só pode ser publicado em uma região de publicação correspondente. Se seu aplicativo estiver, no momento, na região de criação incorreta, exporte-o e importe-o para a região de criação correta para sua região de publicação. 

Os aplicativos LUIS criados no https://www.luis.ai podem ser publicados em todos os pontos de extremidade, exceto nas regiões [europeias](#publishing-to-europe) e [australianas](#publishing-to-australia). 

## <a name="publishing-to-europe"></a>Publicando na Europa

Para publicar nas regiões europeias, crie os aplicativos LUIS em https://eu.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região Europa, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://eu.luis.ai. LUIS apps created at [https://eu.luis.ai][eu.luis.ai] don't automatically migrate to other regions. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="europe-publishing-regions"></a>Europe publishing regions

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| França Central<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa Setentrional<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Oeste da Europa<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Sul do Reino Unido<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>Publicando na Austrália

Para publicar nas regiões australianas, crie os aplicativos LUIS em https://au.luis.ai apenas. Se você tentar publicar em outro lugar usando uma chave na região australiana, o LUIS exibirá uma mensagem de aviso. Em vez disso, use https://au.luis.ai. LUIS apps created at [https://au.luis.ai][au.luis.ai] don't automatically migrate to other regions. Exporte e importe o aplicativo LUIS para migrá-lo.

## <a name="australia-publishing-regions"></a>Australia publishing regions

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| [Austrália](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Austrália Oriental<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>Publishing to other regions

To publish to the other regions, you create LUIS apps at [https://www.luis.ai](https://www.luis.ai) only. 

## <a name="other-publishing-regions"></a>Other publishing regions

 Região global | Região da API de criação e site de criação| Região de publicação e de consulta<br>`API region name`   |  Formato da URL do ponto de extremidade   |
|-----|------|------|------|
| África | `westus`<br>[www.luis.ai][www.luis.ai]| Norte da África do Sul<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Índia Central<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Ásia Oriental<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Leste do Japão<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Oeste do Japão<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Coreia Central<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| Ásia | `westus`<br>[www.luis.ai][www.luis.ai]| Sudeste Asiático<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Canadá Central<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | EUA Central<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Leste dos EUA 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Norte dos EUA<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Centro-Sul dos EUA<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Centro-Oeste dos EUA<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| América do Norte | `westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Norte |`westus`<br>[www.luis.ai][www.luis.ai] | Oeste dos EUA 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| América do Sul | `westus`<br>[www.luis.ai][www.luis.ai] | Sul do Brasil<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>Pontos de Extremidade

LUIS currently has 2 endpoints: one for authoring and one for query prediction analysis.

|Finalidade|URL|
|--|--|
|Criação|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Análise de texto (previsão de consulta)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

A tabela a seguir explica os parâmetros, indicados com chaves `{}`, na tabela anterior.

|.|Finalidade|
|--|--|
|region|Região do Azure – a criação e a publicação têm regiões diferentes|
|appID|A ID do aplicativo LUIS usada na rota de URL e encontrada no painel do aplicativo|
|q|texto de expressão enviado do aplicativo cliente como chat bot|

## <a name="failover-regions"></a>Failover regions

Each region has a secondary region to fail over to. Europe fails over inside Europe and Australia fails over inside Australia.

Authoring regions have [paired fail-over regions](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Referência a entidades predefinidas](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
