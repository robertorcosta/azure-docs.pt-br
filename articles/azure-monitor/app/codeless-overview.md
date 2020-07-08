---
title: Monitore seus aplicativos sem alterações de código-instrumentação automática para Azure Monitor Application Insights | Microsoft Docs
description: Visão geral da instrumentação automática para Azure Monitor gerenciamento de desempenho de aplicativos Application Insights Code-out
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 87342dcd316b0364522baa01e632b704665c998e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85363713"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>O que é a instrumentação automática ou a Azure Monitor de anexação de código Application Insights?

A instrumentação automática, ou anexação sem código, permite habilitar o monitoramento de aplicativos com Application Insights sem alterar seu código.  

O Application Insights é integrado a vários provedores de recursos e funciona em ambientes diferentes. Em essência, tudo o que você precisa fazer é habilitar e, em alguns casos, configurar o agente, que coletará a telemetria pronta para uso. Em nenhum momento, você verá as métricas, os dados e as dependências em seu recurso de Application Insights, o que permitirá que você identifique a fonte de possíveis problemas antes que eles ocorram e analise a causa raiz com a exibição de transação de ponta a ponta.

## <a name="supported-environments-languages-and-resource-providers"></a>Ambientes, linguagens e provedores de recursos com suporte

À medida que estamos adicionando mais e mais integrações, a matriz de recursos de instrumentação automática se torna complexa. A tabela a seguir mostra o estado atual da questão no que diz respeito ao suporte para vários provedores de recursos, linguagens e ambientes.

|Provedor de recursos/ambiente | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Serviço de Azure App no Windows  | GA, OnBD *       | GA, aceitar      | Visualização particular | Visualização particular |
|Serviço de Aplicativo do Azure no Linux    | N/D             | Sem suporte   | Visualização pública  | Visualização pública  |
|Serviço de Azure App no AKS      | N/D             | Em design       | Em design       | Em design       |
|Azure Functions-básico       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Dependências de Azure Functions| Sem suporte   | Sem suporte   | Visualização pública  | Sem suporte   |
|Serviço de Kubernetes do Azure      | N/D             | Em design       | Por meio do agente   | Em design       |
|Janelas de VMs do Azure             | Visualização pública  | Sem suporte   | Sem suporte   | Sem suporte   |
|Janelas de VMs locais       | GA, aceitar      | Sem suporte   | Por meio do agente   | Sem suporte   |
|Agente autônomo – qualquer env.   | Sem suporte   | Sem suporte   | Visualização pública  | Sem suporte   |

* OnBD é curto para ativado por padrão-o Application Insights será habilitado automaticamente depois que você implantar seu aplicativo em ambientes com suporte. 

## <a name="azure-app-service"></a>Serviço de aplicativo do Azure

### <a name="windows"></a>Windows

O [monitoramento de aplicativos no serviço Azure app](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps?tabs=net) está disponível para o aplicativo .net e está habilitado por padrão, o .NET Core pode ser habilitado com um clique, e Java e Node.js estão em visualização privada.

### <a name="linux"></a>Linux 

O monitoramento de aplicativos Java e Node.js no serviço de aplicativo está em visualização pública e pode ser habilitado no portal do Azure, disponível em todas as regiões.

## <a name="azure-functions"></a>Funções do Azure

O monitoramento básico para Azure Functions é habilitado por padrão para coletar logs, desempenho, dados de erro e solicitações HTTP. Para aplicativos Java, você pode habilitar um monitoramento mais rico com rastreamento distribuído e obter os detalhes de transação de ponta a ponta. Essa funcionalidade para Java está em visualização pública e você pode [habilitá-la no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/app/monitor-functions).

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

A instrumentação sem código do serviço kubernetes do Azure está disponível atualmente para aplicativos Java por meio do [agente autônomo](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>VMs do Windows do Azure e conjunto de dimensionamento de máquinas virtuais

[A instrumentação automática para VMs do Azure e conjunto de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps) está disponível para aplicativos .net 

## <a name="on-premises-servers"></a>Servidores locais
Você pode facilmente habilitar o monitoramento para seus [servidores do Windows locais para aplicativos .net](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) e para [aplicativos Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent).

## <a name="other-environments"></a>Outros ambientes
O versátil agente autônomo do Java funciona em qualquer ambiente, não há necessidade de instrumentar seu código. [Siga o guia](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) para habilitar Application insights e leia sobre os recursos incríveis do agente Java. O agente está em visualização pública e disponível em todas as regiões. 

## <a name="next-steps"></a>Próximas etapas

* [Visão geral de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
