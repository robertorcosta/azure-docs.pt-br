---
title: 'Script PowerShell: definir e visualizar as configurações de sicronização do Compartilhamento de dados do Azure| Microsoft Docs'
description: Este script PowerShell define e obtém configurações de sincronização de compartilhamento.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b8d01a383e816bfc73413d21e9cc8d51d326ab14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307176"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Use o PowerShell para monitorar o uso de um compartilhamento de dados enviado

Este script PowerShell define e obtém configurações de sincronização de compartilhamento.

## <a name="sample-script"></a>Exemplo de script


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Configuração de sincronização new-AzDataShare](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | Crie uma sincronização de compartilhamento. |
| [Configuração de sincronização get-AzDataShare](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Obtém configurações de sincronização de uma sincronização de compartilhamento. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script PowerShell do Azure Data Share podem ser encontradas nas [amostras do PowerShell do Compartilhamento de Dados do Azure](../../samples-powershell.md).
