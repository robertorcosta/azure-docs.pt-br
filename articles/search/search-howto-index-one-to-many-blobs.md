---
title: Bolhas de índice contendo vários documentos
titleSuffix: Azure Cognitive Search
description: Crawl Azure blobs para conteúdo de texto usando o indexador Azure Congitive Search Blob, onde cada bolha pode produzir um ou mais documentos de índice de pesquisa.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112271"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexação de bolhas para produzir vários documentos de pesquisa
Por padrão, um indexador blob tratará o conteúdo de uma bolha como um único documento de pesquisa. Certos **valores de análiseMode** suportam cenários onde uma bolha individual pode resultar em vários documentos de pesquisa. Os diferentes tipos de **parsingMode** que permitem que um indexador extraia mais de um documento de pesquisa de uma bolha são:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Chave de documento de um para muitos
Cada documento que aparece em um índice de pesquisa cognitiva do Azure é identificado exclusivamente por uma chave de documento. 

Quando nenhum modo de análise é especificado, e se não houver um mapeamento explícito para o `metadata_storage_path` campo-chave no índice Azure Cognitive Search [mapeie](search-indexer-field-mappings.md) automaticamente a propriedade como a chave. Esse mapeamento garante que cada bolha apareça como um documento de pesquisa distinto.

Ao usar qualquer um dos modos de análise listados acima, um blob mapeia para "muitos" documentos de pesquisa, tornando uma chave de documento exclusivamente baseada em metadados blob inadequados. Para superar essa restrição, o Azure Cognitive Search é capaz de gerar uma chave de documento "de um para muitos" para cada entidade individual extraída de uma bolha. Esta propriedade `AzureSearch_DocumentKey` é nomeada e é adicionada a cada entidade individual extraída da bolha. O valor desta propriedade é garantido ser único para cada entidade individual _em blobs_ e as entidades aparecerão como documentos de pesquisa separados.

Por padrão, quando não são especificados mapeamentos de `AzureSearch_DocumentKey` campo explícitos para o `base64Encode` campo de índice chave, o é mapeado para ele, usando a função de mapeamento de campo.

## <a name="example"></a>Exemplo
Suponha que você tenha uma definição de índice com os seguintes campos:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

E seu recipiente blob tem bolhas com a seguinte estrutura:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Quando você cria um indexador e `jsonLines` define o modo de **análise** para - sem especificar quaisquer mapeamentos de campo explícitos para o campo-chave, o mapeamento a seguir será aplicado implicitamente
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Essa configuração resultará no índice de pesquisa cognitiva do Azure contendo as seguintes informações (id codificado base64 encurtado para brevidade)

| id | temperatura | pressão |  timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Mapeamento de campo personalizado para campo de chave de índice

Assumindo a mesma definição de índice do exemplo anterior, digamos que o seu recipiente blob tem bolhas com a seguinte estrutura:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Quando você cria um `delimitedText` indexador com **parsingMode,** pode parecer natural configurar uma função de mapeamento de campo para o campo-chave da seguinte forma:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

No entanto, esse mapeamento _não_ resultará em 4 `recordid` documentos aparecendo no índice, pois o campo não é único _entre blobs_. Por isso, recomendamos que você faça uso do `AzureSearch_DocumentKey` mapeamento de campo implícito aplicado da propriedade ao campo de índice chave para modos de análise "de um a muitos".

Se você quiser configurar um mapeamento de campo explícito, certifique-se de que o _sourceField_ é distinto para cada entidade individual **em todas as blobs**.

> [!NOTE]
> A abordagem `AzureSearch_DocumentKey` usada para garantir a exclusividade por entidade extraída está sujeita a alterações e, portanto, você não deve confiar em seu valor para as necessidades de sua aplicação.

## <a name="next-steps"></a>Próximas etapas

Se você ainda não está familiarizado com a estrutura básica e o fluxo de trabalho da indexação de bolhas, você deve rever [indexando o Armazenamento Azure Blob com o Azure Cognitive Search](search-howto-index-json-blobs.md) primeiro. Para obter mais informações sobre modos de análise para diferentes tipos de conteúdo blob, revise os seguintes artigos.

> [!div class="nextstepaction"]
> [Indexação de blobs](search-howto-index-csv-blobs.md)
> CSV[Indexando bolhas JSON](search-howto-index-json-blobs.md)
