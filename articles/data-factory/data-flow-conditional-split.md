---
title: Transformação condicional dividida no mapeamento do fluxo de dados
description: Dividir dados em diferentes fluxos usando a transformação condicional dividida no fluxo de dados da Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606475"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformação condicional dividida no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação condicional dividida roteia linhas de dados para diferentes fluxos com base em condições de correspondência. A transformação condicional de divisão é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado.

## <a name="configuration"></a>Configuração

A **configuração Split determina** se a linha de dados flui para o primeiro fluxo correspondente ou cada fluxo ao qual corresponde.

Use o construtor de expressão de fluxo de dados para inserir uma expressão para a condição de divisão. Para adicionar uma nova condição, clique no ícone de adição em uma linha existente. Um fluxo padrão também pode ser adicionado para linhas que não correspondem a nenhuma condição.

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma `SplitByYear` transformação condicional dividida `CleanData`nomeada que leva em fluxo de entrada . Essa transformação tem `year < 1960` duas `year > 1980`condições divididas e . `disjoint`é falso porque os dados vão para a primeira condição de correspondência. Cada linha correspondente à primeira `moviesBefore1960`condição vai para o fluxo de saída . Todas as linhas restantes correspondentes `moviesAFter1980`à segunda condição vão para o fluxo de saída . Todas as outras linhas `AllOtherMovies`fluem através do fluxo padrão .

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Próximas etapas

Transformações comuns de fluxo de dados usadas com divisão condicional são a [transformação de join,](data-flow-join.md) [a transformação da pesquisa](data-flow-lookup.md)e a [transformação seleto](data-flow-select.md)
