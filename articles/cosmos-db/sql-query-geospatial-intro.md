---
title: Dados de localização geoespaciais e geojson no Azure Cosmos DB
description: Entenda como criar objetos espaciais com o Azure Cosmos DB e a API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 59c8b31dcc8594d2cafb2db7832e290b01026f60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367577"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dados de localização geoespaciais e geojson no Azure Cosmos DB

Este artigo é uma introdução à funcionalidade geoespacial no Azure Cosmos DB. Atualmente, armazenar e acessar dados geoespaciais é suportado apenas por contas API Azure Cosmos DB SQL. Depois de ler nossa documentação sobre indexação geoespacial, você poderá responder às seguintes perguntas:

* Como fazer para armazenar dados espaciais no Azure Cosmos DB?
* Como fazer para consultar dados geoespaciais no Azure Cosmos DB, no SQL e no LINQ?
* Como fazer para habilitar ou desabilitar a indexação espacial no Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais

Os dados espaciais descrevem a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, eles correspondem aos objetos na Terra e dados geoespaciais. Os dados espaciais podem ser usados para representar a localização de uma pessoa, um lugar de interesse ou a divisa de uma cidade ou de um lago. Com frequência, os casos de uso comum envolvem consultas de proximidade, por exemplo, “encontre todas as cafeterias próximas ao local atual”.

A API SQL do Azure Cosmos DB suporta dois tipos de dados espaciais: o tipo de dados **de geometria** e o tipo de dados **de geografia.**

- O tipo **de geometria** representa dados em um sistema de coordenadas euclidiano (plano)
- O tipo **geografia** representa dados em um sistema de coordenadas esféricas.

## <a name="supported-data-types"></a>Tipos de dados com suporte

O Azure Cosmos DB dá suporte à indexação e à consulta de dados de ponto geoespaciais representados usando a [especificação GeoJSON](https://tools.ietf.org/html/rfc7946). As estruturas de dados GeoJSON são sempre objetos JSON válidos e, portanto, podem ser armazenadas e consultadas usando o Azure Cosmos DB sem nenhuma ferramenta ou biblioteca especializada.

O Azure Cosmos DB suporta os seguintes tipos de dados espaciais:

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>Pontos

Um **Ponto** denota uma única posição no espaço. Em dados geoespaciais, um Ponto representa o local exato, que poderia ser um endereço de um supermercado, de um quiosque, de um automóvel ou de uma cidade.  Um ponto é representado no GeoJSON (e no Azure Cosmos DB) usando seu par de coordenadas ou a longitude e a latitude.

Aqui está um exemplo JSON para um ponto:

**Pontos no Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Os tipos de dados espaciais podem ser incorporados em um documento Azure Cosmos DB, como mostrado neste exemplo de um perfil de usuário contendo dados de localização:

**Usar o perfil com a localização armazenada no Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Aponta em um sistema de coordenadas de geometria

Para o tipo de dados **de geometria,** a especificação GeoJSON especifica o eixo horizontal primeiro e o eixo vertical segundo.

### <a name="points-in-a-geography-coordinate-system"></a>Pontos em um sistema de coordenadas de geografia

Para o tipo de dados **de geografia,** a especificação GeoJSON especifica longitude primeiro e latitude segundo. Assim como acontece em outros aplicativos de mapeamento, a longitude e a latitude são ângulos e são representados em graus. Os valores de longitude são medidos a partir do Meridiano Principal e estão entre -180,0 graus e 180,0 graus e os valores de latitude são medidos a partir do Equador e estão entre -90,0 graus e 90,0 graus.

O Azure Cosmos DB interpreta coordenadas como representadas de acordo com o sistema de referência WGS-84. Consulte abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.

### <a name="linestrings"></a>Cordas de Linha

**LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os conectam. Em dados geoespaciais, as LineStrings são comumente usadas para representar vias expressas ou rios.

**Strings de linha em GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polígonos

Um **Polígono** é um limite de pontos conectados que formam uma LineString fechada. Os polígonos são comumente usados para representar formações naturais, como lagos ou jurisdições políticas, como cidades e estados. Aqui está um exemplo de um Polígono no Azure Cosmos DB:

**Polígonos no GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A especificação GeoJSON exige que, para Polígonos válidos, o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada.
>
> Os pontos em um Polígono devem ser especificados no sentido anti-horário. Um Polígono especificado no sentido horário representa o inverso da região dentro dele.
>
>

### <a name="multipolygons"></a>MultiPolígonos

Um **MultiPolygon** é uma matriz de zero ou mais Polígonos. **Os multipolígonos** não podem se sobrepor a lados ou ter qualquer área comum. Eles podem tocar em um ou mais pontos.

**MultiPolígonos em GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Sistemas de referência de coordenadas

Como a forma da terra é irregular, as coordenadas de dados geoespaciais da geografia são representadas em muitos sistemas de referência coordenadas (CRS), cada um com seus próprios quadros de referência e unidades de medição. Por exemplo, o "National Grid of Britain" é um sistema de referência preciso para o Reino Unido, mas não para fora dele.

O CRS mais popular em uso hoje é o Sistema Geodésico Mundial [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Os dispositivos GPS e vários serviços de mapeamento, incluindo as APIs do Google Maps e do Bing Mapas usam WGS-84. O Azure Cosmos DB suporta indexação e consulta de dados geoespaciais geográficos usando apenas o WGS-84 CRS.

## <a name="creating-documents-with-spatial-data"></a>Criando documentos com dados espaciais
Quando você cria documentos que contêm valores GeoJSON, eles são indexados automaticamente com um índice espacial de acordo com a política de indexação do contêiner. Se você estiver trabalhando com um SDK do Azure Cosmos DB em uma linguagem dinamicamente tipada, como Python ou Node.js, deverá criar um GeoJSON válido.

**Criar documentos com dados geoespaciais no Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se você estiver trabalhando com as APIs SQL, `MultiPolygon` você pode `Microsoft.Azure.Cosmos.Spatial` usar as `Point` `LineString` `Polygon`classes e classes dentro do namespace para incorporar informações de localização dentro dos objetos do aplicativo. Essas classes ajudam a simplificar a serialização e a desserialização de dados espaciais no GeoJSON.

**Criar documentos com dados geoespaciais no .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Se você não tiver as informações de latitude e longitude, mas tiver os endereços físicos ou nome de localização como cidade ou país/região, você pode procurar as coordenadas reais usando um serviço de geocodificação como bing maps REST Services. Saiba mais sobre a geocodificação do Bing Mapas [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre [a consulta de dados espaciais com o Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [dados espaciais do Índice com o Azure Cosmos DB](sql-query-geospatial-index.md)