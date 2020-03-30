---
title: Mapear entrada para campos de saída
titleSuffix: Azure Cognitive Search
description: Extrair e enriquecer campos de dados de origem e mapear para campos de saída em um índice de pesquisa cognitiva do Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280978"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Como mapear campos enriquecidos com IA para um índice pesquisável

Neste artigo, você aprenderá como mapear campos de entrada enriquecidos para campos de saída em um índice pesquisável. Uma vez que você [definiu um conjunto de qualificações](cognitive-search-defining-skillset.md), mapeie os campos de saída de qualquer habilidade diretamente a contribuição de valores para um determinado campo no índice de pesquisa. 

Os mapeamentos de campo de saída são necessários para mover conteúdo de documentos enriquecidos para o índice.  O documento enriquecido é realmente uma árvore de informações, e mesmo que haja suporte para tipos complexos no índice, às vezes você pode querer transformar as informações da árvore enriquecida em um tipo mais simples (por exemplo, uma matriz de strings). Os mapeamentos de campo de saída permitem que você execute transformações de forma de dados, achatando informações.

## <a name="use-outputfieldmappings"></a>Use outputFieldMappings
Para mapear campos, adicione `outputFieldMappings` à sua definição do indexador, conforme mostrado abaixo:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

O corpo da solicitação é estruturado da seguinte maneira:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Para cada mapeamento de campo de saída, defina a localização dos dados na árvore de documentos enriquecida (sourceFieldName) e o nome do campo como referenciado no índice (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Informações de achatamento de tipos complexos 

O caminho em um sourceFieldName pode representar um elemento ou vários elementos. No exemplo acima, ```/document/content/sentiment``` representa um único valor numérico, enquanto ```/document/content/organizations/*/description``` representa várias descrições de organização. 

Em casos onde há vários elementos, são "simples" em uma matriz que contém cada um dos elementos. 

Mais concretamente, para o ```/document/content/organizations/*/description``` exemplo, os dados no campo *descrições* seriam uma matriz simples de descrições antes que ele obtém indexado:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Este é um princípio importante, por isso vamos dar outro exemplo. Imagine que você tem uma variedade de tipos complexos como parte da árvore de enriquecimento. Digamos que há um membro chamado customEntities que tem uma matriz de tipos complexos como o descrito abaixo.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Vamos supor que seu índice tenha um campo chamado 'doenças' do tipo Coleção (Edm.String), onde você gostaria de armazenar cada um dos nomes das entidades. 

Isso pode ser feito facilmente\*usando o símbolo " " como segue:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Esta operação simplesmente "achatará" cada um dos nomes dos elementos customentities em uma única matriz de strings como esta:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Próximas etapas
Após mapear os campos enriquecidos para campos de pesquisa, defina os atributos de campo para cada um dos campos pesquisáveis [como parte da definição do índice](search-what-is-an-index.md).

Para obter mais informações sobre mapeamento de campo, consulte [mapeamentos de campo em indexadores de pesquisa cognitiva do Azure](search-indexer-field-mappings.md).
