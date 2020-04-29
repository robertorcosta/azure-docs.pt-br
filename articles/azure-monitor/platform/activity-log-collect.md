---
title: Coletar e analisar o log de atividades do Azure no Azure Monitor
description: Colete o log de atividades do Azure em logs de Azure Monitor e use a solução de monitoramento para analisar e pesquisar o log de atividades do Azure em todas as suas assinaturas do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382863"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Coletar e analisar o log de atividades do Azure no Azure Monitor
O [log de atividades do Azure](platform-logs-overview.md) é um [log de plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. Embora seja possível exibir o log de atividades no portal do Azure, você deve configurá-lo para enviar para um espaço de trabalho do Log Analytics para habilitar recursos adicionais do Azure Monitor. Este artigo descreve como executar essa configuração e como enviar o log de atividades para o armazenamento do Azure e os hubs de eventos.

Coletar o log de atividades em um espaço de trabalho Log Analytics oferece as seguintes vantagens:

- Nenhuma ingestão de dados ou cobrança de retenção de dados para dados de log de atividades armazenados em um espaço de trabalho Log Analytics.
- Correlacione os dados do log de atividades com outros dados de monitoramento coletados pelo Azure Monitor.
- Use consultas de log para executar uma análise complexa e obter informações aprofundadas sobre entradas do log de atividades.
- Use alertas de log com entradas de atividade, permitindo uma lógica de alerta mais complexa.
- Armazene entradas do log de atividades por mais de 90 dias.
- Consolide entradas de log de várias assinaturas e locatários do Azure em um único local para análise em conjunto.

> [!IMPORTANT]
> A coleta de logs entre locatários requer o [Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Coletando log de atividades
O log de atividades é coletado automaticamente para [exibição no portal do Azure](activity-log-view.md). Para coletá-lo em um espaço de trabalho Log Analytics ou enviá-lo para o armazenamento do Azure ou hubs de eventos, crie uma [configuração de diagnóstico](diagnostic-settings.md). Esse é o mesmo método usado pelos logs de recursos, tornando-o consistente para todos os [logs da plataforma](platform-logs-overview.md).  

Para criar uma configuração de diagnóstico para o log de atividades, selecione **configurações de diagnóstico** no menu **log de atividades** em Azure monitor. Consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](diagnostic-settings.md) para obter detalhes sobre como criar a configuração. Consulte [categorias no log de atividades](activity-log-view.md#categories-in-the-activity-log) para obter uma descrição das categorias que você pode filtrar. Se você tiver configurações herdadas, certifique-se de desabilitá-las antes de criar uma configuração de diagnóstico. Ter ambos habilitados pode resultar em dados duplicados.

![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> No momento, você só pode criar uma configuração de diagnóstico de nível de assinatura usando o portal do Azure e um modelo do Resource Manager. 


## <a name="legacy-settings"></a>Configurações herdadas 
Embora as configurações de diagnóstico sejam o método preferencial para enviar o log de atividades para destinos diferentes, os métodos herdados continuarão a funcionar se você não optar por substituir por uma configuração de diagnóstico. As configurações de diagnóstico têm as seguintes vantagens em relação aos métodos herdados, e é recomendável que você atualize sua configuração:

- Método consistente para coletar todos os logs de plataforma.
- Coletar log de atividades entre várias assinaturas e locatários.
- Filtrar coleção para coletar somente logs para categorias específicas.
- Coletar todas as categorias de log de atividades. Algumas categorias não são coletadas usando o método herdado.
- Latência mais rápida para a ingestão de logs. O método anterior tem aproximadamente 15 minutos de latência enquanto as configurações de diagnóstico adicionam apenas cerca de 1 minuto.



### <a name="log-profiles"></a>Perfis de log
Perfis de log são o método herdado para enviar o log de atividades para o armazenamento do Azure ou hubs de eventos. Use o procedimento a seguir para continuar trabalhando com um perfil de log ou para desabilitá-lo em preparação para a migração para uma configuração de diagnóstico.

1. No menu **Azure monitor** na portal do Azure, selecione log de **atividades**.
3. Clique em **configurações de diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique na faixa roxa para a experiência herdada.

    ![Experiência herdada](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
O método herdado para coletar o log de atividades em um espaço de trabalho Log Analytics está conectando o log na configuração do espaço de trabalho. 

1. No menu **log Analytics espaços de trabalho** na portal do Azure, selecione o espaço de trabalho para coletar o log de atividades.
1. Na seção **fontes de dados de espaço de trabalho** do menu do espaço de trabalho, selecione **log de atividades do Azure**.
1. Clique na assinatura que você deseja conectar.

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. Clique em **conectar** para conectar o log de atividades na assinatura ao espaço de trabalho selecionado. Se a assinatura já estiver conectada a outro espaço de trabalho, clique em **Desconectar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-collect/connect-workspace.png)


Para desabilitar a configuração, execute o mesmo procedimento e clique em **Desconectar** para remover a assinatura do espaço de trabalho.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analisar o log de atividades no espaço de trabalho Log Analytics
Quando você conecta um log de atividades a um espaço de trabalho Log Analytics, as entradas são gravadas no espaço de trabalho em uma tabela chamada *AzureActivity* que você pode recuperar com uma [consulta de log](../log-query/log-query-overview.md). A estrutura dessa tabela varia dependendo da [categoria da entrada de log](activity-log-view.md#categories-in-the-activity-log). Consulte [esquema de eventos do log de atividades do Azure](activity-log-schema.md) para obter uma descrição de cada categoria.


### <a name="data-structure-changes"></a>Alterações na estrutura de dados
As configurações de diagnóstico coletam os mesmos dados que o método herdado usado para coletar o log de atividades com algumas alterações na estrutura da tabela *AzureActivity* .

As colunas na tabela a seguir foram preteridas no esquema atualizado. Eles ainda existem no *AzureActivity* , mas não terão dados. A substituição dessas colunas não é nova, mas elas contêm os mesmos dados que a coluna preterida. Elas estão em um formato diferente, portanto, talvez seja necessário modificar as consultas de log que as utilizam. 

| Coluna preterida | Coluna de substituição |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNamevalue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Em alguns casos, os valores nessas colunas podem estar em letras maiúsculas. Se você tiver uma consulta que inclua essas colunas, deverá usar o [operador = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) para fazer uma comparação sem diferenciação de maiúsculas e minúsculas.

A seguinte coluna foi adicionada ao *AzureActivity* no esquema atualizado:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitoramento de análise de logs de atividades
A solução de monitoramento de Log Analytics do Azure será preterida em breve e substituída por uma pasta de trabalho usando o esquema atualizado no espaço de trabalho Log Analytics. Você ainda poderá usar a solução se ela já estiver habilitada, mas ela só poderá ser usada se você estiver coletando o log de atividades usando configurações herdadas. 



### <a name="use-the-solution"></a>Usar a solução
As soluções de monitoramento são acessadas no menu **monitorar** na portal do Azure. Selecione **mais** na seção **insights** para abrir a página **visão geral** com os blocos de solução. O bloco **logs de atividade do Azure** exibe uma contagem do número de registros **AzureActivity** em seu espaço de trabalho.

![Bloco de logs de atividade do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no bloco **logs de atividade do Azure** para abrir a exibição **logs de atividade do Azure** . A exibição inclui as partes de visualização na tabela a seguir. Cada parte lista até 10 itens que correspondem aos critérios das partes para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver todos** na parte inferior da parte.

![Painel Logs de Atividade do Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Habilitar a solução para novas assinaturas
Em breve, você não poderá mais adicionar a solução de análise de logs de atividades à sua assinatura usando o portal do Azure. Você pode adicioná-lo usando o procedimento a seguir com um modelo do Resource Manager. 

1. Copie o JSON a seguir em um arquivo chamado *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Implante o modelo usando os seguintes comandos do PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [log de atividades](platform-logs-overview.md).
- Saiba mais sobre a [plataforma de dados Azure monitor](data-platform.md).
- Use [consultas de log](../log-query/log-query-overview.md) para exibir informações detalhadas do seu log de atividades.
