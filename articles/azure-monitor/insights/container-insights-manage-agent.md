---
title: Como gerenciar o agente do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como gerenciar as tarefas de manutenção mais comuns do agente do Log Analytics em contêineres usado pelo Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 5bd3af7787ee38011c52224f5830d8b719031db8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404258"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Como gerenciar o agente do Azure Monitor para contêineres
O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Após a implantação inicial, há tarefas rotineiras ou opcionais que talvez precisem ser executadas durante o ciclo de vida. Este artigo detalha como atualizar o agente manualmente e desabilitar a coleta de variáveis de ambiente de um contêiner específico. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Como atualizar o agente do Azure Monitor para contêineres
O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente é atualizado automaticamente nos clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure).  

Caso a atualização do agente falhe, este artigo descreve o processo para atualizar manualmente o agente. Para seguir as versões lançadas, consulte [comunicados sobre lançamentos de agente](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Agente de upgrade no cluster do Kubernetes monitorado
O processo para atualizar o agente consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com o Azure Monitor para contêineres usando a CLI do Azure.  Siga as etapas descritas no artigo [Desativar monitoramento](container-insights-optout.md?#azure-cli). O uso da CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes armazenados no workspace. 

>[!NOTE]
>Enquanto você está executando esta atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as visualizações de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas em [habilitar monitoramento usando CLI do Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli)para concluir este processo.  

Depois de reativar o monitoramento, pode levar cerca de 15 minutos até visualizar as métricas de integridade atualizadas do cluster. Para verificar se o agente foi atualizado com êxito, execute o comando: `kubectl logs omsagent-484hw --namespace=kube-system`

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Como desabilitar a coleção de variáveis de ambiente em um contêiner
O Azure Monitor para contêineres coleta variáveis ambientais de contêineres em execução em um pod e apresenta-os no painel de propriedades do contêiner selecionado na exibição **Contêineres**. Você pode controlar esse comportamento desabilitando a coleção de um contêiner específico durante a implantação do cluster AKS ou depois definindo a variável de ambiente *AZMON_COLLECT_ENV*. Esse recurso está disponível começando na versão do agente – ciprod11292018 e superior.  

Para desabilitar a coleção de variáveis de ambiente em um contêiner novo ou existente, defina a variável **AZMON_COLLECT_ENV** com um valor **False** em seu arquivo de configuração yaml de implantação do Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Execute o seguinte comando para aplicar a alteração no contêiner do AKS: `kubectl apply -f  <path to yaml file>`.

Para verificar se a alteração de configuração entrou em vigor, selecione um contêiner na exibição **Contêineres** no Azure Monitor para contêineres e, no painel de propriedades, expanda **Variáveis de Ambiente**.  A seção deve mostrar apenas a variável que já foi criada – **AZMON_COLLECT_ENV=FALSE**. Para todos os outros contêineres, a seção Variáveis de Ambiente deverá listar todas as variáveis de ambiente descobertas.   

Para reabilitar a descoberta das variáveis ambientais, aplique o mesmo processo anterior alterando o valor de **False** para **True** e, em seguida, execute novamente o comando `kubectl` para atualizar o contêiner.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Próximos passos
Se você tiver problemas ao atualizar o agente, revise o [guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.
