---
title: Usar configurações de diagnóstico para coletar métricas e logs de plataforma e no Azure
description: Envie Azure Monitor logs e métricas de plataforma para Azure Monitor logs, armazenamento do Azure ou hubs de eventos do Azure usando uma configuração de diagnóstico.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: cbef0244f30a7cf14f8fea4c6a445cf0de662dc4
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737888"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure

[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo o log de atividades do Azure e os logs de recursos, fornecem informações detalhadas de diagnóstico e auditoria para recursos do Azure e a plataforma do Azure da qual dependem. As [métricas de plataforma](data-platform-metrics.md) são coletadas por padrão e normalmente armazenadas no banco de dados de métricas Azure monitor.

Este artigo fornece detalhes sobre como criar e definir configurações de diagnóstico para enviar métricas de plataforma e logs de plataforma para destinos diferentes.

> [!IMPORTANT]
> Antes de criar uma configuração de diagnóstico para coletar o log de atividades, primeiro você deve desabilitar qualquer configuração herdada. Consulte [coletar log de atividades do Azure com configurações herdadas](diagnostic-settings-legacy.md) para obter detalhes.

Cada recurso do Azure requer sua própria configuração de diagnóstico, que define os seguintes critérios:

- As categorias de logs e os dados de métrica enviados aos destinos definidos na configuração. As categorias disponíveis variam para diferentes tipos de recursos.
- Um ou mais destinos para enviar os logs. Os destinos atuais incluem workspace do Log Analytics, Hubs de Eventos e Armazenamento do Azure.

Uma única configuração de diagnóstico pode definir, no máximo, um de cada um dos destinos. Se você quiser enviar dados para mais de um tipo de destino específico (por exemplo, dois workspaces do Log Analytics diferentes), crie várias configurações. Cada recurso pode ter até cinco configurações de diagnóstico.

> [!NOTE]
> As [métricas de plataforma](metrics-supported.md) são coletadas automaticamente para [Azure monitor métricas](data-platform-metrics.md). As configurações de diagnóstico podem ser usadas para coletar métricas para determinados serviços do Azure em Azure Monitor logs para análise com outros dados de monitoramento usando [consultas de log](../log-query/log-query-overview.md) com determinadas limitações. 
>  
>  
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão. *Por exemplo*: a métrica ' IOReadBytes ' em um Blockchain pode ser explorada e grafada em um nível por nó. No entanto, quando exportadas por meio de configurações de diagnóstico, a métrica exportada representa como todos os bytes de leitura de todos os nós Além disso, devido a limitações internas, nem todas as métricas são exportáveis para Azure Monitor logs/Log Analytics. Para obter mais informações, consulte a [lista de métricas exportáveis](metrics-supported-export-diagnostic-settings.md). 
>  
>  
> Para contornar essas limitações para métricas específicas, sugerimos extraí-las manualmente usando a [API REST de métricas](https://docs.microsoft.com/rest/api/monitor/metrics/list) e importá-las em Logs de Azure monitor usando a [API do coletor de dados Azure monitor](data-collector-api.md).  

## <a name="destinations"></a>Destinos

Os logs e as métricas da plataforma podem ser enviados para os destinos na tabela a seguir. Siga cada link na tabela a seguir para obter detalhes sobre como enviar dados para esse destino.

| Destino | Descrição |
|:---|:---|
| [Espaço de trabalho do Log Analytics](resource-logs-collect-workspace.md) | Coletar logs e métricas em um espaço de trabalho Log Analytics permite analisá-los com outros dados de monitoramento coletados por Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos de Azure Monitor, como alertas e visualizações. |
| [Hubs de eventos](resource-logs-stream-event-hubs.md) | O envio de logs e métricas para hubs de eventos permite transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções do log Analytics. |
| [Conta de armazenamento do Azure](resource-logs-collect-storage.md) | O arquivamento de logs e métricas para uma conta de armazenamento do Azure é útil para auditoria, análise estática ou backup. Em comparação com os logs de Azure Monitor e um espaço de trabalho Log Analytics, o armazenamento do Azure é menos dispendioso e os logs podem ser mantidos indefinidamente. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Criar configurações de diagnóstico no portal do Azure

Você pode definir as configurações de diagnóstico no portal do Azure no menu Azure Monitor ou no menu do recurso.

1. Onde você define as configurações de diagnóstico no portal do Azure depende do recurso.

   - Para um único recurso, clique em **configurações de diagnóstico** em **Monitor** no menu do recurso.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-resource.png)

   - Para um ou mais recursos, clique em **configurações de diagnóstico** em **configurações** no menu Azure monitor e clique no recurso.

      ![Configurações de Diagnóstico](media/diagnostic-settings/menu-monitor.png)

   - Para o log de atividades, clique em **log de atividades** no menu **Azure monitor** e, em seguida, **configurações de diagnóstico**. Certifique-se de desabilitar qualquer configuração herdada para o log de atividades. Consulte [desabilitar configurações existentes](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) para obter detalhes.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em **Adicionar configuração de diagnóstico**.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-settings/add-setting.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Clique em **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou **Editar configuração** para editar uma existente. Cada configuração não pode ter mais de um de cada um dos tipos de destino.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome à sua configuração se ela ainda não tiver uma.

    ![Adicionar configuração de diagnóstico](media/diagnostic-settings/setting-new-blank.png)

4. **Detalhes da categoria (o que rotear)** – marque a caixa para cada categoria de dados que você deseja enviar para os destinos especificados mais tarde. A lista de categorias varia para cada serviço do Azure.

     - As **biometrias roteiam** as métricas de plataforma de um recurso para o repositório de logs do Azure, mas no formulário de log. Essas métricas geralmente são enviadas somente para o banco de dados de série temporal de Azure Monitor métricas. Enviá-los para o repositório de logs de Azure Monitor (que é pesquisável via Log Analytics) para integrá-los em consultas que pesquisam em outros logs. Essa opção pode não estar disponível para todos os tipos de recurso. Quando há suporte, [Azure monitor métricas com suporte](metrics-supported.md) lista as métricas coletadas para quais tipos de recursos.

       > [!NOTE]
       > Consulte limitatation para obter métricas de roteamento para Azure Monitor logs anteriormente neste artigo.  


     - **Logs** lista as diferentes categorias disponíveis dependendo do tipo de recurso. Verifique todas as categorias que você deseja rotear para um destino.

5. **Detalhes de destino** -marque a caixa para cada destino. Quando você marca cada caixa, as opções aparecem para permitir que você adicione informações adicionais.

      ![Enviar para Log Analytics ou hubs de eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -Insira a assinatura e o espaço de trabalho.  Se você não tiver um espaço de trabalho, precisará [criar um antes de continuar](../learn/quick-create-workspace.md).

    1. **Hubs de eventos** -especifique os seguintes critérios:
       - A assinatura da qual o Hub de eventos faz parte
       - O namespace do hub de eventos-se você ainda não tiver um, você precisará [criar um](../../event-hubs/event-hubs-create.md)
       - Um nome de Hub de eventos (opcional) para o qual enviar todos os dados. Se você não especificar um nome, um hub de eventos será criado para cada categoria de log. Se você estiver enviando várias categorias, talvez queira especificar um nome para limitar o número de hubs de eventos criados. Consulte [limites e cotas dos hubs de eventos do Azure](../../event-hubs/event-hubs-quotas.md) para obter detalhes.
       - Uma política de Hub de eventos (opcional) uma política define as permissões que o mecanismo de streaming tem. Para obter mais informações, consulte [eventos-hubs-recursos](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Armazenamento** -escolha a assinatura, a conta de armazenamento e a política de retenção.

        ![Enviar para o armazenamento](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere definir a política de retenção como 0 e excluir manualmente os dados do armazenamento usando um trabalho agendado para evitar uma possível confusão no futuro.
        >
        > Em primeiro lugar, se você estiver usando o armazenamento para arquivamento, geralmente desejará seus dados por mais de 365 dias. Em segundo lugar, se você escolher uma política de retenção maior que 0, a data de validade será anexada aos logs no momento do armazenamento. Você não pode alterar a data desses logs depois de armazenado.
        >
        > Por exemplo, se você definir a política de retenção para *WorkflowRuntime* como 180 dias e, em seguida, 24 horas depois defini-la como 365 dias, os logs armazenados durante essas primeiras 24 horas serão excluídos automaticamente após 180 dias, enquanto todos os logs subsequentes desse tipo serão excluídos automaticamente após 365 dias. A alteração da política de retenção mais tarde não faz com que as primeiras 24 horas de logs permaneçam por cerca de 365 dias.

6. Clique em **Save** (Salvar).

Após alguns instantes, a nova configuração aparecerá na lista de configurações desse recurso e os logs serão transmitidos para os destinos especificados à medida que novos dados de evento forem gerados. Pode levar até 15 minutos entre o momento em que um evento é emitido e quando ele [aparece em um espaço de trabalho log Analytics](data-ingestion-time.md).

## <a name="create-diagnostic-settings-using-powershell"></a>Criar configurações de diagnóstico usando o PowerShell

Use o cmdlet [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma configuração de diagnóstico com [Azure PowerShell](powershell-quickstart-samples.md). Consulte a documentação deste cmdlet para obter descrições de seus parâmetros.

> [!IMPORTANT]
> Você não pode usar esse método para o log de atividades do Azure. Em vez disso, use a [configuração criar diagnóstico no Azure monitor usando um modelo do Resource Manager](diagnostic-settings-template.md) para criar um modelo do Resource Manager e implantá-lo com o PowerShell.

Veja a seguir um exemplo de cmdlet do PowerShell para criar uma configuração de diagnóstico usando todos os três destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Criar configurações de diagnóstico usando CLI do Azure

Use o comando [AZ monitor Diagnostics-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) para criar uma configuração de diagnóstico com [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte a documentação deste comando para obter descrições de seus parâmetros.

> [!IMPORTANT]
> Você não pode usar esse método para o log de atividades do Azure. Em vez disso, use a [configuração criar diagnóstico no Azure monitor usando um modelo do Resource Manager](diagnostic-settings-template.md) para criar um modelo do Resource Manager e implantá-lo com a CLI.

Veja a seguir um exemplo de comando da CLI para criar uma configuração de diagnóstico usando todos os três destinos.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Definir configurações de diagnóstico usando a API REST

Consulte [configurações de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para criar ou atualizar as configurações de diagnóstico usando a [API REST do Azure monitor](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Definir configurações de diagnóstico usando o modelo do Resource Manager

Consulte [criar configuração de diagnóstico no Azure monitor usando um modelo do Resource Manager](diagnostic-settings-template.md) para criar ou atualizar configurações de diagnóstico com um modelo do Resource Manager.

## <a name="next-steps"></a>Próximas etapas

- [Leia mais sobre os logs da plataforma Azure](platform-logs-overview.md)
