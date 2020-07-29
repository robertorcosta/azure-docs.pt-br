---
title: Visão geral do Insights no Azure Monitor | Microsoft Docs
description: O Insights fornece uma experiência de monitoramento personalizada no Azure Monitor para aplicativos e serviços específicos. Este artigo fornece uma breve descrição de cada um dos insights disponíveis no momento.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326861"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Visão Geral do Insights no Azure Monitor
O Insights fornece uma experiência de monitoramento personalizada para aplicativos e serviços específicos. Eles armazenam dados na [plataforma de dados do Azure Monitor](../platform/data-platform.md) e usam outros recursos do Azure Monitor para análise e alerta, mas podem coletar dados adicionais e fornecer uma experiência de usuário exclusiva no portal do Azure. Acesse insights na seção **Insights** do menu do Azure Monitor no portal do Azure.


As seções a seguir fornecem uma breve descrição dos insights disponíveis no momento no Azure Monitor. Confira a documentação detalhada para obter detalhes sobre cada um.

## <a name="application-insights"></a>Application Insights
O Application Insights é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele funciona com aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node.js e Java EE, hospedadas localmente, de maneira híbrida ou em qualquer nuvem pública. Ele também integra-se ao seu processo DevOps e tem pontos de conexão para uma ampla variedade de ferramentas de desenvolvimento.

Confira [O que é o Application Insights?](../app/app-insights-overview.md).

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor para Contêineres
O Azure Monitor para contêineres monitora o desempenho das cargas de trabalho de contêineres implantadas nas Instâncias de Contêiner do Azure ou nos clusters Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

Confira [Visão geral do Azure Monitor para contêineres](./container-insights-overview.md).

![Azure Monitor para Contêineres](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor para Grupos de Recursos (versão prévia)
O Azure Monitor para Grupos de Recursos facilita a triagem e o diagnóstico de quaisquer problemas encontrados pelos recursos individuais, oferecendo contexto sobre a integridade e o desempenho do grupo de recursos como um todo.

Confira [Monitorar grupos de recursos com Azure Monitor (versão prévia)](./resource-group-insights.md).

![Azure Monitor para Grupos de Recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor para VMs (versão prévia)
O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos.

Confira [O que é o Azure Monitor para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor para Redes (versão prévia)
O [Azure Monitor para Redes](network-insights-overview.md) fornece uma visão abrangente da integridade e das métricas para todos os seus recursos de rede. O recurso de pesquisa avançada ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site, simplesmente pesquisando o nome do site.

![Azure Monitor para Redes](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [plataforma de dados do Azure Monitor](../platform/data-platform.md) usada pelos insights.
* Saiba mais sobre as diferentes [fontes de dados usadas pelo Azure Monitor](../platform/data-sources.md) e os diferentes tipos de dados coletados por cada um dos insights.

