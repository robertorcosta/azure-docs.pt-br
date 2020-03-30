---
title: Azure Monitor visualiza designer para conversões de blocos de artigos de trabalho
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658619"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Conversões de azulejos de exibição do Azure Monitor
[O View designer](view-designer.md) é um recurso do Azure Monitor que permite criar visualizações personalizadas para ajudá-lo a visualizar dados em seu espaço de trabalho do Log Analytics, com gráficos, listas e cronogramas. Eles estão sendo eliminados e substituídos por livros de trabalho que fornecem funcionalidade adicional. Este artigo fornece detalhes para converter diferentes telhas em livros de trabalho.

## <a name="donut--list-tile"></a>Azulejo da lista de & de donuts

![Lista de Rosquinhas](media/view-designer-conversion-tiles/donut-list.png)

Recriar o bloco de lista de & de rosquinhas em livros de trabalho envolve duas visualizações separadas. Para a porção de donuthá duas opções.
Para ambos, comece selecionando **Adicionar consulta** e cole a consulta original do designer de exibição na célula.

**Opção 1:** Selecione Gráfico de **Tortas** ![no menu de visualização **de visualização:** Menu de visualização do gráfico de tortas](media/view-designer-conversion-tiles/pie-chart.png)

**Opção 2:** Selecione **Definir por consulta** na parada de **visualização** e adicionar `| render piechart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Para criar uma lista e habilitar linhas de ignição, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

A seguir, um exemplo de como o bloco de lista de & de rosquinhas pode ser reinterpretado em livros de trabalho:

![Livros de trabalho da lista de rosquinhas](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Bloco de lista de lista de & gráfico de linha
![Lista de gráficos de linha](media/view-designer-conversion-tiles/line-list.png) 

Para recriar a parte do gráfico de linha, atualize a consulta da seguinte forma:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Há duas opções para visualizar o gráfico de linhas

**Opção 1:** Selecione gráfico de **linha** a partir do dropdown **de visualização:**
 
 ![Menu gráfico de linhas](media/view-designer-conversion-tiles/line-visualization.png)

**Opção 2:** Selecione **Definir por consulta** na parada de **visualização** e adicionar `| render linechart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

**Exemplo**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Para criar uma lista e habilitar linhas de ignição, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

A seguir está um exemplo de como o gráfico de linhas & lista de azulejos de lista pode ser reinterpretado em livros de trabalho:

![Livros de trabalho da lista de gráficos de linha](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Bloco de lista de & de números

 ![Lista de azulejos](media/view-designer-conversion-tiles/tile-list-example.png)

Para o bloco de números, atualize a consulta da seguinte forma:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Consulta atualizada
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Altere a queda de visualização para **Telhas** e, em seguida, **selecione Configurações de azulejos**.
 ![Visualização de telhas](media/view-designer-conversion-tiles/tile-visualization.png)

Deixe a seção **Título** em branco e selecione **Esquerda**. Alterar o valor para **usar coluna:** para **Contagem**e **Renderização de Coluna** para Grande **Número**:

![Configurações de azulejos](media/view-designer-conversion-tiles/tile-settings.png)

 
Para criar uma lista e habilitar linhas de ignição, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

A seguir está um exemplo de como o número & lista de azulejos de lista pode ser reinterpretado em livros de trabalho:

![Listas de trabalho da lista numérica](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Linha do tempo e lista

 ![Lista de linha do tempo](media/view-designer-conversion-tiles/time-list.png)

Para atualizar a linha do tempo, atualize sua consulta da seguinte forma:

Consulta original
```KQL
search * 
| sort by TimeGenerated desc
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Existem duas opções para visualizar a consulta como um gráfico de barras:

**Opção 1:** Selecione gráfico de **barras** a ![partir do dropdown de **visualização** de visualização: visualização do barchart](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Opção 2:** Selecione **Definir por consulta** na parada de **visualização** e adicionar `| render barchart` à consulta:

 ![Menu de visualização](media/view-designer-conversion-tiles/set-by-query.png)

 
Para criar uma lista e habilitar linhas de ignição, consulte o artigo sobre [tarefas comuns](view-designer-conversion-tasks.md).

A seguir está um exemplo de como a linha do tempo & telhas de lista podem ser reinterpretadas em livros de trabalho:

![Agenda sincronia](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do designer de visão para transição de livros de trabalho](view-designer-conversion-overview.md)
