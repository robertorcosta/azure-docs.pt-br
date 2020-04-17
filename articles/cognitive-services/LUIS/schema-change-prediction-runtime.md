---
title: Estender aplicativo em tempo de execução - LUIS
description: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: c0f9d71f5d89d73d9cdce2a2f646859d8eba3adc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538573"
---
# <a name="extend-app-at-prediction-runtime"></a>Estender aplicativo em tempo de execução de previsão

O esquema do aplicativo (modelos e recursos) é treinado e publicado até o ponto final da previsão. Este modelo publicado é usado no tempo de execução da previsão. Você pode passar novas informações, juntamente com o enunciado do usuário, para o tempo de execução da previsão para aumentar a previsão.

Duas alterações no esquema de tempo de execução da previsão incluem:
* [Entidades externas](#external-entities)
* [Listas dinâmicas](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Entidades externas

Entidades externas dão ao seu aplicativo LUIS a capacidade de identificar e rotular entidades durante o tempo de execução, que podem ser usados como recursos para entidades existentes. Isso permite que você use seus próprios extratores de entidades separados e personalizados antes de enviar consultas para o seu ponto final de previsão. Como isso é feito no ponto final da previsão de consulta, você não precisa retreinar e publicar seu modelo.

O cliente-aplicativo está fornecendo seu próprio extrator de entidade, gerenciando a correspondência da entidade e determinando a localização dentro da expressão dessa entidade combinada e, em seguida, enviando essas informações com a solicitação.

Entidades externas são o mecanismo para estender qualquer tipo de entidade enquanto ainda são usados como sinais para outros modelos.

Isso é útil para uma entidade que tem dados disponíveis apenas em tempo de execução da previsão de consulta. Exemplos desse tipo de dados estão mudando constantemente dados ou específicos por usuário. Você pode estender uma entidade de contato LUIS com informações externas da lista de contatos de um usuário.

Entidades externas fazem parte da API de autoria v3. Saiba mais sobre [como migrar](luis-migration-api-v3.md) para esta versão.

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

Nesta virada da conversa, o `him` enunciado `Hazem`usa como referência a . O bot de bate-papo conversacional, `him` no corpo DO POST, pode `Hazem`mapear para o valor da entidade extraído do primeiro enunciado, .

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

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Listas dinâmicas

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

## <a name="next-steps"></a>Próximas etapas

* [Pontuação de previsão](luis-concept-prediction-score.md)
* [Autor de alterações da API V3](luis-migration-api-v3.md)
