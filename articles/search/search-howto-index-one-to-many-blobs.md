---
title: Blobs de índice que contêm vários documentos
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs do Azure para conteúdo de texto usando o indexador de blob Pesquisa Cognitiva do Azure, em que cada blob pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 6606391d7fd5c2419714531e1220d97fb29aea4d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529583"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexando BLOBs para produzir vários documentos de pesquisa
Por padrão, um indexador de blob tratará o conteúdo de um blob como um único documento de pesquisa. Determinados valores de **parsingMode** dão suporte a cenários em que um blob individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador Extraia mais de um documento de pesquisa de um blob são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documento de um para muitos
Cada documento que aparece em um índice de Pesquisa Cognitiva do Azure é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise for especificado, e se não houver mapeamento explícito para o campo de chave no índice, o Pesquisa Cognitiva do Azure [mapeará](search-indexer-field-mappings.md) automaticamente a `metadata_storage_path` propriedade como a chave. Esse mapeamento garante que cada blob apareça como um documento de pesquisa distinto.

Ao usar qualquer um dos modos de análise listados acima, um blob é mapeado para "muitos" documentos de pesquisa, tornando uma chave de documento exclusivamente baseada em metadados de blob inadequados. Para superar essa restrição, o Azure Pesquisa Cognitiva é capaz de gerar uma chave de documento "um para muitos" para cada entidade individual extraída de um blob. Essa propriedade é nomeada `AzureSearch_DocumentKey` e adicionada a cada entidade individual extraída do blob. É garantido que o valor dessa propriedade seja exclusivo para cada entidade individual _entre os BLOBs_ e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando nenhum mapeamento de campo explícito para o campo de índice de chave é especificado, o `AzureSearch_DocumentKey` é mapeado para ele, usando a `base64Encode` função de mapeamento de campo.

## <a name="example"></a>Exemplo
Suponha que você tenha uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu contêiner de BLOBs tem BLOBs com a seguinte estrutura:

_Blob1.jsem_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.jsem_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

Quando você cria um indexador e define o **parsingMode** como `jsonLines` -sem especificar nenhum mapeamento de campo explícito para o campo de chave, o mapeamento a seguir será aplicado implicitamente

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

Essa configuração resultará no índice de Pesquisa Cognitiva do Azure que contém as seguintes informações (ID codificada em base64 reduzida para fins de brevidade)

| id | temperatura | pressão | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Supondo que a mesma definição de índice do exemplo anterior, digamos que o contêiner de blob tenha BLOBs com a seguinte estrutura:

_Blob1.jsem_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jsem_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

Quando você cria um indexador com `delimitedText` **parsingMode**, pode parecer natural configurar uma função de mapeamento de campo para o campo de chave da seguinte maneira:

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

No entanto, esse mapeamento _não_ resultará em quatro documentos exibidos no índice, pois o `recordid` campo não é exclusivo _entre BLOBs_. Portanto, é recomendável que você use o mapeamento de campo implícito aplicado da `AzureSearch_DocumentKey` propriedade ao campo de índice de chave para os modos de análise "um para muitos".

Se você quiser configurar um mapeamento de campo explícito, certifique-se de que _SourceField_ seja distinto para cada entidade individual **em todos os BLOBs**.

> [!NOTE]
> A abordagem usada pelo `AzureSearch_DocumentKey` de assegurar a exclusividade por entidade extraída está sujeita a alterações e, portanto, você não deve depender de seu valor para as necessidades do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não estiver familiarizado com a estrutura básica e o fluxo de trabalho da indexação de BLOBs, examine a [indexação do armazenamento de BLOBs do Azure com o azure pesquisa cognitiva](search-howto-index-json-blobs.md) primeiro. Para obter mais informações sobre os modos de análise para diferentes tipos de conteúdo de BLOB, examine os artigos a seguir.

> [!div class="nextstepaction"]
> [Indexando BLOBs CSV](search-howto-index-csv-blobs.md) 
>  [Indexando BLOBs JSON](search-howto-index-json-blobs.md)
