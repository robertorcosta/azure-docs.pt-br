---
title: Indexar dados geoespaciais com Azure Cosmos DB
description: Indexar dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566368"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexar dados geoespaciais com Azure Cosmos DB

Projetamos Azure Cosmos DB mecanismo de banco de dados para ser verdadeiramente independente de esquema e fornecer suporte de primeira classe para JSON. O mecanismo de banco de dados otimizado para gravação de Azure Cosmos DB entende nativamente os dados espaciais representados no padrão geojson.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas em um plano 2D e então dividida progressivamente em células usando um **quadtree**. Essas células são mapeadas para 1D com base na localização da célula em uma **curva de preenchimento de espaço de Hilbert**, que preserva a localidade de pontos. Além disso, quando os dados de localização são indexados, eles passam por um processo conhecido como **mosaico**, ou seja, todas as células que interseccionam uma localização são identificadas e armazenadas como chaves no índice do Azure Cosmos DB. No momento da consulta, argumentos como pontos e Polígonos também são incluídos no mosaico para extrair os intervalos de IDs de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclua o índice espacial para/* (todos os caminhos), todos os dados encontrados no contêiner serão indexados para consultas espaciais eficientes.

> [!NOTE]
> Azure Cosmos DB dá suporte à indexação de pontos, LineStrings, polígonos e multipolígonos
>
>

## <a name="geography-data-indexing-examples"></a>Exemplos de indexação de dados de Geografia

O trecho JSON a seguir mostra uma política de indexação com indexação espacial habilitada para o tipo de dados **geography** . Ele é válido para dados espaciais com o tipo de dados geography e indexará qualquer ponto geojson, polígono, MultiPolygon ou LineString encontrado em documentos para consultas espaciais. Se você estiver modificando a política de indexação usando o portal do Azure, poderá especificar o JSON a seguir para a política de indexação para habilitar a indexação espacial em seu contêiner:

**Política de indexação de contêiner JSON com indexação espacial de Geografia**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Se o valor GeoJSON de localização no documento estiver malformado ou inválido, então ele não será indexado para consultas espaciais. Você pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.
>
>
>

Você também pode [Modificar a política de indexação](how-to-manage-indexing-policy.md) usando o CLI do Azure, o PowerShell ou qualquer SDK.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre como [consultar dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [os dados geoespaciais e de localização geojson no Azure Cosmos DB](sql-query-geospatial-intro.md)