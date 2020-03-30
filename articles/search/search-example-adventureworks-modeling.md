---
title: 'Exemplo: Modele o banco de dados de inventário adventureworks'
titleSuffix: Azure Cognitive Search
description: Aprenda a modelar dados relacionais, transformando-os em um conjunto de dados achatado, para indexação e pesquisa completa de texto na Pesquisa Cognitiva do Azure.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793003"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Exemplo: Modele o banco de dados de inventário AdventureWorks para pesquisa cognitiva do Azure

A Azure Cognitive Search aceita um conjunto de linhas achatado como entrada suscitado pelo [pipeline de indexação (ingestão de dados).](search-what-is-an-index.md) Se os dados de origem forem originados de um banco de dados relacional do SQL Server, este artigo demonstrará uma abordagem para criar um conjunto de linhas achatados antes da indexação, usando o banco de dados de amostra do AdventureWorks como exemplo.

## <a name="about-adventureworks"></a>Sobre o AdventureWorks

Se você tiver uma instância do SQL Server, você pode estar familiarizado com o [banco de dados de amostra do AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Entre as tabelas incluídas nesse banco de dados estão cinco tabelas que expõem informações sobre o produto.

+ **ProductModel**: nome
+ **Produto**: nome, cor, custo, tamanho, peso, imagem, categoria (cada linha ingressa um ProductModel específico)
+ **ProductDescription**: descrição
+ **ProductModelProductDescription**: localidade (cada linha ingressa um ProductModel a um ProductDescription específico para uma linguagem específica)
+ **ProductCategory**: nome, categoria pai

A combinação de todos esses dados em um conjunto de linhas achatados que pode ser ingerido em um índice de pesquisa é o objetivo deste exemplo. 

## <a name="considering-our-options"></a>Considerando as opções

A abordagem mais simples seria indexar todas as linhas da tabela Produto (ingressadas quando apropriado), já que a tabela Produto possui as informações mais específicas. No entanto, essa abordagem poderia expor o índice de pesquisa para duplicatas percebidas em um conjunto de resultados. Por exemplo, o modelo Road-650 está disponível em duas cores e seis tamanhos. Uma consulta por "bicicletas de estradas" seria, portanto, dominada por doze instâncias do mesmo modelo, diferenciadas apenas por tamanho e cor. Os outros seis modelos específicos de estradas seriam todos relegados ao espaço inferior da pesquisa: página dois.

  ![Lista de produtos](./media/search-example-adventureworks/products-list.png "Lista de produtos")
 
Observe que o modelo Road-650 tem doze opções. Linhas de entidades um para muitos são melhor representadas como campos de vários valores ou campos de valor pré-agregado no índice de pesquisa.

Resolver esse problema não é tão simples quanto mover o índice de destino para a tabela ProductModel. Isso ignoraria os dados importantes na tabela Produto que ainda deveriam ser representados nos resultados da pesquisa.

## <a name="use-a-collection-data-type"></a>Usar um tipo de dados de Coleção

A "abordagem correta" é utilizar um recurso de esquema de pesquisa que não tenha um paralelo direto no modelo de banco de dados: **Collection(Edm.String)**. Este construto é definido no esquema de índice de pesquisa cognitiva do Azure. Um tipo de dados de coleta é usado quando você precisa representar uma lista de strings individuais, em vez de uma seqüência muito longa (única). Se houver marcas ou palavras-chave, é possível usar um tipo de dados de Coleção para esse campo.

Ao definir campos de índice de vários valores de **Collection(Edm.String)** para "cor", "tamanho" e "imagem", as informações auxiliares são retidas para facetamento e filtragem sem poluir o índice com entradas duplicadas. Da mesma forma, aplique funções de agregação aos campos do Produto numérico, indexando **minListPrice** em vez de cada produto individual **listPrice**.

Dado um índice com essas estruturas, uma busca por "mountain bikes" mostraria modelos de bicicletas discretos, preservando metadados importantes como cor, tamanho e menores preços. A captura de tela a seguir fornece uma ilustração.

  ![Exemplo de pesquisa de mountain bike](./media/search-example-adventureworks/mountain-bikes-visual.png "Exemplo de pesquisa de mountain bike")

## <a name="use-script-for-data-manipulation"></a>Usar script para manipulação de dados

Infelizmente, esse tipo de modelagem não pode ser facilmente alcançado apenas por meio de instruções SQL. Em vez disso, use um script NodeJS simples para carregar os dados e, em seguida, mapeie-os em entidades JSON de pesquisa amigável.

O mapeamento de pesquisa de banco de dados final é semelhante a este:

+ modelo (Edm.String: pesquisável, filtrável, recuperável) de "ProductModel.Name"
+ description_en (Edm.String: pesquisável) de "ProductDescription" para o modelo em que culture=’en’
+ cor (Coleção (Edm.String): pesquisável, filtrável, com facetas, recuperável): valores únicos de "Product.Color" para o modelo
+ tamanho (Collection (Edm.String): pesquisável, filtrável, com facetas, recuperável): valores exclusivos de "Product.Size" para o modelo
+ imagem (Collection (Edm.String): recuperável): valores únicos de "Product.ThumbnailPhoto" para o modelo
+ minStandardCost (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.StandardCost" para o modelo
+ minListPrice (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.ListPrice" para o modelo
+ minWeight (Edm.Double: filtrável, com facetas, classificável, recuperável): mínimo agregado de todos os "Product.Weight" para o modelo
+ produtos (Coleção (Edm.String): pesquisável, filtrável, recuperável): valores exclusivos de "Product.Name" para o modelo

Após ingressar a tabela ProductModel com Produto e ProductDescription, use [lodash](https://lodash.com/) (ou Linq em C#) para transformar rapidamente o conjunto de resultados:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

O JSON resultante tem este aspecto:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Finalmente, aqui está a consulta SQL para retornar o conjunto de registros inicial. O módulo npm [mssql](https://www.npmjs.com/package/mssql) foi usado para carregar os dados no aplicativo NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplo: Taxonomies de faceta multinível na Pesquisa Cognitiva do Azure](search-example-adventureworks-multilevel-faceting.md)