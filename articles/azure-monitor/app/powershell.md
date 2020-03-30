---
title: Automatizar o Azure Application Insights com o PowerShell | Microsoft Docs
description: Automatize a criação e o gerenciamento de recursos, alertas e testes de disponibilidade no PowerShell usando um modelo do Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275874"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Gerenciar recursos de insights de aplicativos usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo mostra como automatizar a criação e atualização de recursos do [Application Insights](../../azure-monitor/app/app-insights-overview.md) automaticamente usando o Gerenciamento de Recursos do Azure. Por exemplo, você pode fazer isso como parte de um processo de compilação. Juntamente com o recurso básico do Application Insights, é possível criar [testes na Web de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md), configurar [alertas](../../azure-monitor/app/alerts.md), definir o [esquema de preços](pricing.md) e criar outros recursos do Azure.

A chave para criar esses recursos são os modelos de JSON para o [Gerenciador de Recursos do Azure](../../azure-resource-manager/management/manage-resources-powershell.md). O procedimento básico é: baixar as definições JSON dos recursos existentes; parametrizar certos valores, como nomes; e, em seguida, execute o modelo sempre que você quiser criar um novo recurso. Você pode empacotar vários recursos juntos, criá-los de uma só vez - por exemplo, um monitor de aplicativo com testes de disponibilidade, alertas e armazenamento para exportação contínua. Existem algumas sutilezas para algumas das parametrizações, que vamos explicar aqui.

## <a name="one-time-setup"></a>Configuração única
Se você nunca usou o PowerShell com sua assinatura do Azure:

Instale o módulo do Azure Powershell no computador em que você deseja executar os scripts.

