---
title: Transformação do filtro no mapeamento do fluxo de dados
description: Filtre as linhas usando a transformação do filtro no fluxo de dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413723"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação do filtro no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As transformações do filtro permitem a filtragem de remar com base em uma condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação do filtro é semelhante a uma cláusula WHERE no SQL.

## <a name="configuration"></a>Configuração

Use o construtor de expressão de fluxo de dados para inserir uma expressão para a condição do filtro. Para abrir o construtor de expressão, clique na caixa azul. A condição do filtro deve ser do tipo booleano. Para obter mais informações sobre como criar uma expressão, consulte a documentação do [construtor de expressão.](concepts-data-flow-expression-builder.md)

![Transformação do filtro](media/data-flow/filter1.png "Transformação do filtro")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é `FilterBefore1960` uma transformação de `CleanData`filtro nomeada que leva no fluxo de entrada . A condição do `year <= 1960`filtro é a expressão .

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Transformação do filtro](media/data-flow/filter1.png "Transformação do filtro")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Próximas etapas

Filtrar colunas com a [transformação selecionada](data-flow-select.md)
