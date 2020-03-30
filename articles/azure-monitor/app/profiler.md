---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275770"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Você pode executar o Profiler em ASP.NET e ASP.NET aplicativos Core que estão sendo executados no Azure App Service usando o nível de serviço Básico ou superior. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço Azure, aqui estão instruções para habilitar o Profiler em outras plataformas suportadas:
* [Serviços de Nuvem](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler é pré-instalado como parte do tempo de execução do App Services. As etapas abaixo mostrarão como habilitá-lo para o seu Serviço de Aplicativo. Siga essas etapas mesmo que você tenha incluído o App Insights SDK em seu aplicativo no momento da compilação.

1. Ative a configuração "Always On" para o serviço do aplicativo. Você pode atualizar a configuração na página Configuração do serviço de aplicativo em Configurações Gerais.
1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
1. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Verifique também o Profiler está **On**. Se o recurso Application Insights estiver em uma assinatura diferente do seu Serviço de Aplicativo, você não poderá usar esta página para configurar o Application Insights. Você ainda pode fazê-lo manualmente, porém, criando as configurações necessárias do aplicativo manualmente. [A próxima seção contém instruções para ativar manualmente o Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitar o Profiler manualmente ou com o Azure Resource Manager
O Application Insights Profiler pode ser habilitado criando configurações de aplicativos para o seu Serviço de Aplicativos Azure. A página com as opções mostradas acima cria essas configurações do aplicativo para você. Mas você pode automatizar a criação dessas configurações usando um modelo ou outros meios. Essas configurações também funcionarão se o recurso Application Insights estiver em uma assinatura diferente do seu Serviço de Aplicativos Azure.
Aqui estão as configurações necessárias para ativar o profiler:

|Configurações de Aplicativo    | Valor    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Você pode definir esses valores usando [modelos de gerenciador de recursos do Azure,](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager) [Azure Powershell,](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Habilitando o Profiler para outras nuvens manualmente

Se você quiser ativar o profiler para outras nuvens, você pode usar as configurações do aplicativo abaixo.

|Configurações de Aplicativo    | Valores do governo dos EUA| Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para interromper ou reiniciar o Profiler de uma instância individual do aplicativo, em **Trabalhos Web**, acesse o recurso do aplicativo. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Os arquivos do Profiler podem ser excluídos ao usar o WebDeploy para implantar alterações no seu aplicativo web. Você pode impedir que a exclusão excluindo a pasta App_Data seja excluída durante a implantação. 


## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
