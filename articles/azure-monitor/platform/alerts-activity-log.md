---
title: Crie, visualize e gerencie alertas de registro de atividades no Azure Monitor
description: Crie alertas de registro de atividades usando o portal Azure, um modelo do Azure Resource Manager e o Azure PowerShell.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132402"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Crie, visualize e gerencie alertas de registro de atividades usando o Azure Monitor  

## <a name="overview"></a>Visão geral

Alertas do log de atividades são os alertas ativados quando ocorre um novo evento de log de atividades que corresponde às condições especificadas no alerta.

Esses alertas são para os recursos do Azure e podem ser criados usando um modelo do Azure Resource Manager. Eles também podem ser criados, atualizados ou excluídos no portal do Azure. Normalmente, você cria alertas de registro de atividadepara receber notificações quando ocorrem alterações específicas nos recursos da sua assinatura do Azure. Os alertas são frequentemente escopo para determinados grupos de recursos ou recursos. Por exemplo, você pode querer ser notificado quando qualquer máquina virtual no grupo de recursos de exemplo **myProductionResourceGroup** for excluído. Ou, você pode querer ser notificado se quaisquer novas funções forem atribuídas a um usuário em sua assinatura.

> [!IMPORTANT]
> Os alertas sobre a notificação de saúde do serviço não podem ser criados através da interface para criação de alertas de registro de atividades. Para saber mais sobre como criar e usar notificações de saúde do serviço, consulte [Receber alertas de registro de atividades sobre notificações de saúde do serviço](alerts-activity-log-service-notifications.md).

Ao criar regras de alerta, certifique-se de:

- A assinatura no escopo não é diferente da assinatura onde o alerta é criado.
- Os critérios devem ser o nível, status, chamador, grupo de recursos, ID de recurso ou categoria de evento do tipo de recurso na qual o alerta está configurado.
- Não há nenhuma condição "anyOf" ou condições aninhadas na configuração de alerta JSON. Basicamente, apenas uma condição "allOf" é permitida sem condições adicionais de "allOf" ou "anyOf".
- Quando a categoria é "administrativa", você deve especificar pelo menos um dos critérios precedentes em seu alerta. Você não pode criar um alerta que seja ativado sempre que um evento for criado nos logs de atividades.

## <a name="azure-portal"></a>Portal do Azure

Você pode usar o portal Azure para criar e modificar as regras de alerta de registro de atividades. A experiência é integrada a um registro de atividades do Azure para garantir a criação de alertas perfeitos para eventos específicos de interesse.

### <a name="create-with-the-azure-portal"></a>Criar com o portal Azure

Use o procedimento a seguir.

