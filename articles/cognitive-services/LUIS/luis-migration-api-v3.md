---
title: Mudanças de ponto final de previsão na API V3
description: As APIs do ponto final de previsão de consulta V3 foram alteradas. Use este guia para entender como migrar para APIs de ponto final da versão 3.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 4b6d28b24ffc6c0a848d1c7a34e863da0606d936
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530378"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Mudanças no ponto final da previsão para V3

As APIs do ponto final de previsão de consulta V3 foram alteradas. Use este guia para entender como migrar para APIs de ponto final da versão 3.

**Geralmente disponível status** - esta API V3 inclui solicitações e alterações de resposta significativas da API V2.

A API V3 fornece os seguintes novos recursos:

* [Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Mudanças de entidade pré-construída JSON](#prebuilt-entity-changes)

A [solicitação](#request-changes) e [a resposta](#response-changes) do ponto final de previsão têm alterações significativas para suportar os novos recursos listados acima, incluindo o seguinte:

* [Alterações do objeto de resposta](#top-level-json-changes)
* [Referências do nome da entidade em vez do nome da entidade](#entity-role-name-instead-of-entity-name)
* [Propriedades para marcar entidades em enunciados](#marking-placement-of-entities-in-utterances)

[A documentação de referência](https://aka.ms/luis-api-v3) está disponível para V3.

## <a name="v3-changes-from-preview-to-ga"></a>V3 muda de visualização para GA

A V3 fez as seguintes alterações como parte da mudança para a GA:

* As seguintes entidades pré-construídas têm diferentes respostas JSON:
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [Hora da dataV2](luis-reference-prebuilt-datetimev2.md)
    * Nome da chave `units` da unidade mensurável de para`unit`

* Solicitar alteração json do corpo:
    * de `preferExternalEntities` para`preferExternalEntities`
    * parâmetro `score` opcional para entidades externas

* O corpo de resposta JSON muda:
    * `normalizedQuery`Removido

## <a name="suggested-adoption-strategy"></a>Estratégia de adoção sugerida

Se você usar o Bot Framework, Bing Spell Check V7 ou quiser migrar apenas a sua autoria do aplicativo LUIS, continue a usar o ponto final v2.

Se você sabe que nenhum dos seus aplicativos ou integrações clientes (Bot Framework e Bing Spell Check V7) são impactados e você está confortável migrando sua autoria do aplicativo LUIS e seu ponto final de previsão ao mesmo tempo, comece a usar o ponto final da previsão V3. O ponto final da previsão v2 ainda estará disponível e é uma boa estratégia de recuo.


## <a name="not-supported"></a>Sem suporte

### <a name="bing-spell-check"></a>Verificação Ortográfica do Bing

Esta API não é suportada no ponto final da previsão de V3 - continue a usar o ponto final de previsão da API V2 para correções ortográficas. Se você precisar de correção ortográfica ao usar a API V3, peça ao aplicativo cliente para chamar a API [de verificação ortográfica](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview) de Bing e alterar o texto para a ortografia correta, antes de enviar o texto para a API DE LUIS.

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Aplicativos clientes do Bot Framework e do Azure Bot Service

Continue a usar o ponto final de previsão da API V2 até que o V4.7 do Bot Framework seja lançado.

## <a name="v2-api-deprecation"></a>Depreciação da API V2

A API de previsão v2 não será depreciada por pelo menos 9 meses após a pré-visualização do V3, 8 de junho de 2020.

## <a name="endpoint-url-changes"></a>Alterações de URL de ponto final

### <a name="changes-by-slot-name-and-version-name"></a>Alterações por nome de slot e nome da versão

O formato da chamada HTTP ponto final V3 foi alterado.

Se você quiser consultar por versão, primeiro você precisa `"directVersionPublish":true`publicar via [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) com . Consulta o ponto final referente ao ID da versão em vez do nome do slot.

|VERSÃO DA API DE PREVISÃO|METHOD|URL|
|--|--|--|
|V3|GET|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|V3|POST|https://<b>{REGIÃO}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|V2|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|V2|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>previsão</b>/<b>v3.0</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|

|Valores válidos para`SLOT-NAME`|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>Alterações de solicitação

### <a name="query-string-changes"></a>Alterações da seqüência de string susquins de consulta

A API V3 tem diferentes parâmetros de seqüência de consulta.

|Nome param|Type|Versão|Padrão|Finalidade|
|--|--|--|--|--|
|`log`|booleano|V2 & V3|false|Armazene a consulta no arquivo log. O valor padrão é falso.|
|`query`|string|Somente V3|Sem padrão - é necessário na solicitação GET|**Em V2,** o enunciado a `q` ser previsto está no parâmetro. <br><br>**Em V3,** a funcionalidade é `query` passada no parâmetro.|
|`show-all-intents`|booleano|Somente V3|false|Retornar todas as intenções com a pontuação correspondente no objeto **predição.intents.** As intenções são devolvidas `intents` como objetos em um objeto pai. Isso permite o acesso programático sem a `prediction.intents.give`necessidade de encontrar a intenção em uma matriz: . Em V2, estes foram devolvidos em uma matriz. |
|`verbose`|booleano|V2 & V3|false|**Em V2,** quando definido como verdadeiro, todas as intenções previstas foram devolvidas. Se você precisar de todas as intenções previstas, use o param V3 de `show-all-intents`.<br><br>**Em V3,** este parâmetro fornece apenas detalhes de metadados da entidade da previsão da entidade.  |
|`timezoneOffset`|string|V2|-|Fuso horário aplicado a entidades DatetimeV2.|
|`datetimeReference`|string|V3|-|[Fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado a entidades DatetimeV2. Substitui-se `timezoneOffset` de V2.|


### <a name="v3-post-body"></a>Corpo V3 POST

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Propriedade|Type|Versão|Padrão|Finalidade|
|--|--|--|--|--|
|`dynamicLists`|matriz|Somente V3|Não obrigatório.|[Listas dinâmicas](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time) permitem que você amplie uma entidade de lista treinada e publicada já existente, já no aplicativo LUIS.|
|`externalEntities`|matriz|Somente V3|Não obrigatório.|[Entidades externas](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time) dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usados como recursos para entidades existentes. |
|`options.datetimeReference`|string|Somente V3|Sem padrão|Usado para determinar a [datadedeslocamentov2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para a datadereferênciaReferência é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|booleano|Somente V3|false|Especifica se a entidade externa do usuário [(com o mesmo nome da entidade existente)](schema-change-prediction-runtime.md#override-existing-model-predictions) é usada ou se a entidade existente no modelo é usada para predição. |
|`query`|string|Somente V3|Obrigatórios.|**Em V2,** o enunciado a `q` ser previsto está no parâmetro. <br><br>**Em V3,** a funcionalidade é `query` passada no parâmetro.|

## <a name="response-changes"></a>Mudanças de resposta

A resposta de consulta JSON foi alterada para permitir maior acesso programático aos dados usados com mais freqüência.

### <a name="top-level-json-changes"></a>Mudanças de JSON de nível superior



As principais propriedades JSON para `verbose` V2 são, quando é definida como `intents` verdadeira, o que retorna todas as intenções e suas pontuações na propriedade:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

As principais propriedades JSON para V3 são:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

O `intents` objeto é uma lista não ordenada. Não assuma que `intents` o primeiro `topIntent`filho no corresponde ao . Em vez `topIntent` disso, use o valor para encontrar a pontuação:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

As alterações do esquema JSON de resposta permitem:

* Clara distinção entre `query`o enunciado `prediction`original, e a previsão retornada, .
* Acesso programático mais fácil aos dados previstos. Em vez de enumerar através de uma matriz em V2, você pode acessar valores por **nome** para intenções e entidades. Para funções de entidade previstas, o nome da função é devolvido porque é único em todo o aplicativo.
* Os tipos de dados, se determinados, são respeitados. Numéricos não são mais devolvidos como cordas.
* Distinção entre informações de previsão de primeira `$instance` prioridade e metadados adicionais, retornados no objeto.

### <a name="entity-response-changes"></a>Mudanças de resposta da entidade

#### <a name="marking-placement-of-entities-in-utterances"></a>Marcando a colocação de entidades em enunciados

**Em V2**, uma entidade foi marcada `startIndex` `endIndex`em um pronunciamento com o e .

**Em V3,** a entidade `startIndex` `entityLength`é marcada com e .

#### <a name="access-instance-for-entity-metadata"></a>Acesso `$instance` para metadados de entidades

Se você precisar de metadados da entidade, `verbose=true` a seqüência de consultas `$instance` precisa usar o sinalizador e a resposta contém os metadados no objeto. Exemplos são mostrados nas respostas JSON nas seções a seguir.

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>Cada entidade prevista é representada como uma matriz

O `prediction.entities.<entity-name>` objeto contém uma matriz porque cada entidade pode ser prevista mais de uma vez no enunciado.

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>Mudanças de entidade pré-construídas

O objeto de resposta V3 inclui alterações em entidades pré-construídas. Revise [entidades pré-construídas específicas](luis-reference-prebuilt-entities.md) para saber mais.

#### <a name="list-entity-prediction-changes"></a>Mudanças na previsão da entidade de lista

O JSON para uma previsão de entidade de lista mudou para ser uma matriz de matrizes:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Cada matriz interior corresponde ao texto dentro da expressão. O objeto interior é uma matriz porque o mesmo texto pode aparecer em mais de uma sublista de uma entidade de lista.

Ao mapear `entities` entre o `$instance` objeto e o objeto, a ordem dos objetos é preservada para as previsões da entidade de lista.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>Nome da entidade em vez do nome da entidade

Em V2, `entities` a matriz devolveu todas as entidades previstas, com o nome da entidade sendo o identificador único. Em V3, se a entidade usa funções e a previsão é para uma função de entidade, o principal identificador é o nome da função. Isso é possível porque os nomes das opções de entidade devem ser únicos em todo o aplicativo, incluindo outros nomes de modelo (intenção, entidade).

No exemplo a seguir: considere um enunciado que inclua o texto, `Yellow Bird Lane`. Este texto é previsto `Location` como o papel `Destination`de uma entidade personalizada de .

|Texto de enunciado|Nome da entidade|Nome da função|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

Em V2, a entidade é identificada pelo nome da _entidade_ com a função como propriedade do objeto:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

Na V3, a entidade é referenciada pelo papel da _entidade,_ se a previsão é para o papel:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

Em V3, o mesmo `verbose` resultado com o sinalizador para retornar metadados da entidade:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>Estender o aplicativo na hora da previsão

Aprenda [conceitos](schema-change-prediction-runtime.md) sobre como estender o aplicativo em tempo de execução de previsão.

## <a name="deprecation"></a>Reprovação

A API V2 não será depreciada por pelo menos 9 meses após a pré-visualização do V3.

## <a name="next-steps"></a>Próximas etapas

Use a documentação da API V3 para atualizar as chamadas REST existentes para APIs [de ponto final](https://aka.ms/luis-api-v3) do LUIS.
