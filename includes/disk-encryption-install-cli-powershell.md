---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: b1da7b69db20b036b69add5604f80896e5d35587
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245026"
---
Azure Disk Encryption pode ser habilitado e gerenciado por meio do [CLI do Azure](/cli/azure) e [Azure PowerShell](/azure/new-azureps-module-az). Para fazer isso, você deve instalar as ferramentas localmente e conectar-se à sua assinatura do Azure.

### <a name="azure-cli"></a>CLI do Azure

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta de linha de comando para gerenciar recursos do Azure. A CLI é projetada para consultar dados com flexibilidade, dar suporte a operações de longa execução como processos desbloqueados e facilitar o script. Você pode instalá-lo localmente seguindo as etapas em [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para [entrar em sua conta do Azure com o CLI do Azure](/cli/azure/authenticate-azure-cli), use o comando [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Se você deseja selecionar um locatário a ser usado para entrar, use:
    
```azurecli
az login --tenant <tenant>
```

Se você tiver várias assinaturas e quiser especificar uma lista específica, obtenha a lista de assinaturas com [az account list](/cli/azure/account#az-account-list) e especifique com [az account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Para obter mais informações, consulte [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
O [módulo Azure PowerShell AZ](/azure/new-azureps-module-az) fornece um conjunto de cmdlets que usa o modelo de [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) para gerenciar seus recursos do Azure. Você pode usá-lo em seu navegador com [Azure cloud Shell](/azure/cloud-shell/overview), ou pode instalá-lo em seu computador local usando as instruções em [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps). 

Se você já tiver instalado localmente, verifique se que você usar a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Para [entrar em sua conta do Azure com Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), use o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Se você tiver várias assinaturas e quiser especificar uma, use o cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) para listá-las, seguidos do cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Executar o cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verificará se a assinatura correta foi selecionada.

Para confirmar se os cmdlets Azure Disk Encryption estão instalados, use o cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Para obter mais informações, consulte [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps). 