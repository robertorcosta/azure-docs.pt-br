---
title: Coletar e analisar o registro de atividades do Azure no Monitor Do Azure
description: Colete o Log de Atividades do Azure no Azure Monitor Logs e use a solução de monitoramento para analisar e pesquisar o registro de atividades do Azure em todas as suas assinaturas do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/24/2020
ms.openlocfilehash: 4265f6050b237cb40afeddfc228ade9be06be039
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396771"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Coletar e analisar o registro de atividades do Azure no Monitor do Azure
O [registro de atividades do Azure](platform-logs-overview.md) é um log de [plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. Embora você possa visualizar o login de atividade no portal Azure, você deve configurá-lo para enviar a um espaço de trabalho do Log Analytics para habilitar recursos adicionais do Azure Monitor. Este artigo descreve como executar essa configuração e como enviar o registro de atividades para os hubs de armazenamento e eventos do Azure.

A coleta do Registro de Atividades em um espaço de trabalho do Log Analytics oferece as seguintes vantagens:

- Nenhuma inserção de dados ou taxa de retenção de dados para dados de registro de atividade armazenados em um espaço de trabalho do Log Analytics.
- Correlacionar dados de registro de atividade com outros dados de monitoramento coletados pelo Azure Monitor.
- Use consultas de log para realizar análises complexas e obter insights profundos sobre entradas do Registro de Atividades.
- Use alertas de log com entradas de atividade permitindo uma lógica de alerta mais complexa.
- Entradas de registro de atividade da loja por mais de 90 dias.
- Consolide entradas de log de várias assinaturas e inquilinos do Azure em um único local para análise em conjunto.



## <a name="collecting-activity-log"></a>Coleta de registro de atividades
O registro Atividade é coletado automaticamente para [visualização no portal Azure](activity-log-view.md). Para recolhê-lo em um espaço de trabalho do Log Analytics ou enviá-lo para o armazenamento do Azure ou hubs de eventos, crie uma [configuração de diagnóstico](diagnostic-settings.md). Este é o mesmo método usado pelos logs de recursos, tornando-o consistente para todos os [logs de plataforma](platform-logs-overview.md).  

Para criar uma configuração de diagnóstico para o registro atividade, selecione Configurações de diagnóstico no menu **de registro de atividades** no Monitor Azure. **Diagnostic settings** Consulte [Criar configuração de diagnóstico para coletar registros e métricas da plataforma no Azure](diagnostic-settings.md) para obter detalhes sobre a criação da configuração. Consulte [Categorias no registro de atividades](activity-log-view.md#categories-in-the-activity-log) para obter uma descrição das categorias que você pode filtrar. Se você tiver alguma configuração de legado, certifique-se de desativá-las antes de criar uma configuração de diagnóstico. Ter ambos habilitados pode resultar em dados duplicados.

![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Atualmente, você só pode criar uma configuração de diagnóstico de nível de assinatura usando o portal Azure e um modelo de Gerenciador de recursos. 


## <a name="legacy-settings"></a>Configurações do legado 
Embora as configurações de diagnóstico sejam o método preferido para enviar o registro de atividade para diferentes destinos, os métodos legados continuarão a funcionar se você não optar por substituir por uma configuração de diagnóstico. As configurações de diagnóstico têm as seguintes vantagens em relação aos métodos legados, e é recomendável que você atualize sua configuração:

- Método consistente para coletar todos os registros da plataforma.
- Coletar registro de atividades em várias assinaturas e inquilinos.
- Coleta de filtros para coletar apenas logs para categorias específicas.
- Colete todas as categorias de registro de atividades. Algumas categorias não são coletadas usando o método legado.
- Latência mais rápida para ingestão de troncos. O método anterior tem cerca de 15 minutos de latência, enquanto as configurações de diagnóstico adicionam apenas cerca de 1 minuto.



### <a name="log-profiles"></a>Perfis de log
Os perfis de log são o método legado para enviar o registro de atividades para os hubs de armazenamento ou eventos do Azure. Use o procedimento a seguir para continuar trabalhando com um perfil de registro ou para desativá-lo em preparação para migrar para uma configuração de diagnóstico.

1. No menu Monitor do **Azure** no portal Azure, selecione **Registro de atividades**.
3. Clique **em Configurações de diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência de legado](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
O método legado para coletar o log de atividade em um espaço de trabalho do Log Analytics está conectando o log na configuração do espaço de trabalho. 

1. No menu de espaços de trabalho do Log Analytics no portal Azure, selecione o espaço de trabalho para coletar o Registro de **atividades.**
1. Na seção Fontes de dados do espaço de **trabalho** do menu do espaço de trabalho, selecione O log de atividade **do Azure**.
1. Clique na assinatura que deseja conectar.

    ![Workspaces](media/activity-log-collect/workspaces.png)

1. Clique **em Conectar** para conectar o login atividade na assinatura do espaço de trabalho selecionado. Se a assinatura já estiver conectada a outro espaço de trabalho, clique **em Desconectar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-collect/connect-workspace.png)


Para desativar a configuração, execute o mesmo procedimento e clique **em Desconectar** para remover a assinatura do espaço de trabalho.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analisar o log de atividade no espaço de trabalho do Log Analytics
Quando você conecta um registro de atividades a um espaço de trabalho do Log Analytics, as entradas serão escritas no espaço de trabalho em uma tabela chamada *AzureActivity* que você pode recuperar com uma [consulta de log](../log-query/log-query-overview.md). A estrutura desta tabela varia dependendo da [categoria de entrada de registro](activity-log-view.md#categories-in-the-activity-log). Consulte [o esquema de eventos do Azure Activity Log](activity-log-schema.md) para obter uma descrição de cada categoria.


### <a name="data-structure-changes"></a>Mudanças na estrutura de dados
As configurações de diagnóstico coletam os mesmos dados do método legado usado para coletar o registro de atividades com algumas alterações na estrutura da tabela *AzureActivity.*

As colunas na tabela a seguir foram preteridas no esquema atualizado. Eles ainda existem no *AzureActivity,* mas não terão dados. A substituição dessas colunas não é nova, mas contém os mesmos dados da coluna depreciada. Eles estão em um formato diferente, então você pode precisar modificar consultas de log que as usam. 

| Coluna depreciada | Coluna de substituição |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| Substatus da atividade | AtividadeSubstatusValor |
| OperationName     | OperaçãoValor de Nome     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> Em alguns casos, os valores nestas colunas podem estar em todas as maiúsculas. Se você tiver uma consulta que inclua essas colunas, você deve usar o [operador =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) para fazer uma comparação insensível do caso.

A seguinte coluna foi adicionada ao *AzureActivity* no esquema atualizado:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitoramento do Activity Logs Analytics
A solução de monitoramento do Azure Log Analytics será depreciada em breve e substituída por uma pasta de trabalho usando o esquema atualizado no espaço de trabalho do Log Analytics. Você ainda pode usar a solução se já a tiver ativada, mas ela só pode ser usada se você estiver coletando o registro Atividade usando configurações de legado. 



### <a name="use-the-solution"></a>Usar a solução
As soluções de monitoramento são acessadas a partir do menu **Monitor** no portal Azure. Selecione **Mais** na seção **'Insights'** para abrir a página **Visão geral** com os ladrilhos da solução. O **azulejo Azure Activity Logs** exibe uma contagem do número de registros do **AzureActivity** em seu espaço de trabalho.

![Bloco de logs de atividade do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no **bloco Azure Activity Logs** para abrir a exibição **Logs de atividade do Azure.** A visualização inclui as partes de visualização na tabela a seguir. Cada peça lista até 10 itens que correspondem aos critérios das peças para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver tudo** na parte inferior da peça.

![Painel Logs de Atividade do Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Habilite a solução para novas assinaturas
Em breve, você não poderá mais adicionar a solução Activity Logs Analytics à sua assinatura usando o portal Azure. Você pode adicioná-lo usando o seguinte procedimento com um modelo de gerenciador de recursos. 

1. Copie o json a seguir em um arquivo chamado *ActivityLogTemplate*.json.

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

2. Implante o modelo usando os seguintes comandos PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Registro de Atividades](platform-logs-overview.md).
- Saiba mais sobre a [plataforma de dados do Azure Monitor](data-platform.md).
- Use [consultas de log](../log-query/log-query-overview.md) para exibir informações detalhadas do seu Registro de Atividades.
