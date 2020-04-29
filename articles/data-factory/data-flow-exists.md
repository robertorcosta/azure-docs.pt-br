---
title: Existe transformação no fluxo de dados de mapeamento
description: Verificar as linhas existentes usando a transformação Exists no fluxo de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 9c43b141608e5a9051499fdfb2adb5d8b0b593df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232456"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existe transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação Exists é uma transformação de filtragem de linha que verifica se os dados existem em outra fonte ou fluxo. O fluxo de saída inclui todas as linhas no fluxo à esquerda que existem ou não existem no fluxo correto. A transformação Exists é semelhante ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS```e.

## <a name="configuration"></a>Configuração

1. Escolha qual fluxo de dados você está verificando a existência no menu suspenso de **fluxo à direita** .
1. Especifique se você está procurando os dados existentes ou não existentes na configuração de **tipo exist** .
1. Selecione se deseja ou não uma **expressão personalizada**.
1. Escolha quais colunas de chave você deseja comparar como suas condições de existência. Por padrão, o fluxo de dados procura igualdade entre uma coluna em cada fluxo. Para comparar por meio de um valor calculado, passe o mouse sobre a lista suspensa coluna e selecione **coluna computada**.

![Configurações existentes](media/data-flow/exists.png "existe 1")

### <a name="multiple-exists-conditions"></a>Várias condições existentes

Para comparar várias colunas de cada fluxo, adicione uma nova condição EXISTS clicando no ícone de adição ao lado de uma linha existente. Cada condição adicional é unida por uma instrução "and". Comparar duas colunas é o mesmo que a seguinte expressão:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expressão personalizada

Para criar uma expressão de forma livre que contenha operadores diferentes de "and" e "Equals to", selecione o campo **expressão personalizada** . Insira uma expressão personalizada por meio do construtor de expressões de fluxo de dados clicando na caixa azul.

![Existe configurações personalizadas](media/data-flow/exists1.png "existe personalizado")

## <a name="broadcast-optimization"></a>Otimização de difusão

![Junção de difusão](media/data-flow/broadcast.png "Junção de difusão")

Em junções, pesquisas e a transformação Exists, se um ou ambos os fluxos de dados couberem na memória do nó de trabalho, você poderá otimizar o desempenho habilitando a **difusão**. Por padrão, o mecanismo do Spark decidirá automaticamente se deseja ou não difundir um lado. Para escolher manualmente qual lado deve ser difundido, selecione **fixo**.

Não é recomendável desabilitar a difusão por meio da opção **desligar** , a menos que suas junções estejam em erros de tempo limite.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação Exists `checkForChanges` chamada que usa fluxo `NameNorm2` à esquerda e `TypeConversions`fluxo à direita.  A condição EXISTS é a `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` expressão que retornará true se as `EMPID` colunas `Region` e em cada fluxo corresponderem. Como estamos verificando a existência, `negate` é false. Não estamos habilitando nenhuma difusão na guia otimizar, `broadcast` portanto, `'none'`tem valor.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Exemplo de Exists](media/data-flow/exists-script.png "Exemplo de Exists")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Próximas etapas

As transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
