---
title: Use configurações de diagnóstico para cofres dos Serviços de Recuperação
description: Este artigo descreve como usar os eventos de diagnóstico antigos e novos para o Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617310"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Use configurações de diagnóstico para cofres dos Serviços de Recuperação

O Azure Backup envia eventos de diagnóstico que podem ser coletados e usados para fins de análise, alerta e emissão de relatórios.

Você pode configurar as configurações de diagnóstico para um cofre de Serviços de Recuperação através do portal Azure indo para o cofre e selecionando **configurações de Diagnóstico**. Selecionar **+ Adicionar configuração de diagnóstico** permite enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, um hub de eventos ou um espaço de trabalho do Log Analytics.

![Painel Configurações de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para usuários do Azure Backup

O Azure Backup fornece os seguintes eventos de diagnóstico. Cada evento fornece dados detalhados sobre um conjunto específico de artefatos relacionados ao backup:

* CoreAzureBackup
* AddonAzureBackupAlertas
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* Política de backup do AddonAzure
* AddonAzureBackupArmazenamento

Para obter mais informações, consulte [o modelo Dados para eventos de diagnóstico do Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

Os dados desses eventos podem ser enviados para uma conta de armazenamento, um espaço de trabalho do Log Analytics ou um hub de eventos. Se você estiver enviando esses dados para um espaço de trabalho do Log Analytics, selecione o **alternador específico de recursos** na tela **de configurações do Diagnóstico.** Para obter mais informações, consulte as próximas seções.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Use configurações de diagnóstico com o Log Analytics

Agora você pode usar o Azure Backup para enviar dados de diagnóstico de cofre para tabelas dedicadas do Log Analytics para backup. Essas tabelas são chamadas [tabelas específicas de recursos.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

Para enviar os dados de diagnóstico do cofre para o Log Analytics:

1. Vá para o seu cofre e **selecione Configurações de diagnóstico**. Selecione **+ Adicione a configuração de diagnóstico**.
1. Dê um nome à configuração de diagnóstico.
1. Selecione a caixa de seleção **Enviar para o Log Analytics** e selecione um espaço de trabalho do Log Analytics.
1. Selecione **recurso específico** no alternador e selecione os seis eventos seguintes: **CoreAzureBackup,** **AddonAzureBackupJobs,** **AddonAzureBackupAlerts,** **AddonAzureBackupPolicy,** **AddonAzureBackupStorage**e **AddonAzureBackupProtectedInstance**.
1. Clique em **Salvar**.

   ![Modo específico de recursos](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Depois que os dados fluem para o espaço de trabalho do Log Analytics, tabelas dedicadas para cada um desses eventos são criadas em seu espaço de trabalho. Você pode consultar qualquer uma dessas tabelas diretamente. Você também pode realizar adesões ou sindicatos entre essas tabelas, se necessário.

> [!IMPORTANT]
> Os seis eventos, ou seja, CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, são suportados *apenas* no modo específico de recursos backup em [relatórios](https://docs.microsoft.com/azure/backup/configure-reports). *Se você tentar enviar dados para esses seis eventos no modo de diagnóstico do Azure, nenhum dado será visível nos relatórios de backup.*

## <a name="legacy-event"></a>Evento legado

Tradicionalmente, todos os dados de diagnóstico relacionados ao backup de um cofre foram contidos em um único evento chamado AzureBackupReport. Os seis eventos descritos aqui são, em essência, uma decomposição de todos os dados contidos no AzureBackupReport. 

Atualmente, continuamos a apoiar o evento AzureBackupReport para compatibilidade retrógrada nos casos em que os usuários têm consultas personalizadas existentes neste evento. Exemplos são alertas de log personalizados e visualizações personalizadas. *Recomendamos que você se mude para os [novos eventos](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) o mais cedo possível.* Os novos eventos:

- Torne os dados muito mais fáceis de trabalhar em consultas de log.
- Fornecer melhor desconheça os esquemas e sua estrutura.
- Melhore o desempenho em tempos de latência e consulta de ingestão. 

*O evento legado no modo de diagnóstico do Azure será eventualmente preterido. A escolha dos novos eventos pode ajudá-lo a evitar migrações complexas em uma data posterior*. Nossa [solução de relatórios](https://docs.microsoft.com/azure/backup/configure-reports) que usa o Log Analytics também deixará de suportar dados do evento legado.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Etapas para mover para novas configurações de diagnóstico para um espaço de trabalho do Log Analytics

1. Identifique quais cofres estão enviando dados para os espaços de trabalho do Log Analytics usando o evento legado e as assinaturas a que pertencem. Execute os seguintes espaços de trabalho para identificar esses cofres e assinaturas.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. Use a [diretiva Azure incorporada](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) no Backup do Azure para adicionar uma nova configuração de diagnóstico para todos os cofres em um escopo especificado. Essa política adiciona uma nova configuração de diagnóstico aos cofres que ou não têm uma configuração de diagnóstico ou têm apenas uma configuração de diagnóstico legado. Esta política pode ser atribuída a uma assinatura inteira ou grupo de recursos por vez. Você deve ter acesso ao Proprietário a cada assinatura para a qual a apólice é atribuída.

Você pode optar por ter configurações de diagnóstico separadas para o AzureBackupReport e os seis novos eventos até que você tenha migrado todas as suas consultas personalizadas para usar dados das novas tabelas. A imagem a seguir mostra um exemplo de um cofre que tem duas configurações de diagnóstico. A primeira configuração, chamada **Configuração1,** envia dados de um evento AzureBackupReport para um espaço de trabalho do Log Analytics no modo de diagnóstico do Azure. A segunda configuração, chamada **Configuração2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho do Log Analytics no modo específico de recursos.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport é suportado *apenas* no modo de diagnóstico do Azure. *Se você tentar enviar dados para este evento no modo específico de recursos, nenhum dado fluirá para o espaço de trabalho do Log Analytics.*

> [!NOTE]
> O alternador para **diagnósticos do Azure** ou **específicos de recursos** será exibido somente se o usuário selecionar Enviar para o Log **Analytics**. Para enviar dados para uma conta de armazenamento ou um hub de eventos, um usuário seleciona o destino necessário e seleciona as caixas de seleção para qualquer um dos eventos desejados, sem quaisquer entradas adicionais. Novamente, recomendamos que você não escolha o evento legado AzureBackupReport daqui para frente.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Envie eventos de recuperação do site do Azure para o Log Analytics

Os eventos de recuperação do Azure Backup e do Azure Site São enviados do mesmo cofre dos Serviços de Recuperação. A recuperação do site do Azure não está disponível para tabelas específicas de recursos. Os usuários que desejam enviar eventos de recuperação do site do Azure para o Log Analytics são direcionados apenas para usar o *modo*de diagnóstico do Azure, como mostrado na imagem. *A escolha do modo específico de recursos para eventos de recuperação do Site do Azure impedirá que os dados necessários sejam enviados para o espaço de trabalho do Log Analytics*.

![Eventos de recuperação de sites](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Resumidamente:

* Se você já tiver os diagnósticos do Log Analytics configurados com o Azure Diagnostics e tiver escrito consultas personalizadas em cima dele, mantenha essa configuração *intacta* até que você migre suas consultas para usar dados dos novos eventos.
* Se você também quiser embarcar em novas tabelas, como recomendamos, crie uma **nova** configuração de diagnóstico, selecione **específico de recursos**e selecione os seis novos eventos.
* Se você estiver enviando atualmente eventos de recuperação do site do Azure para o Log Analytics, *não* escolha o modo específico de recursos para esses eventos. Caso contrário, os dados desses eventos não fluem para o espaço de trabalho do Log Analytics. Em vez disso, crie uma configuração de diagnóstico adicional, **selecione diagnósticos do Azure**e selecione os eventos relevantes de recuperação do site do Azure.

A imagem a seguir mostra um exemplo de um usuário que tem três configurações de diagnóstico para um cofre. A primeira configuração, chamada **Configuração1,** envia dados de um evento AzureBackupReport para um espaço de trabalho do Log Analytics no modo de diagnóstico do Azure. A segunda configuração, chamada **Configuração2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho do Log Analytics no modo específico de recursos. A terceira configuração, chamada **Setting3,** envia dados dos eventos de recuperação do site do Azure para um espaço de trabalho do Log Analytics no modo de diagnóstico do Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Próximas etapas

[Conheça o modelo de dados do Log Analytics para os eventos de diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
