---
title: Azure Service Fabric monitorando as melhores práticas
description: As melhores práticas e considerações de design para monitorar clusters e aplicativos usando o Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: af03223e8b007cbd2a00d54c3076056cd110ecc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551809"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

[O monitoramento e o diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são fundamentais para o desenvolvimento, teste e implantação de cargas de trabalho em qualquer ambiente em nuvem. Por exemplo, você pode controlar como seus aplicativos são usados, as ações realizadas pela plataforma do Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e evitar que eles ocorram no futuro.

## <a name="application-monitoring"></a>Monitoramento de aplicativo

O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo estão sendo usados. Monitore seus aplicativos para garantir que os problemas que afetam os usuários sejam capturados. O monitoramento de aplicativo é a responsabilidade daqueles que desenvolvem o aplicativo e seus serviços, pois depende exclusivamente da lógica de negócios do aplicativo. É recomendável que você configure o monitoramento de aplicativo com o [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), a ferramenta de monitoramento de aplicativo do Azure.

## <a name="cluster-monitoring"></a>Monitoramento do cluster

Uma das metas do Service Fabric é tornar os aplicativos resilientes a falhas de hardware. Esse objetivo é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se, ao tentar implantar ou mover uma carga de trabalho, as regras para a veiculação de serviços forem violadas? O Service Fabric fornece diagnóstico para esses e outros problemas, a fim de garantir que você seja informado sobre como a plataforma Service Fabric interage com aplicativos, serviços, contêineres e nós.

Para clusters do Windows, recomenda-se configurar o monitoramento de clusters com registros [do Diagnostics Agent](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) e do Monitor Do [Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Para clusters Linux, os logs do Azure Monitor também são a ferramenta recomendada para o monitoramento da plataforma e da infra-estrutura do Azure. O diagnóstico da plataforma Linux exige outra configuração, conforme observado em [Eventos de cluster do Linux do Service Fabric no Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitoramento da infraestrutura

[Os registros do Monitor do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) são recomendados para monitorar eventos de nível de cluster. Depois de configurar o agente do Log Analytics no workspace, conforme descrito no link anterior, você poderá coletar métricas de desempenho como Utilização da CPU, contadores de desempenho do .NET como utilização da CPU no nível do processo, contadores de desempenho do Service Fabric como o número de exceções de um serviço confiável e métricas de contêiner como Utilização da CPU.  Você precisará escrever registros de contêineres para stdout ou stderr para que eles estejam disponíveis nos registros do Monitor Do Azure.

## <a name="watchdogs"></a>Watchdogs

Em geral, um watchdog é um serviço separado que inspeciona a integridade e a carga entre serviços, executa ping em pontos de extremidade e relata eventos de integridade inesperados no cluster. Isso pode ajudar a evitar erros que podem não ser detectados com base apenas no desempenho de um único serviço. Os watchdogs também são um bom local para hospedar o código que executa ações corretivas que não exigem a interação do usuário, como limpeza de arquivos de log no armazenamento em determinados intervalos de tempo. Confira uma implementação de serviço de watchdog de exemplo em [Eventos de cluster do Linux do Service Fabric no Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Próximas etapas

* Comece a instrumentar seus aplicativos: [Evento de nível de aplicação e geração de log](service-fabric-diagnostics-event-generation-app.md).
* Siga as etapas para configurar o Application Insights para seu aplicativo com [Monitorar e diagnosticar um aplicativo ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre o monitoramento da plataforma e dos eventos que o Service Fabric oferece para você: [evento de nível de plataforma e geração de log](service-fabric-diagnostics-event-generation-infra.md).
* Configure a integração de logs do Monitor do Azure com o Service Fabric: [Configure os logs do Monitor do Azure para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar registros do Monitor Do Azure para monitorar contêineres: [Monitoramento e Diagnóstico de Contêineres windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Confira os problemas de diagnóstico de exemplo e as soluções com o Service Fabric: [Cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md)
* Conheça as recomendações gerais de monitoramento dos recursos do Azure: [Melhores Práticas - Monitoramento e diagnóstico .](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)