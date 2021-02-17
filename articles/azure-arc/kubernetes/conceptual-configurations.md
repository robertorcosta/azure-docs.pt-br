---
title: Configurações e GitOps-kubernetes habilitado para arco do Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Este artigo fornece uma visão geral conceitual da capacidade de configuração e GitOps do kubernetes habilitado para Arc do Azure.
keywords: Kubernetes, Arc, Azure, contêineres, configuração, GitOps
ms.openlocfilehash: 780c3c5c578c8a9b12eb7dda711070790477ac5f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561304"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Configurações e GitOps com o Azure Arc habilitado kubernetes

Em relação a kubernetes, o GitOps é a prática de declarar o estado desejado de configurações de cluster kubernetes (implantações, namespaces, etc.) em um repositório git. Essa declaração é seguida por uma implantação baseada em sondagem e pull dessas configurações de cluster usando um operador. O repositório git pode conter:
* Manifestos de formato YAML que descrevem quaisquer recursos de kubernetes válidos, incluindo namespaces, ConfigMaps, implantações, DaemonSets, etc.
* Gráficos do Helm para implantação de aplicativos.

O [fluxo](https://docs.fluxcd.io/), uma popular ferramenta de software livre no espaço GitOps, pode ser implantado no cluster kubernetes para facilitar o fluxo de configurações de um repositório Git para um cluster kubernetes. O fluxo dá suporte à implantação de seu operador no cluster e nos escopos de namespace. Um operador de fluxo implantado com escopo de namespace só pode implantar objetos kubernetes dentro desse namespace específico. A capacidade de escolher entre cluster ou escopo de namespace ajuda a obter padrões de implantação multilocatário no mesmo cluster kubernetes.

## <a name="configurations"></a>Configurações

[![Arquitetura ](./media/conceptual-configurations.png) de configurações](./media/conceptual-configurations.png#lightbox)

A conexão entre o cluster e um repositório Git é criada como um `Microsoft.KubernetesConfiguration/sourceControlConfigurations` recurso de extensão na parte superior do recurso kubernetes habilitado para Arc do Azure (representado por `Microsoft.Kubernetes/connectedClusters` ) no Azure Resource Manager. 

As `sourceControlConfiguration` Propriedades de recurso são usadas para implantar o operador de fluxo no cluster com os parâmetros apropriados, como o repositório Git do qual efetuar pull de manifestos e o intervalo de sondagem para recebê-los. Os `sourceControlConfiguration` dados são armazenados criptografados, em repouso, em um Azure Cosmos DB banco de dados, para garantir a confidencialidade do dado.

A `config-agent` execução no seu cluster é responsável por:
* Acompanhando recursos de extensão novos ou atualizados `sourceControlConfiguration` no recurso kubernetes habilitado para Arc do Azure.
* Implantando um operador de fluxo para assistir ao repositório Git para cada um `sourceControlConfiguration` .
* Aplicando qualquer atualização feita a qualquer `sourceControlConfiguration` . 

Você pode criar vários recursos de escopo de namespace `sourceControlConfiguration` no mesmo cluster de kubernetes habilitado para o Azure ARC para obter multilocação.

> [!NOTE]
> * Como os `config-agent` monitores para recursos de extensão novos ou atualizados `sourceControlConfiguration` estão disponíveis no recurso kubernetes habilitado para Arc do Azure, os agentes exigem conectividade para que o estado desejado seja obtido para o cluster. Sempre que os agentes não conseguem se conectar ao Azure, as propriedades de estado desejadas declaradas no `sourceControlConfiguration` recurso no Azure Resource Manager não são aplicadas no cluster.
> * As entradas confidenciais do cliente, como chave privada, conteúdo de hosts conhecidos, nome de usuário HTTPS e token/senha, não são armazenadas por mais de 48 horas nos serviços kubernetes habilitados para o Azure Arc. Se você estiver usando entradas confidenciais para configurações, seja aconselhável colocar os clusters online o mais regularmente possível.

## <a name="apply-configurations-at-scale"></a>Aplicar configurações em escala

Como Azure Resource Manager gerencia suas configurações, você pode usar Azure Policy para automatizar a criação da mesma configuração em todos os recursos de kubernetes habilitados para o Azure Arc dentro do escopo de uma assinatura ou de um grupo de recursos. 

Essa imposição em escala garante que uma configuração de linha de base comum (contendo configurações como ClusterRoleBindings, RoleBindings e NetworkPolicy) possa ser aplicada em toda a frota ou inventário de clusters de kubernetes habilitados para Arc do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster ao arco do Azure](./connect-cluster.md)
* [Criar configurações em seu cluster kubernetes habilitado para Arc](./use-gitops-connected-cluster.md)
* [Usar Azure Policy para aplicar configurações em escala](./use-azure-policy.md)