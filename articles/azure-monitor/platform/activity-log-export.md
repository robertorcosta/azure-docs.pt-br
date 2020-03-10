---
title: Exportar o log de atividades do Azure
description: Exportar o log de atividades do Azure para o armazenamento para arquivamento ou hubs de eventos do Azure para exportação fora do Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379526"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportar o log de atividades do Azure para o armazenamento ou hubs de eventos do Azure

> [!IMPORTANT]
> O método para enviar o log de atividades do Azure para o armazenamento do Azure e hubs de eventos do Azure foi alterado para [configurações de diagnóstico](diagnostic-settings.md). Este artigo descreve o método herdado que está no processo de ser preterido. Consulte atualizar para a [coleta e exportação do log de atividades do Azure](diagnostic-settings-legacy.md) para obter uma comparação.


O [log de atividades do Azure](platform-logs-overview.md) fornece informações sobre eventos no nível da assinatura que ocorreram em sua assinatura do Azure. Além de exibir o log de atividades no portal do Azure ou copiá-lo para um espaço de trabalho Log Analytics onde ele pode ser analisado com outros dados coletados pelo Azure Monitor, você pode criar um perfil de log para arquivar o log de atividades em uma conta de armazenamento do Azure ou transmiti-lo para um  Hub de eventos.

## <a name="archive-activity-log"></a>Log de atividades de arquivamento
O arquivamento do log de atividades em uma conta de armazenamento é útil se você quiser manter seus dados de log por mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisa reter seus eventos por 90 dias ou menos, não é necessário configurar o arquivamento para uma conta de armazenamento, pois os eventos do log de atividades são mantidos na plataforma do Azure por 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Transmitir log de atividades para o Hub de eventos
Os [hubs de eventos do Azure](/azure/event-hubs/) são uma plataforma de streaming de dados e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Duas maneiras de usar o recurso de streaming para o log de atividades são:
* **Transmitir para sistemas de registro em log e telemetria de terceiros**: ao longo do tempo, a transmissão de Hubs de Eventos do Azure se tornará o mecanismo para direcionar seus Logs de Atividade para SIEMs e soluções de análise de logs de terceiros.
* **Compilar uma plataforma de registro em log e telemetria personalizada**: se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de log de atividade.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

### <a name="storage-account"></a>Conta de armazenamento
Se você estiver arquivando o log de atividades, precisará [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) se ainda não tiver uma. Você não deve usar uma conta de armazenamento existente que tenha outros dados de não monitoramento armazenados nele para que você possa controlar melhor o acesso aos dados de monitoramento. Se você também estiver arquivando logs e métricas em uma conta de armazenamento, poderá optar por usar essa mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

A conta de armazenamento não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas. 

