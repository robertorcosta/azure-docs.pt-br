---
title: Criar uma configuração de diagnóstico para coletar logs e métricas no Azure
description: Crie configurações de diagnóstico para encaminhar os logs da plataforma Azure para logs do Monitor Do Azure, armazenamento Azure ou Hubs de Eventos Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672405"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Crie configuração de diagnóstico para coletar logs e métricas da plataforma no Azure
[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo logs de atividades do Azure e registros de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure da qual dependem. Este artigo fornece detalhes sobre a criação e configuração de configurações de diagnóstico para enviar logs de plataforma para diferentes destinos.

> [!IMPORTANT]
> Antes de criar uma configuração de diagnóstico para coletar o registro atividade, você deve primeiro desativar qualquer configuração legado. Consulte [O registro de atividades do Collect Azure com as configurações do legado](diagnostic-settings-legacy.md) para obter detalhes.

Cada recurso do Azure requer sua própria configuração de diagnóstico, que define o seguinte:

- As categorias de logs e os dados de métrica enviados aos destinos definidos na configuração. As categorias disponíveis variam para diferentes tipos de recursos.
- Um ou mais destinos para enviar os logs. Os destinos atuais incluem workspace do Log Analytics, Hubs de Eventos e Armazenamento do Azure.
 
Uma única configuração de diagnóstico pode definir não mais do que um de cada um dos destinos. Se você quiser enviar dados para mais de um tipo de destino específico (por exemplo, dois workspaces do Log Analytics diferentes), crie várias configurações. Cada recurso pode ter até cinco configurações de diagnóstico.


> [!NOTE]
> [As métricas da plataforma](metrics-supported.md) são coletadas automaticamente no [Azure Monitor Metrics](data-platform-metrics.md). As configurações de diagnóstico podem ser usadas para coletar métricas de certos serviços do Azure no Azure Monitor Logs para análise com outros dados de monitoramento usando [consultas de log](../log-query/log-query-overview.md).

## <a name="destinations"></a>Destinos 
Os registros da plataforma podem ser enviados para os destinos na tabela a seguir. A configuração para cada destino é realizada usando o mesmo processo para criar configurações diagnósticas descritas neste artigo. Siga cada link na tabela a seguir para obter detalhes sobre o envio de dados para esse destino.

| Destino | Descrição |
|:---|:---|
| [Espaço de trabalho do Log Analytics](resource-logs-collect-workspace.md) | A coleta de logs em um espaço de trabalho do Log Analytics permite analisá-los com outros dados de monitoramento coletados pelo Azure Monitor usando consultas de log poderosas e também para aproveitar outros recursos do Azure Monitor, como alertas e visualizações. |
| [Hubs de eventos](resource-logs-stream-event-hubs.md) | O envio de logs para o Event Hubs permite transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise de log. |
| [Conta de armazenamento do Azure](resource-logs-collect-storage.md) | Arquivar logs em uma conta de armazenamento Do Zure é útil para auditoria, análise estática ou backup. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Crie configurações de diagnóstico no portal Azure
Você pode configurar as configurações de diagnóstico no portal Azure no menu do Azure Monitor ou no menu do recurso.

1. Onde você configura as configurações de diagnóstico no portal Azure depende do recurso.

   - Para obter um único recurso, clique em **Configurações de diagnóstico** em **Monitor** no menu do recurso.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-resource.png)

    - Para obter um ou mais recursos, clique **em** **Configurações de diagnóstico** no menu Monitor do Azure e clique no recurso.
    
        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-monitor.png)

    - Para o registro atividade, clique em Entrar em **atividade** no menu **Monitor do Azure** e, em seguida, **configurações de Diagnóstico**. Certifique-se de desativar qualquer configuração legado para o registro de atividades. Consulte [Desativar as configurações existentes](diagnostic-settings-legacy.md#disable-existing-settings) para obter detalhes.

        ![Configurações de Diagnóstico](media/diagnostic-settings/menu-activity-log.png)

2. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em **Adicionar configuração de diagnóstico**.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-settings/add-setting.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já configuradas. Clique em **Adicionar configuração de diagnóstico** para adicionar uma nova configuração ou Editar a **configuração** para editar uma já existente. Cada configuração não pode ter mais do que um dos tipos de destino.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-settings/edit-setting.png)

3. Dê um nome se ele ainda não tiver um.
4. Verifique a caixa de cada destino para enviar os registros. Clique **em Configurar** para especificar suas configurações conforme descrito na tabela a seguir.

    | Configuração | Descrição |
    |:---|:---|
    | Espaço de trabalho do Log Analytics | Nome do espaço de trabalho. |
    | Conta de armazenamento | Nome da conta de armazenamento. |
    | Namespace do Hub de Eventos | O namespace onde o hub de eventos é criado (se esta é a primeira vez que os logs de streaming) ou transmitidos para (se já houver recursos que estão transmitindo essa categoria de log para este namespace).
    | Nome do Hub de Eventos | Opcionalmente, especifique um nome de hub de eventos para enviar todos os dados na configuração. Se você não especificar um nome, um hub de eventos será criado para cada categoria de log. Se você estiver enviando várias categorias, você pode querer especificar um nome para limitar o número de hubs de eventos criados. Consulte [as cotas e limites do Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para obter detalhes. |
    | Nome da política do hub de eventos | Define as permissões que o mecanismo de streaming tem. |

    ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-settings/setting-details.png)

5. Verifique a caixa para cada uma das categorias de dados a serem enviadas para os destinos especificados. A lista de categorias vai variar para cada serviço do Azure.

   > [!NOTE]
   > Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
   >
   > *Por exemplo*: a métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.

6. Clique em **Salvar**.

Após alguns momentos, a nova configuração aparece em sua lista de configurações para este recurso e os logs são transmitidos para os destinos especificados à medida que novos dados de evento são gerados. Observe que pode haver até quinze minutos entre quando um evento é emitido e quando ele [aparece em um espaço de trabalho do Log Analytics](data-ingestion-time.md).



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

* [Leia mais sobre logs da plataforma Azure](platform-logs-overview.md)
