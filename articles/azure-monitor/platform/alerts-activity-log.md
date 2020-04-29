---
title: Criar, exibir e gerenciar alertas do log de atividades no Azure Monitor
description: Crie alertas do log de atividades usando o portal do Azure, um modelo de Azure Resource Manager e Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132402"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Criar, exibir e gerenciar alertas do log de atividades usando Azure Monitor  

## <a name="overview"></a>Visão geral

Alertas do log de atividades são os alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta.

Esses alertas são para recursos do Azure e podem ser criados usando um modelo de Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Normalmente, você cria alertas do log de atividades para receber notificações quando ocorrem alterações específicas nos recursos em sua assinatura do Azure. Os alertas geralmente são delimitados a recursos ou grupos de recursos específicos. Por exemplo, talvez você queira ser notificado quando qualquer máquina virtual no grupo de recursos de exemplo **myProductionResourceGroup** for excluída. Ou talvez você queira ser notificado se alguma nova função for atribuída a um usuário em sua assinatura.

> [!IMPORTANT]
> Alertas sobre a notificação de integridade do serviço não podem ser criados por meio da interface para a criação do alerta do log de atividades. Para saber mais sobre como criar e usar notificações de integridade do serviço, consulte [receber alertas do log de atividades em notificações de integridade do serviço](alerts-activity-log-service-notifications.md).

Ao criar regras de alerta, verifique o seguinte:

- A assinatura no escopo não é diferente da assinatura em que o alerta é criado.
- Os critérios devem ser o nível, o status, o chamador, o grupo de recursos, a ID do recurso ou a categoria de evento do tipo de recurso no qual o alerta está configurado.
- Não há nenhuma condição "anyOf" ou condições aninhadas no JSON de configuração de alerta. Basicamente, apenas uma condição "allOf" é permitida sem mais condições "allOf" ou "anyOf".
- Quando a categoria é "administrativa", você deve especificar pelo menos um dos critérios anteriores em seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.

## <a name="azure-portal"></a>Portal do Azure

Você pode usar o portal do Azure para criar e modificar as regras de alerta do log de atividades. A experiência é integrada a um log de atividades do Azure para garantir a criação de alertas contínuos para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal do Azure

Use o procedimento a seguir.

