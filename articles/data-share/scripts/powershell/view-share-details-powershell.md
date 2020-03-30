---
title: 'Script PowerShell: Liste as ações existentes no Azure Data Share | Microsoft Docs'
description: Este script powerShell lista e exibe detalhes de ações.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307132"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Use o PowerShell para visualizar os detalhes de um compartilhamento de dados enviado

Este script do PowerShell lista os compartilhamentos de dados de uma conta existente e obtém os detalhes de uma ação específica.


## <a name="sample-script"></a>Exemplo de script

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Recebe e lista de ações em uma conta. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script PowerShell do Azure Data Share podem ser encontradas nas [amostras do PowerShell do Compartilhamento de Dados do Azure](../../samples-powershell.md).
