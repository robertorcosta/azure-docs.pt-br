---
title: Existe transformação no mapeamento do fluxo de dados
description: Verifique se há linhas existentes usando a transformação existente no fluxo de dados da Fábrica de Dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 512d9a688d7f808056a91b5bc0484c378af33948
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413810"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no mapeamento do fluxo de dados

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A transformação existe é uma transformação de filtragem de linha que verifica se seus dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo esquerdo que existem ou não existem no fluxo direito. A transformação existe ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```é semelhante e .

## <a name="configuration"></a>Configuração

1. Escolha qual fluxo de dados você está verificando a existência no fluxo de **dados** certo.
1. Especifique se você está procurando que os dados existam ou não existam na **configuração de tipo Exist.**
1. Selecione se deseja ou não uma **expressão Personalizada**.
1. Escolha quais colunas-chave deseja comparar como suas condições existem. Por padrão, o fluxo de dados busca igualdade entre uma coluna em cada fluxo. Para comparar através de um valor computado, paire sobre a coluna suspensa e **selecione Coluna Computada**.

![Existe configurações](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Múltiplas condições existem

Para comparar várias colunas de cada fluxo, adicione uma nova condição existente clicando no ícone de adição ao lado de uma linha existente. Cada condição adicional é acompanhada por uma declaração de "e". Comparar duas colunas é o mesmo que a seguinte expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores diferentes de "e" e "igual a", selecione o campo **de expressão Personalizado.** Digite uma expressão personalizada através do construtor de expressão de fluxo de dados clicando na caixa azul.

![Existe configurações personalizadas](media/data-flow/exists1.png "existe costume")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é `checkForChanges` uma transformação `NameNorm2` existe `TypeConversions`chamada que leva fluxo esquerdo e fluxo direito .  A condição existe `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` é a expressão `EMPID` que `Region` retorna verdadeira se as colunas e colunas em cada fluxo corresponderem. Como estamos verificando a `negate` existência, é falso. Não estamos permitindo nenhuma transmissão na guia `broadcast` otimizar, `'none'`então tem valor .

No UX da Fábrica de Dados, essa transformação se parece com a imagem abaixo:

![Existe exemplo](media/data-flow/exists-script.png "Existe exemplo")

O script de fluxo de dados para essa transformação está no trecho abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Próximas etapas

Transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