1. Na portal do Azure, selecione **monitorar** > **alertas**.
2. Selecione **nova regra de alerta** no canto superior esquerdo da janela **alertas** .

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     A janela **Criar regra** é exibida.

      ![Novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Em **definir condição de alerta**, forneça as seguintes informações e selecione **concluído**:

   - **Destino do alerta:** Para exibir e selecionar o destino do novo alerta, use **Filtrar por filtro de assinatura** / **por tipo de recurso**. Selecione o recurso ou grupo de recursos na lista exibida.

     > [!NOTE]
     > 
     > Você pode selecionar apenas [Azure Resource Manager](../../azure-resource-manager/management/overview.md) recurso controlado, grupo de recursos ou uma assinatura inteira para um sinal do log de atividades. 

     **Exibição de exemplo do destino de alerta**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - Em **critérios de destino**, selecione **Adicionar critérios**. Todos os sinais disponíveis para o destino são exibidos, o que os inclui de várias categorias de **log de atividades**. O nome da categoria é anexado ao nome do **serviço monitor** .

   - Selecione o sinal na lista exibida com as várias operações possíveis para o tipo **Log de Atividades**.

     É possível selecionar a linha do tempo do histórico de log e a lógica de alerta correspondente para esse sinal de destino:

     **Tela Adicionar critérios**

     ![Adicionar critérios](media/alerts-activity-log/add-criteria.png)

     - **Tempo do histórico**: os eventos disponíveis para a operação selecionada podem ser plotados nas últimas 6, 12 ou 24 horas ou na última semana.

     - **Lógica de Alerta**:

       - **Nível de evento**: o nível de severidade do evento: _detalhado_, _informativo_, _aviso_, _erro_ou _crítico_.
       - **Status**: o status do evento: _iniciado_, _com falha_ou com _êxito_.
       - **Evento iniciado por**: também conhecido como chamador. O endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

       Este grafo de sinal de exemplo tem a lógica de alerta aplicada:

       ![Critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Em **definir detalhes do alerta**, forneça os seguintes detalhes:

    - **Nome da regra de alerta**: o nome da nova regra de alerta.
    - **Descrição**: a descrição da nova regra de alerta.
    - **Salvar alerta no grupo de recursos**: selecione o grupo de recursos no qual você deseja salvar essa nova regra.

5. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Ou [crie um novo grupo de ação](../../azure-monitor/platform/action-groups.md) e atribua-o à nova regra. Para criar um novo grupo, selecione **+ novo grupo**.

6. Para habilitar as regras depois de criá-las, selecione **Sim** para a opção **habilitar regra na criação** .
7. Selecione **Criar regra de alerta**.

    A nova regra de alerta para o log de atividades é criada e uma mensagem de confirmação é exibida no canto superior direito da janela.

    É possível habilitar, desabilitar, editar ou excluir uma regra. Saiba mais sobre como gerenciar regras de log de atividades.


Uma analogia simples para entender as condições em que as regras de alerta podem ser criadas em um log de atividades é explorar ou filtrar eventos por meio do [log de atividades no portal do Azure](activity-log-view.md#azure-portal). Na tela **log de Azure monitor de atividades** , você pode filtrar ou localizar o evento necessário e, em seguida, criar um alerta usando o botão de **alerta Adicionar log de atividades** . Em seguida, siga as etapas 4 a 7, conforme mostrado anteriormente.
    
 ![Adicionar alerta do log de atividades](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Exibir e gerenciar no portal do Azure

1. Na portal do Azure, selecione **monitorar** > **alertas**. Selecione **gerenciar regras de alerta** no canto superior esquerdo da janela.

    ![Gerenciar regras de alerta](media/alerts-activity-log/manage-alert-rules.png)

    A lista das regras disponíveis é exibida.

2. Procure a regra do log de atividades a ser modificada.

    ![Regras de alerta do log de atividades de pesquisa](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Você pode usar os filtros, a _assinatura_, _o grupo de recursos_, o _recurso_, o _tipo de sinal_ou o _status_disponíveis para localizar a regra de atividade que deseja editar.

   > [!NOTE]
   > 
   > Você pode editar somente a **Descrição**, os **critérios de destino**e os grupos de **ação**.

3. Selecione a regra e clique duas vezes para editar as opções de regra. Faça as alterações necessárias e, em seguida, selecione **salvar**.

   ![Gerenciar regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Você pode habilitar, desabilitar ou excluir uma regra. Selecione a opção apropriada na parte superior da janela depois de selecionar a regra, conforme descrito na etapa 2.


## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Para criar uma regra de alerta do log de atividades usando um modelo de Azure Resource Manager, você cria um recurso `microsoft.insights/activityLogAlerts`do tipo. Em seguida, você preencherá todas as propriedades relacionadas. Aqui está um modelo que cria uma regra de alerta do log de atividades:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
O JSON de exemplo anterior pode ser salvo como, por exemplo, sampleActivityLogAlert. JSON para fins deste passo a passo e pode ser implantado usando [Azure Resource Manager no portal do Azure](../../azure-resource-manager/templates/deploy-portal.md).

Os campos a seguir são as opções que você pode usar no modelo de Azure Resource Manager para os campos de condições: Observe que "Resource Health", "Supervisor" e "integridade do serviço" têm campos de propriedades extras para seus campos especiais. 
1. ResourceId: a ID do recurso afetado no evento do log de atividades no qual o alerta deve ser gerado.
2. Categoria: a categoria de no evento do log de atividades. Por exemplo: administrativo, imhealth, ResourceHealth, autoescala, segurança, recomendação, política.
3. chamador: o endereço de email ou o identificador de Azure Active Directory do usuário que realizou a operação do evento do log de atividades.
4. nível: nível da atividade no evento do log de atividades no qual o alerta deve ser gerado. Por exemplo: crítico, erro, aviso, informativo, detalhado.
5. operationName: o nome da operação no evento do log de atividades. Por exemplo: Microsoft. Resources/Implantations/Write
6. resourcegroup: o nome do grupo de recursos para o recurso afetado no evento do log de atividades.
7. resourceprovider: [explicação de tipos e provedores de recursos do Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Para obter uma lista que mapeia os provedores de recursos para os serviços do Azure, consulte [provedores de recursos para serviços do Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: cadeia de caracteres que descreve o status da operação no evento de atividade. Por exemplo: iniciado, em andamento, com êxito, com falha, ativo, resolvido
9. substatus: geralmente o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevem um substatus.   Por exemplo: OK (código de status HTTP: 200), criado (código de status HTTP: 201), aceito (código de status HTTP: 202), nenhum conteúdo (código de status HTTP: 204), solicitação inválida (código de status HTTP: 400), não encontrado (código de status HTTP: 404), conflito (código de status HTTP: 409), erro interno do servidor (código de status http: 500), serviço não disponível (código de status HTTP: 503), tempo limite do gateway (código de status
10. resourceType: o tipo do recurso que foi afetado pelo evento. Por exemplo: Microsoft. Resources/Implantations

Por exemplo:

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
Mais detalhes sobre os campos do log de atividades que você pode encontrar [aqui](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Pode levar até 5 minutos para que a nova regra de alerta do log de atividades fique ativa.

## <a name="rest-api"></a>API REST 
A [API de alertas do log de atividades Azure monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma API REST. Ele é totalmente compatível com a API REST do Azure Resource Manager. Ele pode ser usado por meio do PowerShell usando o cmdlet do Gerenciador de recursos ou o CLI do Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implantar o modelo do Resource Manager com o PowerShell
Para usar o PowerShell para implantar o modelo do Resource Manager de exemplo mostrado na seção [modelo de Azure Resource Manager](#azure-resource-manager-template) anterior, use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

em que sampleActivityLogAlert. Parameters. JSON contém os valores fornecidos para os parâmetros necessários para a criação da regra de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Usar cmdlets do PowerShell do log de atividades

Os alertas do log de atividades têm cmdlets do PowerShell dedicados disponíveis:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): cria um novo alerta do log de atividades ou atualiza um alerta do log de atividades existente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Obtém um ou mais recursos de alerta do log de atividades.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): habilita um alerta do log de atividades existente e define suas marcas.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): desabilita um alerta do log de atividades existente e define suas marcas.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Remove um alerta do log de atividades.

## <a name="azure-cli"></a>CLI do Azure

Os comandos da CLI do Azure dedicados sob o conjunto [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) estão disponíveis para gerenciar regras de alerta do log de atividades.

Para criar uma nova regra de alerta do log de atividades, use os seguintes comandos nesta ordem:

1. [AZ monitor atividade – alerta de log criar](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): criar um novo recurso de regra de alerta do log de atividades.
1. [AZ monitor atividade – escopo do alerta do log](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Adicionar escopo para a regra de alerta do log de atividades criado.
1. [AZ monitor Activity – alerta do log de ações-grupo](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): adicionar um grupo de ação à regra de alerta do log de atividades.

Para recuperar um recurso de regra de alerta do log de atividades, use o comando CLI do Azure [AZ monitor atividade-log Alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Para exibir todos os recursos de regra de alerta do log de atividades em um grupo de recursos, use a [lista de alertas AZ monitor Activity-log](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Os recursos da regra de alerta do log de atividades podem ser removidos usando o comando CLI do Azure [AZ monitor atividade-log Delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [esquema de webhook para logs de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leia uma [visão geral dos logs de atividades](../../azure-monitor/platform/activity-log-alerts.md).
- Saiba mais sobre [grupos de ações](../../azure-monitor/platform/action-groups.md).  
- Saiba mais sobre as [notificações de integridade do serviço](../../azure-monitor/platform/service-notifications.md).
