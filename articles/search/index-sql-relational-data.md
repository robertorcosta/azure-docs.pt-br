---
title: Dados relacionais modelo SQL para importação e indexação
titleSuffix: Azure Cognitive Search
description: Aprenda a modelar dados relacionais, desnormalizados em um conjunto de resultados planos, para indexação e pesquisa completa de texto na Pesquisa Cognitiva do Azure.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790088"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Como modelar dados SQL relacionais para importação e indexação na Pesquisa Cognitiva do Azure

A Azure Cognitive Search aceita um conjunto de linhas planas como entrada para o [pipeline de indexação](search-what-is-an-index.md). Se os dados de origem se originam de tabelas unidas em um banco de dados relacional do SQL Server, este artigo explica como construir o conjunto de resultados e como modelar uma relação pai-filho em um índice de Pesquisa Cognitiva Do Azure.

Como ilustração, vamos nos referir a um banco de dados hipotético de hotéis, baseado em [dados de demonstração.](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels) Suponha que o banco de dados seja composto por uma mesa de Hotéis$ com 50 hotéis, e uma mesa Rooms$ com quartos de diferentes tipos, tarifas e comodidades, totalizando 750 quartos. Há uma relação de um a muitos entre as mesas. Em nossa abordagem, uma visão fornecerá a consulta que retorna 50 linhas, uma linha por hotel, com detalhes associados do quarto incorporados em cada linha.

   ![Tabelas e visualização no banco de dados de Hotéis](media/index-sql-relational-data/hotels-database-tables-view.png "Tabelas e visualização no banco de dados de Hotéis")


## <a name="the-problem-of-denormalized-data"></a>O problema dos dados desnormalizados

Um dos desafios em trabalhar com relacionamentos únicos é que as consultas padrão construídas em tabelas unidas retornarão dados desnormalizados, o que não funciona bem em um cenário de Pesquisa Cognitiva do Azure. Considere o seguinte exemplo que une hotéis e quartos.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Os resultados desta consulta retornam todos os campos do Hotel, seguidos por todos os campos do Room, com informações preliminares do hotel se repetindo para cada valor do quarto.

   ![Dados desnormalizados, dados de hotéis redundantes quando os campos de quarto são adicionados](media/index-sql-relational-data/denormalize-data-query.png "Dados desnormalizados, dados de hotéis redundantes quando os campos de quarto são adicionados")


Embora essa consulta tenha sucesso na superfície (fornecendo todos os dados em um conjunto de linhas planas), ela falha em fornecer a estrutura de documento certa para a experiência de pesquisa esperada. Durante a indexação, o Azure Cognitive Search criará um documento de pesquisa para cada linha ingerida. Se seus documentos de pesquisa se parecessem com os resultados acima, você teria percebido duplicatas - sete documentos separados apenas para o hotel Twin Dome. Uma consulta sobre "hotéis na Flórida" devolveria sete resultados apenas para o hotel Twin Dome, empurrando outros hotéis relevantes para os resultados da pesquisa.

Para obter a experiência esperada de um documento por hotel, você deve fornecer um conjunto de linhas na granularidade certa, mas com informações completas. Felizmente, você pode fazer isso facilmente adotando as técnicas neste artigo.

## <a name="define-a-query-that-returns-embedded-json"></a>Defina uma consulta que retorna JSON incorporado

Para oferecer a experiência de pesquisa esperada, seu conjunto de dados deve consistir em uma linha para cada documento de pesquisa no Azure Cognitive Search. Em nosso exemplo, queremos uma fila para cada hotel, mas também queremos que nossos usuários possam pesquisar em outros campos relacionados ao quarto que eles se preocupam, como a diária, tamanho e número de camas, ou uma vista da praia, todas elas fazem parte de um detalhe do quarto.

A solução é capturar o detalhe da sala como JSON aninhado e, em seguida, inserir a estrutura JSON em um campo em uma visão, como mostrado na segunda etapa. 

1. Suponha que você tenha duas mesas unidas, Hotéis$ e Quartos$, que contêm detalhes para 50 hotéis e 750 quartos, e são unidos no campo HotelID. Individualmente, essas mesas contêm 50 hotéis e 750 quartos relacionados.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Crie uma exibição composta por todos`SELECT * from dbo.Hotels$`os campos na tabela pai ( ), com a adição de um novo campo *Salas* que contém a saída de uma consulta aninhada. Uma cláusula **PARA JSON AUTO** sobre `SELECT * from dbo.Rooms$` estruturas da saída como JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   A captura de tela a seguir mostra a visualização resultante, com o campo *De salas* nvarchar na parte inferior. O campo *Quartos* existe apenas na vista do HotelRooms.

   ![Vista de HotelRooms](media/index-sql-relational-data/hotelsrooms-view.png "Visualização hoteRooms")

1. Corra `SELECT * FROM dbo.HotelRooms` para recuperar o conjunto de linhas. Esta consulta retorna 50 linhas, uma por hotel, com informações de quartos associadas como uma coleção JSON. 

   ![Rowset da visualização do HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Rowset da visualização do HotelRooms")

Este conjunto de linhas está agora pronto para importação na Pesquisa Cognitiva do Azure.

> [!NOTE]
> Esta abordagem pressupõe que o JSON incorporado está abaixo dos [limites máximos de tamanho da coluna do SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Se seus dados não se encaixam, você pode tentar uma abordagem programática, como ilustrado em [Exemplo: Modele o banco de dados de inventário AdventureWorks para pesquisa cognitiva do Azure](search-example-adventureworks-modeling.md).

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Use uma coleção complexa para o lado "muitos" de um relacionamento de um a muitos

No lado da Pesquisa Cognitiva do Azure, crie um esquema de índice que modele a relação de um a muitos usando JSON aninhado. O conjunto de resultados criado na seção anterior geralmente corresponde ao esquema de índice fornecido abaixo (cortamos alguns campos para a brevidade).

O exemplo a seguir é semelhante ao exemplo em [Como modelar tipos de dados complexos](search-howto-complex-data-types.md#creating-complex-fields). A estrutura *do Rooms,* que tem sido o foco deste artigo, está na coleção de campos de um índice chamado *hotéis.* Este exemplo também mostra um tipo complexo de *Endereço*, que difere de *Salas* na medida em que é composto por um conjunto fixo de itens, em oposição ao número múltiplo e arbitrário de itens permitidos em uma coleção.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Dado o conjunto de resultados anterior e o esquema de índice acima, você tem todos os componentes necessários para uma operação de indexação bem sucedida. O conjunto de dados achatado atende aos requisitos de indexação, mas preserva informações detalhadas. No índice de Pesquisa Cognitiva do Azure, os resultados da pesquisa cairão facilmente em entidades baseadas em hotéis, preservando o contexto dos quartos individuais e seus atributos.

## <a name="next-steps"></a>Próximas etapas

Usando seu próprio conjunto de dados, você pode usar o [assistente de dados Importar](search-import-data-portal.md) para criar e carregar o índice. O assistente detecta a coleção JSON incorporada, como a contida em *Rooms,* e infere um esquema de índice que inclui uma coleção de tipo complexa. 

  ![Índice inferido pelo assistente de dados importação](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Índice inferido pelo assistente de dados importação")

Experimente o seguinte quickstart para aprender as etapas básicas do assistente de dados Importação.

> [!div class="nextstepaction"]
> [Quickstart: Crie um índice de pesquisa usando o portal Azure](search-get-started-portal.md)