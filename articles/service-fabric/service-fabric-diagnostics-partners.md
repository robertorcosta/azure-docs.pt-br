---
title: Parceiros de Monitoramento do Azure Service Fabric
description: Saiba como monitorar aplicativos, clusters e infra-estrutura do Azure Service Fabric com soluções de monitoramento de parceiros.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645711"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Parceiros de Monitoramento do Azure Service Fabric

Este artigo ilustra como monitorar seus aplicativos do Microsoft Azure Service Fabric, clusters e infraestrutura com um algumas soluções de parceiros. Trabalhamos com cada um dos parceiros abaixo para criar ofertas integradas para o Microsoft Azure Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Nossa integração com o Dynatrace fornece muitos recursos renovadores para monitorar os clusters do Microsoft Azure Service Fabric. Instalar o OneAgent do Dynatrace em suas instâncias do VMSS fornece contadores de desempenho e uma topologia de sua implantação do Service Fabric no nível do aplicativo. O DynaTrace também é uma ótima opção para o monitoramento no local. Confira mais dos recursos listados no [anúncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [instruções](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) para ativar o Dynatrace em seu cluster. 

## <a name="datadog"></a>Datadog

O Datadog tem uma extensão para VMSS para instâncias do Windows e Linux. Ao usar o Datadog, você pode coletar logs de eventos do Windows e, portanto, coletar eventos de plataforma do Service Fabric no Windows. Confira as instruções sobre como enviar seus dados de diagnóstico para Datadog [aqui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

Integração do Service Fabric com o AppDynamics é no nível do aplicativo. Ao atualizar as variáveis de ambiente e usar o aplicativo o aplicativo Dynamics NuGets, você pode enviar telemetria de aplicativo appdynamics. Consulte estas [instruções](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) sobre como integrar seus aplicativos .NET do Service Fabric com o AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic é outra ferramenta de gerenciamento de desempenho de aplicativos que se integra bem com aplicativos do Microsoft Azure Service Fabric. Você pode instalar os pacotes do New Relic NuGet e adicionar variáveis de ambiente específicas nos arquivos de manifesto para enviar a telemetria de aplicativo New relic. Confira estas [Instruções](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) para habilitar a telemetria do New Relic para seus aplicativos .NET do Microsoft Azure Service Fabric.

## <a name="elk"></a>ELK 

A pilha ELK é uma coleção de tecnologias de código aberto: Elasticsearch, Logstash e Kibana. Usando essas tecnologias em combinação, você pode coletar, armazenar e analisar dados de monitoramento e diagnóstico do Service Fabric. Temos um tutorial para saber como fazer isso com aplicativos nativos de Java do Service Fabric [aqui](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

O Humio é um serviço de coleta de registros que pode coletar registros de seus aplicativos e eventos da Service Fabric na nuvem ou no local em tempo real. Além da observabilidade ao vivo, o Humio oferece recursos de análise e visualização de última geração para visualização e coleta de informações de seus diagnósticos. Humio tem planos de preços econômicos e é construído para escalar, mantendo sua velocidade de clareamento rápido. Ele se integra diretamente com eventos da plataforma Service Fabric e telemetria de aplicativos. Você pode ler mais sobre a integração Humio e Service Fabric [aqui](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Próximas etapas

* Obter uma [visão geral do monitoramento e diagnóstico](service-fabric-diagnostics-overview.md) no Microsoft Azure Service Fabric
* Saiba como [Diagnosticar cenários comuns de](service-fabric-diagnostics-common-scenarios.md) com nossas primeiras ferramentas de parceiro comercial
