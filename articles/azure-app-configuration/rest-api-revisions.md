---
title: API REST de configuração de Azure App – revisões de chave-valor
description: Páginas de referência para trabalhar com revisões de chave-valor usando a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7d1990d6bc524a69de2b22b4f7c5aeec88c3ce9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423900"
---
# <a name="key-value-revisions"></a>Revisões de chave-valor

versão da API: 1,0

Uma **revisão de chave-valor** define a representação histórica de um recurso de chave-valor. As revisões expiram após 7 dias para lojas de camada gratuita ou 30 dias para repositórios de camada Standard. As revisões oferecem suporte às seguintes operações:

- Lista

Para todas as operações, ``key`` é um parâmetro opcional. Se omitido, ele implica **qualquer** chave.
Para todas as operações, ``label`` é um parâmetro opcional. Se omitido, ele implica **qualquer** rótulo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Listar revisões

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Response**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Paginação

O resultado será paginado se o número de itens retornados exceder o limite de resposta. Siga o ``Link`` cabeçalho de resposta opcional e use ``rel="next"`` para navegação.  Como alternativa, o conteúdo fornece um link seguinte na forma da ``@nextLink`` propriedade.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Responde**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Accept-Ranges: items
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="list-subset-of-revisions"></a>Listar subconjunto de revisões

Use o `Range` cabeçalho da solicitação. A resposta conterá um `Content-Range` cabeçalho. Se o servidor não puder atender ao intervalo solicitado, ele responderá com HTTP `416` (RangeNotSatisfiable)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Resposta**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Filtragem

`key`Há suporte para uma combinação de e `label` filtragem.
Use os `key` parâmetros de cadeia de caracteres opcional e de `label` consulta.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Filtros com suporte

|Filtro de chave|Efeito|
|--|--|
|`key` é omitido ou `key=*`|Corresponde a **qualquer** chave|
|`key=abc`|Corresponde a uma chave chamada  **ABC**|
|`key=abc*`|Corresponde aos nomes de chaves que começam com **ABC**|
|`key=*abc`|Corresponde aos nomes de chaves que terminam com **ABC**|
|`key=*abc*`|Corresponde aos nomes de chaves que contêm **ABC**|
|`key=abc,xyz`|Corresponde aos nomes de chaves **ABC** ou **XYZ** (limitado a 5 CSV)|

|Filtro de rótulo|Efeito|
|--|--|
|`label` é omitido ou `label=`|Corresponde à entrada sem rótulo|
|`label=*`|Corresponde a **qualquer** rótulo|
|`label=prod`|Corresponde ao rótulo **prod**|
|`label=prod*`|Corresponde a rótulos que começam com **prod**|
|`label=*prod`|Faz a correspondência de rótulos que terminam com **prod**|
|`label=*prod*`|Corresponde a rótulos que contêm **prod**|
|`label=prod,test`|Faz a correspondência dos rótulos **prod** ou **Test** (limitado a 5 CSV)|

### <a name="reserved-characters"></a>Caracteres reservados

`*`, `\`, `,`

Se um caractere reservado fizer parte do valor, ele deverá ter um escape usando `\{Reserved Character}` . Caracteres não reservados também podem ser ignorados.

### <a name="filter-validation"></a>Validação de filtro

Se ocorrer um erro de validação de filtro, a resposta será HTTP `400` com os detalhes do erro:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Exemplos

- Tudo

    ```http
    GET /revisions
    ```

- Itens em que o nome da chave começa com **ABC**

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- Itens em que o nome da chave é **ABC** ou **XYZ** e os rótulos contêm **prod**

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Campos específicos da solicitação

Use o parâmetro opcional de `$select` cadeia de caracteres de consulta e forneça a lista separada por vírgulas dos campos solicitados. Se o `$select` parâmetro for omitido, a resposta conterá o conjunto padrão.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Acesso Time-Based

Obtenha uma representação do resultado como estava em um momento anterior. Consulte a seção [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1)

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Responde**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
