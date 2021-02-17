---
title: Perguntas frequentes de kubernetes habilitadas para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo contém uma lista de perguntas frequentes relacionadas ao kubernetes habilitado para Arc do Azure
keywords: Kubernetes, Arc, Azure, contêineres, configuração, GitOps, perguntas frequentes
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561310"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Perguntas frequentes-kubernetes habilitado para o Azure Arc

Este artigo aborda as perguntas frequentes sobre o kubernetes habilitado para o Azure Arc.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Qual é a diferença entre o Kubernetes habilitado para o Azure Arc e o AKS (Serviço Kubernetes do Azure)?

AKS é a oferta de kubernetes gerenciada pelo Azure. O AKS simplifica a implantação de um cluster kubernetes gerenciado no Azure, descarregando grande parte da complexidade e sobrecarga operacional no Azure. Como os mestres do Kubernetes são gerenciados pelo Azure, você só gerencia e mantém os nós do agente.

O kubernetes habilitado para Arc do Azure permite que você estenda os recursos de gerenciamento do Azure (como Azure Monitor e Azure Policy) conectando clusters kubernetes ao Azure. Você mantém o próprio cluster kubernetes subjacente.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>É necessário conectar meus clusters do AKS em execução no Azure no Azure Arc?

Não. Todos os recursos de kubernetes habilitados para o Azure Arc, incluindo Azure Monitor e Azure Policy (gatekeeper), estão disponíveis no AKS (um recurso nativo no Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Devo conectar meu cluster AKS-HCI e clusters kubernetes no Hub Azure Stack e Azure Stack borda ao Azure Arc?

Sim, conectar seu cluster AKS-HCI ou clusters kubernetes no Azure Stack Edge ou Azure Stack Hub ao Arc do Azure fornece clusters com representação de recursos no Azure Resource Manager. Essa representação de recursos estende os recursos como configuração de cluster, Azure Monitor e Azure Policy (gatekeeper) para os clusters kubernetes que você se conecta.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Como lidar com os recursos de kubernetes habilitados para o Arc do Azure com vencimento?

O certificado MSI (identidade de serviço gerenciado) associado à sua Kuberenetes habilitada para o Arc do Azure tem uma janela de expiração de 90 dias. Quando esse certificado expira, o recurso é considerado `Expired` e todos os recursos, como configuração, monitoramento e política, param de funcionar nesse cluster. Siga estas etapas para fazer com que seu cluster kubernetes funcione com o arco do Azure novamente:

1. Excluir recurso kubernetes e agentes habilitados para o Azure Arc no cluster 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Recrie o recurso kubernetes habilitado para arco do Azure implantando agentes no cluster novamente.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` também excluirá as configurações na parte superior do cluster. Depois de executar `az connectedk8s connect` o, crie as configurações no cluster novamente, manualmente ou usando o Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Se já estiver usando pipelines de CI/CD, ainda poderei usar as configurações e kubernetes habilitadas para o Arc do Azure?

Sim, você ainda pode usar configurações em um cluster que recebe implantações por meio de um pipeline de CI/CD. Em comparação com os pipelines de CI/CD tradicionais, as configurações apresentam dois benefícios adicionais:

**Reconciliação descompasso**

O pipeline de CI/CD aplica as alterações apenas uma vez durante a execução do pipeline. No entanto, o operador GitOps no cluster sonda continuamente o repositório Git para buscar o estado desejado dos recursos de kubernetes no cluster. Se o operador GitOps encontrar o estado desejado dos recursos para ser diferente do estado real dos recursos no cluster, essa descompasso será reconciliada.

**Aplicar GitOps em escala**

Pipelines de CI/CD são bons para implantações controladas por eventos em seu cluster kubernetes, em que o evento pode ser um envio por push para um repositório git. No entanto, a implantação da mesma configuração em todos os seus clusters kubernetes requer que o pipeline de CI/CD seja configurado com credenciais de cada um desses clusters kubernetes manualmente. Por outro lado, no caso do Azure Arc habilitado kubernetes, como Azure Resource Manager gerencia suas configurações, você pode usar Azure Policy para automatizar a aplicação da configuração desejada em todos os clusters do kubernetes em um escopo de assinatura ou grupo de recursos em um só lugar. Esse recurso é ainda aplicável aos recursos de kubernetes habilitados para Arc do Azure criados após a atribuição de política.

O recurso de configurações é usado para aplicar configurações de linha de base como diretivas de rede, associações de função e políticas de segurança de pod em todo o inventário de clusters kubernetes para requisitos de conformidade e governança.

## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster ao arco do Azure](./connect-cluster.md)
* [Criar configurações em seu cluster kubernetes habilitado para Arc](./use-gitops-connected-cluster.md)
* [Usar Azure Policy para aplicar configurações em escala](./use-azure-policy.md)
