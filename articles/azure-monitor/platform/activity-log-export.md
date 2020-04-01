---
title: Exportar o log de atividades do Azure
description: Exportar registro de atividade do Azure para armazenamento para arquivamento ou Hubs de Eventos Azure para exportação fora do Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396711"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportar registro de atividades do Azure para armazenamento ou Hubs de Eventos Do Azure

> [!IMPORTANT]
> O método para enviar o registro de atividades do Azure para o Azure Storage e o Azure Event Hubs foi alterado para [configurações de diagnóstico](diagnostic-settings.md). Este artigo descreve o método legado que está em processo de ser preterido. Consulte Atualizar para [coletar e analisar o login do Azure Activity no Azure Monitor](activity-log-collect.md) para uma comparação.


O [Azure Activity Log](platform-logs-overview.md) fornece informações sobre eventos de nível de assinatura que ocorreram em sua assinatura do Azure. Além de visualizar o login de Atividade no portal do Azure ou copiá-lo em um espaço de trabalho do Log Analytics, onde ele pode ser analisado com outros dados coletados pelo Azure Monitor, você pode criar um perfil de log para arquivar o registro de atividade em uma conta de armazenamento do Azure ou transmiti-lo para um Event Hub.

## <a name="archive-activity-log"></a>Registro de atividades de arquivamento
Arquivar o Registro de Atividades em uma conta de armazenamento é útil se você quiser reter seus dados de registro por mais de 90 dias (com controle total sobre a política de retenção) para auditoria, análise estática ou backup. Se você só precisa reter seus eventos por 90 dias ou menos, você não precisa configurar arquivamento em uma conta de armazenamento, uma vez que os eventos do Registro de Atividades são retidos na plataforma Azure por 90 dias.

## <a name="stream-activity-log-to-event-hub"></a>Stream Activity Log para Event Hub
[O Azure Event Hubs](/azure/event-hubs/) é uma plataforma de streaming de dados e serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Duas maneiras de usar o recurso de streaming do Registro de atividades são:
* **Transmitir para sistemas de registro em log e telemetria de terceiros**: ao longo do tempo, a transmissão de Hubs de Eventos do Azure se tornará o mecanismo para direcionar seus Logs de Atividade para SIEMs e soluções de análise de logs de terceiros.
* **Compilar uma plataforma de registro em log e telemetria personalizada**: se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de log de atividade.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="storage-account"></a>Conta de armazenamento
Se você está arquivando seu Registro de Atividades, você precisa [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) se você ainda não tiver uma. Você não deve usar uma conta de armazenamento existente que tenha outros dados não-monitorados armazenados nele para que você possa controlar melhor o acesso aos dados de monitoramento. Se você também estiver arquivando registros e métricas para uma conta de armazenamento, porém, você pode optar por usar essa mesma conta de armazenamento para manter todos os dados de monitoramento em um local central.

A conta de armazenamento não precisa estar na mesma assinatura que a assinatura que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas. 