1. Instale o [Microsoft Web Platform Installer (v5 ou superior)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Use-o para instalar o Microsoft Azure PowerShell

Além de usar modelos do Gerenciador de Recursos, há um rico conjunto de [cmdlets PowerShell do Application Insights,](https://docs.microsoft.com/powershell/module/az.applicationinsights)que facilitam a configuração dos recursos do Application Insights de forma programática. Os recursos habilitados pelos cmdlets incluem:

* Criar e excluir recursos do Application Insights
* Obtenha listas de recursos do Application Insights e suas propriedades
* Criar e gerenciar a exportação contínua
* Criar e gerenciar chaves de aplicativos
* Defina o Daily Cap
* Defina o Plano de Preços

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Crie recursos de insights de aplicativos usando um cmdlet PowerShell

Veja como criar um novo recurso application Insights no datacenter do Azure East EUA usando o cmdlet [New-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Crie recursos do Application Insights usando um modelo do Gerenciador de recursos

Veja como criar um novo recurso do Application Insights usando um modelo do Gerenciador de Recursos.

### <a name="create-the-azure-resource-manager-template"></a>Crie o modelo do Azure Resource Manager

Criar um novo arquivo .json - vamos chamá-lo de `template1.json` neste exemplo. Copie este conteúdo nele:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Use o modelo do Gerenciador de recursos para criar um novo recurso do Application Insights

1. No PowerShell, faça login no Azure usando`$Connect-AzAccount`
2. Defina seu contexto para uma assinatura com`Set-AzContext "<subscription ID>"`
2. Execute uma nova implantação para criar um novo recurso do Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` é o grupo em que você deseja criar novos recursos.
   * `-TemplateFile` deve ocorrer antes dos parâmetros personalizados.
   * `-appName` O nome do recurso a ser criado.

Você pode adicionar outros parâmetros – você encontrará suas descrições na seção de parâmetros do modelo.

## <a name="get-the-instrumentation-key"></a>Pegue a chave de instrumentação

Depois de criar um recurso de aplicativo, você desejará a chave de instrumentação: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Para ver uma lista de muitas outras propriedades do recurso Application Insights, use:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Propriedades adicionais estão disponíveis através dos cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Consulte a [documentação detalhada](https://docs.microsoft.com/powershell/module/az.applicationinsights) para os parâmetros desses cmdlets.  

## <a name="set-the-data-retention"></a>Defina a retenção de dados 

Para obter a retenção de dados atual para o recurso Application Insights, você pode usar a ferramenta OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Saiba mais sobre armclient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Aqui está um `ARMClient`exemplo usando , para obter a retenção atual:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Para definir a retenção, o comando é um PUT semelhante:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Para definir a retenção de dados para 365 dias usando o modelo acima, execute:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

O script a seguir também pode ser usado para alterar a retenção. Copie este script `Set-ApplicationInsightsRetention.ps1`para salvar como .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Este script pode então ser usado como:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Defina a tampa diária

Para obter as propriedades de tampa diária, use o [cmdlet Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir as propriedades da tampa diária, use o mesmo cmdlet. Por exemplo, para definir a tampa para 300 GB/dia,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Você também pode usar [armclient](https://github.com/projectkudu/ARMClient) para obter e definir parâmetros de tampa diária.  Para obter os valores atuais, use:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Defina o tempo de reset da tampa diária

Para definir o tempo de reset da tampa diária, você pode usar [ARMClient](https://github.com/projectkudu/ARMClient). Aqui está um `ARMClient`exemplo usando , para definir o tempo de reset para uma nova hora (neste exemplo 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Defina o plano de preços 

Para obter o plano de preços atual, use o [cmdlet Set-AzApplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para definir o plano de preços, use `-PricingPlan` o mesmo cmdlet com o especificado:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Você também pode definir o plano de preços em um recurso existente do Application Insights usando o modelo do `dependsOn` Gerenciador de recursos acima, omitindo o recurso "microsoft.insights/components" e o nó do recurso de cobrança. Por exemplo, para defini-lo para o plano Por GB (anteriormente chamado de plano Básico), execute:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

O `priceCode` é definido como:

|priceCode|plan|
|---|---|
|1|Por GB (anteriormente chamado de plano Básico)|
|2|Por Nó (anteriormente nome do plano Enterprise)|

Finalmente, você pode usar [o ARMClient](https://github.com/projectkudu/ARMClient) para obter e definir planos de preços e parâmetros de limite diário.  Para obter os valores atuais, use:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

E você pode definir todos esses parâmetros usando:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Isso definirá o limite diário para 200 GB/dia, configurará o tempo de reset da tampa diária para 12:00 UTC, enviará e-mails quando a tampa for atingida e o nível de aviso for atingido e definirá o limite de aviso para 90% da tampa.  

## <a name="add-a-metric-alert"></a>Adicionar um alerta de Métrica

Para automatizar a criação de alertas métricos, consulte o [artigo do modelo de alertas métricos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Adicionar um teste de disponibilidade

Para automatizar testes de disponibilidade, consulte o [artigo do modelo de alertas métricos](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Adicionar mais recursos

Para automatizar a criação de qualquer outro recurso de qualquer variante, crie um exemplo manualmente e, em seguida, copie e parametrize seu código do [Azure Resource Manager](https://resources.azure.com/). 

1. Abra o [Gerenciador de Recursos do Azure](https://resources.azure.com/). Navegar por `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` até o recurso do aplicativo. 
   
    ![Navegação no Gerenciador de recursos do Azure](./media/powershell/01.png)
   
    *Componentes* são os recursos básicos do Application Insights para exibir aplicativos. Há recursos separados para as regras de alerta associadas e testes da web de disponibilidade.
2. Copie o JSON do componente para o local apropriado no `template1.json`.
3. Exclua essas propriedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra `webtests` as `alertrules` seções e copie o JSON para itens individuais em seu modelo. (Não copie dos `webtests` nós `alertrules` ou: vá para os itens abaixo deles.)
   
    Cada teste da web tem uma regra de alerta associada, então você precisa copiar ambos.
   
    Você também pode incluir alertas sobre métricas. [Nomes métricos](powershell-alerts.md#metric-names).
5. Insira esta linha em cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrizar o modelo
Agora você tem que substituir os nomes específicos por parâmetros. Para [parametrizar um modelo](../../azure-resource-manager/templates/template-syntax.md), escreva expressões que usam um [conjunto de funções auxiliares](../../azure-resource-manager/templates/template-functions.md). 

Você não pode parametrizar apenas uma parte de uma cadeia de caracteres, então use `concat()` para criar cadeias de caracteres.

Estes são exemplos das substituições que você vai querer fazer. Há várias ocorrências de cada substituição. Talvez seja necessário outras em seu modelo. Esses exemplos usam os parâmetros e variáveis definidos na parte superior do modelo.

| localizar | substitua por |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minúscula) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Definir dependências entre os recursos
O Azure deve configurar os recursos na ordem explícita. Para certificar-se de que a instalação é concluída antes do início da próxima, adicione linhas de dependência:

* No recurso do teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* No recurso de alerta para um teste de disponibilidade:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Próximas etapas
Outros artigos sobre automação:

* [Criar um recurso do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) -método rápido sem usar um modelo.
* [Configurar alertas](powershell-alerts.md)
* [Criar testes na Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Enviar o Diagnóstico do Azure para o Application Insights](powershell-azure-diagnostics.md)
* [Implantar no Azure pelo GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Criar anotações de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)