---
title: Transformação agregada no mapeamento do fluxo de dados
description: Saiba como agregar dados em escala na Fábrica de Dados Do Azure com o fluxo de dados de mapeamento Transformação agregada.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 1830a16108e6d8bb251d7ca45ae471e2f606874b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240612"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação agregada no mapeamento do fluxo de dados 

A transformação Aggregate define agregações de colunas em seus fluxos de dados. Usando o Expression Builder, você pode definir diferentes tipos de agregações, como Soma, MIN, MAX e COUNT agrupadas por colunas existentes ou computadas.

## <a name="group-by"></a>Agrupar por

Selecione uma coluna existente ou crie uma nova coluna computada para usar como um grupo por cláusula para sua agregação. Para usar uma coluna existente, selecione-a a partir da estada. Para criar uma nova coluna computada, gire sobre a cláusula e clique **em Coluna Computada**. Isso abre o construtor de [expressão de fluxo de dados](concepts-data-flow-expression-builder.md). Depois de criar sua coluna computada, digite o nome da coluna de saída no campo **Nome como.** Se você deseja adicionar um grupo adicional por cláusula, gire sobre uma cláusula existente e clique no ícone de adição.

![Grupo de transformação agregado por configurações](media/data-flow/agg.png "Grupo de transformação agregado por configurações")

Um grupo por cláusula é opcional em uma transformação Agregada.

## <a name="aggregate-column"></a>Coluna agregada 

Vá para a guia **Agregados** para construir expressões de agregação. Você pode substituir uma coluna existente com uma agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa à direita ao lado do seletor de nomes da coluna. Para editar a expressão, clique na caixa de texto para abrir o construtor de expressão. Para adicionar agregações adicionais, gire sobre uma expressão existente e clique em um ícone para criar uma nova coluna de agregação ou [padrão de coluna](concepts-data-flow-column-pattern.md).

Cada expressão de agregação deve conter pelo menos uma função agregada.

![Configurações agregadas de transformação](media/data-flow/agg2.png "Configurações agregadas de transformação")


> [!NOTE]
> No modo Debug, o construtor de expressão não pode produzir visualizações de dados com funções agregadas. Para exibir visualizações de dados para transformações agregadas, feche o construtor de expressões e visualize os dados através da guia 'Visualização de dados'.

## <a name="reconnect-rows-and-columns"></a>Reconecte linhas e colunas

As transformações agregadas são semelhantes às consultas selecionadas agregadas sql. As colunas que não estão incluídas no seu grupo por cláusula ou funções agregadas não fluirão até a saída de sua transformação agregada. Se desejar incluir outras colunas na sua saída agregada, faça um dos seguintes métodos:

* Use uma função `last()` agregada como ou `first()` para incluir essa coluna adicional.
* Rejunte as colunas ao fluxo de saída usando o [padrão de auto-adesão](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Removendo linhas duplicadas

Um uso comum da transformação agregada é remover ou identificar entradas duplicadas nos dados de origem. Esse processo é conhecido como deduplicação. Com base em um conjunto de chaves de grupo por chaves, use uma heurística de sua escolha para determinar qual linha duplicada manter. A heurística `first()`comum é, `last()`e `max()` `min()`. Use [padrões de coluna](concepts-data-flow-column-pattern.md) para aplicar a regra em cada coluna, exceto no grupo por colunas.

![Eliminação de duplicação](media/data-flow/agg-dedupe.png "Eliminação de duplicação")

No exemplo acima, `ProductID` colunas e `Name` estão sendo usados para agrupamento. Se duas linhas têm os mesmos valores para essas duas colunas, elas são consideradas duplicadas. Nesta transformação agregada, os valores da primeira linha correspondidos serão mantidos e todos os outros serão descartados. Usando a sintaxe de padrão de `ProductID` coluna, todas as colunas cujos nomes não são e `Name` são mapeadas para o nome da coluna existente e dado o valor das primeiras linhas combinadas. O esquema de saída é o mesmo do esquema de entrada.

Para cenários de `count()` validação de dados, a função pode ser usada para contar quantas duplicatas existem.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo pega `MoviesYear` um fluxo de `year`entrada e agrupa linhas por coluna . A transformação cria `avgrating` uma coluna agregada `Rating`que avalia a média da coluna. Esta transformação `AvgComedyRatingsByYear`agregada é nomeada .

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Grupo por exemplo](media/data-flow/agg-script1.png "Grupo por exemplo")

![Exemplo agregado](media/data-flow/agg-script2.png "Exemplo agregado")

O script de fluxo de dados para essa transformação está no trecho abaixo.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Próximas etapas

* Defina a agregação baseada em janelas usando a [transformação da janela](data-flow-window.md)
