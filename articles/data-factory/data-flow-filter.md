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
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930364"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação do filtro no mapeamento do fluxo de dados

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
