---
title: 'PowerShell: Implantar um código no slot de preparo'
description: Saiba como usar o Azure PowerShell para automatizar a implantação e o gerenciamento do Serviço de Aplicativo. Esta amostra descreve como implantar um código em um ambiente de preparo.
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7e8ebe28fa7989997b5d3adfd14c582438a00b1b
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684782"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Criar um aplicativo web e implantar o código em um ambiente de preparo

Este script de exemplo cria um aplicativo Web no serviço de aplicativo com um slot de implantação adicional chamado "teste" e, em seguida, implanta um aplicativo de exemplo para o slot de "teste".

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

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
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Cria um Plano do Serviço de Aplicativo. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Cria um aplicativo web. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modifica um plano do Serviço de Aplicativo para alterar seu tipo de preço. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Cria um slot de implantação para um aplicativo Web. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Modifica um recurso em um grupo de recursos. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Troca um slot de implantação de um aplicativo Web para produção. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo do Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais do Azure PowerShell para Aplicativos Web do Serviço de Aplicativo do Azure podem ser encontrados nos [exemplos do Azure PowerShell](../samples-powershell.md).
