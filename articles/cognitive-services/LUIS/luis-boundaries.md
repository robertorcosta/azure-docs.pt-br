---
title: Limites - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém os limites conhecidos do LUIS (Reconhecimento vocal) dos Serviços Cognitivos do Azure. O LUIS tem várias áreas de limite. O limite de modelo controla intenções, entidades e recursos no LUIS. Limites de cota com base no tipo de chave. A combinação de teclado controla o site do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/07/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6c021e68f8b76d8b0d3e6e9ff21c242580f53313
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520950"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para seu modelo e suas chaves do LUIS
O LUIS tem várias áreas de limite. A primeira é o [limite de modelo](#model-boundaries), que controla intenções, entidades e recursos no LUIS. A segunda área é [limites de cota](#key-limits) com base no tipo de chave. A terceira área de limites é a [combinação de teclado](#keyboard-controls) para controlar o site do LUIS. Uma quarta área é o [mapeamento de região do mundo](luis-reference-regions.md) entre o site de criação do LUIS e as APIs do [ponto de extremidade](luis-glossary.md#endpoint) do LUIS.


## <a name="model-boundaries"></a>Limites de modelo

Se seu aplicativo exceder os limites e os perímetros do modelo LUIS, considere usar um aplicativo de [expedição LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou um [contêiner LUIS](luis-container-howto.md).

|Área|Limite|
|--|:--|
| [Nome do aplicativo][luis-get-started-create-app] | *Máximo de caracteres padrão |
| Aplicativos| 500 aplicações por recurso de autoria do Azure |
| [Teste de lote][batch-testing]| 10 conjuntos de dados, 1000 declarações por conjunto de dados|
| Lista explícita | 50 por aplicativo|
| Entidades externas | sem limites |
| [Intenções][intents]|500 por aplicação: 499 intenções personalizadas, e a intenção _nenhuma_ necessária.<br>[O](https://aka.ms/dispatch-tool) aplicativo baseado em despacho tem 500 fontes de despacho correspondentes.|
| [Entidades de lista](./luis-concept-entity-types.md) | Pai: 50, filho: 20 mil itens. O nome Canonical é o máximo de caracteres padrão* Valores de sinônimos não têm restrição de comprimento. |
| [Entidades aprendidas por máquina + funções:](./luis-concept-entity-types.md)<br> Composto<br>Simples<br>papel da entidade|Um limite de 100 entidades-mãe ou 330 entidades, qualquer que seja o limite que o usuário acessa primeiro. Um papel conta como uma entidade para o propósito deste limite. Um exemplo é um composto com uma entidade simples, que tem 2 funções é: 1 composto + 1 simples + 2 funções = 4 das 330 entidades.<br>Os subcomponentes podem ser aninhados até 5 níveis.|
|Modelo como recurso| Número máximo de modelos que podem ser usados como descritor (recurso) para um modelo específico para ser 10 modelos. O número máximo de listas de frases usadas como descritor (recurso) para um modelo específico seja de 10 listas de frases.|
| [Pré-visualização - Entidades de lista dinâmica](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listas de ~1k por solicitação de ponto final de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicativo.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 entidades Pattern.any por padrão<br>Máximo de 2 textos opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicativo, 3 entidades pattern.any por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. 10 listas de frases globais devido ao modelo como um limite de recurso. A lista de frases não intercambiáveis tem no máximo 5.000 frases. A lista de frases intercambiáveis tem no máximo 50.000 frases. Número máximo de frases totais por aplicação de 500.000 frases.|
| [Entidades pré-construídas](./luis-prebuilt-entities.md) | nenhum limite|
| [Entidades de expressão regular](./luis-concept-entity-types.md)|20 entidades<br>500 caracteres, no máximo, por padrão de entidade de expressão regular|
| [Funções](luis-concept-roles.md)|300 funções por aplicativo. 10 funções por entidade|
| [Declaração][utterances] | 500 caracteres|
| [Declarações][utterances] | 15.000 por aplicação - não há limite no número de enunciados por intenção|
| [Versões](luis-concept-version.md)| 100 versões por aplicativo |
| [Nome da versão][luis-how-to-manage-versions] | 10 caracteres restritos a alfanuméricos e ponto (.) |

*O máximo de caracteres padrão é 50 caracteres.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Exclusividade de nome

Use as seguintes regras de exclusividade de nomeação.

O seguinte deve ser único dentro de um aplicativo LUIS:

* nome da versão
* intenção
* entidade
* roles

O seguinte deve ser único dentro do escopo aplicado:

* lista de frases

## <a name="object-naming"></a>Nomeação de objeto

Não use os seguintes caracteres nos seguintes nomes.

|Objeto|Excluir caracteres|
|--|--|
|Intenção, entidade e nomes de papéis|`:`<br>`$` <br> `&`|
|Nome da versão|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="key-usage"></a>Uso de chave

O Reconhecimento Vocal tem chaves separadas: um tipo para criação e outro para consulta do ponto de extremidade de previsão. Para saber mais sobre as diferenças entre tipos de chaves, confira [Chaves de ponto de extremidade de previsão de criação e consulta no LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

## <a name="resource-key-limits"></a>Limites da chave de recursos

As chaves de recursos têm limites diferentes para autoria e ponto final. A tecla de ponto final da consulta de previsão DE LUIS só é válida para consultas de ponto final.

* 500 aplicações por recurso de autoria do Azure

|Chave|Criação|Ponto de extremidade|Finalidade|
|--|--|--|--|
|Inicial|1 milhão/mês, 5/segundo|1 mil/mês, 5/segundo|Criando seu aplicativo LUIS|
|F0 - Nível livre |1 milhão/mês, 5/segundo|10 mil/mês, 5/segundo|Consultando seu ponto de extremidade LUIS|
|S0 - Nível básico|-|50/segundo|Consultando seu ponto de extremidade LUIS|
|S0 - Nível padrão|-|50/segundo|Consultando seu ponto de extremidade LUIS|
|[Integração de análise de sentimento](luis-how-to-publish-app.md#enable-sentiment-analysis)|-|-|A adição de informações de sentimento, incluindo a extração de dados de frases-chave, é fornecida sem exigir outro recurso do Azure. |
|[Integração de fala](../speech-service/how-to-recognize-intents-from-speech-csharp.md)|-|1 mil solicitações de ponto final por custo unitário|Converter declaração falada em declaração de texto e retornar resultados do LUIS|

[Saiba mais sobre preços.][pricing]

## <a name="keyboard-controls"></a>Controles de teclado

|Entrada de teclado | Descrição |
|--|--|
|Control+E|alterna entre tokens e entidades na lista de declarações|

## <a name="website-sign-in-time-period"></a>Período de tempo de login no site

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
