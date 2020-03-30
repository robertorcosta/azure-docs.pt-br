---
title: Visão geral do Insights no Azure Monitor | Microsoft Docs
description: Os insights fornecem uma experiência de monitoramento personalizada no Azure Monitor para determinados aplicativos e serviços. Este artigo fornece uma breve descrição de cada um dos insights que estão disponíveis atualmente.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657241"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Visão geral do Insights no Azure Monitor
Os insights fornecem uma experiência de monitoramento personalizada para determinados aplicativos e serviços. Eles armazenam dados na [plataforma de dados do Azure Monitor](../platform/data-platform.md) e aproveitam outros recursos do Azure Monitor para análise e alerta, mas podem coletar dados adicionais e fornecer uma experiência única do usuário no portal Azure. Acesse insights da seção **Insights** do menu Do Monitor do Azure no portal Azure.

As seções a seguir fornecem uma breve descrição dos insights que estão atualmente disponíveis no Azure Monitor. Consulte a documentação detalhada para obter detalhes sobre cada um.

## <a name="application-insights"></a>Application Insights
O Application Insights é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele funciona para aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedados no local, híbridos ou qualquer nuvem pública. Ele também se integra ao seu processo DevOps e tem pontos de conexão para uma variedade de ferramentas de desenvolvimento.

Veja [o que é o Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor para Contêineres
O Azure Monitor para contêineres monitora o desempenho das cargas de trabalho de contêineres implantadas nas Instâncias de Contêiner do Azure ou nos clusters Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

Consulte [o Monitor Azure para ver a visão geral dos contêineres](../insights/container-insights-overview.md).

![Azure Monitor para Contêineres](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Monitor do Azure para Grupos de Recursos (visualização)
O Azure Monitor para grupos de recursos ajuda a triagem e diagnóstico de quaisquer problemas que seus recursos individuais encontrem, ao mesmo tempo em que oferece contexto quanto à saúde e desempenho do grupo de recursos como um todo.

Consulte [Grupos de recursos do Monitor com o Azure Monitor (visualização)](../insights/resource-group-insights.md).

![Monitor do Azure para grupos de recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Monitor Azure para VMs (visualização)
O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos.

Veja [o que é o Monitor Azure para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Monitor de Redes Do Azure (visualização)
[O Azure Monitor for Networks](network-insights-overview.md) fornece uma visão abrangente da saúde e métricas para todos os recursos da sua rede. O recurso avançado de pesquisa ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site, simplesmente pesquisando o nome do seu site.

![Azure Monitor para Redes](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [plataforma de dados do Azure Monitor](../platform/data-platform.md) alavancada por insights.
* Conheça as [diferentes fontes de dados usadas pelo Azure Monitor](../platform/data-sources.md) e os diferentes tipos de dados coletados por cada um dos insights.
