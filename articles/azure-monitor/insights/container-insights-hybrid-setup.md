---
title: Configure clusters Hybrid Kubernetes com Monitor Azure para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar o Azure Monitor para contêineres para monitorar os clusters Kubernetes hospedados no Azure Stack ou em outro ambiente.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198047"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configure clusters kubernetes híbridos com monitor azure para contêineres

O Azure Monitor para contêineres oferece uma rica experiência de monitoramento para o Azure Kubernetes Service (AKS) e [o AKS Engine no Azure](https://github.com/Azure/aks-engine), que é um cluster Kubernetes auto-gerenciado hospedado no Azure. Este artigo descreve como permitir o monitoramento de clusters Kubernetes hospedados fora do Azure e obter uma experiência de monitoramento semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

* Um espaço de trabalho do Log Analytics.

    O Azure Monitor para contêineres suporta um espaço de trabalho log analytics nas regiões listadas em Produtos Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado através [do Azure Resource Manager](../platform/template-workspace-configuration.md), através do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Não é suportado o monitoramento de vários clusters com o mesmo nome de cluster para o mesmo espaço de trabalho do Log Analytics. Os nomes de cluster devem ser únicos.
    >

* Você é um membro da função de contribuinte do Log Analytics para permitir o monitoramento de **contêineres.** Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [Gerenciar acesso a dados de espaço de trabalho e registro](../platform/manage-access.md)

* [Cliente HELM](https://helm.sh/docs/using_helm/) a bordo do gráfico Azure Monitor para contêineres para o cluster Kubernetes especificado.

* As seguintes informações de configuração de proxy e firewall são necessárias para que a versão contêiner do agente Log Analytics para Linux se comunique com o Azure Monitor:

    |Recurso de agente|Portas |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |*.dc.services.visualstudio.com |Porta 443 |

* O agente contêiner requer `cAdvisor secure port: 10250` kubelet `unsecure port :10255` ou ser aberto em todos os nós no cluster para coletar métricas de desempenho. Recomendamos que `secure port: 10250` você configure no cAdvisor do Kubelet se ele ainda não estiver configurado.

* O agente contêiner requer que as seguintes variáveis ambientais sejam especificadas no contêiner para se comunicar com o `KUBERNETES_SERVICE_HOST` serviço `KUBERNETES_PORT_443_TCP_PORT`de API Kubernetes dentro do cluster para coletar dados de inventário - e .

>[!IMPORTANT]
>A versão mínima do agente suportada para monitorar clusters Kubernetes híbridos é ciprod10182019 ou posterior.

## <a name="supported-configurations"></a>Configurações com suporte

O seguinte é oficialmente suportado com o Azure Monitor para contêineres.

- Ambientes: Kubernetes no local, Motor AKS no Azure e Azure Stack. Para obter mais informações, consulte [AKS Engine no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Versões do Kubernetes e política de suporte são as mesmas que versões do [AKS suportado](../../aks/supported-kubernetes-versions.md).
- Tempo de execução do contêiner: Docker e Moby
- Versão do Sistema Operacional Linux para nós mestres e trabalhados: Ubuntu (18.04 LTS e 16.04 LTS)
- Controle de acesso suportado: Kubernetes RBAC e não-RBAC

## <a name="enable-monitoring"></a>Habilitar o monitoramento

Habilitar o Monitor Azure para contêineres para o cluster Kubernetes híbrido consiste em executar as seguintes etapas em ordem.

1. Configure seu espaço de trabalho log analytics com a solução Container Insights.

2. Habilite o Monitor Azure para o gráfico HELM de contêineres com espaço de trabalho log analytics.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução Azure Monitor Containers

Você pode implantar a solução com o modelo do Azure Resource Manager, `New-AzResourceGroupDeployment` usando o cmdlet Azure PowerShell ou com o Azure CLI.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a versão 2.0.59 do Azure CLI ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- **workspaceResourceId** - o ID completo de recursos do seu espaço de trabalho log analytics.
- **espaço de trabalhoRegião** - a região em que o espaço de trabalho é criado, que também é referido como **Localização** nas propriedades do espaço de trabalho ao visualizar a partir do portal Azure.

Para primeiro identificar o ID completo de recursos do `workspaceResourceId` espaço de trabalho do Log Analytics necessário para o valor do parâmetro no arquivo **containerSolutionParams.json,** execute as etapas a seguir e execute o comando PowerShell cmdlet ou Azure CLI para adicionar a solução.

1. Liste todas as assinaturas às que você tenha acesso usando o seguinte comando:

    ```azurecli
    az account list --all -o table
    ```

    A saída será semelhante à seguinte:

    ```azurecli
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

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, encontre o nome do espaço de trabalho e copie o ID de recurso completo desse espaço de trabalho do Log Analytics o **ID de**campo.

4. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Salve este arquivo como containerSolution.json em uma pasta local.

6. Cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Edite os valores para **o espaço de trabalhoResourceId** usando o valor copiado na etapa 3 e para **o espaço de trabalhoRegião** copie o valor **da Região** depois de executar o monitor de espaço de [trabalho az monitor log-analytics](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)do azure

8. Salve este arquivo como containerSolutionParams.json em uma pasta local.

9. Você está pronto para implantar o modelo.

   * Para implantar com o Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Para implantar com o Azure CLI, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

## <a name="install-the-chart"></a>Instale o gráfico

Para habilitar o gráfico HELM, faça o seguinte:

1. Adicione o repositório de gráficos do Azure à sua lista local executando o seguinte comando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho do Log Analytics estiver no Azure China, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho do Log Analytics estiver no Governo dos EUA do Azure, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurar a coleta de dados do agente

Olhando para a versão do gráfico 1.0.0, as configurações de coleta de dados do agente são controladas a partir do ConfigMap. Consulte a documentação sobre as configurações de coleta de dados do agente [aqui](container-insights-agent-config.md).

Depois de ter implantado com sucesso o gráfico, você pode revisar os dados do seu cluster Kubernetes híbrido no Azure Monitor para contêineres do portal Azure.  

>[!NOTE]
>A latência de ingestão é de cerca de cinco a dez minutos do agente para se comprometer no espaço de trabalho do Azure Log Analytics. O status do cluster mostrará o valor **Nenhum dado** ou **Desconhecido** até que todos os dados de monitoramento necessários estejam disponíveis no Azure Monitor.

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um erro ao tentar ativar o monitoramento do cluster Kubernetes híbrido, copie o script do PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e salve-o em uma pasta no computador. Este script é fornecido para ajudar a detectar e corrigir os problemas encontrados. Os problemas que ele foi projetado para detectar e tentar a correção são os seguintes:

* O espaço de trabalho do Log Analytics especificado é válido
* O espaço de trabalho log analytics é configurado com a solução Azure Monitor for Containers. Caso assim, configure o espaço de trabalho.
* As cápsulas de replicaset do OmsAgent estão sendo executados
* As cápsulas de daemonset do OmsAgent estão sendo executados
* O serviço de saúde OmsAgent está em execução
* O ID do espaço de trabalho do Log Analytics e a chave configurada no agente contêiner combinam com o espaço de trabalho com o qual o Insight está configurado.
* Valide todos os nós `kubernetes.io/role=agent` do trabalhador Linux têm rótulo para agendar rs pod. Se não existe, adicione-o.
* `cAdvisor secure port:10250` Valide `unsecure port: 10255` ou é aberto em todos os nós do cluster.

Para executar com o Azure PowerShell, use os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para coletar a saúde e a utilização de recursos do seu cluster Kubernetes híbrido e cargas de trabalho em execução neles, aprenda [a usar o](container-insights-analyze.md) Monitor Azure para contêineres.
