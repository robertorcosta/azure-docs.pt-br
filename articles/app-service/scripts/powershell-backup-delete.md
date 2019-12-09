---
title: 'PowerShell: Excluir um backup de aplicativo'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como excluir um backup de aplicativo.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b8d3175b4266e132d94f6f2dde163cb60fb11396
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685808"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Excluir um backup de um aplicativo Web usando o Azure PowerShell

Este script de exemplo cria um aplicativo Web no Serviço de Aplicativo com seus recursos relacionados e então cria um backup único para ele. 

Para executar esse script, você precisará de um backup existente para um aplicativo Web. Para criar um, consulte [Backup up a web app](powershell-backup-onetime.md) (Fazer backup de um aplicativo Web) ou [Create a scheduled backup for a web app](powershell-backup-scheduled.md) (Criar um backup agendado para um aplicativo Web).

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de backups para um aplicativo Web. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Remove o backup especificado de um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