> [!TIP]
> Consulte [Configurar firewalls de armazenamento do Azure e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para fornecer acesso a uma conta de armazenamento por trás de uma rede virtual protegida.

### <a name="event-hubs"></a>Hubs de Eventos
Se você está enviando seu Registro de Atividades para um centro de eventos, então você precisa [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se você ainda não tiver um. Se você já transmitiu eventos do Activity Log para este namespace do Event Hubs, então esse hub de eventos será reutilizado.

A política de acesso compartilhado define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciamento, envio e escuta. Você pode criar ou modificar políticas de acesso compartilhado para o namespace de Hubs de Eventos no Portal do Azure, na guia Configurar, para seu namespace de Hubs de Eventos.

Para atualizar o perfil de log do Registro de Atividades para incluir o streaming, você deve ter a permissão ListKey nessa regra de autorização do Event Hubs. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

Transmita o registro de atividades para um hub de eventos [criando um perfil de log](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Criar um perfil de log
Você define como seu registro de atividade do Azure é exportado usando um **perfil de log**. Cada assinatura do Azure só pode ter um perfil de log. Essas configurações podem ser configuradas através da opção **Exportar** na lâmina Do Registro de Atividades no portal. Elas também podem ser definidas por meio de programação [usando a API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), de cmdlets do PowerShell ou da CLI.

O perfil de registro define o seguinte.

**Para onde o Registro de Atividades deve ser enviado.** Atualmente, as opções disponíveis são Storage Account ou Event Hubs.

**Quais categorias de eventos devem ser enviadas.** O significado da *categoria* em eventos de Log Profiles e Activity Log é diferente. No Perfil de Registro, *categoria* representa o tipo de operação (Write, Delete, Action). Em um evento do Registro de Atividades, a *categoria*"* propriedade representa a origem ou o tipo de evento (por exemplo, Administração, ServiceHealth e Alerta).

**Quais regiões (locais) devem ser exportados.** Você deve incluir todos os locais, já que muitos eventos no Registro de Atividades são eventos globais.

**Quanto tempo o Log de Atividades deve ser mantido em uma Conta de Armazenamento.** Uma retenção de zero dias significa que os logs serão mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 365.

Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento for desativado, as políticas de retenção não terão efeito. As políticas de retenção são aplicadas por dia, para que, ao final de um dia (UTC), os logs do dia após a política de retenção sejam excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início do dia de hoje, os logs de anteontem serão excluídos. A exclusão começa à meia-noite UTC, mas observe que pode levar até 24 horas para que os logs sejam excluídos da conta de armazenamento.


> [!IMPORTANT]
> Você pode receber um erro ao criar um perfil de log se o provedor de recursos Microsoft.Insights não estiver registrado. Consulte [os provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md) para registrar este provedor.


### <a name="create-log-profile-using-the-azure-portal"></a>Criar perfil de registro usando o portal Azure

Crie ou edite um perfil de log com a opção **Exportar para o Hub de Eventos** no portal Azure.

1. No menu Monitor do **Azure** no portal Azure, selecione **Registro de atividades**.
3. Clique **em Configurações de diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência de legado](media/diagnostic-settings-subscription/legacy-experience.png)

3. Na lâmina que aparece, especifique o seguinte:
   * Regiões com eventos para exportar. Você deve selecionar todas as regiões para garantir que você não perca eventos-chave, uma vez que o Registro de Atividades é um registro global (não regional) e, portanto, a maioria dos eventos não tem uma região associada a eles.
   * Se você quiser escrever na conta de armazenamento:
       * A conta de armazenamento para a qual você gostaria de salvar eventos.
       * O número de dias que você deseja reter esses eventos no armazenamento. Uma configuração de 0 dias retém os logs para sempre.
   * Se você quiser escrever para o hub de eventos:
       * O Service Bus Namespace no qual você gostaria que um Event Hub fosse criado para transmitir esses eventos.

     ![Folha Exportar Log de Atividades](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Clique em **Salvar** para salvar as configurações. As configurações serão aplicadas imediatamente à sua assinatura.


### <a name="configure-log-profile-using-powershell"></a>Configurar o perfil de log usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se um perfil de log já existir, primeiro você precisa remover o perfil de log existente e, em seguida, criar um novo.

1. Use `Get-AzLogProfile` para identificar se já existe um perfil de log.  Se um perfil de registro existir, anote a propriedade *nome.*

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
    | Nome |Sim |Nome de seu perfil de log. |
    | StorageAccountId |Não |ID de recursos da conta de armazenamento onde o Registro de atividades deve ser salvo. |
    | serviceBusRuleId |Não |ID da Regra de Barramento de Serviço para o namespace do Barramento de Serviço no qual você gostaria que os hubs de eventos fossem criados. Esta é uma seqüência com o formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sim |Lista separada por vírgulas de regiões para as quais você gostaria de coletar eventos do Log de Atividades. |
    | RetentionInDays |Sim |Número de dias para os quais os eventos devem ser retidos na conta de armazenamento, entre 1 e 365. Um valor de zero armazena os logs indefinidamente. |
    | Categoria |Não |Lista separada por vírgulas de categorias de eventos que devem ser coletados. Os valores possíveis são _Write,_ _Delete_e _Action_. |

### <a name="example-script"></a>Script de exemplo
A seguir está um script de amostra PowerShell para criar um perfil de registro que grava o Registro de atividades em uma conta de armazenamento e no centro de eventos.

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


### <a name="configure-log-profile-using-azure-cli"></a>Configurar o perfil de log usando o Azure CLI

Se um perfil de log já existir, primeiro será necessário remover o perfil de log existente e criar um novo.

1. Use `az monitor log-profiles list` para identificar se já existe um perfil de log.
2. Use `az monitor log-profiles delete --name "<log profile name>` para remover o perfil de log usando o valor da propriedade *nome*.
3. Use `az monitor log-profiles create` para criar um novo perfil de log:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propriedade | Obrigatório | Descrição |
    | --- | --- | --- |
    | name |Sim |Nome de seu perfil de log. |
    | storage-account-id |Sim |A ID de Recurso da Conta de Armazenamento na qual os Logs de Atividades devem ser salvos. |
    | Locais |Sim |Lista separada por espaço de regiões para as quais você gostaria de coletar eventos do Log de Atividades. É possível exibir uma lista de todas as regiões para a assinatura usando `az account list-locations --query [].name`. |
    | dias |Sim |Número de dias para os quais os eventos devem ser mantidos, entre 1 e 365. Um valor de zero armazenará os logs indefinidamente (para sempre).  Se zero, então o parâmetro ativado deve ser definido como falso. |
    |Habilitado | Sim |Verdadeiro ou falso.  Usado para habilitar ou desabilitar a política de retenção.  Se for Verdadeiro, o parâmetro de dias deverá ser um valor maior que 0.
    | Categorias |Sim |Lista separada por espaço de categorias de eventos que devem ser coletadas. Os valores possíveis são Gravação, Exclusão e Ação. |



## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre o Registro de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Coletar log de atividades em logs de monitor do Azure](activity-log-collect.md)