> [!TIP]
> Consulte [Configurar firewalls de armazenamento do Azure e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para fornecer acesso a uma conta de armazenamento por trás de uma rede virtual protegida.

### <a name="event-hubs"></a>Hubs de evento
Se você estiver enviando o log de atividades para um hub de eventos, você precisará [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se você tiver transmitido anteriormente eventos do log de atividades para esse namespace de hubs de eventos, esse Hub de eventos será reutilizado.

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciar, enviar e escutar. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de hubs de eventos na portal do Azure na guia Configurar para seu namespace de hubs de eventos.

Para atualizar o perfil de log do log de atividades para incluir streaming, você deve ter a permissão ListKey nessa regra de autorização de hubs de eventos. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

Transmita o log de atividades para um hub de eventos [criando um perfil de log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Criar um perfil de log
Você define como o log de atividades do Azure é exportado usando um **perfil de log**. Cada assinatura do Azure pode ter apenas um perfil de log. Essas configurações podem ser definidas por meio da opção **Exportar** na folha log de atividades no Portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI.

O perfil de log define o seguinte.

**Onde o log de atividades deve ser enviado.** Atualmente, as opções disponíveis são conta de armazenamento ou hubs de eventos.

**Quais categorias de evento devem ser enviadas.** O significado da *categoria* nos perfis de log e nos eventos do log de atividades é diferente. No perfil de log, *categoria* representa o tipo de operação (gravação, exclusão, ação). Em um evento do log de atividades, a propriedade *categoria*"* representa a origem ou o tipo de evento (por exemplo, administração, integridade e alerta).

**Quais regiões (locais) devem ser exportadas.** Você deve incluir todos os locais, pois muitos eventos no log de atividades são eventos globais.

**Por quanto tempo o log de atividades deve ser retido em uma conta de armazenamento.** Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.

Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado, as políticas de retenção não terão nenhum efeito. As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. A exclusão começa à meia-noite UTC, mas observe que pode levar até 24 horas para que os logs sejam excluídos da conta de armazenamento.


> [!IMPORTANT]
> Você pode receber um erro ao criar um perfil de log se o provedor de recursos Microsoft. insights não estiver registrado. Consulte [provedores de recursos do Azure e tipos](../../azure-resource-manager/management/resource-providers-and-types.md) para registrar este provedor.


### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de log usando o portal do Azure

Crie ou edite um perfil de log com a opção **exportar para o Hub de eventos** no portal do Azure.

1. No menu **Azure monitor** na portal do Azure, selecione log de **atividades**.
3. Clique em **Configurações do Diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique na faixa roxa para a experiência herdada.

    ![Experiência herdada](media/diagnostic-settings-subscription/legacy-experience.png)

3. Na folha que aparece, especifique o seguinte:
   * Regiões com os eventos a serem exportados. Você deve selecionar todas as regiões para garantir que não perca eventos de chave, pois o log de atividades é um log global (não regional) e, portanto, a maioria dos eventos não tem uma região associada a eles.
   * Se você quiser gravar na conta de armazenamento:
       * A conta de armazenamento na qual você deseja salvar eventos.
       * O número de dias que você deseja manter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
   * Se você quiser gravar no Hub de eventos:
       * O namespace do barramento de serviço no qual você deseja que um hub de eventos seja criado para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.


### <a name="configure-log-profile-using-powershell"></a>Configurar o perfil de log usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se um perfil de log já existir, primeiro você precisará remover o perfil de log existente e, em seguida, criar um novo.

1. Use `Get-AzLogProfile` para identificar se já existe um perfil de log.  Se houver um perfil de log, observe a propriedade *Name* .

1. Use `Remove-AzLogProfile` para remover o perfil de log usando o valor da propriedade *nome*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para criar um novo perfil de log:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | {1&gt;Nome&lt;1} |Sim |Nome de seu perfil de log. |
    | StorageAccountId |Não |ID de recurso da conta de armazenamento em que o log de atividades deve ser salvo. |
    | serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Esta é uma cadeia de caracteres com o formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Local |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
    | RetentionInDays |Sim |Número de dias pelos quais os eventos devem ser retidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os logs indefinidamente. |
    | Categoria |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são _gravação_, _exclusão_e _ação_. |

### <a name="example-script"></a>Script de exemplo
Veja a seguir um exemplo de script do PowerShell para criar um perfil de log que grava o log de atividades em uma conta de armazenamento e no Hub de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configurar o perfil de log usando CLI do Azure

Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `az monitor log-profiles list` para identificar se já existe um perfil de log.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *nome*.
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | {1&gt;name&lt;1} |Sim |Nome de seu perfil de log. |
    | storage-account-id |Sim |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
    | locais |Sim |Lista separada por espaço de regiões para as quais você gostaria de coletar eventos do Log de Atividades. É possível exibir uma lista de todas as regiões para a assinatura usando `az account list-locations --query [].name`. |
    | dias |Sim |Número de dias pelos quais os eventos devem ser retidos, entre 1 e 365. Um valor de zero armazenará os logs indefinidamente (para sempre).  Se for zero, o parâmetro Enabled deverá ser definido como false. |
    |Habilitado | Sim |Verdadeiro ou falso.  Usado para habilitar ou desabilitar a política de retenção.  Se for Verdadeiro, o parâmetro de dias deverá ser um valor maior que 0.
    | categorias |Sim |Lista separada por espaço de categorias de eventos que devem ser coletadas. Os valores possíveis são Gravação, Exclusão e Ação. |



## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Leia mais sobre o Log de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Coletar log de atividades em logs de Azure Monitor](activity-log-collect.md)
