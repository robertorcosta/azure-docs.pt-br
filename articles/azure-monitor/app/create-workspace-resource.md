---
title: Criar um novo Azure Monitor Application Insights recurso baseado no espaço de trabalho | Microsoft Docs
description: Saiba mais sobre as etapas necessárias para habilitar o novo Azure Monitor Application Insights recursos baseados no espaço de trabalho.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211402"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Recursos de Application Insights baseados em espaço de trabalho (versão prévia)

Os recursos baseados em espaço de trabalho dão suporte à integração completa entre Application Insights e Log Analytics. Agora você pode optar por enviar sua telemetria de Application Insights para um espaço de trabalho Log Analytics comum, que permite acesso completo a todos os recursos do Log Analytics ao mesmo tempo em que mantém os logs de aplicativos, infraestrutura e plataforma em um único local consolidado.

Isso também permite o RBAC (controle de acesso baseado em função) comum em seus recursos e elimina a necessidade de consultas entre aplicativos/espaços de trabalho.

> [!NOTE]
> A ingestão de dados e a retenção de recursos de Application Insights baseados em espaço de trabalho são cobradas por meio do espaço de trabalho Log Analytics onde os dados estão localizados. [Saiba mais]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) sobre a cobrança de recursos de Application insights baseados em espaço de trabalho.

Para testar a nova experiência, entre no [portal do Azure](https://portal.azure.com)e crie um recurso de Application insights:

![Recurso de Application Insights baseado em espaço de trabalho](./media/create-workspace-resource/create-workspace-based.png)

Se você ainda não tiver um espaço de trabalho Log Analytics existente, [consulte a documentação de criação do espaço de trabalho log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Para os **recursos baseados em espaço de trabalho de visualização pública estão limitados atualmente a oeste dos EUA 2, leste dos EUA e EUA Central do Sul.**

Depois que o recurso for criado, você verá as informações de espaço de trabalho correspondentes no painel **visão geral** :

![Nome do workspace](./media/create-workspace-resource/workspace-name.png)

Clicar no texto do link azul levará você para o espaço de trabalho Log Analytics associado, onde você poderá aproveitar o novo ambiente de consulta de espaço de trabalho unificado.

> [!NOTE]
> Ainda fornecemos compatibilidade total com versões anteriores para suas Application Insights consultas de recursos clássicos, pastas de trabalho e alertas baseados em log na experiência de Application Insights. Para consultar/exibir em relação à [nova estrutura/esquema de tabela baseada em espaço de trabalho](apm-tables.md) , você deve primeiro navegar para seu espaço de trabalho log Analytics. Durante a visualização, a seleção de **logs** de dentro dos painéis de Application insights dará acesso à experiência de consulta de Application insights clássica.

## <a name="copy-the-connection-string"></a>Copiar a cadeia de conexão

A [cadeia de conexão]() identifica o recurso ao qual você deseja associar os dados de telemetria. Ele também permite que você modifique os pontos de extremidade que o recurso usará como um destino para a telemetria. Você precisará copiar a cadeia de conexão e adicioná-la ao código do aplicativo ou a uma variável de ambiente.

![Clique e copie a chave de instrumentação](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Configuração de monitoramento

Depois que um recurso de Application Insights baseado em espaço de trabalho tiver sido criado, a configuração do monitoramento será relativamente simples.

### <a name="code-based-application-monitoring"></a>Monitoramento de aplicativos baseados em código

Para o monitoramento de aplicativos baseados em código, basta instalar o SDK do Application Insights apropriado e apontar para a chave de instrumentação ou a cadeia de conexão para o recurso recém-criado.  

Para obter uma documentação detalhada sobre como configurar um SDK Application Insights para o monitoramento baseado em código, consulte a documentação específica da linguagem/estrutura:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Tarefas em segundo plano & aplicativos de console modernos (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Aplicativos de console clássico (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Java](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitoramento de código e criação de recursos do Visual Studio

Para o monitoramento de serviços com código, como Azure Functions e serviços de Azure App, você também precisará primeiro criar o recurso de Application Insights baseado no espaço de trabalho e, em seguida, apontar para esse recurso durante a fase de configuração de monitoramento.

Embora esses serviços ofereçam a opção de criar um novo recurso de Application Insights dentro de seu próprio processo de criação de recursos, os recursos criados por meio dessas opções de interface do usuário estão atualmente restritos à experiência de Application Insights clássica.

O mesmo se aplica à experiência de criação de recursos Application Insights no Visual Studio para ASP.NET e ASP.NET Core. Você deve selecionar um recurso existente baseado em espaço de trabalho no com a interface do usuário de habilitação de monitoramento do Visual Studio. Selecionar Criar novo recurso no Visual Studio irá limitar a criação de um recurso de Application Insights clássico.

## <a name="creating-a-resource-automatically"></a>Criando um recurso automaticamente

### <a name="azure-cli"></a>CLI do Azure

Para acessar a visualização Application Insights CLI do Azure comandos, primeiro você precisa executar:

```azurecli
 az extension add -n application-insights
```

Se você não executar o `az extension add` comando, verá uma mensagem de erro que diz:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora você pode executar o seguinte para criar o recurso de Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Para obter a documentação completa de CLI do Azure para este comando, consulte a [documentação do CLI do Azure](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`No momento, o comando do PowerShell não dá suporte à criação de um recurso de Application insights baseado em espaço de trabalho. Para criar um recurso baseado em espaço de trabalho com o PowerShell, você pode usar os modelos de Azure Resource Manager abaixo e implantar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modificando o espaço de trabalho associado

Depois que um recurso de Application Insights baseado em espaço de trabalho tiver sido criado, você poderá modificar o espaço de trabalho Log Analytics associado.

No painel de recursos Application insights, selecione **Propriedades**  >  **alterar espaço**de trabalho  >  **log Analytics espaços de trabalho**

## <a name="next-steps"></a>Próximas etapas

* [Explorar métricas](../../azure-monitor/platform/metrics-charts.md)
* [Escrever consultas do Analytics](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
