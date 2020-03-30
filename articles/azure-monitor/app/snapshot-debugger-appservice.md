---
title: Habilite o Snapshot Debugger para aplicativos .NET no Serviço de Aplicativos Azure | Microsoft Docs
description: Habilite o Depurador de Instantâneos para aplicativos .NET no Serviço de Aplicativos Azure
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298265"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Habilite o Depurador de Instantâneos para aplicativos .NET no Serviço de Aplicativos Azure

O Snapshot Debugger atualmente funciona para aplicativos ASP.NET e ASP.NET Core que estão sendo executados no Azure App Service nos planos de serviço do Windows.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>Ativar depurador de instantâneos
Para ativar o Snapshot Debugger para um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço Azure, aqui estão instruções para ativar o Snapshot Debugger em outras plataformas suportadas:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de malha do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas no local](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Se você estiver usando uma versão de visualização do .NET Core, siga as instruções para [Habilitar snapshot depurador para outros ambientes](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) primeiro para incluir o pacote [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet com o aplicativo e, em seguida, complete o resto das instruções abaixo. 

O Depurador de instantâneos do Aplicativo Insights está pré-instalado como parte do tempo de execução do App Services, mas você precisa atimá-lo para obter instantâneos para o aplicativo App Service. Depois de implantar um aplicativo, mesmo que tenha incluído o Application Insights SDK no código-fonte, siga as etapas abaixo para ativar o depurador de instantâneos.

1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
2. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Certifique-se também de que ambos os switches para Snapshot Debugger estão **ligados**.

   ![Adicione a extensão de site do App Insights][Enablement UI]

4. O Snapshot Debugger agora está ativado usando uma configuração do aplicativo App Services.

    ![Configuração do aplicativo para depurador de instantâneos][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desativar o depurador de instantâneos

Siga os mesmos passos de **Habilitar snapshot depurador,** mas alterne ambos os switches para Snapshot Debugger para **Off**.
Recomendamos que você tenha o Snapshot Debugger ativado em todos os seus aplicativos para facilitar o diagnóstico de exceções de aplicativos.

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

Para um Serviço de Aplicativo Azure, você pode definir as configurações do aplicativo em um modelo do Azure Resource Manager para habilitar o Snapshot Debugger e o Profiler. Você adiciona um recurso de configuração que contém as configurações do aplicativo como um recurso filho do site:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Próximas etapas

- Gerar tráfego para o seu aplicativo que pode desencadear uma exceção. Em seguida, aguarde de 10 a 15 minutos para que os instantâneos sejam enviados para a instância Doaplicativo Insights.
- Veja [fotos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal Azure.
- Para obter ajuda para solucionar problemas do Depurador de problemas, consulte [Solução de problemas do Depurador de instantâneos](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

