---
title: Use configurações de diagnóstico para coletar métricas e logs da plataforma e no Azure
description: Envie métricas e logs da plataforma do Azure Monitor para logs do Monitor Do Azure, armazenamento Azure ou Hubs de Eventos Azure usando uma configuração de diagnóstico.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681234"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Crie configuração de diagnóstico para coletar registros de recursos e métricas no Azure

[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo o registro de atividades do Azure e os registros de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem. [As métricas da plataforma](data-platform-metrics.md) são coletadas por padrão e normalmente armazenadas no banco de dados de métricas do Azure Monitor.

Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar métricas da plataforma e logs de plataforma para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma configuração de diagnóstico para coletar o registro atividade, você deve primeiro desativar qualquer configuração legado. Consulte [O registro de atividades do Collect Azure com as configurações do legado](diagnostic-settings-legacy.md) para obter detalhes.

Cada recurso do Azure requer sua própria configuração de diagnóstico, que define os seguintes critérios:

- As categorias de logs e os dados de métrica enviados aos destinos definidos na configuração. As categorias disponíveis variam para diferentes tipos de recursos.
- Um ou mais destinos para enviar os logs. Os destinos atuais incluem workspace do Log Analytics, Hubs de Eventos e Armazenamento do Azure.

Uma única configuração de diagnóstico pode definir não mais do que um de cada um dos destinos. Se você quiser enviar dados para mais de um tipo de destino específico (por exemplo, dois workspaces do Log Analytics diferentes), crie várias configurações. Cada recurso pode ter até cinco configurações de diagnóstico.

> [!NOTE]
> [As métricas da plataforma](metrics-supported.md) são coletadas automaticamente no [Azure Monitor Metrics](data-platform-metrics.md). As configurações de diagnóstico podem ser usadas para coletar métricas de certos serviços do Azure no Azure Monitor Logs para análise com outros dados de monitoramento usando [consultas de log](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinos

Os registros e métricas da plataforma podem ser enviados para os destinos na tabela a seguir. Siga cada link na tabela a seguir para obter detalhes sobre o envio de dados para esse destino.

| Destino | Descrição |
|:---|:---|
| [Espaço de trabalho do Log Analytics](resource-logs-collect-workspace.md) | A coleta de logs e métricas em um espaço de trabalho do Log Analytics permite analisá-los com outros dados de monitoramento coletados pelo Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos do Azure Monitor, como alertas e visualizações. |
| [Hubs de eventos](resource-logs-stream-event-hubs.md) | O envio de logs e métricas para o Event Hubs permite transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise de log. |
| [Conta de armazenamento do Azure](resource-logs-collect-storage.md) | Arquivar registros e métricas em uma conta de armazenamento Do Zure é útil para auditoria, análise estática ou backup. Comparado com o Azure Monitor Logs e um espaço de trabalho do Log Analytics, o armazenamento do Azure é menos caro e os registros podem ser mantidos lá indefinidamente. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Crie configurações de diagnóstico no portal Azure

Você pode configurar as configurações de diagnóstico no portal Azure no menu do Azure Monitor ou no menu do recurso.

1. Onde você configura as configurações de diagnóstico no portal Azure depende do recurso.

   - Para obter um único recurso, clique em **Configurações de diagnóstico** em **Monitor** no menu do recurso.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-resource.png)

   - Para obter um ou mais recursos, clique **em** **Configurações de diagnóstico** no menu Monitor do Azure e clique no recurso.

      ![Configurações de Diagnóstico](media/diagnostic-settings/menu-monitor.png)

   - Para o registro atividade, clique em Entrar em **atividade** no menu **Monitor do Azure** e, em seguida, **configurações de Diagnóstico**. Certifique-se de desativar qualquer configuração legado para o registro de atividades. Consulte [Desativar as configurações existentes](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) para obter detalhes.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em **Adicionar configuração de diagnóstico**.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-settings/add-setting.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Clique em **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou Editar a **configuração** para editar uma já existente. Cada configuração não pode ter mais do que um dos tipos de destino.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome se ele ainda não tiver um.

    ![Adicionar configuração de diagnóstico](media/diagnostic-settings/setting-new-blank.png)

4. **Detalhes da categoria (o que fazer rota)** - Verifique a caixa para cada categoria de dados que deseja enviar para destinos especificados posteriormente. A lista de categorias varia para cada serviço do Azure.

     - **A AllMetrics** encaminha as métricas da plataforma de um recurso para a loja Azure Logs, mas em formulário de log. Essas métricas geralmente são enviadas apenas para o banco de dados de métricas do Azure Monitor. Enviando-os para a loja Azure Monitor Logs (que pode ser pesquisada via Log Analytics) você os integra em consultas que pesquisam em outros logs. Esta opção pode não estar disponível para todos os tipos de recursos. Quando é suportado, [as métricas suportadas pelo Azure Monitor](metrics-supported.md) listam quais métricas são coletadas para quais tipos de recursos.

       > [!NOTE]
       > Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
       >
       > *Por exemplo:* A métrica 'IOReadBytes' em um Blockchain pode ser explorada e mapeada em um nível por nó. No entanto, quando exportado através de configurações diagnósticas, a métrica exportada representa como todos os bytes lidos para todos os nós.

     - **Os logs** listam as diferentes categorias disponíveis dependendo do tipo de recurso. Verifique todas as categorias que você gostaria de encaminhar para um destino.

5. **Detalhes do destino** - Verifique a caixa de cada destino. Quando você verifica cada caixa, as opções parecem permitir que você adicione informações adicionais.

      ![Enviar para log analytics ou hubs de eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - Digite a assinatura e o espaço de trabalho.  Se você não tem um espaço de trabalho, você precisa [criar um antes de prosseguir](../learn/quick-create-workspace.md).

    1. **Hubs de eventos** - Especifique os seguintes critérios:
       - A assinatura da qual o hub de eventos faz parte
       - O espaço de nome do hub event - Se você ainda não tiver um, você precisará [criar um](../../event-hubs/event-hubs-create.md)
       - Nome do hub event (opcional) para enviar todos os dados. Se você não especificar um nome, um hub de eventos será criado para cada categoria de log. Se você estiver enviando várias categorias, você pode querer especificar um nome para limitar o número de hubs de eventos criados. Consulte [as cotas e limites do Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para obter detalhes.
       - Uma diretiva event hub (opcional) Uma política define as permissões que o mecanismo de streaming possui. Para obter mais informações, consulte [Event-hubs-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Armazenamento** - Escolha a política de assinatura, armazenamento e retenção.

        ![Enviar para armazenamento](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere definir a política de retenção para 0 e excluir manualmente seus dados do armazenamento usando um trabalho programado para evitar possíveis confusões no futuro.
        >
        > Primeiro, se você estiver usando o armazenamento para arquivamento, você geralmente quer seus dados por mais de 365 dias. Em segundo lugar, se você escolher uma política de retenção maior que 0, a data de validade será anexada aos logs no momento do armazenamento. Você não pode alterar a data para esses registros uma vez armazenados.
        >
        > Por exemplo, se você definir a política de retenção para *WorkflowRuntime* para 180 dias e, em seguida, 24 horas depois defini-la para 365 dias, os logs armazenados durante essas primeiras 24 horas serão automaticamente excluídos após 180 dias, enquanto todos os logs subseqüentes desse tipo serão automaticamente excluídos após 365 dias. Mudar a política de retenção mais tarde não faz com que as primeiras 24 horas de logs permaneçam por 365 dias.

6. Clique em **Save** (Salvar).

Após alguns momentos, a nova configuração aparece em sua lista de configurações para este recurso e os logs são transmitidos para os destinos especificados à medida que novos dados de evento são gerados. Pode levar até 15 minutos entre quando um evento é emitido e quando ele [aparece em um espaço de trabalho log analytics](data-ingestion-time.md).

## <a name="create-diagnostic-settings-using-powershell"></a>Crie configurações de diagnóstico usando o PowerShell

Use o [cmdlet Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) para criar uma configuração de diagnóstico com [o Azure PowerShell](powershell-quickstart-samples.md). Consulte a documentação deste cmdlet para descrições de seus parâmetros.

> [!IMPORTANT]
> Não é possível usar este método para o registro de atividades do Azure. Em vez disso, use [Criar configuração de diagnóstico no Azure Monitor usando um modelo de Gerenciador de recursos](diagnostic-settings-template.md) para criar um modelo de Gerenciador de recursos e implantá-lo com o PowerShell.

A seguir está um exemplo do cmdlet PowerShell para criar uma configuração de diagnóstico usando os três destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Crie configurações de diagnóstico usando o Azure CLI

Use o comando [az monitor diagnostic-setting create para](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) criar uma configuração de diagnóstico com [o Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Consulte a documentação deste comando para obter descrições de seus parâmetros.

> [!IMPORTANT]
> Não é possível usar este método para o registro de atividades do Azure. Em vez disso, use [Criar configuração de diagnóstico no Azure Monitor usando um modelo de Gerenciador de recursos](diagnostic-settings-template.md) para criar um modelo de Gerenciador de recursos e implantá-lo com CLI.

A seguir está um exemplo de comando CLI para criar uma configuração de diagnóstico usando os três destinos.

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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Configure as configurações de diagnóstico usando a API REST

Consulte [Configurações de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) para criar ou atualizar as configurações de diagnóstico usando a [API Rest do Monitor Do Azure](https://docs.microsoft.com/rest/api/monitor/).

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Configure as configurações de diagnóstico usando o modelo do Gerenciador de recursos

Consulte [Criar configuração de diagnóstico no Azure Monitor usando um modelo do Gerenciador de recursos](diagnostic-settings-template.md) para criar ou atualizar as configurações de diagnóstico com um modelo do Gerenciador de recursos.

## <a name="next-steps"></a>Próximas etapas

- [Leia mais sobre logs da plataforma Azure](platform-logs-overview.md)
