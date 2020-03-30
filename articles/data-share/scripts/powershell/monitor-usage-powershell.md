---
title: 'Script PowerShell: Monitore o uso de um Compartilhamento de Dados do Azure | Microsoft Docs'
description: Este script PowerShell recupera métricas de uso de um compartilhamento de dados enviado.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307201"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Use o PowerShell para monitorar o uso de um compartilhamento de dados enviado

Este script PowerShell monitora o uso de dados listando as sincronizações de um compartilhamento de dados enviado e obtendo os detalhes de uma sincronização específica.

## <a name="sample-script"></a>Exemplo de script


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzDataShareSynchroning](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | Liste sincronizações em uma ação. |
| [Detalhes de sincronização get-AzDataShare](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | Obtém detalhes de sincronização de uma sincronização de compartilhamento. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script PowerShell do Azure Data Share podem ser encontradas nas [amostras do PowerShell do Compartilhamento de Dados do Azure](../../samples-powershell.md).
