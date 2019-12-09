---
title: 'PowerShell: Fazer backup de um aplicativo'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como fazer backup de um aplicativo.
author: msangapu-msft
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 5b9906abaa253c667c883a2e0e8ecd6e4cc9d496
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685759"
---
# <a name="back-up-a-web-app-using-powershell"></a>Fazer backup de um aplicativo Web usando o PowerShell

Este script de exemplo cria um aplicativo Web no Serviço de Aplicativo com seus recursos relacionados e então cria um backup único para ele. 

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir pode ser usado para remover o grupo de recursos, o aplicativo Web e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma conta de armazenamento. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Cria um contêiner de armazenamento do Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Gera um token SAS para um contêiner de armazenamento do Azure.  |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo web. |
| [New-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Cria um backup de um aplicativo Web. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Obtém uma lista de backups para um aplicativo Web. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
