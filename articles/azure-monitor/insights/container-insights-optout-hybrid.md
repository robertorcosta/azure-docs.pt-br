---
title: Como parar de monitorar o cluster kubernetes híbrido | Microsoft Docs
description: Este artigo descreve como você pode interromper o monitoramento do seu cluster kubernetes híbrido com Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 8369c82b83cfbaa7128383c6203aaf584916cae9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091191"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Como parar de monitorar o cluster híbrido

Depois de habilitar o monitoramento do cluster kubernetes, você poderá parar de monitorar o cluster com Azure Monitor para contêineres se decidir que não deseja mais monitorá-lo. Este artigo mostra como fazer isso para os seguintes ambientes:

- Mecanismo de AKS no Azure e Azure Stack
- OpenShift versão 4 e superior
- Kubernetes habilitado para Azure Arc (versão prévia)

## <a name="how-to-stop-monitoring-using-helm"></a>Como interromper o monitoramento usando o Helm

As etapas a seguir se aplicam aos seguintes ambientes:

- Mecanismo de AKS no Azure e Azure Stack
- OpenShift versão 4 e superior

1. Para identificar primeiro o Azure Monitor para contêineres Helm versão do gráfico instalado no cluster, execute o seguinte comando Helm.

    ```
    helm list
    ```

    A saída será semelhante à seguinte:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *Azmon-containers-Release-1* representa a versão do gráfico de helm para Azure monitor para contêineres.

2. Para excluir a versão do gráfico, execute o comando Helm a seguir.

    `helm delete <releaseName>`

    Exemplo:

    `helm delete azmon-containers-release-1`

    Isso removerá a versão do cluster. Você pode verificar executando o `helm list` comando:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A alteração da configuração pode levar alguns minutos para ser concluída. Como o Helm rastreia suas versões mesmo depois de excluí-las, você pode auditar o histórico de um cluster e até mesmo cancelar a exclusão de uma versão com `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Como interromper o monitoramento em kubernetes habilitados para Arc

### <a name="using-powershell"></a>Usando o PowerShell

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Configure a `$azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `$kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` . Se você quiser usar o contexto atual, defina o valor como `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Execute o comando a seguir para interromper o monitoramento do cluster.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Como usar o Bash

1. Baixe e salve o script em uma pasta local que configura o cluster com o complemento de monitoramento usando os seguintes comandos:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Configure a `azureArcClusterResourceId` variável definindo os valores correspondentes para `subscriptionId` `resourceGroupName` e `clusterName` representando a ID de recurso do seu recurso de cluster kubernetes habilitado para arco do Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Configure a `kubeContext` variável com o **Kube-Context** do cluster executando o comando `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Para interromper o monitoramento do cluster, há comandos diferentes fornecidos com base no seu cenário de implantação.

    Execute o comando a seguir para interromper o monitoramento do cluster usando o contexto atual.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Execute o comando a seguir para interromper o monitoramento do cluster especificando um contexto

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>Próximas etapas

Se o espaço de trabalho Log Analytics foi criado apenas para dar suporte ao monitoramento do cluster e ele não for mais necessário, você precisará excluí-lo manualmente. Se você não estiver familiarizado com como excluir um espaço de trabalho, consulte [excluir um espaço de trabalho do Azure log Analytics](../platform/delete-workspace.md).
