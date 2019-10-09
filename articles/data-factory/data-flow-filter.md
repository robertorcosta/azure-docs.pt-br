---
title: Transformação do Filtro de Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação do Filtro de Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029351"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformação de filtro de data factory do Azure



As transformações de filtro proporcionam filtragem de linha. Crie uma expressão que defina a condição do filtro. Clique na caixa de texto para iniciar o Construtor de Expressões. Dentro do construtor de expressões, crie uma expressão de filtro para controlar quais linhas do fluxo de dados atual podem passar (filtrar) para a próxima transformação. Considere a transformação de filtro como a cláusula WHERE de uma instrução SQL.

## <a name="filter-on-loan_status-column"></a>Filtrar na coluna loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtro na coluna de ano na demonstração Filmes:

```
year > 1980
```

## <a name="next-steps"></a>Próximas etapas

Experimente uma transformação filtragem de coluna, a [transformação selecionar](data-flow-select.md)
