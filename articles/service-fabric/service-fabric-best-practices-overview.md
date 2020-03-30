---
title: Práticas recomendadas de aplicativo e cluster do Azure Service Fabric
description: As melhores práticas e considerações de design para gerenciar clusters, aplicativos e serviços usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551770"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Práticas recomendadas de aplicativo e cluster do Azure Service Fabric

Este artigo fornece links para práticas recomendadas para gerenciar aplicativos e clusters do Azure Service Fabric. Recomendamos que você implemente essas práticas para otimizar a confiabilidade do seu ambiente de produção. Use um dos [modelos](https://github.com/Azure-Samples/service-fabric-cluster-templates) de cluster service fabric para começar a projetar sua solução de produção ou atualize seu modelo existente para incorporar essas práticas.

## <a name="security"></a>Segurança

* [Práticas recomendadas para segurança](service-fabric-best-practices-security.md)

## <a name="networking"></a>Rede

* [Práticas recomendadas para rede](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planejamento e escala de computação

* [Práticas recomendadas para dimensionamento de computação](service-fabric-best-practices-capacity-scaling.md)
* [Planejamento de capacidade da computação](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestrutura como código

* [Práticas recomendadas para implementar infraestrutura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

* [Práticas recomendadas para monitoramento de cluster e diagnóstico](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Design do aplicativo

* [Melhores práticas para o design de aplicativos](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de verificação

Depois de implementar as práticas sugeridas nas seções anteriores, certifique-se de integrar todas as melhores práticas na lista de verificação de prontidão de produção:
* [Lista de verificação de prontidão da produção do Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Próximas etapas

* Crie um cluster em VMs ou em computadores que estejam executando o Windows Server: [Criação de um cluster do Service Fabric para o Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Criar um cluster em VMs ou em computadores que estejam executando o Linux: [Criar um cluster do Linux](service-fabric-cluster-creation-via-portal.md)
* Solucionar problemas tecido de serviço: [guias de solução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)