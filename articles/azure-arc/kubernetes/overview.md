---
title: Visão geral do Kubernetes habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Este artigo fornece uma visão geral do Kubernetes habilitado para o Azure Arc.
keywords: Kubernetes, Arc, Azure, contêineres
ms.custom: references_regions
ms.openlocfilehash: fb8a7b7c2e1e5b3de7d1ccdb4054e44825231458
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604794"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a versão prévia do Kubernetes habilitado para o Azure Arc?

Você pode anexar e configurar clusters do Kubernetes dentro ou fora do Azure usando a versão prévia do Kubernetes habilitado para o Azure Arc. Quando um cluster do Kubernetes é anexado ao Azure Arc, ele aparecerá na portal do Azure. Ele terá uma ID do Azure Resource Manager e uma identidade gerenciada. Os clusters são anexados a assinaturas do Azure padrão, estão localizados em um grupo de recursos e podem receber marcas, assim como qualquer outro recurso do Azure. 

Para conectar um cluster do Kubernetes ao Azure, o administrador do cluster precisa implantar agentes. Esses agentes são executados em um namespace do Kubernetes chamado `azure-arc` e são implantações padrão do Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, coletando métricas e logs do Azure Arc e inspecionando as solicitações de configuração. 

O Kubernetes habilitado para o Azure Arc dá suporte ao SSL padrão do setor para proteger dados em trânsito. Além disso, os dados são armazenados criptografados quando em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.
 
> [!NOTE]
> O Kubernetes habilitado para Azure Arc está em versão prévia. Não o recomendamos para carga de trabalho de produção.

## <a name="supported-kubernetes-distributions"></a>Distribuições do Kubernetes compatíveis

O Kubernetes habilitado para o Azure Arc funciona com qualquer cluster do Kubernetes certificado pela CNCF (Cloud Native Computing Foundation), como o Mecanismo do AKS no Azure, o Mecanismo do AKS no Azure Stack Hub, o GKE, o EKS e o cluster do VMware vSphere.

Os recursos do Kubernetes habilitado para o Azure Arc foram testados pela equipe do Arc nas seguintes distribuições:
* Red Hat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* Mecanismo do AKS
* Mecanismo do AKS no Azure Stack Hub
* Provedor de API de cluster do Azure

## <a name="supported-scenarios"></a>Cenários com suporte 

O Kubernetes habilitado para o Azure Arc dá suporte a estes cenários: 

* Conectar o Kubernetes em execução fora do Azure para inventário, agrupamento e marcação.

* Implantar aplicativos e aplicar a configuração usando o gerenciamento de configuração baseado em GitOps. 

* Usar o Azure Monitor para contêineres para exibir e monitorar seus clusters. 

* Aplicar políticas usando o Azure Policy para Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regiões com suporte 

Atualmente, há suporte para o Kubernetes habilitado para o Azure Arc nestas regiões: 

* Leste dos EUA 
* Europa Ocidental


## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster](./connect-cluster.md)
