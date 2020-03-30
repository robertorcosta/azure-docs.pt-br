---
title: 'Script PowerShell: Crie um convite para compartilhar dados do Azure| Microsoft Docs'
description: Este script PowerShell envia um convite de compartilhamento de dados.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307241"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Use o PowerShell para monitorar o uso de um compartilhamento de dados enviado

Este script PowerShell cria um convite de compartilhamento de dados.

## <a name="sample-script"></a>Exemplo de script


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzDataShareConvite](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Crie um convite de compartilhamento de dados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Amostras adicionais de script PowerShell do Azure Data Share podem ser encontradas nas [amostras do PowerShell do Compartilhamento de Dados do Azure](../../samples-powershell.md).
