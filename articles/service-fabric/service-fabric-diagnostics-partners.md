---
title: Parceiros de Monitoramento do Azure Service Fabric | Microsoft Docs
description: Saiba como monitorar o Azure Service Fabric com monitoramento de soluções de parceiro
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: 8353c8846820aa996214e6b119a2f9027b8b6fe6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232441"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de Monitoramento do Azure Service Fabric

Este artigo ilustra como monitorar seus aplicativos do Microsoft Azure Service Fabric, clusters e infraestrutura com um algumas soluções de parceiros. Trabalhamos com cada um dos parceiros abaixo para criar ofertas integradas para o Microsoft Azure Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Nossa integração com o Dynatrace fornece muitos recursos renovadores para monitorar os clusters do Microsoft Azure Service Fabric. Instalar o OneAgent do Dynatrace em suas instâncias do VMSS fornece contadores de desempenho e uma topologia de sua implantação do Service Fabric no nível do aplicativo. O DynaTrace também é uma ótima opção para o monitoramento no local. Check out more of the features listed in the [announcement](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) and [instructions](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) to enable Dynatrace on your cluster. 

## <a name="datadog"></a>Datadog

O Datadog tem uma extensão para VMSS para instâncias do Windows e Linux. Ao usar o Datadog, você pode coletar logs de eventos do Windows e, portanto, coletar eventos de plataforma do Service Fabric no Windows. Confira as instruções sobre como enviar seus dados de diagnóstico para Datadog [aqui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Integração do Service Fabric com o AppDynamics é no nível do aplicativo. Ao atualizar as variáveis de ambiente e usar o aplicativo o aplicativo Dynamics NuGets, você pode enviar telemetria de aplicativo appdynamics. Consulte estas [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) sobre como integrar seus aplicativos .NET do Service Fabric com o AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic é outra ferramenta de gerenciamento de desempenho de aplicativos que se integra bem com aplicativos do Microsoft Azure Service Fabric. Você pode instalar os pacotes do New Relic NuGet e adicionar variáveis de ambiente específicas nos arquivos de manifesto para enviar a telemetria de aplicativo New relic. Confira estas [Instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para habilitar a telemetria do New Relic para seus aplicativos .NET do Microsoft Azure Service Fabric.

## <a name="elk"></a>ELK 

A pilha ELK é uma coleção de tecnologias de software livre: Elasticsearch, Logstash e Kibana. Ao usar estes em combinação, você pode coletar, armazenar e analisar dados de monitoramento e diagnóstico do Microsoft Azure Service Fabric. Temos um tutorial para saber como fazer isso com aplicativos nativos de Java do Service Fabric [aqui](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio is a log collection service that can gather logs from your applications and events from Service Fabric in the cloud or on-premises in real time. In addition to live observability, Humio offers state of the art analysis and visualization capabilities for viewing and collecting insights from your diagnostics. Humio has cost effective pricing plans and is built to scale while retaining it's lightening fast speed. It directly integrates with Service Fabric platform events and Application telemetry. You can read more about the Humio and Service Fabric integration [here](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Próximos passos

* Obter uma [visão geral do monitoramento e diagnóstico](service-fabric-diagnostics-overview.md) no Microsoft Azure Service Fabric
* Saiba como [Diagnosticar cenários comuns de](service-fabric-diagnostics-common-scenarios.md) com nossas primeiras ferramentas de parceiro comercial
