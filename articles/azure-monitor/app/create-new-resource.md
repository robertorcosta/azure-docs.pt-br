---
title: Criar um novo recurso do Azure Application Insights | Microsoft Docs
description: Configure manualmente o monitoramento do Application Insights para um novo aplicativo em tempo real.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537569"
---
# <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

O Azure Application Insights exibe dados sobre o seu aplicativo em um *recurso* do Microsoft Azure. Por isso, criar um novo recurso faz parte da [configuração do Application Insights para monitorar um novo aplicativo][start]. Depois de criar seu novo recurso, você pode obter sua chave de instrumentação e usá-la para configurar o Application Insights SDK. A chave de instrumentação liga sua telemetria ao recurso.

## <a name="sign-in-to-microsoft-azure"></a>Faça login no Microsoft Azure

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Faça login no [portal do Azure](https://portal.azure.com)e crie um recurso application Insights:

![Clique no sinal '+' no canto superior esquerdo. Selecione ferramentas de desenvolvedor seguidas por insights de aplicativos](./media/create-new-resource/new-app-insights.png)

   | Configurações        |  Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor exclusivo | Nome que identifica o aplicativo que você está monitorando. |
   | **Grupo de recursos**     | myResourceGroup      | Nome do novo ou existente grupo de recursos para hospedar dados do App Insights. |
   | **Localidade** | Leste dos EUA | Escolha um local perto de você ou perto de onde seu aplicativo está hospedado. |

> [!NOTE]
> Embora você possa usar o mesmo nome de recurso em diferentes grupos de recursos, pode ser benéfico usar um nome único globalmente. Isso pode ser útil se você planeja [realizar consultas de recursos cruzados,](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) pois simplifica a sintaxe necessária.

Digite os valores apropriados nos campos necessários e, em seguida, selecione **'Revisar + criar '''**

![Digite valores nos campos necessários e selecione "revisão + criar".](./media/create-new-resource/review-create.png)

Quando seu aplicativo foi criado, um novo painel é aberto. Este painel é onde você vê dados de desempenho e uso sobre o seu aplicativo monitorado. 

## <a name="copy-the-instrumentation-key"></a>Copiar a chave de instrumentação

A chave de instrumentação identifica o recurso com o qual você deseja associar seus dados de telemetria. Você precisará copiar a chave de instrumentação e adicioná-la ao código do seu aplicativo.

![Clique e copie a tecla de instrumentação](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Instalar o SDK em seu aplicativo

Instale o SDK do Application Insights SDK no aplicativo. Esta etapa depende muito do tipo do seu aplicativo.

Use a chave de instrumentação para configurar o [SDK instalado no seu aplicativo][start].

O SDK inclui módulos padrão que enviam telemetria sem que você tenha que escrever qualquer código adicional. Para rastrear ações do usuário ou diagnosticar problemas mais detalhadamente, [use a API][api] para enviar sua própria telemetria.

## <a name="creating-a-resource-automatically"></a>Criando um recurso automaticamente

### <a name="powershell"></a>PowerShell

Criar um novo recurso do Application Insights

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exemplo

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultados

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Para obter a documentação completa do PowerShell para este cmdlet, e para saber como recuperar a chave de instrumentação, consulte a [documentação do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Azure CLI (visualização)

Para acessar os comandos de visualização do Application Insights Azure CLI, você primeiro precisa executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar `az extension add` o comando, você verá uma mensagem de erro que afirma:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar seu recurso Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultados

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Para obter a documentação completa do Azure CLI para este comando, e para saber como recuperar a chave de instrumentação consulte a [documentação cli do Azure](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Próximas etapas
* [Pesquisa de diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md)
* [Escrever consultas do Analytics](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
