---
title: Como gerenciar o agente do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como gerenciar as tarefas de manutenção mais comuns do agente do Log Analytics em contêineres usado pelo Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275315"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerenciar o agente do Azure Monitor para contêineres

O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Após a implantação inicial, há tarefas rotineiras ou opcionais que talvez precisem ser executadas durante o ciclo de vida. Este artigo detalha como atualizar o agente manualmente e desabilitar a coleta de variáveis de ambiente de um contêiner específico. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o agente do Azure Monitor para contêineres

O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente é automaticamente atualizado em seus clusters Kubernetes gerenciados hospedados no Azure Kubernetes Service (AKS) e no Azure Red Hat OpenShift. Para um [cluster Kubernetes híbrido,](container-insights-hybrid-setup.md) o agente não é gerenciado, e você precisa atualizar manualmente o agente.

Se a atualização do agente falhar para um cluster hospedado no AKS, este artigo também descreverá o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Agente de atualização no cluster Kubernetes monitorado

O processo de atualização do agente em clusters, além do Azure Red Hat OpenShift, consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com o Azure Monitor para contêineres usando a CLI do Azure. Siga as etapas descritas no artigo [Desativar monitoramento](container-insights-optout.md?#azure-cli). O uso da CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes armazenados no workspace. 

>[!NOTE]
>Enquanto você está executando esta atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as visualizações de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas no [monitoramento de habilitação usando o Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli), para concluir esse processo.  

Depois de reativar o monitoramento, pode levar cerca de 15 minutos até que você possa visualizar métricas de saúde atualizadas para o cluster. Para verificar se o agente foi atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

O status deve se parecer com o exemplo a seguir, em que os valores de *omi* e de *omsagent* devem corresponder à versão mais recente especificada no [histórico de versões do agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Agente de atualização no cluster Kubernetes híbrido

O processo de atualização do agente em um cluster Kubernetes hospedado no local, o AKS Engine no Azure e o Azure Stack pode ser concluído executando o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Se o espaço de trabalho do Log Analytics estiver no Azure China, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se o espaço de trabalho do Log Analytics estiver no Governo dos EUA do Azure, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desabilitar a coleção de variáveis de ambiente em um contêiner

O Azure Monitor para contêineres coleta variáveis ambientais de contêineres em execução em um pod e apresenta-os no painel de propriedades do contêiner selecionado na exibição **Contêineres**. Você pode controlar esse comportamento desativando a coleta de um contêiner específico durante a implantação do cluster Kubernetes ou depois definindo a variável ambiente *AZMON_COLLECT_ENV*. Esse recurso está disponível começando na versão do agente – ciprod11292018 e superior.  

Para desabilitar a coleção de variáveis de ambiente em um contêiner novo ou existente, defina a variável **AZMON_COLLECT_ENV** com um valor **False** em seu arquivo de configuração yaml de implantação do Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o seguinte comando para aplicar a alteração em clusters Kubernetes `kubectl apply -f  <path to yaml file>`diferentes do Azure Red Hat OpenShift): . Para editar ConfigMap e aplicar esta alteração para clusters Azure Red Hat OpenShift, execute o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Isso abre seu editor de texto padrão. Depois de definir a variável, salve o arquivo no editor.

Para verificar se a alteração de configuração entrou em vigor, selecione um contêiner na exibição **Contêineres** no Azure Monitor para contêineres e, no painel de propriedades, expanda **Variáveis de Ambiente**.  A seção deve mostrar apenas a variável que já foi criada – **AZMON_COLLECT_ENV=FALSE**. Para todos os outros contêineres, a seção Variáveis de Ambiente deverá listar todas as variáveis de ambiente descobertas.

Para reabilitar a descoberta das variáveis ambientais, aplique o mesmo processo anterior alterando o valor de **False** para **True** e, em seguida, execute novamente o comando `kubectl` para atualizar o contêiner.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas ao atualizar o agente, revise o [guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.
