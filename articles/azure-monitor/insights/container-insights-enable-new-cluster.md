---
title: Monitore um novo cluster Azure Kubernetes Service (AKS) | Microsoft Docs
description: Saiba como ativar o monitoramento de um novo cluster Azure Kubernetes Service (AKS) com o Azure Monitor para assinatura de contêineres.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275432"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Habilite o monitoramento de um novo cluster Azure Kubernetes Service (AKS)

Este artigo descreve como configurar o Azure Monitor para contêineres para monitorar o cluster Kubernetes gerenciado hospedado no [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) que você está preparando para implantar em sua assinatura.

Você pode habilitar o monitoramento de um cluster AKS usando um dos métodos suportados:

* CLI do Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Habilitar usando a CLI do Azure

Para habilitar o monitoramento de um novo cluster do AKS criado com a CLI do Azure, siga a etapa no artigo de início rápido na seção [Criar cluster do AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a versão 2.0.74 do Azure CLI ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Se você tiver instalado a versão CLI de visualização aks 0.4.12 ou superior, remova quaisquer alterações feitas para habilitar uma extensão de visualização, pois ela pode substituir o comportamento padrão do Azure CLI, uma vez que os recursos do AKS Preview não estão disponíveis na nuvem DoAzure US Governmnet.

## <a name="enable-using-terraform"></a>Habilitar usando Terraform

Se estiver [implantando um novo cluster do AKS usando o Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), especifique os argumentos necessários no perfil [para criar um espaço de trabalho do Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) se não optar por especificar um existente. 

>[!NOTE]
>Se optar por usar o Terraform, você deverá estar executando o Provedor de RM do Terraform do Azure versão 1.17.0 ou superior.

Para adicionar o Azure Monitor para contêineres ao workspace, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) e preencha o perfil incluindo o [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) e especifique **oms_agent**. 

Depois de habilitar o monitoramento e todas as tarefas de configuração terem sido concluídas com sucesso, você poderá monitorar o desempenho do cluster de uma destas duas maneiras:

* Diretamente no cluster do AKS selecionando **Integridade** no painel esquerdo.
* Ao selecionar o bloco **Monitorar insights do contêiner** na página de cluster do AKS do cluster selecionado. No Azure Monitor, no painel esquerdo, selecione **Integridade**. 

  ![Opções para selecionar o Azure Monitor para contêineres no AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Depois de habilitar o monitoramento, poderão ser necessários cerca de 15 minutos antes de exibir as métricas de integridade para o cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Verificar a implantação do agente e solução
Com a versão do agente *06072018* ou posterior, você pode verificar se tanto o agente quanto a solução foram implantados com êxito. Com versões anteriores do agente, você pode verificar somente a implantação do agente.

### <a name="agent-version-06072018-or-later"></a>Agente versão 06072018 ou posterior
Execute o seguinte comando para verificar se o agente foi implantado com êxito. 

```
kubectl get ds omsagent --namespace=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para verificar a implantação da solução, execute o seguinte comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A saída deve ser semelhante à seguinte, que indica que ela foi implantada corretamente:

```
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

```
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

```
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
