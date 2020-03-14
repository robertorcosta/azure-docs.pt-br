---
title: Excluir dados do Azure Data Explorer
description: Este artigo descreve os cenários de exclusão no Azure Data Explorer, incluindo limpeza, descarte de extensões e exclusões baseadas em retenção.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204606"
---
# <a name="delete-data-from-azure-data-explorer"></a>Excluir dados do Azure Data Explorer

O Azure Data Explorer dá suporte a vários cenários de exclusão descritos neste artigo. 

## <a name="delete-data-using-the-retention-policy"></a>Excluir dados usando a política de retenção

O Azure Data Explorer exclui automaticamente os dados com base na [política de retenção](/azure/kusto/management/retentionpolicy). Esse método é a maneira mais eficiente e sem complicações de excluir dados. Defina a política de retenção no nível do banco de dados ou da tabela.

Considere um banco de dados ou tabela que é definida para 90 dias de retenção. Se forem necessários apenas 60 dias de dados, exclua os dados mais antigos da seguinte maneira:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Excluir dados ao remover extensões

A [extensão (fragmento de dados)](/azure/kusto/management/extents-overview) é a estrutura interna na qual os dados são armazenados. Cada extensão pode conter até milhões de registros. As extensões podem ser excluídas individualmente ou como um grupo usando [comandos de soltar extensões](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Exemplos

Você pode excluir todas as linhas de uma tabela ou apenas uma extensão específica.

* Excluir todas as linhas de uma tabela:

    ```kusto
    .drop extents from TestTable
    ```

* Excluir uma extensão específica:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Excluir linhas individuais usando limpar

A [limpeza de dados](/azure/kusto/management/data-purge) pode ser usada para excluir linhas de indivíduos. A exclusão não é imediata e requer recursos significativos do sistema. Como tal, ele é recomendado apenas para cenários de conformidade.  

