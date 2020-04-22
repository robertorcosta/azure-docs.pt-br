---
title: Transformação pivô no mapeamento do fluxo de dados
description: Dados pivôs de linhas para colunas usando o Azure Data Factory mapeando o fluxo de dados Transformação pivô
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686390"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformação pivô no mapeamento do fluxo de dados


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação pivô para criar várias colunas a partir dos valores de linha únicos de uma única coluna. Pivô é uma transformação de agregação onde você seleciona grupo por colunas e gera colunas pivôs usando [funções agregadas](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configuração

A transformação pivô requer três entradas diferentes: grupo por colunas, a chave pivô e como gerar as colunas pivotadas

### <a name="group-by"></a>Agrupar por

![Agrupar por opções](media/data-flow/pivot2.png "[Grupo por opções")

Selecione quais colunas agregar as colunas pivôs. Os dados de saída agruparão todas as linhas com o mesmo grupo por valores em uma linha. A agregação feita na coluna pivotada ocorrerá sobre cada grupo.

Esta seção é opcional. Se nenhum grupo por coluna for selecionado, todo o fluxo de dados será agregado e apenas uma linha será saída.

### <a name="pivot-key"></a>Chave pivô

![Chave pivô](media/data-flow/pivot3.png "Chave pivô")

A chave pivô é a coluna cujos valores de linha são pivotados em novas colunas. Por padrão, a transformação de pivô criará uma nova coluna para cada valor de linha único.

Na seção **denominada Valor,** você pode inserir valores de linha específicos a serem pivotados. Apenas os valores de linha inseridos nesta seção serão pivotados. Habilitar **valor nulo** criará uma coluna pivotada para os valores nulos na coluna.

### <a name="pivoted-columns"></a>Colunas pivotadas

![Colunas pivotadas](media/data-flow/pivot4.png "Colunas pivotadas")

Para cada valor de chave pivô único que se torna uma coluna, gere um valor de linha agregado para cada grupo. Você pode criar várias colunas por chave pivô. Cada coluna pivô deve conter pelo menos uma [função agregada](data-flow-expression-functions.md#aggregate-functions).

**Padrão de nome da coluna:** Selecione como formatar o nome da coluna de cada coluna dinâmica. O nome da coluna outputted será uma combinação do valor da chave pivô, prefixo da coluna e prefixo opcional, basta, caracteres médios. 

**Arranjo da coluna:** Se você gerar mais de uma coluna pivô por chave pivô, escolha como deseja que as colunas sejam encomendadas. 

**Prefixo da coluna:** Se você gerar mais de uma coluna pivô por chave pivô, digite um prefixo de coluna para cada coluna. Esta configuração é opcional se você tiver apenas uma coluna pivotada.

## <a name="help-graphic"></a>Gráfico de ajuda

O gráfico de ajuda abaixo mostra como os diferentes componentes pivôs interagem entre si

![Gráficos de ajuda de pivô](media/data-flow/pivot5.png "Gráfico de ajuda pivô")

## <a name="pivot-metadata"></a>Metadados pivôs

Se nenhum valor for especificado na configuração da tecla pivô, as colunas pivôs serão geradas dinamicamente no tempo de execução. O número de colunas pivotadas será igual ao número de valores-chave pivô únicos multiplicados pelo número de colunas pivôs. Como este pode ser um número de alteração, o UX não exibirá os metadados da coluna na guia **Inspecionar** e não haverá propagação de colunas. Para transformar essas colunas, use os recursos de padrão de [coluna](concepts-data-flow-column-pattern.md) do mapeamento do fluxo de dados. 

Se os valores-chave de pivô específicos forem definidos, as colunas pivôs aparecerão no metadata.e nomes de coluna estarão disponíveis para você no mapeamento Inspecionar e Afundar.

### <a name="generate-metadata-from-drifted-columns"></a>Gerar metadados a partir de colunas derivadas

O pivô gera novos nomes de coluna dinamicamente com base nos valores da linha. Você pode adicionar essas novas colunas nos metadados que podem ser referenciados mais tarde no seu fluxo de dados. Para fazer isso, use o [mapa de ação](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) rápida na visualização de dados. 

![Colunas pivôs](media/data-flow/newpivot1.png "Mapa decolunas pivôs à deriva")

### <a name="sinking-pivoted-columns"></a>Colunas pivotadas afundando

Embora as colunas pivotadas sejam dinâmicas, elas ainda podem ser gravadas no seu armazenamento de dados de destino. Habilitar **Permitir a deriva do esquema** nas configurações da pia. Isso permitirá que você escreva colunas que não estão incluídas em metadados. seus metadados de coluna, mas a opção de deriva de esquema permitirá que você aterrisse os dados.

### <a name="rejoin-original-fields"></a>Junte-se aos campos originais

A transformação pivô só projetará o grupo por colunas e colunas pivotadas. Se você quiser que seus dados de saída incluam outras colunas de entrada, use um padrão [de auto-adesão.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Exemplo

As telas mostradas na seção de configuração têm o seguinte script de fluxo de dados:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Próximas etapas

Tente a [transformação sem pivô](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
