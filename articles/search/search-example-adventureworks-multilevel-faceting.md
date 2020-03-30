---
title: 'Exemplo: Facetas multi-nível'
titleSuffix: Azure Cognitive Search
description: Saiba como criar estruturas de facetas para taxonomias de vários níveis criando uma estrutura de navegação aninhada que pode ser incluída em páginas de aplicativo.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 8672fa0911d1a031205bb3340fa0c03ab9492a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792952"
---
# <a name="example-multi-level-facets-in-azure-cognitive-search"></a>Exemplo: Facetas multinível na Pesquisa Cognitiva do Azure

Os esquemas de pesquisa cognitiva do Azure não suportam explicitamente categorias de taxonomia de vários níveis, mas você pode aproximá-las manipulando conteúdo antes da indexação e, em seguida, aplicando algum manuseio especial aos resultados. 

## <a name="start-with-the-data"></a>Iniciar com os dados

O exemplo deste artigo se baseia em um exemplo anterior, [Model the AdventureWorks Inventory database](search-example-adventureworks-modeling.md), para demonstrar faceting multi-nível na Pesquisa Cognitiva do Azure.

O AdventureWorks tem uma taxonomia simples de dois níveis com uma relação pai-filho. Para as profundidades de taxonomia de comprimento fixo dessa estrutura, uma consulta de junção SQL simples pode ser usada para agrupar a taxonomia:

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![Resultados da consulta](./media/search-example-adventureworks/prod-query-results.png "Resultados da consulta")

## <a name="indexing-to-a-collection-field"></a>Indexação em um campo Collection

No índice que contém essa estrutura, crie um campo **De coleção (Edm.String)** no esquema de Pesquisa Cognitiva do Azure para armazenar esses dados, certificando-se de que os atributos de campo incluam pesquisáveis, filtrantes, facetable e recuperáveis.

Agora, ao indexar o conteúdo que se refere a uma categoria de taxonomia específica, envie a taxonomia como uma matriz que contém o texto de cada nível da taxonomia. Por exemplo, para uma entidade com `ProductCategoryId = 5 (Mountain Bikes)`, envie o campo como `[ "Bikes", "Bikes|Mountain Bikes"]`

Observe a inclusão da categoria pai "Bicicletas" no valor da categoria filho "Mountain Bikes". Cada subcategoria deve inserir o caminho inteiro em relação ao elemento raiz. O separador de caractere de barra vertical é arbitrário, mas precisa ser consistente e não deve aparecer no texto de origem. O caractere separador será usado no código do aplicativo para reconstruir a árvore de taxonomia com base nos resultados da faceta.

## <a name="construct-the-query"></a>Construir a consulta

Ao emitir consultas, inclua a seguinte especificação de faceta (em que a taxonomia é o campo de taxonomia com faceta): `facet = taxonomy,count:50,sort:value`

O valor da contagem precisa ser alto o suficiente para retornar todos os possíveis valores de taxonomia. Os dados do AdventureWorks contêm 41 valores distintos de taxonomia e, portanto, `count:50` é suficiente.

  ![Filtro facetado](./media/search-example-adventureworks/facet-filter.png "Filtro facetado")

## <a name="build-the-structure-in-client-code"></a>Criar a estrutura no código do cliente

No código do aplicativo cliente, reconstrua a árvore de taxonomia dividindo cada valor da faceta no caractere de barra vertical.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

O objeto **categories** agora pode ser usado para renderizar uma árvore de taxonomia recolhível com contagens precisas:

  ![filtro facetado multinível](./media/search-example-adventureworks/multi-level-facet.png "filtro facetado multinível")

 
Cada link na árvore deverá aplicar o filtro relacionado. Por exemplo: 

+ **taxonomia/qualquer** `(x:x eq 'Accessories')` devolução de todos os documentos na filial Acessórios
+ **taxonomia/qualquer** `(x:x eq 'Accessories|Bike Racks')` retorna apenas os documentos com uma subcategoria de Bicicletários a filial Acessórios.

Essa técnica será dimensionada para abranger cenários mais complexos como árvores de taxonomia mais profundas e subcategorias duplicadas que ocorrem em diferentes categorias pai (por exemplo, `Bike Components|Forks` e `Camping Equipment|Forks`).

> [!TIP]
> A velocidade da consulta é afetada pelo número de facetas retornadas. Para dar suporte a conjuntos de taxonomia muito grandes, considere a possibilidade de adicionar um campo **Edm.String** com faceta para conter o valor de taxonomia de nível superior em cada documento. Em seguida, aplique a mesma técnica acima, mas só execute a consulta de faceta de coleção (filtrada no campo de taxonomia raiz) quando o usuário expandir um nó de nível superior. Ou, se um recall de 100% não for necessário, apenas reduza a contagem de facetas para um número razoável e garanta que as entradas de faceta sejam classificadas pela contagem.

## <a name="see-also"></a>Confira também

[Exemplo: Modele o banco de dados de inventário AdventureWorks para pesquisa cognitiva do Azure](search-example-adventureworks-modeling.md)