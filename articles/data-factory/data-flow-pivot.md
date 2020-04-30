---
title: Transformação dinâmica no fluxo de dados de mapeamento
description: Dados dinâmicos de linhas para colunas usando Azure Data Factory mapeamento dinâmico do fluxo de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686390"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformação dinâmica no fluxo de dados de mapeamento


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação dinâmica para criar várias colunas a partir dos valores de linha exclusivos de uma única coluna. Pivot é uma transformação de agregação em que você seleciona agrupar por colunas e gerar colunas dinâmicas usando [funções de agregação](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configuração

A transformação dinâmica requer três entradas diferentes: agrupar por colunas, a chave dinâmica e como gerar as colunas dinâmicas

### <a name="group-by"></a>Agrupar por

![Agrupar por opções](media/data-flow/pivot2.png "[Opções de agrupar por")

Selecione as colunas nas quais as colunas dinâmicas são agregadas. Os dados de saída agruparão todas as linhas com os mesmos valores de Group by em uma linha. A agregação feita na coluna dinâmica ocorrerá em cada grupo.

Esta seção é opcional. Se nenhuma coluna Agrupar por for selecionada, todo o fluxo de dados será agregado e apenas uma linha será emitida.

### <a name="pivot-key"></a>Chave dinâmica

![Chave dinâmica](media/data-flow/pivot3.png "Chave dinâmica")

A chave dinâmica é a coluna cujos valores de linha são dinamizados em novas colunas. Por padrão, a transformação dinâmica criará uma nova coluna para cada valor de linha exclusivo.

Na seção denominada **valor**, você pode inserir valores de linha específicos a serem dinamizados. Somente os valores de linha inseridos nesta seção serão dinamizados. A habilitação do **valor nulo** criará uma coluna dinâmica para os valores nulos na coluna.

### <a name="pivoted-columns"></a>Colunas dinâmicas

![Colunas dinâmicas](media/data-flow/pivot4.png "Colunas dinâmicas")

Para cada valor de chave dinâmica exclusivo que se torna uma coluna, gere um valor de linha agregado para cada grupo. Você pode criar várias colunas por chave dinâmica. Cada coluna dinâmica deve conter pelo menos uma [função de agregação](data-flow-expression-functions.md#aggregate-functions).

**Padrão de nome de coluna:** Selecione como formatar o nome da coluna de cada coluna dinâmica. O nome de coluna de saída será uma combinação do valor de chave dinâmica, prefixo de coluna e prefixo opcional, suficiente, caracteres intermediários. 

**Organização da coluna:** Se você gerar mais de uma coluna dinâmica por chave dinâmica, escolha como deseja que as colunas sejam ordenadas. 

**Prefixo da coluna:** Se você gerar mais de uma coluna dinâmica por chave dinâmica, insira um prefixo de coluna para cada coluna. Essa configuração será opcional se você tiver apenas uma coluna dinâmica.

## <a name="help-graphic"></a>Gráfico de ajuda

O gráfico de ajuda abaixo mostra como os diferentes componentes dinâmicos interagem uns com os outros

![Gráficos de ajuda dinâmica](media/data-flow/pivot5.png "Gráfico de ajuda dinâmica")

## <a name="pivot-metadata"></a>Metadados dinâmicos

Se nenhum valor for especificado na configuração da chave dinâmica, as colunas dinâmicas serão geradas dinamicamente em tempo de execução. O número de colunas dinâmicas será igual ao número de valores de chave dinâmica exclusivos multiplicado pelo número de colunas dinâmicas. Como esse pode ser um número variável, o UX não exibirá os metadados da coluna na guia **inspecionar** e não haverá nenhuma propagação de coluna. Para fazer a transformação dessas colunas, use os recursos de [padrão de coluna](concepts-data-flow-column-pattern.md) do fluxo de dados de mapeamento. 

Se valores de chave dinâmica específicos forem definidos, as colunas dinâmicas serão exibidas nos metadados. e os nomes de coluna estarão disponíveis para você no mapeamento inspecionar e coletor.

### <a name="generate-metadata-from-drifted-columns"></a>Gerar metadados de colunas descompassos

A tabela dinâmica gera novos nomes de coluna dinamicamente com base em valores de linha. Você pode adicionar essas novas colunas aos metadados que podem ser referenciados posteriormente no fluxo de dados. Para fazer isso, use a ação rápida [mapa](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) desfeito na visualização de dados. 

![Colunas dinâmicas](media/data-flow/newpivot1.png "Mapear colunas dinâmicas descompassos")

### <a name="sinking-pivoted-columns"></a>Coletando colunas dinâmicas

Embora as colunas dinâmicas sejam dinâmicas, elas ainda podem ser gravadas no armazenamento de dados de destino. Habilite **permitir descompasso de esquema** em suas configurações de coletor. Isso permitirá que você grave colunas que não estão incluídas nos metadados. seus metadados de coluna, mas a opção de descompasso de esquema permitirá que você pouse os dados.

### <a name="rejoin-original-fields"></a>Reassociar campos originais

A transformação dinâmica projetará apenas as colunas agrupar por e dinâmicas. Se você quiser que os dados de saída incluam outras colunas de entrada, use um padrão de [autojunção](data-flow-join.md#self-join) .

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

Experimente a [transformação não dinâmica](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
