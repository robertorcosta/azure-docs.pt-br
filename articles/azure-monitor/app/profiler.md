---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367669"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Você pode executar o Profiler em aplicativos ASP.NET e ASP.NET Core que estão em execução no serviço Azure App usando a camada de serviço básica ou superior. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o Profiler em outras plataformas com suporte:
* [Serviços de Nuvem](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler é pré-instalado como parte do tempo de execução dos serviços de aplicativo. As etapas a seguir mostrarão como habilitá-lo para o serviço de aplicativo. Siga estas etapas mesmo se você tiver incluído o SDK do App insights em seu aplicativo no momento da compilação.

1. Habilite a configuração "Always On" para o serviço de aplicativo. Você pode atualizar a configuração na página configuração do serviço de aplicativo em configurações gerais.
1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
1. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Verifique também o Profiler está **On**. Se o recurso de Application Insights estiver em uma assinatura diferente do serviço de aplicativo, você não poderá usar essa página para configurar Application Insights. Você ainda pode fazê-lo manualmente criando as configurações de aplicativo necessárias manualmente. [A próxima seção contém instruções para habilitar manualmente o profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Habilitar o profiler manualmente ou com Azure Resource Manager
Application Insights Profiler pode ser habilitado pela criação de configurações de aplicativo para seu serviço de Azure App. A página com as opções mostradas acima cria essas configurações de aplicativo para você. Mas você pode automatizar a criação dessas configurações usando um modelo ou outros meios. Essas configurações também funcionarão se o recurso de Application Insights estiver em uma assinatura diferente do serviço de Azure App.
Aqui estão as configurações necessárias para habilitar o criador de perfil:

|Configurações de Aplicativo    | {1&gt;Valor&lt;1}    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey para o recurso de Application Insights    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


Você pode definir esses valores usando [modelos de Azure Resource Manager](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), o [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [CLI do Azure](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Habilitando o Profiler para outras nuvens manualmente

Se você quiser habilitar o criador de perfil para outras nuvens, poderá usar as configurações de aplicativo abaixo.

|Configurações de Aplicativo    | Valores do governo dos EUA| Nuvem da China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para interromper ou reiniciar o Profiler de uma instância individual do aplicativo, em **Trabalhos Web**, acesse o recurso do aplicativo. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Os arquivos do criador de perfil podem ser excluídos ao usar o WebDeploy para implantar alterações em seu aplicativo Web. Você pode impedir a exclusão excluindo a pasta App_Data de ser excluída durante a implantação. 


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
