---
title: (PRETERIDO) Introdução ao Serviço de Contêiner do Azure para Kubernetes
description: O Serviço de Contêiner do Azure para Kubernetes simplifica a implantação e o gerenciamento de aplicativos baseados em contêiner no Azure.
author: gabrtv
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: c0ef7255a087dd5dc26532316deab337f9eff715
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271581"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(PRETERIDO) Introdução ao Serviço de Contêiner do Azure para Kubernetes

> [!TIP]
> Para a versão atualizada deste artigo que usa o Serviço de Kubernetes do Azure, confira [Visão geral do AKS (Serviço de Kubernetes do Azure)](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

O Serviço de Contêiner do Azure para Kubernetes simplifica a criação, a configuração e o gerenciamento de um cluster de máquinas virtuais pré-configuradas para executar os aplicativos em contêineres. Isso permite que você use suas habilidades existentes ou explore uma experiência cada vez maior da comunidade para implantar e gerenciar aplicativos baseados em contêiner no Microsoft Azure.

Usando o Serviço de Contêiner do Azure, você pode aproveitar as vantagens dos recursos de nível empresarial do Azure e ainda manter a portabilidade do aplicativo pelo formato de imagem do Docker e Kubernetes.

## <a name="using-azure-container-service-for-kubernetes"></a>Usando o Serviço de Contêiner do Azure para Kubernetes
Nosso objetivo com o Serviço de Contêiner do Azure é fornecer um ambiente de hospedagem de contêineres usando ferramentas e tecnologias de código-fonte aberto, que são comuns entre os nossos clientes hoje. Para esse fim, vamos expor os pontos de extremidade da API do Kubernetes padrão. Usando esses pontos de extremidade padrão, é possível utilizar qualquer software que possa se comunicar com um cluster Kubernetes. Por exemplo, você pode escolher [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/), ou [draft](https://github.com/Azure/draft).

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Criando um Cluster Kubernetes usando o Serviço de Contêiner do Azure
Para começar a usar o Serviço de Contêiner do Azure, implante um cluster do Serviço de Contêiner do Azure com a [CLI do Azure](container-service-kubernetes-walkthrough.md) ou por meio do portal (pesquise **Serviço de Contêiner do Azure** no Marketplace). Se você for um usuário avançado que precisa de mais controle sobre os modelos do Azure Resource Manager, poderá usar o projeto [acs-engine](https://github.com/Azure/acs-engine) de software livre para criar seu próprio cluster Kubernetes personalizado e implantá-lo por meio da CLI `az`.

### <a name="using-kubernetes"></a>Como usar Kubernetes
O Kubernetes automatiza a implantação, o dimensionamento e o gerenciamento de aplicativos em contêineres. Ele tem um conjunto avançado de recursos, incluindo:
* Binpacking automático
* Autorrecuperação
* Dimensionamento em escala horizontal
* Descoberta de serviço e balanceamento de carga
* Reversões e distribuições automatizadas
* Segredos e gerenciamento de configuração
* Orquestração de armazenamento
* Execução do Lote

Diagrama da arquitetura de Kubernetes implantado por meio do Serviço de Contêiner do Azure:

![Serviço de Contêiner do Azure configurado para usar o Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>vídeos

Suporte a Kubernetes nos Serviços de Contêiner do Azure (Azure Friday, janeiro de 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Ferramentas para desenvolver e implantar aplicativos no Kubernetes (Azure OpenDev, junho de 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Próximas etapas

Explorar o [Início rápido de Kubernetes](container-service-kubernetes-walkthrough.md) para começar a explorar o Serviço de Contêiner do Azure hoje.
