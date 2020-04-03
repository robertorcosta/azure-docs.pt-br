---
title: Limites - LUIS
description: Este artigo contém os limites conhecidos do LUIS (Reconhecimento vocal) dos Serviços Cognitivos do Azure. O LUIS tem várias áreas de limite. O limite de modelo controla intenções, entidades e recursos no LUIS. Limites de cota com base no tipo de chave. A combinação de teclado controla o site do LUIS.
ms.topic: reference
ms.date: 04/02/2020
ms.openlocfilehash: 4aa69cb0fd36fe5bf4ea2928022aea602b8830d6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618860"
---
# <a name="boundaries-for-your-luis-model-and-keys"></a>Limites para seu modelo e suas chaves do LUIS
O LUIS tem várias áreas de limite. A primeira é o [limite de modelo](#model-boundaries), que controla intenções, entidades e recursos no LUIS. A segunda área é [limites de cota](#key-limits) com base no tipo de chave. A terceira área de limites é a [combinação de teclado](#keyboard-controls) para controlar o site do LUIS. Uma quarta área é o [mapeamento de região do mundo](luis-reference-regions.md) entre o site de criação do LUIS e as APIs do [ponto de extremidade](luis-glossary.md#endpoint) do LUIS.


## <a name="model-boundaries"></a>Limites de modelo

Se seu aplicativo exceder os limites e os perímetros do modelo LUIS, considere usar um aplicativo de [expedição LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) ou um [contêiner LUIS](luis-container-howto.md).

|Área|Limite|
|--|:--|
| [Nome do aplicativo][luis-get-started-create-app] | *Máximo de caracteres padrão |
| Aplicativos| 500 aplicações por recurso de autoria do Azure |
| [Teste em lote][batch-testing]| 10 conjuntos de dados, 1000 declarações por conjunto de dados|
| Lista explícita | 50 por aplicativo|
| Entidades externas | sem limites |
| [Intenções][intents]|500 por aplicação: 499 intenções personalizadas, e a intenção _nenhuma_ necessária.<br>[O](https://aka.ms/dispatch-tool) aplicativo baseado em despacho tem 500 fontes de despacho correspondentes.|
| [Entidades de lista](./luis-concept-entity-types.md) | Pai: 50, filho: 20 mil itens. O nome Canonical é o máximo de caracteres padrão* Valores de sinônimos não têm restrição de comprimento. |
| [Entidades aprendidas por máquina + funções:](./luis-concept-entity-types.md)<br> Composto<br>Simples<br>papel da entidade|Um limite de 100 entidades-mãe ou 330 entidades, qualquer que seja o limite que o usuário acessa primeiro. Um papel conta como uma entidade para o propósito deste limite. Um exemplo é um composto com uma entidade simples, que tem 2 funções é: 1 composto + 1 simples + 2 funções = 4 das 330 entidades.<br>Os subcomponentes podem ser aninhados até 5 níveis.|
|Modelo como recurso| Número máximo de modelos que podem ser usados como descritor (recurso) para um modelo específico para ser 10 modelos. O número máximo de listas de frases usadas como descritor (recurso) para um modelo específico seja de 10 listas de frases.|
| [Pré-visualização - Entidades de lista dinâmica](https://aka.ms/luis-api-v3-doc#dynamic-lists-passed-in-at-prediction-time)|2 listas de ~1k por solicitação de ponto final de previsão de consulta|
| [Padrões](luis-concept-patterns.md)|500 padrões por aplicativo.<br>O comprimento máximo do padrão é de 400 caracteres.<br>3 entidades Pattern.any por padrão<br>Máximo de 2 textos opcionais aninhados no padrão|
| [Pattern.any](./luis-concept-entity-types.md)|100 por aplicativo, 3 entidades pattern.any por padrão |
| [Lista de frases][phrase-list]|500 listas de frases. 10 listas de frases globais devido ao modelo como um limite de recurso. Lista de frases não intercambiáveis tem no máximo 5.000 frases. Lista de frases intercambiáveis tem no máximo 50.000 frases. Número máximo de frases totais por aplicação de 500.000 frases.|
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

## <a name="resource-usage-and-limits"></a>Uso e limites de recursos

Language Understand tem recursos separados, um tipo para autoria e um tipo para consultar o ponto final da previsão. Para saber mais sobre as diferenças entre tipos de chaves, confira [Chaves de ponto de extremidade de previsão de criação e consulta no LUIS](luis-concept-keys.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limites de recursos de autoria

Use _kind_o `LUIS.Authoring`tipo , ao filtrar recursos no portal Azure. Luis limita 500 aplicações por recurso de autoria do Azure.

|Recurso de autoria|Autoria TPS|
|--|--|
|Inicial|1 milhão/mês, 5/segundo|
|F0 - Nível livre |1 milhão/mês, 5/segundo|

* TPS = Transações por segundo

[Saiba mais sobre preços.][pricing]

### <a name="query-prediction-resource-limits"></a>Limites de recursos de previsão de consulta

Use _kind_o `LUIS`tipo , ao filtrar recursos no portal Azure. O recurso de ponto final de previsão de consulta LUIS, usado no tempo de execução, só é válido para consultas de ponto final.

|Recurso de previsão de consulta|Consulta TPS|
|--|--|
|F0 - Nível livre |10 mil/mês, 5/segundo|
|S0 - Nível padrão|50/segundo|

### <a name="sentiment-analysis"></a>Análise de sentimento

[A integração de análise de sentimentos](luis-how-to-publish-app.md#enable-sentiment-analysis), que fornece informações de sentimento, é fornecida sem exigir outro recurso do Azure.

### <a name="speech-integration"></a>Integração de fala

[A integração de](../speech-service/how-to-recognize-intents-from-speech-csharp.md) fala fornece 1 mil solicitações de ponto final por custo unitário.

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
