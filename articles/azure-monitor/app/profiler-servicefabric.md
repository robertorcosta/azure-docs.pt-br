---
title: Perfil ao vivo aplicativos de malha de serviço do Azure com insights de aplicativos
description: Habilitar o Profiler para um aplicativo do Service Fabric
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671606"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights

Você também pode implantar o Application Insights Profiler nesses serviços:
* [Serviço de aplicativo do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Application Insights Profiler está incluído com o Diagnóstico do Azure. Você pode instalar a extensão de Diagnóstico do Azure usando um modelo do Azure Resource Manager para seu cluster do Service Fabric. Obtenha um [Modelo que instala o Diagnóstico do Azure em um Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Para configurar seu ambiente, faça o seguinte:

1. O Profiler suporta .NET Framework e .Net Core. Se estiver usando o .NET Framework, certifique-se de que está usando [o .NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente para confirmar que o `Windows Server 2012 R2` sistema operacional implantado é ou posterior. O Profiler suporta aplicativos .NET Core 2.1 e mais novos.

1. Pesquise a extensão [Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo de modelo de implantação.

1. Adicione a seguinte seção `SinksConfig` como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação do Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de Diagnóstico em seu modelo de implantação, consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. Usando o modelo do Azure Resource Manager, implante seu cluster do Service Fabric.  
  Se as configurações estiverem corretas, o Application Insights Profiler será instalado e habilitado quando a extensão do Diagnóstico do Azure for instalada. 

1. Adicione o Application Insights ao seu aplicativo do Service Fabric.  
  Para que o Profiler colete perfis para suas solicitações, seu aplicativo deve estar rastreando operações com insights de aplicativos. Para APIs apátridas, você pode consultar instruções para [rastrear solicitações de criação de perfil](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre como rastrear operações personalizadas em outros tipos de aplicativos, consulte [acompanhar operações personalizadas com o Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Reimplante o aplicativo.


## <a name="next-steps"></a>Próximas etapas

* Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
* Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para obter ajuda para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
