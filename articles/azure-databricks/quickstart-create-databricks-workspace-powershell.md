---
title: Início Rápido – Criar um workspace do Azure Databricks usando o PowerShell
description: Este guia de início rápido mostra como usar o PowerShell para criar um workspace do Azure Databricks.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485683"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Início Rápido: Criar um workspace do Azure Databricks usando o PowerShell

Este guia de início rápido descreve como usar o PowerShell para criar um workspace do Azure Databricks. Você pode usar o PowerShell para criar e gerenciar recursos do Azure interativamente ou em scripts.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se você optar por usar o PowerShell localmente, este artigo exigirá que você instale o módulo Az PowerShell e conecte-se à sua conta do Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount). Para obter mais informações sobre como instalar o módulo Az PowerShell, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Embora o módulo Az.Databricks do PowerShell esteja em versão prévia, você precisará instalá-lo separadamente por meio do módulo do Az PowerShell usando o seguinte comando: `Install-Module -Name Az.Databricks -AllowPrerelease`.
> Depois que o módulo Az.Databricks do PowerShell estiver em disponibilidade geral, ele passará a fazer parte das versões futuras do módulo do Az PowerShell e ficará disponível nativamente no Azure Cloud Shell.

Se esta for a primeira vez que você usa o Azure Databricks, deverá registrar o provedor de recursos **Microsoft.Databricks**.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma ID de assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos chamado **myresourcegroup** na região **Oeste dos EUA 2**.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Nesta seção, você criará um workspace do Azure Databricks usando o PowerShell.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Forneça os seguintes valores:

|       **Propriedade**       |                                                                                **Descrição**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Nome                     | Forneça um nome para seu workspace do Databricks                                                                                                                                   |
| ResourceGroupName        | Especificar um nome do grupo de recursos existente                                                                                                                                        |
| Location                 | Selecione **Oeste dos EUA 2**. Para outras regiões disponíveis, confira [serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/)                                     |
| ManagedResourceGroupName | Especifique se deseja criar um grupo de recursos gerenciados ou usar um existente.                                                                                        |
| Sku                      | Escolha entre o cluster **Standard**, **Premium** ou **Avaliação**. Para saber mais sobre essas camadas, confira [Preço do Databricks](https://azure.microsoft.com/pricing/details/databricks/) |

A criação do workspace leva alguns minutos. Quando esse processo for concluído, sua conta de usuário será adicionada automaticamente como um usuário administrador no workspace.

Quando a implantação de um workspace falha, ele ainda é criado com estado de falha. Exclua o workspace com falha e crie um que resolva os erros de implantação. Quando você exclui o workspace com falha, o grupo de recursos gerenciado e todos os recursos implantados com êxito também são excluídos.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Determinar o estado de provisionamento de um workspace do Databricks

Para determinar se um workspace do Databricks foi provisionado com êxito, você pode usar o cmdlet `Get-AzDatabricksWorkspace`.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste guia de início rápido não forem necessários para outro guia de início rápido ou tutorial, você poderá excluí-los executando o exemplo a seguir.

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste guia de início rápido no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Para excluir somente o servidor criado neste guia de início rápido sem excluir o grupo de recursos, use o cmdlet `Remove-AzDatabricksWorkspace`.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar um cluster Spark no Databricks](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)
