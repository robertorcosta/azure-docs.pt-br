---
title: Como gerenciar o agente do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como gerenciar as tarefas de manutenção mais comuns do agente do Log Analytics em contêineres usado pelo Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: ca0fa88cf27db15d45a2c855a1af351764c48fde
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887512"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerenciar o agente do Azure Monitor para contêineres

O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Após a implantação inicial, há tarefas rotineiras ou opcionais que talvez precisem ser executadas durante o ciclo de vida. Este artigo detalha como atualizar o agente manualmente e desabilitar a coleta de variáveis de ambiente de um contêiner específico. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o agente do Azure Monitor para contêineres

O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente é atualizado automaticamente nos clusters do Kubernetes gerenciado hospedado no AKS (Serviço de Kubernetes do Azure) e no Red Hat OpenShift no Azure versão 3.x. Para um [cluster híbrido do Kubernetes](container-insights-hybrid-setup.md) e o Red Hat OpenShift no Azure versão 4. x, o agente não é gerenciado e você precisa atualizá-lo manualmente.

Se a atualização do agente falhar para um cluster hospedado no AKS ou no Red Hat OpenShift no Azure versão 3. x, este artigo também descreverá o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>Atualizar o agente no cluster do AKS

O processo para atualizar o agente nos clusters do AKS consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com o Azure Monitor para contêineres usando a CLI do Azure. Siga as etapas descritas no artigo [Desativar monitoramento](container-insights-optout.md?#azure-cli). O uso da CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes armazenados no workspace. 

>[!NOTE]
>Enquanto você está executando esta atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as visualizações de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas em [habilitar o monitoramento usando a CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli) para concluir esse processo.  

Depois de reabilitar o monitoramento, poderá levar cerca de 15 minutos até ser possível exibir as métricas de integridade atualizadas do cluster. Para verificar se o agente foi atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

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

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Atualizar o agente no cluster híbrido do Kubernetes

Execute as seguintes etapas para atualizar o agente em um cluster do Kubernetes em execução em:

* Clusters do Kubernetes autogerenciado hospedado no Azure usando o mecanismo do AKS.
* Clusters do Kubernetes autogerenciado hospedado no Azure Stack ou localmente usando o Mecanismo do AKS.
* Red Hat OpenShift versão 4.x.

Se o workspace do Log Analytics estiver no Azure comercial, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Se o espaço de trabalho Log Analytics estiver na 21Vianet do Azure na China, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Se o workspace do Log Analytics estiver no Azure US Government, execute o seguinte comando:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Atualizar o agente no Red Hat OpenShift no Azure v4

Execute as seguintes etapas para atualizar o agente em um cluster do Kubernetes em execução no Red Hat OpenShift no Azure versão 4.x. 

>[!NOTE]
>O Red Hat OpenShift no Azure versão 4.x dá suporte apenas à execução na nuvem comercial do Azure.
>

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<azureAroV4ResourceId> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Atualizar o agente no Azure Arc habilitado kubernetes

Execute o comando a seguir para atualizar o agente em um cluster kubernetes habilitado para o Azure Arc sem um ponto de extremidade proxy.

```
$ helm upgrade --install azmon-containers-release-1  –set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<resourceIdOfAzureArcK8sCluster>
```

Execute o comando a seguir para atualizar o agente quando um ponto de extremidade de proxy for especificado. Para obter mais informações sobre o ponto de extremidade de proxy, consulte [Configurar ponto de extremidade de proxy](container-insights-enable-arc-enabled-clusters.md#configure-proxy-endpoint).

```
helm upgrade –name azmon-containers-release-1 –set omsagent.proxy=<proxyEndpoint>,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterId=<resourceIdOfAzureArcK8sCluster>
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desabilitar a coleção de variáveis de ambiente em um contêiner

O Azure Monitor para contêineres coleta variáveis ambientais de contêineres em execução em um pod e apresenta-os no painel de propriedades do contêiner selecionado na exibição **Contêineres**. Você pode controlar esse comportamento desabilitando a coleção de um contêiner específico durante a implantação do cluster do Kubernetes ou depois definindo a variável de ambiente *AZMON_COLLECT_ENV*. Esse recurso está disponível começando na versão do agente – ciprod11292018 e superior.  

Para desabilitar a coleção de variáveis de ambiente em um contêiner novo ou existente, defina a variável **AZMON_COLLECT_ENV** com um valor **False** em seu arquivo de configuração yaml de implantação do Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o seguinte comando para aplicar a alteração em clusters do Kubernetes diferentes do Red Hat OpenShift no Azure: `kubectl apply -f  <path to yaml file>`. Para editar ConfigMap e aplicar essa alteração para os clusters do Red Hat OpenShift no Azure, execute o comando:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Isso abre o editor de texto padrão. Depois de definir a variável, salve o arquivo no editor.

Para verificar se a alteração de configuração entrou em vigor, selecione um contêiner na exibição **Contêineres** no Azure Monitor para contêineres e, no painel de propriedades, expanda **Variáveis de Ambiente**.  A seção deve mostrar apenas a variável que já foi criada – **AZMON_COLLECT_ENV=FALSE**. Para todos os outros contêineres, a seção Variáveis de Ambiente deverá listar todas as variáveis de ambiente descobertas.

Para reabilitar a descoberta das variáveis ambientais, aplique o mesmo processo anterior alterando o valor de **False** para **True** e, em seguida, execute novamente o comando `kubectl` para atualizar o contêiner.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Próximas etapas

Se você tiver problemas ao atualizar o agente, revise o [guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.
