---
title: Como parar de monitorar o cluster do Azure Red Hat OpenShift v3 | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster do Azure Red Hat OpenShift com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: e1d3200af62ad185fa942fa2c8f7f3b4e6bfd89b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196186"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Como parar de monitorar o cluster do Azure Red Hat OpenShift v3

Depois de habilitar o monitoramento do cluster do Azure Red Hat OpenShift versão 3. x, você poderá parar de monitorar o cluster com Azure Monitor para contêineres se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso usando o modelo de Azure Resource Manager fornecido.  

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

São fornecido dois modelos do Azure Resource Manager para dar suporte para remover os recursos de solução de forma consistente e repetida no seu grupo de recursos. Um é um modelo JSON que especifica a configuração para interromper o monitoramento e o outro contém valores de parâmetro que você configura para especificar a ID de recurso de cluster OpenShift e a região do Azure na qual o cluster é implantado.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.65 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

4. Edite os valores de **aroResourceId** e **aroResourceLocation** usando os valores do cluster OpenShift, que podem ser encontrados na página de **Propriedades** do cluster selecionado.

    ![Página de propriedades do contêiner](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Salve esse arquivo como **OptOutParam.json** em uma pasta local.

6. Você está pronto para implantar o modelo.

### <a name="remove-the-solution-using-azure-cli"></a>Remover a solução usando a CLI do Azure

Execute o comando a seguir com CLI do Azure no Linux para remover a solução e limpar a configuração no cluster.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Remover a solução usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Execute os seguintes comandos do PowerShell na pasta que contém o modelo para remover a solução e limpar a configuração do cluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A alteração da configuração pode levar alguns minutos para ser concluída. Quando estiver concluído, uma mensagem semelhante à seguinte, incluindo o resultado, será retornada:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Próximas etapas

Se o workspace foi criado apenas para dar suporte ao monitoramento do cluster e não for mais necessário, você terá que excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../../log-analytics/log-analytics-manage-del-workspace.md).
