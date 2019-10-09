---
title: Padrões de Coluna do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando Azure Data Factory padrões de coluna no mapeamento de fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 535656f315f65ffb7aa241618fe9e73b8246b71f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027853"
---
# <a name="mapping-data-flows-column-patterns"></a>Mapeando padrões de coluna de fluxos de dados



Várias transformações de Fluxo de Dados do Azure Data Factory dão suporte à ideia de "Padrões de colunas" para que você possa criar colunas de modelo com base nos padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no construtor de expressões para definir padrões para corresponder colunas para transformação em vez de exigir nomes de campo específicos e exatos. Padrões são úteis se os campos de origem de entrada são alterados com frequência, especialmente no caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Essa condição às vezes é chamada de "descompasso de esquema".

Essa manipulação de "esquema flexível" atualmente é encontrada na coluna derivada e nas transformações agregadas, bem como nas transformações Select e Sink como "mapeamento baseado em regras".

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

## <a name="column-patterns"></a>Padrões de coluna
Padrões de coluna são úteis para lidar com cenários de Descompasso de esquema, bem como cenários gerais. É uma boa opção para condições em que não é possível saber totalmente cada nome de coluna. Você pode padronizar a correspondência no nome de coluna e no tipo de dados de coluna e criar uma expressão para a transformação que executará essa operação em relação a qualquer campo no fluxo de dados que corresponde aos seus padrões `name` & `type`.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar coluna padrão". Padrões de coluna permitem que a coluna de descompasso do esquema corresponda aos padrões.

Ao criar padrões de coluna de modelo, use `$$` na expressão para declarar uma referência para cada campo correspondente do fluxo de dados de entrada.

Se optar por usar uma das funções regex do Construtor de Expressões, você poderá, posteriormente, usar $1, $2, $3 ... para fazer referência a subpadrões com correspondência de sua expressão regex.

Um exemplo de cenário de Padrão de Coluna está usando SUM com uma série de campos de entrada. Os cálculos SUM agregados ficam na transformação de agregação. Em seguida, você pode usar SUM em cada correspondência de tipos de campo que correspondem a "Integer" e, em seguida, use $ $ para fazer referência a cada correspondência em sua expressão.

## <a name="match-columns"></a>Colunas de correspondência
![](media/data-flow/pattern2.png "tipos") de padrões de coluna tipos padrão

Para criar padrões com base em colunas, você pode corresponder ao nome da coluna, ao tipo, ao fluxo ou à posição e usar qualquer combinação delas com funções de expressão e expressões regulares.

![](media/data-flow/position.png "posição da coluna") de posição da coluna

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao mapear colunas na origem e selecionar transformações, você terá a opção de escolher "mapeamento fixo" ou "mapeamento baseado em regras". Quando você conhece o esquema de seus dados e espera colunas específicas do conjunto do dados de origem que sempre correspondem a nomes estáticos específicos, você pode usar o mapeamento fixo. Mas quando você estiver trabalhando com esquemas flexíveis, use o mapeamento baseado em regras. Você poderá criar uma correspondência de padrões usando as regras descritas acima.

![](media/data-flow/rule2.png "mapeamento") baseado em regra de mapeamento baseado em regra

Crie suas regras usando o construtor de expressões. Suas expressões retornarão um valor booliano para corresponder colunas (true) ou excluir colunas (false).

## <a name="pattern-matching-special-columns"></a>Padrões de correspondência de colunas especiais

* `$$` será convertido para o nome de cada correspondência no tempo de design no modo de depuração e na execução em tempo de execução
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo ou transformação em seu fluxo
* `position` é a posição ordinal das colunas em seu fluxo de dados

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [linguagem de expressão](https://aka.ms/dataflowexpressions) de fluxo de dados de mapeamento do ADF para transformações de dados
* Usar padrões de coluna na [transformação do coletor](data-flow-sink.md) e [selecionar a transformação](data-flow-select.md) com mapeamento baseado em regras
