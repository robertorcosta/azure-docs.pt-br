---
title: Crie um cluster e banco de dados do Azure Data Explorer usando um modelo do Azure Resource Manager
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando um modelo do Azure Resource Manager
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911957"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Crie um cluster e banco de dados do Azure Data Explorer usando um modelo do Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modelo de Gerenciador de recursos do Azure](create-cluster-database-resource-manager.md)

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. 

Neste artigo, você cria um cluster e banco de dados do Azure Data Explorer usando um [modelo do Azure Resource Manager](../azure-resource-manager/management/overview.md). O artigo mostra como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades. Para obter informações sobre a criação de modelos, consulte [a criação de modelos do Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates). Para que a sintaxe e propriedades JSON seja usada em um modelo, consulte [os tipos de recursos microsoft.Kusto](/azure/templates/microsoft.kusto/allversions).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Modelo do Azure Resource Manager para criação de cluster e banco de dados

Neste artigo, você usa um [modelo de partida rápida existente](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Para encontrar mais exemplos de modelos, consulte [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Implantar o modelo e verificar a implantação do modelo

Você pode implantar o modelo do Azure Resource Manager [usando o portal Azure](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) ou [usando powershell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Use o portal Azure para implantar o modelo e verificar a implantação do modelo

1. Para criar um cluster e um banco de dados, use o botão a seguir para iniciar a implantação. Clique com o botão direito do mouse e selecione **Abrir em nova janela** para que você possa acompanhar o restante das etapas neste artigo.

    [![Implantar no Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    O botão **Implantar no Azure** leva você ao portal do Azure para preencher um formulário de implantação.

    ![Implantar no Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Você pode [editar e implantar o modelo no portal Azure](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) usando o formulário.

1. Completar as seções **BASICS** e **SETTINGS.** Selecione nomes exclusivos de cluster e banco de dados.
Leva alguns minutos para criar um cluster e banco de dados do Azure Data Explorer.

1. Para verificar a implantação, você abre o grupo de recursos no [portal Azure](https://portal.azure.com) para encontrar seu novo cluster e banco de dados. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Use powershell para implantar o modelo e verificar a implantação do modelo

#### <a name="deploy-the-template-using-powershell"></a>Implantar o modelo usando powershell

1. Selecione **Testar** no seguinte bloco de código e, em seguida, siga as instruções para entrar no Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Selecione **Copiar** para copiar o script do PowerShell.
1. Clique com o botão direito do mouse no console do Shell e selecione **Colar**.
Leva alguns minutos para criar um cluster e banco de dados do Azure Data Explorer.

#### <a name="verify-the-deployment-using-powershell"></a>Verifique a implantação usando o PowerShell

Para verificar a implantação, use o seguinte script Azure PowerShell.  Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host). Para obter mais informações sobre o gerenciamento de recursos do Azure Data Explorer no PowerShell, leia [Az.Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

[Inserimento de dados no cluster e banco de dados do Azure Data Explorer](ingest-data-overview.md)
