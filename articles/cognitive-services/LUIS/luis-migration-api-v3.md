---
title: Mudanças de ponto final de previsão na API V3
description: As APIs do ponto final de previsão de consulta V3 foram alteradas. Use este guia para entender como migrar para APIs de ponto final da versão 3.
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117376"
---
# <a name="prediction-endpoint-changes-for-v3"></a>Mudanças no ponto final da previsão para V3

As APIs do ponto final de previsão de consulta V3 foram alteradas. Use este guia para entender como migrar para APIs de ponto final da versão 3.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Geralmente disponível status** - esta API V3 inclui solicitações e alterações de resposta significativas da API V2.

A API V3 fornece os seguintes novos recursos:

* [Entidades externas](#external-entities-passed-in-at-prediction-time)
* [Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time)
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
|`dynamicLists`|matriz|Somente V3|Não obrigatório.|[Listas dinâmicas](#dynamic-lists-passed-in-at-prediction-time) permitem que você amplie uma entidade de lista treinada e publicada já existente, já no aplicativo LUIS.|
|`externalEntities`|matriz|Somente V3|Não obrigatório.|[Entidades externas](#external-entities-passed-in-at-prediction-time) dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usados como recursos para entidades existentes. |
|`options.datetimeReference`|string|Somente V3|Sem padrão|Usado para determinar a [datadedeslocamentov2](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). O formato para a datadereferênciaReferência é [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).|
|`options.preferExternalEntities`|booleano|Somente V3|false|Especifica se a entidade externa do usuário [(com o mesmo nome da entidade existente)](#override-existing-model-predictions) é usada ou se a entidade existente no modelo é usada para predição. |
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

## <a name="external-entities-passed-in-at-prediction-time"></a>Entidades externas passaram na hora da previsão

Entidades externas dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usados como recursos para entidades existentes. Isso permite que você use seus próprios extratores de entidades separados e personalizados antes de enviar consultas para o seu ponto final de previsão. Como isso é feito no ponto final da previsão de consulta, você não precisa retreinar e publicar seu modelo.

O cliente-aplicativo está fornecendo seu próprio extrator de entidade, gerenciando a correspondência da entidade e determinando a localização dentro da expressão dessa entidade combinada e, em seguida, enviando essas informações com a solicitação.

Entidades externas são o mecanismo para estender qualquer tipo de entidade enquanto ainda são usados como sinais para outros modelos, como papéis, compostos e outros.

Isso é útil para uma entidade que tem dados disponíveis apenas em tempo de execução da previsão de consulta. Exemplos desse tipo de dados estão mudando constantemente dados ou específicos por usuário. Você pode estender uma entidade de contato LUIS com informações externas da lista de contatos de um usuário.

### <a name="entity-already-exists-in-app"></a>Entidade já existe no aplicativo

O valor `entityName` da entidade externa, passado no endpoint da solicitação DO órgão POST, já deve existir no aplicativo treinado e publicado no momento da solicitação. O tipo de entidade não importa, todos os tipos são suportados.

### <a name="first-turn-in-conversation"></a>Primeira volta na conversa

Considere uma primeira expressão em uma conversa de bot de bate-papo onde um usuário insere as seguintes informações incompletas:

`Send Hazem a new message`

A solicitação do bot de bate-papo para LUIS `Hazem` pode passar informações no corpo do POST sobre isso é diretamente correspondido como um dos contatos do usuário.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui essa entidade externa, com todas as outras entidades previstas, porque está definida no pedido.

### <a name="second-turn-in-conversation"></a>Segunda volta na conversa

A próxima expressão do usuário no bot de bate-papo usa um termo mais vago:

`Send him a calendar reminder for the party.`

No pronunciamento anterior, o enunciado `him` `Hazem`usa como referência a . O bot de bate-papo conversacional, `him` no corpo DO POST, pode `Hazem`mapear para o valor da entidade extraído do primeiro enunciado, .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

A resposta de previsão inclui essa entidade externa, com todas as outras entidades previstas, porque está definida no pedido.

### <a name="override-existing-model-predictions"></a>Substituir as previsões de modelo existentes

A `preferExternalEntities` propriedade de opções especifica que se o usuário enviar uma entidade externa que se sobreponha a uma entidade prevista com o mesmo nome, a LUIS escolhe a entidade aprovada ou a entidade existente no modelo.

Por exemplo, considere a consulta `today I'm free`. LUIS detecta `today` como um DatetimeV2 com a seguinte resposta:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se o usuário enviar à entidade externa:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Se `preferExternalEntities` o estiver `false`definido para , LUIS retorna uma resposta como se a entidade externa não fosse enviada.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Se `preferExternalEntities` o estiver `true`definido para , LUIS retorna uma resposta incluindo:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Resolução

A propriedade _opcional_ `resolution` retorna na resposta de previsão, permitindo que você passe os metadados associados à entidade externa e, em seguida, recebê-lo de volta na resposta.

O objetivo principal é estender entidades pré-construídas, mas não se limita a esse tipo de entidade.

A `resolution` propriedade pode ser um número, uma seqüência, um objeto ou uma matriz:

* "Dallas"
* {"texto": "valor"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Listas dinâmicas aprovadas na hora da previsão

Listas dinâmicas permitem que você amplie uma entidade de lista treinada e publicada já existente, já no aplicativo LUIS.

Use esse recurso quando os valores da entidade da lista precisarem ser trocados periodicamente. Este recurso permite que você amplie uma entidade de lista já treinada e publicada:

* No momento da solicitação de ponto final de previsão de consulta.
* Por um único pedido.

A entidade da lista pode estar vazia no aplicativo LUIS, mas tem que existir. A entidade de lista no aplicativo LUIS não foi alterada, mas a capacidade de previsão no ponto final é estendida para incluir até 2 listas com cerca de 1.000 itens.

### <a name="dynamic-list-json-request-body"></a>Corpo de solicitação JSON da lista dinâmica

Envie o seguinte órgão JSON para adicionar uma nova sublista com sinônimos à `LUIS`lista e `POST` prever a entidade de lista para o texto, com a solicitação de previsão de consulta:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

A resposta de previsão inclui essa entidade de lista, com todas as outras entidades previstas, pois está definida no pedido.

## <a name="deprecation"></a>Reprovação

A API V2 não será depreciada por pelo menos 9 meses após a pré-visualização do V3.

## <a name="next-steps"></a>Próximas etapas

Use a documentação da API V3 para atualizar as chamadas REST existentes para APIs [de ponto final](https://aka.ms/luis-api-v3) do LUIS.
