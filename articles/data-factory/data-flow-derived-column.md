---
title: Transformação de colunas derivadas no mapeamento do fluxo de dados
description: Saiba como transformar dados em escala na Fábrica de Dados Do Azure com a transformação do fluxo de dados de mapeamento Da Coluna Derivada.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 06cb868071612ae2825e86cac32734dcd279c99f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413858"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de colunas derivadas no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Use a transformação de colunas derivadas para gerar novas colunas em seu fluxo de dados ou para modificar campos existentes.

## <a name="derived-column-settings"></a>Configurações de colunas derivadas

Para substituir uma coluna existente, selecione-a através da coluna de sibilamento. Caso contrário, use o campo de seleção de colunas como caixa de texto e digite o nome da nova coluna. Para construir a expressão da coluna derivada, clique na caixa 'Inserir expressão' para abrir o [Data Flow Expression Builder](concepts-data-flow-expression-builder.md).

![Configurações de colunas derivadas](media/data-flow/dc1.png "Configurações de colunas derivadas")

Para adicionar colunas derivadas adicionais, gire sobre uma coluna derivada existente e clique no ícone de mais. Escolha **Adicionar coluna** ou **Adicionar padrão de coluna**. Os padrões das colunas podem ser úteis se os nomes das colunas forem variáveis de suas fontes. Para obter mais informações, consulte [Padrões de coluna](concepts-data-flow-column-pattern.md).

![Nova seleção de colunas derivadas](media/data-flow/columnpattern.png "Nova seleção de colunas derivadas")

## <a name="build-schemas-in-output-schema-pane"></a>Construir esquemas no painel Output Schema

As colunas que você está modificando e adicionando ao seu esquema estão listadas no painel Output Schema,. Você pode construir interativamente estruturas de dados simples e complexas aqui. Para adicionar campos adicionais, selecione **Adicionar coluna**. Para construir hierarquias, selecione **Adicionar subcoluna**.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar subcoluna")

Para obter mais informações sobre o tratamento de tipos complexos no fluxo de dados, consulte [o manuseio do JSON no mapeamento do fluxo de dados](format-json.md#mapping-data-flow-properties).

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

O exemplo abaixo é uma `CleanData` coluna derivada `MoviesYear` chamada que pega um fluxo de entrada e cria duas colunas derivadas. A primeira coluna derivada `Rating` substitui a coluna pelo valor de Rating como um tipo inteiro. A segunda coluna derivada é um padrão que corresponde a cada coluna cujo nome começa com 'filmes'. Para cada coluna combinada, cria `movie` uma coluna igual ao valor da coluna combinada prefixada com 'movie_'. 

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Exemplo de deriva](media/data-flow/derive-script1.png "Exemplo de deriva")

O script de fluxo de dados para essa transformação está no trecho abaixo:

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

- Saiba mais sobre a linguagem de [expressão Mapping Data Flow](data-flow-expression-functions.md).
