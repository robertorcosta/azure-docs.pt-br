---
title: Indexe dados geoespaciais com Azure Cosmos DB
description: Indexar dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137896"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexe dados geoespaciais com Azure Cosmos DB

Projetamos o mecanismo de banco de dados do Azure Cosmos DB para ser verdadeiramente agnóstico e fornecer suporte de primeira classe para json. O mecanismo de banco de dados otimizado de gravação do Azure Cosmos DB entende nativamente os dados espaciais representados no padrão GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas em um plano 2D e então dividida progressivamente em células usando um **quadtree**. Essas células são mapeadas para 1D com base na localização da célula em uma **curva de preenchimento de espaço de Hilbert**, que preserva a localidade de pontos. Além disso, quando os dados de localização são indexados, eles passam por um processo conhecido como **mosaico**, ou seja, todas as células que interseccionam uma localização são identificadas e armazenadas como chaves no índice do Azure Cosmos DB. No momento da consulta, argumentos como pontos e Polígonos também são incluídos no mosaico para extrair os intervalos de IDs de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclua índice espacial para /* (todos os caminhos), todos os dados encontrados dentro do contêiner serão indexados para consultas espaciais eficientes.

> [!NOTE]
> O Azure Cosmos DB suporta indexação de Pontos, Cordas de Linha, Polígonos e MultiPolígenos
>
>

## <a name="modifying-geospatial-data-type"></a>Modificando o tipo de dados geoespaciais

No seu contêiner, o `geospatialConfig` especifica como os dados geoespaciais serão indexados. Você deve `geospatialConfig` especificar um por recipiente: geografia ou geometria. Se não for `geospatialConfig` especificado, o padrão será padrão para o tipo de dados de geografia. Quando você `geospatialConfig`modificar, todos os dados geoespaciais existentes no contêiner serão reindexados.

> [!NOTE]
> Azure Cosmos DB atualmente suporta modificações no geoespacialConfig no .NET SDK apenas nas versões 3.6 ou superior.
>

Aqui está um exemplo para modificar o `geometry` tipo `geospatialConfig` de dados geoespaciais para definir a propriedade e adicionar uma **Caixa delimitadora**:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Exemplos de indexação de dados de geografia

O trecho JSON a seguir mostra uma política de indexação com indexação espacial habilitada para o tipo de dados **de geografia.** É válido para dados espaciais com o tipo de dados de geografia e indexará qualquer Ponto GeoJSON, Polígono, MultiPolygon ou String encontrado dentro de documentos para consulta espacial. Se você estiver modificando a política de indexação usando o portal Azure, você pode especificar o Seguinte JSON para política de indexação para permitir a indexação espacial em seu contêiner:

**Política de indexação de contêineres JSON com indexação espacial geografia**

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

Você também pode modificar a [política de indexação](how-to-manage-indexing-policy.md) usando o Azure CLI, PowerShell ou qualquer SDK.

## <a name="geometry-data-indexing-examples"></a>Exemplos de indexação de dados de geometria

Com o tipo de dados **de geometria,** semelhante ao tipo de dados de geografia, você deve especificar caminhos e tipos relevantes para indexar. Além disso, você também `boundingBox` deve especificar um dentro da política de indexação para indicar a área desejada a ser indexada para esse caminho específico. Cada caminho geoespacial`boundingBox`requer o seu próprio.

A caixa delimitador consiste nas seguintes propriedades:

- **xmin**: a coordenada x indexada mínima
- **ymin**: a coordenada y indexada mínima
- **xmax**: a coordenada x indexada máxima
- **ymax**: a coordenada y indexada máxima

Uma caixa delimitador é necessária porque os dados geométricos ocupam um plano que pode ser infinito. Os índices espaciais, no entanto, requerem um espaço finito. Para o tipo de dados **de geografia,** a Terra é o limite e você não precisa definir uma caixa delimitadora.

Você deve criar uma caixa delimitador que contenha todos (ou a maioria) de seus dados. Somente as operações computadas nos objetos que estão inteiramente dentro da caixa delimitador serão capazes de utilizar o índice espacial. Você não deve tornar a caixa delimitador significativamente maior do que o necessário, porque isso afetará negativamente o desempenho da consulta.

Aqui está um exemplo de política de indexação que indexa dados **de geometria** com **geoespacialConfig** definido para: `geometry`

```json
 {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

A política de indexação acima tem uma **Caixa delimitadora** de (-10, 10) para coordenadas x e (-20, 20) para coordenadas y. O contêiner com a política de indexação acima indexará todos os Pontos, Polígonos, MultiPolídeos e Strings que estão inteiramente dentro desta região.

> [!NOTE]
> Se você tentar adicionar uma política de indexação com `geography` uma **Caixa delimitador** a um contêiner com tipo de dados, ele falhará. Você deve modificar o **geoespacialConfig** `geometry` do recipiente antes de adicionar uma **caixa de limitadora**. Você pode adicionar dados e modificar o restante de sua política de indexação (como os caminhos e tipos) antes ou depois de selecionar o tipo de dados geoespaciais para o contêiner.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre [a consulta de dados espaciais com o Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [dados de localização geoespacial e geojson no Azure Cosmos DB](sql-query-geospatial-intro.md)