1. No portal Azure, selecione**Alertas de** **Monitor** > .
2. Selecione **Nova regra de alerta** no canto superior esquerdo da janela **Alertas.**

     ![Nova regra de alerta](media/alerts-activity-log/AlertsPreviewOption.png)

     A janela **Criar regra** é exibida.

      ![Novas opções de regra de alerta](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Em **Definir condição de alerta,** forneça as seguintes informações e selecione **Feito**:

   - **Alvo de alerta:** Para visualizar e selecionar o destino do novo alerta, use **Filtro por assinatura** / **Filtro por tipo de recurso**. Selecione o grupo de recursos ou recursos na lista exibida.

     > [!NOTE]
     > 
     > Você pode selecionar apenas o recurso rastreado do [Azure Resource Manager,](../../azure-resource-manager/management/overview.md) o grupo de recursos ou uma assinatura inteira para um sinal de registro de atividades. 

     **Exibição de exemplo do destino de alerta**

     ![Selecionar o destino](media/alerts-activity-log/select-target.png)

   - Em **critérios de destino,** selecione **Adicionar critérios**. Todos os sinais disponíveis para o destino são exibidos, o que inclui aqueles de várias categorias do Registro de **Atividades**. O nome da categoria é anexado ao nome do Serviço de **Monitor.**

   - Selecione o sinal na lista exibida com as várias operações possíveis para o tipo **Log de Atividades**.

     É possível selecionar a linha do tempo do histórico de log e a lógica de alerta correspondente para esse sinal de destino:

     **Tela Adicionar critérios**

     ![Adicionar critérios](media/alerts-activity-log/add-criteria.png)

     - **Tempo de história**: Os eventos disponíveis para a operação selecionada podem ser plotados nas últimas 6, 12 ou 24 horas ou ao longo da última semana.

     - **Lógica de Alerta**:

       - **Nível do evento**: O nível de gravidade do evento: _Verbose_, _Informativo_, _Aviso_, _Erro_ou _Crítico_.
       - **Status**: O status do evento: _Iniciado,_ _Falhou_ou _Teve Sucesso_.
       - **Evento iniciado por**: Também conhecido como o chamador. O endereço de email ou o identificador do Azure Active Directory do usuário que realizou a operação.

       Este gráfico de sinal de amostra tem a lógica de alerta aplicada:

       ![Critérios selecionados](media/alerts-activity-log/criteria-selected.png)

4. Em **Definir detalhes de alerta,** forneça os seguintes detalhes:

    - **Nome da regra de alerta:** O nome da nova regra de alerta.
    - **Descrição**: A descrição da nova regra de alerta.
    - **Salvar alerta para o grupo de recursos**: Selecione o grupo de recursos onde deseja salvar essa nova regra.

5. Em **Grupo de ações**, no menu suspenso, especifique o grupo de ações que deseja atribuir à nova regra de alerta. Ou, [crie um novo grupo de ação](../../azure-monitor/platform/action-groups.md) e atribua-o à nova regra. Para criar um novo grupo, selecione **+ Novo grupo**.

6. Para habilitar as regras depois de criá-las, selecione **Sim** para a regra Ativar na opção **de criação.**
7. Selecione **Criar regra de alerta**.

    A nova regra de alerta para o registro de atividades é criada e uma mensagem de confirmação aparece no canto superior direito da janela.

    É possível habilitar, desabilitar, editar ou excluir uma regra. Saiba mais sobre como gerenciar as regras do registro de atividades.


Uma analogia simples para entender as condições sobre as quais as regras de alerta podem ser criadas em um registro de atividade satisfaz ou filtrar eventos através do [registro de atividades no portal Azure](activity-log-view.md#azure-portal). Na tela **de registro do Azure Monitor - Atividade,** você pode filtrar ou encontrar o evento necessário e, em seguida, criar um alerta usando o botão **Adicionar alerta de registro de atividade.** Em seguida, siga as etapas 4 a 7 como mostrado anteriormente.
    
 ![Adicionar alerta do registro de atividades](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Veja e gerencie no portal Azure

1. No portal Azure, selecione**Alertas de** **Monitor** > . Selecione **Gerenciar regras de alerta** no canto superior esquerdo da janela.

    ![Gerenciar regras de alerta](media/alerts-activity-log/manage-alert-rules.png)

    A lista das regras disponíveis é exibida.

2. Pesquise a regra do registro de atividades para modificar.

    ![Regras de alerta de registro de atividade de pesquisa](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Você pode usar os filtros disponíveis, _Assinatura,_ _Grupo de Recursos,_ _Recurso,_ _Tipo de Sinal_ou _Status,_ para encontrar a regra de atividade que deseja editar.

   > [!NOTE]
   > 
   > Você pode editar apenas **grupos descrição,** **target e**grupos **de ação**.

3. Selecione a regra e clique duas vezes para editar as opções de regra. Faça as alterações necessárias e selecione **Salvar**.

   ![Gerenciar regras de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Você pode ativar, desativar ou excluir uma regra. Selecione a opção apropriada na parte superior da janela depois de selecionar a regra conforme descrito na etapa 2.


## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Para criar uma regra de alerta de log de atividadeusando um modelo `microsoft.insights/activityLogAlerts`do Azure Resource Manager, você cria um recurso do tipo . Em seguida, você preencherá todas as propriedades relacionadas. Aqui está um modelo que cria uma regra de alerta de registro de atividade:

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
A amostra anterior JSON pode ser salva como, por exemplo, sampleActivityLogAlert.json para fins deste walk-through e pode ser implantada usando [o Azure Resource Manager no portal Azure](../../azure-resource-manager/templates/deploy-portal.md).

Os seguintes campos são as opções que você pode usar no modelo azure Resource Manager para os campos de condições: Observe que "Resource Health", "Advisor" e "Service Health" têm campos de propriedades extras para seus campos especiais. 
1. resourceId: O ID de recurso do recurso impactado no evento de registro de atividades em que o alerta deve ser gerado.
2. categoria: A categoria de no evento de registro de atividades. Por exemplo: Administrativo, ServiceHealth, ResourceHealth, Autoscale, Security, Recommendation, Policy.
3. chamador: O endereço de e-mail ou o identificador do Diretório Ativo do Azure do usuário que realizou a operação do evento de registro de atividades.
4. nível: Nível da atividade no evento de registro de atividade sustais no que o alerta deve ser gerado. Por exemplo: Crítico, Erro, Aviso, Informativo, Verbose.
5. operaçãoNome: O nome da operação no evento de registro de atividades. Por exemplo: Microsoft.Resources/implantações/write
6. resourceGroup: Nome do grupo de recursos para o recurso impactado no evento de registro de atividades.
7. resourceProvider: [Provedores de recursos do Azure e explicação de tipos](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0). Para obter uma lista que mapeie provedores de recursos para serviços do Azure, consulte [provedores de recursos para serviços do Azure](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0).
8. status: String descrevendo o status da operação no evento de atividade. Por exemplo: Started, In Progress, Succeeded, Failed, Active, Resolved
9. subStatus: Normalmente o código de status HTTP da chamada REST correspondente, mas também pode incluir outras strings descrevendo um substatus.   Por exemplo: OK (CÓDIGO DE STATUS HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Pedido Ruim (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Servidor Interno Erro (CÓDIGO DE STATUS HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo de Gateway (Código de Status HTTP: 504).
10. resourceType: O tipo do recurso que foi afetado pelo evento. Por exemplo: Microsoft.Resources/implantações

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
Mais detalhes sobre os campos de registro de atividades que você pode encontrar [aqui](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0).



> [!NOTE]
> Pode levar até 5 minutos para que a nova regra de alerta de registro de atividade se torne ativa.

## <a name="rest-api"></a>API REST 
A [API de registro de atividade do monitor do Azure](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) é uma API REST. É totalmente compatível com a API REST do Azure Resource Manager. Ele pode ser usado via PowerShell usando o cmdlet do Resource Manager ou o Azure CLI.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implantar o modelo de gerenciador de recursos com o PowerShell
Para usar o PowerShell para implantar o modelo de gerenciador de recursos de amostra mostrado na seção de modelo anterior [do Azure Resource Manager,](#azure-resource-manager-template) use o seguinte comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

onde a sampleActivityLogAlert.parameters.json contém os valores fornecidos para os parâmetros necessários para a criação de regras de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Use cmdlets powershell de registro de atividade

Os alertas do log de atividades têm cmdlets do PowerShell dedicados disponíveis:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): Cria um novo alerta de registro de atividades ou atualiza um alerta de registro de atividade existente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): Obtém um ou mais recursos de alerta de registro de atividades.
- [Habilitar-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): Habilita um alerta de registro de atividades existente e define suas tags.
- [Desativar-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): Desativa um alerta de registro de atividadeexistente e define suas tags.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): Remove um alerta de registro de atividades.

## <a name="azure-cli"></a>CLI do Azure

Os comandos da CLI do Azure dedicados sob o conjunto [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) estão disponíveis para gerenciar regras de alerta do log de atividades.

Para criar uma nova regra de alerta de registro de atividade, use os seguintes comandos nesta ordem:

1. [az monitora o alerta de registro de atividade sustado :](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)Crie um novo recurso de regra de alerta de registro de atividades.
1. [az monitore o escopo de alerta de registro de atividade](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): Adicione escopo para a regra de alerta de registro de atividade criada.
1. [az monitore o grupo de ação de alerta de registro de atividade](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): Adicione um grupo de ação à regra de alerta de registro de atividades.

Para recuperar um recurso de regra de alerta de log de atividade, use o az comando [az monitor az monitor alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
). Para exibir todos os recursos de regra de alerta de registro de atividade sus, use [a lista de alertas de registro de atividade do monitor az](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Os recursos da regra de alerta de registro de atividade podem ser removidos usando o az monitor de alerta de monitor de atividade do [azure](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)CLI .

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o esquema do webhook para registros de atividades](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Leia uma [visão geral dos registros de atividades](../../azure-monitor/platform/activity-log-alerts.md).
- Saiba mais sobre [grupos de ação](../../azure-monitor/platform/action-groups.md).  
- Conheça as [notificações de saúde dos serviços](../../azure-monitor/platform/service-notifications.md).
