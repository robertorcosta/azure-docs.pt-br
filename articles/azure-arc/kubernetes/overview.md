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
ms.openlocfilehash: 3960a0f0b6866a0a5d3476e841d29c6e5a89a238
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309927"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>O que é a versão prévia do Kubernetes habilitado para o Azure Arc?

Você pode anexar e configurar clusters do Kubernetes dentro ou fora do Azure com a versão prévia do Kubernetes habilitado para o Azure Arc. Quando um cluster do Kubernetes é anexado ao Azure Arc, ele aparecerá na portal do Azure. Ele terá uma ID do Azure Resource Manager e uma identidade gerenciada. Os clusters são anexados a assinaturas do Azure padrão, estão localizados em um grupo de recursos e podem receber marcas, assim como qualquer outro recurso do Azure. 

Para conectar um cluster do Kubernetes ao Azure, o administrador do cluster precisa implantar agentes. Esses agentes são executados em um namespace do Kubernetes chamado `azure-arc` e são implantações padrão do Kubernetes. Os agentes são responsáveis pela conectividade com o Azure, coletando métricas e logs do Azure Arc e inspecionando as solicitações de configuração. 

O Kubernetes habilitado para o Azure Arc dá suporte a SSL padrão da indústria para proteger dados em trânsito. Além disso, os dados são armazenados criptografados quando em repouso em um banco de dados Azure Cosmos DB para garantir a confidencialidade dos dados.
 
 > [!NOTE]
> O Kubernetes habilitado para o Azure Arc está em versão prévia. Não o recomendamos para carga de trabalho de produção. 


## <a name="supported-scenarios"></a>Cenários com suporte 

O Kubernetes habilitado para o Azure Arc dá suporte esses cenários: 

* Conectar o Kubernetes em execução fora do Azure para inventário, agrupamento e marcação.

* Implantar aplicativos e aplicar a configuração usando o gerenciamento de configuração baseado em GitOps. 

* Usar o Azure Monitor para contêineres para exibir e monitorar seus clusters. 

* Aplicar políticas usando o Azure Policy para Kubernetes. 

 
## <a name="supported-regions"></a>Regiões com suporte 

Atualmente, há suporte para o Kubernetes habilitado para o Azure Arc nessas regiões: 

* Leste dos EUA 
* Europa Ocidental 

## <a name="next-steps"></a>Próximas etapas

* [Conectar um cluster](./connect-cluster.md)
