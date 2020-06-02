---
title: Visão geral do Kubernetes habilitado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665270"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é o Kubernetes habilitado para o Azure Arc (versão prévia)?

Você pode anexar e configurar clusters do Kubernetes dentro ou fora do Azure com o Kubernetes habilitado para o Azure Arc (versão prévia). Quando um cluster do Kubernetes é anexado ao Azure Arc, ele aparece no portal do Azure, tem uma ID de Azure Resource Manager e uma Identidade Gerenciada. Os clusters são anexados a assinaturas do Azure padrão, ficam em um grupo de recursos e podem receber marcas, assim como qualquer outro recurso do Azure. 


Conectar um cluster do Kubernetes ao Azure requer um administrador de cluster para implantar os agentes. Esses agentes são executados em um namespace do Kubernetes chamado `azure-arc` e são implantações padrão do Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, coletando métricas e logs do Azure Arc e inspecionando as solicitações de configuração.  
 
 > [!NOTE]
> O Kubernetes habilitado para o Azure Arc está na versão prévia e não é recomendado para cargas de trabalho de produção. 


## <a name="supported-scenarios"></a>Cenários com suporte 

O Kubernetes habilitado para o Azure Arc dá suporte aos seguintes cenários: 

* Conectar Kubernetes em execução fora do Azure para inventário, agrupamento e marcação 

* Implantar aplicativos e aplicar a configuração usando o gerenciamento de configuração baseado em GitOps 

* Usar o Azure Monitor para contêineres para exibir e monitorar seus clusters 

* Aplicar políticas usando o Azure Policy para Kubernetes 

 
## <a name="supported-regions"></a>Regiões com Suporte 

Atualmente, há suporte para o Kubernetes habilitado para o Azure Arc nas seguintes regiões: 

* Leste dos EUA 
* Europa Ocidental 


## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster ](./connect-cluster.md)
