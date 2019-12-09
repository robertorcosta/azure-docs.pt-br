---
title: 'PowerShell: Conectar-se a uma conta de armazenamento'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como conectar um aplicativo a uma conta de armazenamento.
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: d1c8951445c0be6a3d4b9c4763d719fc7d79d2d5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685326"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>Conectar um aplicativo do Serviço de Aplicativo a uma conta de armazenamento

Neste cenário, você aprenderá a criar uma conta de Armazenamento do Azure e um aplicativo do Serviço de Aplicativo. Em seguida, você vinculará a conta de armazenamento ao aplicativo usando as configurações de aplicativo.

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Após a execução da amostra de script, o comando a seguir poderá ser usado para remover o grupo de recursos, o aplicativo do Serviço de Aplicativo e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo do Serviço de Aplicativo. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Cria uma conta de armazenamento. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtém as chaves de acesso para a conta de Armazenamento do Azure. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica a configuração de um aplicativo do Serviço de Aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Encontre amostras adicionais do Azure PowerShell para o Serviço de Aplicativo do Azure nas [amostras do Azure PowerShell](../samples-powershell.md).
