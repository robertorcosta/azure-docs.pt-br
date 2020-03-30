---
title: Monitore um cluster Azure Kubernetes Service (AKS) implantado | Microsoft Docs
description: Saiba como ativar o monitoramento de um cluster Azure Kubernetes Service (AKS) com o Azure Monitor para contêineres já implantados em sua assinatura.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275445"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Habilite o monitoramento do cluster Azure Kubernetes Service (AKS) já implantado

Este artigo descreve como configurar o Azure Monitor para contêineres para monitorar o cluster Kubernetes gerenciado hospedado no [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) que já foram implantados em sua assinatura.

Você pode habilitar o monitoramento de um cluster AKS já implantado usando um dos métodos suportados:

* CLI do Azure
* Terraform
* [Do Azure Monitor](#enable-from-azure-monitor-in-the-portal) ou [diretamente do cluster AKS](#enable-directly-from-aks-cluster-in-the-portal) no portal Azure
* Com o [modelo do Azure Resource Manager fornecido](#enable-using-an-azure-resource-manager-template) usando o `New-AzResourceGroupDeployment` cmdlet Do Azure PowerShell ou com o Azure CLI.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Habilitar usando a CLI do Azure

A etapa a seguir permite o monitoramento do cluster do AKS usando a CLI do Azure. Neste exemplo, não é necessário criar ou especificar um workspace existente. Esse comando simplificará o processo, criando um workspace padrão no grupo de recursos padrão da assinatura do cluster do AKS, se ainda não existir um na região.  O workspace padrão criado é semelhante ao formato do *DefaultWorkspace-\<GUID>-\<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A saída será semelhante à seguinte:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integre-se a um espaço de trabalho existente

Se você preferir integrar-se a um espaço de trabalho existente, execute as seguintes etapas para `--workspace-resource-id` primeiro identificar o ID completo de recursos do espaço de trabalho do Log Analytics necessário para o parâmetro e, em seguida, execute o comando para ativar o complemento de monitoramento em relação ao espaço de trabalho especificado.  

1. Liste todas as assinaturas às que você tenha acesso usando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída será semelhante à seguinte:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copie o valor de **SubscriptionId**.

2. Mude para a assinatura que hospeda o espaço de trabalho do Log Analytics usando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir exibe a lista de espaços de trabalho em suas assinaturas no formato JSON padrão.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e copie o ID de recurso completo desse espaço de trabalho do Log Analytics o **id de**campo.

4. Execute o seguinte comando para ativar o complemento de `--workspace-resource-id` monitoramento, substituindo o valor do parâmetro. O valor da seqüência deve estar dentro das cotações duplas:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A saída será semelhante à seguinte:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Habilitar usando Terraform

1. Adicione o perfil de complemento **oms_agent** ao [recurso azurerm_kubernetes_cluster](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) existente

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adicione [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) após as etapas na documentação do Terraform.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Habilitar a partir do Monitor Do Azure no portal

Para habilitar o monitoramento do seu contêiner AKS no portal do Azure por meio do Azure Monitor, faça o seguinte:

1. No portal Azure, selecione **Monitor**.

2. Selecione **Contêineres** na lista.

3. Na página **Monitor – contêineres**, selecione **Clusters não monitorados**.

4. Na lista de clusters não monitorados, localize o contêiner na lista e clique em **Habilitar**.   

5. Na página **Integração do Azure Monitor para contêineres**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  
    A lista seleciona previamente o workspace e o local padrão no qual o contêiner do AKS está implantado na assinatura.

    ![Habilitar o monitoramento de insights do contêiner do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Crie o workspace na mesma assinatura em que o contêiner AKS é implantado.

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Habilite diretamente do cluster AKS no portal

Para habilitar o monitoramento diretamente de um de seus clusters AKS no portal Azure, faça o seguinte:

1. No portal Azure, selecione **Todos os serviços**.

2. Na lista de recursos, comece digitando **Contêineres**.  A lista filtra com base na sua entrada.

3. Selecione **Serviços de Kubernetes**.  

    ![O link de serviços do Kubernetes](./media/container-insights-onboard/portal-search-containers-01.png)

4. Na lista de contêineres, selecione um contêiner.

5. Na página de visão geral do contêiner, selecione **Monitorar contêineres**.  

6. Na página **Integração do Azure Monitor para contêineres**, se você tiver um espaço de trabalho do Log Analytics existente na mesma assinatura do cluster, selecione-o na lista suspensa.  
    A lista seleciona previamente o workspace e o local padrão no qual o contêiner do AKS está implantado na assinatura.

    ![Habilitar o monitoramento de integridade do contêiner do AKS](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Se você quiser criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do cluster, siga as instruções em [Criar um espaço de trabalho do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Crie o workspace na mesma assinatura em que o contêiner AKS é implantado.

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir dados operacionais para o cluster.

## <a name="enable-using-an-azure-resource-manager-template"></a>Habilite o uso de um modelo do Azure Resource Manager

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

* A ID de recurso do contêiner do AKS.
* O grupo de recursos no qual o cluster está implantado.

>[!NOTE]
>O modelo precisa ser implantado no mesmo grupo de recursos que o cluster.
>

O espaço de trabalho do Log Analytics deve ser criado antes de ativar o monitoramento usando o Azure PowerShell ou o CLI. Para criar o workspace, você pode configurá-lo por meio do [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../../azure-monitor/learn/quick-create-workspace.md).

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a versão 2.0.59 do Azure CLI ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Salve esse arquivo como **existingClusterOnboarding.json** em uma pasta local.

3. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Editar os valores para **aksResourceId** e **aksResourceLocation** usando os valores na página **AKS Overview** para o cluster AKS. O valor para **workspaceResourceId** é a ID do recurso completa do espaço de trabalho do Log Analytics, que inclui o nome do espaço de trabalho.

    Editar os valores para **aksResourceTagValues** para corresponder aos valores de tag existentes especificados para o cluster AKS.

5. Salve esse arquivo como **existingClusterParam.json** em uma pasta local.

6. Você está pronto para implantar o modelo.

   * Para implantar com o Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```output
       provisioningState       : Succeeded
       ```

   * Para implantar com o Azure CLI, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```output
       provisioningState       : Succeeded
       ```

       Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e solução

Com a versão do agente *06072018* ou posterior, você pode verificar se tanto o agente quanto a solução foram implantados com êxito. Com versões anteriores do agente, você pode verificar somente a implantação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou posterior

Execute o seguinte comando para verificar se o agente foi implantado com êxito.

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versão do agente anterior a 06072018

Para verificar se a versão do agente do Log Analytics liberada antes de *06072018* está implementada corretamente, execute o seguinte comando:  

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Exibir configuração com CLI

Use o comando `aks show` para obter detalhes, como se a solução estivesse habilitada ou não, o que é o resourceID do espaço de trabalho do Log Analytics e detalhes de resumo sobre o cluster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Após alguns minutos, o comando concluirá e retornará informações no formato JSON sobre a solução.  Os resultados do comando deverão mostrar o perfil do complemento de monitoramento e serem semelhantes à seguinte saída de exemplo:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Próximas etapas

* Se enfrentar problemas ao tentar carregar a solução, examine o [guia de solução de problemas](container-insights-troubleshoot.md)

* Com o monitoramento habilitado para coletar a saúde e a utilização de recursos do seu cluster AKS e cargas de trabalho em execução neles, aprenda [a usar o](container-insights-analyze.md) Monitor Do Azure para contêineres.
