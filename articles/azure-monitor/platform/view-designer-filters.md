---
title: Filtros nas visualizações do Monitor Do Azure | Microsoft Docs
description: Um filtro em uma exibição do Azure Monitor permite que os usuários filtrem os dados na exibição pelo valor de uma determinada propriedade sem modificar a exibição em si.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658567"
---
# <a name="filters-in-azure-monitor-views"></a>Filtros nas visualizações do Monitor Do Azure
Um **filtro** em uma [exibição do Azure Monitor](view-designer.md) permite que os usuários filtrem os dados na exibição pelo valor de uma determinada propriedade sem modificar a exibição em si.  Por exemplo, você pode permitir que os usuários da sua exibição filtrem somente os dados de um computador ou conjunto de computadores específico.  É possível criar vários filtros em uma única exibição para permitir que os usuários filtrem por várias propriedades.  Este artigo descreve como usar um filtro e adicioná-lo a uma exibição personalizada.

## <a name="using-a-filter"></a>Como usar um filtro
Clique no intervalo de datas na parte superior de uma exibição para abrir a queda onde você pode alterar o intervalo de data para a exibição.

![Exemplo de filtro](media/view-designer-filters/filters-example-time.png)

Clique **+** no para adicionar um filtro usando filtros personalizados definidos para a exibição. Selecione um valor para o filtro no menu suspenso ou digite um valor. Continue a adicionar filtros **+** clicando no . 


![Exemplo de filtro](media/view-designer-filters/filters-example-custom.png)

Se você remover todos os valores de um filtro, ele não será aplicado.


## <a name="creating-a-filter"></a>Como criar um filtro

Crie um filtro por meio da guia **Filtros** ao [editar uma exibição](view-designer.md).  O filtro é global para a exibição e se aplica a todas as partes da exibição.  

![Configurações de filtro](media/view-designer-filters/filters-settings.png)

A tabela a seguir descreve as configurações de um filtro.

| Configuração | Descrição |
|:---|:---|
| Nome do campo | Nome do campo usado para filtragem.  Este campo deve corresponder ao campo resumido em **Consulta para Valores**. |
| Consulta de valores | Consulta que deve ser executada para popular a lista suspensa do filtro para o usuário.  Esta consulta deve ser [usada para resumir](/azure/kusto/query/summarizeoperator) ou [ser distinta](/azure/kusto/query/distinctoperator) para fornecer valores únicos para um determinado campo e deve corresponder ao Nome do **Campo**.  Você pode usar [sort](/azure/kusto/query/sortoperator) para classificar os valores que são exibidos para o usuário. |
| Marca | Nome do campo que é usado em consultas de apoio do filtro e que também é exibido para o usuário. |

### <a name="examples"></a>Exemplos

A tabela a seguir inclui alguns exemplos de filtros comuns.  

| Nome do campo | Consulta de valores | Marca |
|:--|:--|:--|
| Computador   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computadores |
| EventLevelName | Event &#124; distinct EventLevelName | Severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Severity |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificar as consultas de exibição

Para que um filtro tenha efeito, é necessário modificar todas as consultas na exibição para filtrar os valores selecionados.  Se você não modificar quaisquer consultas na exibição, então quaisquer valores que o usuário selecionar não terão efeito.

A sintaxe para usar um valor de filtro em uma consulta é: 

    where ${filter name}  

Por exemplo, se sua exibição tiver uma consulta que retorne eventos e use um filtro chamado _Computadores,_ você poderá usar a seguinte consulta.

    Event | where ${Computers} | summarize count() by EventLevelName

Se você adicionar outro filtro chamado Gravidade, você poderá usar a consulta a seguir para usar ambos os filtros.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [Partes da Visualização](view-designer-parts.md) que você pode adicionar ao modo de exibição personalizado.
