---
title: Limites-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém os limites conhecidos do LUIS (Reconhecimento vocal) dos Serviços Cognitivos do Azure. O LUIS tem várias áreas de limite. O limite de modelo controla intenções, entidades e recursos no LUIS. Limites de cota com base no tipo de chave. A combinação de teclado controla o site do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5a65ee27d5175887b7bf0d9146afa025e665657c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488413"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para seu modelo e suas chaves do LUIS
O LUIS tem várias áreas de limite. A primeira é o [limite de modelo](#model-boundaries), que controla intenções, entidades e recursos no LUIS. A segunda área é [limites de cota](#key-limits) com base no tipo de chave. A terceira área de limites é a [combinação de teclado](#keyboard-controls) para controlar o site do LUIS. Uma quarta área é o [mapeamento de região do mundo](luis-reference-regions.md) entre o site de criação do LUIS e as APIs do [ponto de extremidade](luis-glossary.md#endpoint) do LUIS. 


## <a name="model-boundaries"></a>Limites de modelo

Se seu aplicativo exceder os limites e os perímetros do modelo LUIS, considere usar um aplicativo de [expedição LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou um [contêiner LUIS](luis-container-howto.md). 

|Área|Limite|
|--|:--|
| [Nome do aplicativo][luis-get-started-create-app] | *Máximo de caracteres padrão |
| Aplicativos| 500 aplicativos por recurso de criação do Azure |
| [Teste em lote][batch-testing]| 10 conjuntos de dados, 1000 declarações por conjunto de dados|
| Lista explícita | 50 por aplicativo|
| Entidades externas | sem limites |
| [Tentativas][intents]|500 por aplicativo: 499 tentativas personalizadas e a intenção _nenhuma_ necessária.<br>O aplicativo [baseado em expedição](https://aka.ms/dispatch-tool) tem 500 fontes de expedição correspondentes.|
| [Entidades de lista](./luis-concept-entity-types.md) | Pai: 50, filho: 20 mil itens. O nome Canonical é o máximo de caracteres padrão* Valores de sinônimos não têm restrição de comprimento. |
| [Entidades e funções aprendidas por computador](./luis-concept-entity-types.md):<br> Spot<br>único<br>função de entidade|Um limite de entidades pai 100 ou de 330 entidades, o que limitará o usuário primeiro. Uma função conta como uma entidade para a finalidade desse limite. Um exemplo é uma composição com uma entidade simples, que tem duas funções: 1 composição + 1 simples + 2 funções = 4 das entidades 330.|
|Modelo como um recurso| Número máximo de modelos que podem ser usados como um descritor (recurso) para um modelo específico para ter 10 modelos. O número máximo de listas de frases usadas como um descritor (recurso) para um modelo específico ter 10 listas de frases.|
| [Visualização-entidades de lista dinâmica](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listas de ~ 1K por solicitação de ponto de extremidade de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicativo.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 entidades Pattern.any por padrão<br>Máximo de 2 textos opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicativo, 3 entidades pattern.any por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. A expressão de frase não intercambiável tem no máximo 5.000 frases. A expressão intercambiável tem no máximo 50.000 frases. Número máximo de frases totais por aplicativo de 500.000 frases.|
| [Entidades predefinidas](./luis-prebuilt-entities.md) | nenhum limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres, no máximo, por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicativo. 10 funções por entidade|
| [Expressão][utterances] | 500 caracteres|
| [Declarações][utterances] | 15.000 por aplicativo – não há limite para o número de declarações por tentativa|
| [Versões](luis-concept-version.md)| 100 versões por aplicativo |
| [Nome da versão][luis-how-to-manage-versions] | 10 caracteres restritos a alfanuméricos e ponto (.) |

*O máximo de caracteres padrão é 50 caracteres. 

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Exclusividade do nome

Use as regras de exclusividade de nomenclatura a seguir.

O seguinte deve ser exclusivo em um aplicativo LUIS:

* Nome da versão
* tentativa
* entidade
* funções

O seguinte deve ser exclusivo dentro do escopo aplicado:

* lista de frases 

## <a name="object-naming"></a>Nomenclatura de objeto

Não use os seguintes caracteres nos nomes a seguir.

|Objeto|Excluir caracteres|
|--|--|
|Nomes de intenção, entidade e função|`:`<br>`$`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Uso de chave

O Reconhecimento Vocal tem chaves separadas: um tipo para criação e outro para consulta do ponto de extremidade de previsão. Para saber mais sobre as diferenças entre tipos de chaves, confira [Chaves de ponto de extremidade de previsão de criação e consulta no LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limites de chave de recurso

As chaves de recurso têm limites diferentes para criação e ponto de extremidade. A chave do ponto de extremidade de consulta de previsão LUIS só é válida para consultas de ponto de extremidade. 

* 500 aplicativos por recurso de criação do Azure 

|Chave|Criação|Ponto de extremidade|Finalidade|
|--|--|--|--|
|Starter|1 milhão/mês, 5/segundo|1 mil/mês, 5/segundo|Criando seu aplicativo LUIS|
|F0-camada gratuita |1 milhão/mês, 5/segundo|10 mil/mês, 5/segundo|Consultando seu ponto de extremidade LUIS|
|S0 – camada básica|-|50/segundo|Consultando seu ponto de extremidade LUIS|
|S0 – camada padrão|-|50/segundo|Consultando seu ponto de extremidade LUIS|
|[Integração de análise de sentimento](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|Adicionar informações de sentimentos, incluindo extração de dados de frase-chave, é fornecido sem a necessidade de outro recurso do Azure. |
|[Integração de fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1000 solicitações de ponto de extremidade por custo unitário|Converter declaração falada em declaração de texto e retornar resultados do LUIS|

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada de teclado | DESCRIÇÃO | 
|--|--|
|Control+E|alterna entre tokens e entidades na lista de declarações|

## <a name="website-sign-in-time-period"></a>Período de tempo de entrada do site

O acesso de entrada é para **60 minutos**. Após esse período, você obterá esse erro. Você precisa entrar novamente.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
