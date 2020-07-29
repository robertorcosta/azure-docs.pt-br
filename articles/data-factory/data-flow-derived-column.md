---
title: Transformação de coluna derivada no fluxo de dados de mapeamento
description: Saiba como transformar dados em escala no Azure Data Factory com a transformação de coluna derivada do fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606496"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de coluna derivada no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação de coluna derivada para gerar novas colunas no fluxo de dados ou modificar os campos existentes.

## <a name="derived-column-settings"></a>Configurações de coluna derivada

Para substituir uma coluna existente, selecione-a por meio da lista suspensa de colunas. Caso contrário, use o campo de seleção de coluna como uma caixa de texto e digite o nome da nova coluna. Para criar a expressão da coluna derivada, clique na caixa 'Inserir expressão' para abrir o [Construtor de Expressões de Fluxo de Dados](concepts-data-flow-expression-builder.md).

![Configurações de coluna derivada](media/data-flow/dc1.png "Configurações de coluna derivada")

Para adicionar mais colunas derivadas, posicione o cursor em uma coluna derivada existente e clique no ícone de adição. Escolha **Adicionar coluna** ou **Adicionar padrão de coluna**. Os padrões de coluna poderão ser úteis se os nomes de colunas forem variáveis nas fontes. Para obter mais informações, confira [Padrões de colunas](concepts-data-flow-column-pattern.md).

![Seleção de nova coluna derivada](media/data-flow/columnpattern.png "Seleção de nova coluna derivada")

## <a name="build-schemas-in-output-schema-pane"></a>Criar esquemas no painel Esquema de Saída

As colunas que você modifica e adiciona ao esquema são listadas no painel Esquema de Saída. Você pode criar de maneira interativa estruturas de dados simples e complexas aqui. Para adicionar mais campos, selecione **Adicionar coluna**. Para criar hierarquias, selecione **Adicionar subcoluna**.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

Para obter mais informações sobre como processar tipos complexos no fluxo de dados, confira [Tratamento de JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar coluna complexa](media/data-flow/complexcolumn.png "Adicionar colunas")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma coluna derivada chamada `CleanData` que usa um fluxo de entrada `MoviesYear` e cria duas colunas derivadas. A primeira coluna derivada substitui a coluna `Rating` pelo valor da classificação como um tipo inteiro. A segunda coluna derivada é um padrão que faz a correspondência de cada coluna cujo nome começa com 'movies'. Para cada coluna correspondente, ele cria uma coluna `movie` que é igual ao valor da coluna correspondente prefixada com 'movie_'. 

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Obter um exemplo](media/data-flow/derive-script1.png "Obter um exemplo")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de expressão do fluxo de dados de mapeamento](data-flow-expression-functions.md).
