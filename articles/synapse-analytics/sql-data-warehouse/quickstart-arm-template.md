---
title: Criar um pool de SQL usando o modelo do Azure Resource Manager
description: Aprenda a criar um pool de SQL do Azure Synapse Analytics usando o modelo do Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 766a41b61f32804c7d7f59c946530f8e1a7b869a
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296263"
---
# <a name="quickstart-create-an-azure-synapse-analytics-sql-pool-by-using-an-arm-template"></a>Início Rápido: Criar um pool de SQL do Azure Synapse Analytics usando um modelo ARM

Esse modelo criará um pool de SQL do Azure Synapse Analytics com a Transparent Data Encryption habilitada. O pool de SQL do Synapse refere-se aos recursos corporativos de data warehousing que estão em disponibilidade geral no Azure Synapse.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/).

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json" highlight="57-97":::

O modelo define um recurso:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo. Este modelo cria um pool de SQL do Synapse.
   
   [![Implantar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Insira ou atualize os seguintes valores:

   * **Assinatura**: Selecione uma assinatura do Azure.
   * **Grupo de recursos**: Selecione **Criar** e insira um nome exclusivo para o grupo de recursos e selecione **OK**. Um novo grupo de recursos facilitará a limpeza de recursos.
   * **Região**: Selecione uma região.  Por exemplo, **Centro dos EUA**.
   * **Nome do SQL Server**: aceite o nome padrão ou insira um nome para o SQL Server.
   * **Logon de Administrador**: insira o nome de usuário do administrador do SQL Server.
   * **Senha do administrador do SQL**: insira a senha de administrador para o SQL Server.
   * **Nome do Data Warehouse**: insira um nome para o pool de SQL.
   * **Transparent Data Encryption**: aceito o padrão, habilitado. 
   * **Objetivo de nível de serviço**: aceite o padrão, DW400c.
   * **Localização**: aceite o local padrão do grupo de recursos.
   * **Examinar e criar**: selecione.
   * **Criar**: selecione.

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Você pode usar o portal do Azure para verificar os recursos implantados ou usar o script da CLI do Azure ou do Azure PowerShell para listar os recursos implantados.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your Synapse SQL pool exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your SQL pool account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais dele, exclua o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um pool de SQL do Azure Synapse Analytics usando um modelo do Azure Resource Manager e validou a implantação. Para saber mais sobre o Azure Synapse Analytics e o Azure Resource Manager, continue com os artigos abaixo.

- Leia uma [Visão geral do Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md)
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Criar e implantar seu primeiro modelo do Azure Resource Manager](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
