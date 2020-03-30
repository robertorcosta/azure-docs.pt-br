---
title: Excluir dados do Azure Data Explorer
description: Este artigo descreve cenários de exclusão no Azure Data Explorer, incluindo purga, queda de extensões e exclusões baseadas em retenção.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501421"
---
# <a name="delete-data-from-azure-data-explorer"></a>Excluir dados do Azure Data Explorer

O Azure Data Explorer suporta vários cenários de exclusão descritos neste artigo. 

## <a name="delete-data-using-the-retention-policy"></a>Excluir dados usando a política de retenção

O Azure Data Explorer exclui automaticamente dados com base na [política de retenção](/azure/kusto/management/retentionpolicy). Este método é a maneira mais eficiente e livre de apagar dados. Defina a política de retenção no banco de dados ou no nível da tabela.

Considere um banco de dados ou tabela que é definida para 90 dias de retenção. Se fornecessário apenas 60 dias de dados, exclua os dados mais antigos da seguinte forma:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Excluir dados por queda de extensões

[Extensão (fragmento de dados)](/azure/kusto/management/extents-overview) é a estrutura interna onde os dados são armazenados. Cada extensão pode conter milhões de registros. As extensões podem ser excluídas individualmente ou como um grupo usando [comandos de extensão de gota.](/azure/kusto/management/extents-commands#drop-extents) 

### <a name="examples"></a>Exemplos

Você pode excluir todas as linhas em uma tabela ou apenas uma extensão específica.

* Exclua todas as linhas em uma tabela:

    ```kusto
    .drop extents from TestTable
    ```

* Exclua uma extensão específica:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Exclua linhas individuais usando purga

[A limpeza de dados](/azure/kusto/concepts/data-purge) pode ser usada para excluir linhas de indivíduos. A exclusão não é imediata e requer recursos significativos do sistema. Como tal, é apenas recomendado para cenários de conformidade.  

