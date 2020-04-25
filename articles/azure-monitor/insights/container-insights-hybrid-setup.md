---
title: Configurar clusters kubernetes híbridos com Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como você pode configurar Azure Monitor para contêineres para monitorar clusters kubernetes hospedados em Azure Stack ou em outro ambiente.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: c0dbbf9f65aa96db1ebcd0b03552bba8d1f91863
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143165"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Configurar clusters kubernetes híbridos com Azure Monitor para contêineres

Azure Monitor para contêineres fornece experiência de monitoramento avançada para o AKS (serviço kubernetes do Azure) e o [mecanismo do AKS no Azure](https://github.com/Azure/aks-engine), que é um cluster kubernetes autogerenciado hospedado no Azure. Este artigo descreve como habilitar o monitoramento de clusters kubernetes hospedados fora do Azure e obter uma experiência de monitoramento semelhante.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem o seguinte:

* Um espaço de trabalho do Log Analytics.

    Azure Monitor para contêineres dá suporte a um espaço de trabalho Log Analytics nas regiões listadas em produtos do Azure [por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Para criar seu próprio espaço de trabalho, ele pode ser criado por meio de [Azure Resource Manager](../platform/template-workspace-configuration.md), por meio do [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)ou no [portal do Azure](../learn/quick-create-workspace.md).

    >[!NOTE]
    >Não há suporte para a habilitação do monitoramento de vários clusters com o mesmo nome de cluster para o mesmo espaço de trabalho Log Analytics. Os nomes de cluster devem ser exclusivos.
    >

* Você é membro da **função colaborador de log Analytics** para habilitar o monitoramento de contêiner. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, consulte [gerenciar o acesso ao espaço de trabalho e aos dados de log](../platform/manage-access.md)

* [Helm cliente](https://helm.sh/docs/using_helm/) para carregar o Azure monitor para o gráfico de contêineres para o cluster kubernetes especificado.

* As seguintes informações de configuração de proxy e firewall são necessárias para que a versão em contêiner do agente de Log Analytics para Linux se comunique com Azure Monitor:

    |Recurso de agente|Portas |
    |------|---------|   
    |*.ods.opinsights.azure.com |Porta 443 |  
    |*.oms.opinsights.azure.com |Porta 443 |  
    |*.blob.core.windows.net |Porta 443 |  
    |*. dc.services.visualstudio.com |Porta 443 |

* O agente em contêineres requer que `cAdvisor secure port: 10250` o `unsecure port :10255` Kubelet ou seja aberto em todos os nós no cluster para coletar métricas de desempenho. Recomendamos que você `secure port: 10250` configure o no CAdvisor do Kubelet se ele ainda não estiver configurado.

* O agente em contêiner requer que as seguintes variáveis de ambiente sejam especificadas no contêiner para se comunicar com o serviço de API kubernetes no cluster para coletar dados de `KUBERNETES_SERVICE_HOST` inventário e. `KUBERNETES_PORT_443_TCP_PORT`

>[!IMPORTANT]
>A versão mínima do agente com suporte para monitorar clusters híbridos do kubernetes é ciprod10182019 ou posterior.

## <a name="supported-configurations"></a>Configurações com suporte

O seguinte é oficialmente suportado com Azure Monitor para contêineres.

- Ambientes: kubernetes local, AKS Engine no Azure e Azure Stack. Para obter mais informações, consulte [AKs Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- As versões do kubernetes e da política de suporte são as mesmas que as versões do [AKs com suporte](../../aks/supported-kubernetes-versions.md).
- Tempo de execução do contêiner: Docker e Moby
- Versão do sistema operacional Linux para nós mestres e trabalhados: Ubuntu (18, 4 LTS e 16, 4 LTS)
- Suporte ao controle de acesso: kubernetes RBAC e non-RBAC

## <a name="enable-monitoring"></a>Habilitar o monitoramento

A habilitação de Azure Monitor para contêineres para o cluster híbrido kubernetes consiste em executar as etapas a seguir na ordem.

1. Configure seu espaço de trabalho Log Analytics com a solução de informações de contêiner.

2. Habilite a Azure Monitor para contêineres HELM gráfico com Log Analytics espaço de trabalho.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Como adicionar a solução de contêineres de Azure Monitor

Você pode implantar a solução com o modelo de Azure Resource Manager fornecido usando o cmdlet `New-AzResourceGroupDeployment` Azure PowerShell ou com CLI do Azure.

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando o CLI do Azure versão 2.0.59 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Esse método inclui dois modelos JSON. Um modelo especifica a configuração para habilitar o monitoramento e o outro contém valores de parâmetro que você configura para especificar o seguinte:

- **workspaceResourceId** -a ID de recurso completa do seu espaço de trabalho log Analytics.
- **workspaceRegion** -a região em que o espaço de trabalho é criado, que também é conhecido como **local** nas propriedades do espaço de trabalho ao exibir da portal do Azure.

Para primeiro identificar a ID de recurso completo do seu espaço de trabalho de `workspaceResourceId` log Analytics necessário para o valor do parâmetro no arquivo **containerSolutionParams. JSON** , execute as etapas a seguir e execute o cmdlet do PowerShell ou CLI do Azure comando para adicionar a solução.

1. Liste todas as assinaturas às quais você tem acesso usando o seguinte comando:

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

2. Alterne para a assinatura que hospeda o espaço de trabalho Log Analytics usando o seguinte comando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. O exemplo a seguir exibe a lista de espaços de trabalho em suas assinaturas no formato JSON padrão.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Na saída, localize o nome do espaço de trabalho e copie a ID de recurso completo do espaço de trabalho Log Analytics sob a **ID**do campo.

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

5. Salve esse arquivo como containerSolution. JSON em uma pasta local.

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

7. Edite os valores de **workspaceResourceId** usando o valor que você copiou na etapa 3 e, para **workspaceRegion** , copie o valor da **região** depois de executar o comando de CLI do Azure [AZ monitor log-Analytics Workspace show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Salve esse arquivo como containerSolutionParams. JSON em uma pasta local.

9. Você está pronto para implantar o modelo.

   * Para implantar com Azure PowerShell, use os seguintes comandos na pasta que contém o modelo:

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

   * Para implantar com o CLI do Azure, execute os seguintes comandos:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster.

## <a name="install-the-chart"></a>Instalar o gráfico

>[!NOTE]
>Os comandos a seguir são aplicáveis somente para a versão 2 do Helm. O uso do parâmetro--Name não é aplicável ao Helm versão 3.

Para habilitar o gráfico HELM, faça o seguinte:

1. Adicione o repositório de gráficos do Azure à lista local executando o seguinte comando:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Instale o gráfico executando o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no Azure China, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se o espaço de trabalho Log Analytics estiver no governo dos EUA do Azure, execute o seguinte comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Configurar coleta de dados do agente

Em estrela com a versão 1.0.0 do gráfico, as configurações de coleta de dados do agente são controladas por meio do ConfigMap. Consulte a documentação sobre as configurações de coleta de dados do agente [aqui](container-insights-agent-config.md).

Depois de ter implantado com êxito o gráfico, você pode examinar os dados para o cluster kubernetes híbrido em Azure Monitor para contêineres do portal do Azure.  

>[!NOTE]
>A latência de ingestão é de cerca de cinco a dez minutos do agente para ser confirmada no espaço de trabalho Log Analytics do Azure. O status do cluster mostra o valor **sem dados** ou **desconhecido** até que todos os dados de monitoramento necessários estejam disponíveis no Azure monitor.

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um erro ao tentar habilitar o monitoramento para o cluster kubernetes híbrido, copie o script do PowerShell [TroubleshootError_nonAzureK8s. ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) e salve-o em uma pasta no computador. Esse script é fornecido para ajudar a detectar e corrigir os problemas encontrados. Os problemas projetados para detectar e tentar a correção do são os seguintes:

* O espaço de trabalho de Log Analytics especificado é válido
* O espaço de trabalho Log Analytics é configurado com a Azure Monitor para a solução de contêineres. Caso contrário, configure o espaço de trabalho.
* Os pods OmsAgent réplicaset estão em execução
* Os pods OmsAgent daemonset estão em execução
* O serviço de integridade do OmsAgent está em execução
* A ID e a chave do espaço de trabalho Log Analytics configuradas no agente em contêiner correspondem ao espaço de trabalho com o qual a percepção está configurada.
* Valide se todos os nós de trabalho `kubernetes.io/role=agent` do Linux têm o rótulo para agendar o Pod RS. Se ele não existir, adicione-o.
* Valide `cAdvisor secure port:10250` ou `unsecure port: 10255` é aberto em todos os nós no cluster.

Para executar com Azure PowerShell, use os seguintes comandos na pasta que contém o script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para coletar a utilização de recursos e de integridade do seu cluster kubernetes híbrido e cargas de trabalho em execução neles, saiba [como usar](container-insights-analyze.md) Azure monitor para contêineres.
