---
title: How to stop monitoring your Azure Red Hat OpenShift cluster | Microsoft Docs
description: This article describes how you can stop monitoring of your Azure Red Hat OpenShift cluster with Azure Monitor for containers.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: f769749532a05260bf3c2c9f99483c5607d985a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384325"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>How to stop monitoring your Azure Red Hat OpenShift cluster with Azure Monitor for containers

After you enable monitoring of your Azure Red Hat OpenShift cluster, you can stop monitoring the cluster if you decide you no longer want to monitor it. This article shows how to accomplish this using the provided Azure Resource Manager templates.  

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

São fornecido dois modelos do Azure Resource Manager para dar suporte para remover os recursos de solução de forma consistente e repetida no seu grupo de recursos. One is a JSON template specifying the configuration to stop monitoring and the other contains parameter values that you configure to specify the OpenShift cluster resource ID and Azure region that the cluster is deployed in. 

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. You must be running the Azure CLI version 2.0.65 or later. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Criar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Salve esse arquivo como **OptOutTemplate.json** em uma pasta local.

3. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Edit the values for **aroResourceId** and **aroResourceLocation** by using the values of the OpenShift cluster, which you can find on the **Properties** page for the selected cluster.

    ![Página de propriedades do contêiner](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Salve esse arquivo como **OptOutParam.json** em uma pasta local.

6. Você está pronto para implantar o modelo. 

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução usando a CLI do Azure

Execute the following command with Azure CLI on Linux to remove the solution and clean up the configuration on your cluster.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remover a solução usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute the following PowerShell commands in the folder containing the template to remove the solution and clean up the configuration from your cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Próximos passos

Se o workspace foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. If you are not familiar with how to delete a workspace, see [Delete an Azure Log Analytics workspace](../../log-analytics/log-analytics-manage-del-workspace.md). 
