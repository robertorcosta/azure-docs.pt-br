---
title: Monitore o backup do Azure com o Monitor Azure
description: Monitore as cargas de trabalho do Azure Backup e crie alertas personalizados usando o Monitor Do Azure.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459507"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitore em escala usando o Monitor Azure

O Backup do Azure oferece [funcionalidades internas de monitoramento e alertas](backup-azure-monitoring-built-in-monitor.md) em um cofre dos Serviços de Recuperação. Essas funcionalidades estão disponíveis sem nenhuma infraestrutura de gerenciamento adicional. Mas esse serviço integrado é limitado nos seguintes cenários:

- Se você monitorar dados de vários cofres do Recovery Services através de assinaturas
- Se o canal de notificação preferido *não* for e-mail
- Se os usuários quiserem alertas para mais cenários
- Se você quiser visualizar informações de um componente local, como o System Center Data Protection Manager no Azure, que o portal não mostra em [**trabalhos de backup**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [**alertas de backup**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Usando o espaço de trabalho do Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Crie alertas usando o Log Analytics

No Azure Monitor, você pode criar seus próprios alertas em um espaço de trabalho do Log Analytics. No espaço de trabalho, você usa *grupos de ação do Azure* para selecionar seu mecanismo de notificação preferido.

> [!IMPORTANT]
> Para obter informações sobre o custo de criação desta consulta, consulte [os preços do Monitor do Azure](https://azure.microsoft.com/pricing/details/monitor/).

Abra a seção **Logs** do espaço de trabalho Log Analytics e crie uma consulta para seus próprios Logs. Quando você seleciona **Nova regra de alerta,** a página de criação de alerta do Monitor do Azure é aberta, conforme mostrado na imagem a seguir.

![Crie um alerta em um espaço de trabalho do Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Aqui, o recurso já está marcado como o espaço de trabalho do Log Analytics e a integração do grupo de ação é fornecida.

![A página de criação de alerta do Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condição de alerta

A característica definidora de um alerta é sua condição de desencadeamento. Selecione **Condição** para carregar automaticamente a consulta Kusto na página **Logs,** conforme mostrado na imagem a seguir. Aqui você pode editar a condição para atender às suas necessidades. Para obter mais informações, consulte [Amostras de consultas kusto](#sample-kusto-queries).

![Configuração de uma condição de alerta](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessário, você pode editar a consulta kusto. Escolha um limiar, período e frequência. O limiar determina quando o alerta será levantado. O período é a janela de tempo em que a consulta é executada. Por exemplo, se o limite for maior que 0, o período é de 5 minutos, e a frequência é de 5 minutos, então a regra executa a consulta a cada 5 minutos, revendo os 5 minutos anteriores. Se o número de resultados for maior que 0, você será notificado através do grupo de ação selecionado.

#### <a name="alert-action-groups"></a>Grupos de ação de alerta

Use um grupo de ação para especificar um canal de notificação. Para ver os mecanismos de notificação disponíveis, em **grupos de ação,** selecione **Criar novo**.

![Mecanismos de notificação disponíveis na janela "Adicionar grupo de ação"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Você pode satisfazer todos os requisitos de alerta e monitoramento apenas do Log Analytics, ou pode usar o Log Analytics para complementar notificações incorporadas.

Para obter mais informações, consulte [Criar, visualizar e gerenciar alertas de log usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [Criar e gerenciar grupos de ação no portal Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Amostra de consultas kusto

Os gráficos padrão dão a você consultas kusto para cenários básicos nos quais você pode construir alertas. Você também pode modificar as consultas para obter os dados que deseja ser alertado. Cole as seguintes consultas de kusto na página **Logs** e, em seguida, crie alertas sobre as consultas:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Todos os trabalhos de backup fracassados

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Todos os trabalhos de backup bem-sucedidos do Azure VM

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos de backup de log sql bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos bem sucedidos de agente de backup do Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Armazenamento de backup consumido por item de backup

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>Frequência de atualização de dados de diagnóstico

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho do Log Analytics com algum atraso. Todos os eventos chegam ao espaço de trabalho do Log Analytics *de 20 a 30 minutos* depois de serem empurrados do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o lag:

- Em todas as soluções, os alertas incorporados do serviço de backup são empurrados assim que são criados. Assim, eles geralmente aparecem no espaço de trabalho do Log Analytics após 20 a 30 minutos.
- Em todas as soluções, os trabalhos de backup sob demanda e os empregos de restauração são empurrados assim que *terminam*.
- Para todas as soluções, exceto backup SQL, os trabalhos de backup programados são empurrados assim que *terminam*.
- Para backup sql, como os backups de log podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup programados completos, incluindo logs, são loteadas e empurradas a cada 6 horas.
- Em todas as soluções, outras informações, como o item de backup, política, pontos de recuperação, armazenamento e assim por diante, são empurradas pelo menos *uma vez por dia.*
- Uma alteração na configuração de backup (como alterar política ou editar política) desencadeia um impulso de todas as informações de backup relacionadas.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Usando os registros de atividades do cofre do Recovery Services

> [!CAUTION]
> As etapas a seguir se aplicam apenas aos *backups do Azure VM.* Você não pode usar essas etapas para soluções como o agente de backup do Azure, backups SQL dentro do Azure ou arquivos Azure.

Você também pode usar registros de atividades para receber notificação para eventos como sucesso de backup. Para começar, siga estas etapas:

1. Entre no Portal do Azure.
1. Abra o cofre relevante dos Serviços de Recuperação.
1. Nas propriedades do cofre, abra a seção **de registro de atividades.**

Para identificar o registro apropriado e criar um alerta:

1. Verifique se você está recebendo registros de atividades para backups bem-sucedidos aplicando os filtros mostrados na imagem a seguir. Alterar o valor do **Timespan** conforme necessário para visualizar registros.

   ![Filtragem para encontrar registros de atividades para backups do Azure VM](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selecione o nome da operação para ver os detalhes relevantes.
1. Selecione **Nova regra de alerta** para abrir a página Criar **regra.**
1. Crie um alerta seguindo as etapas em [Criar, visualizar e gerenciar alertas de registro de atividades usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nova regra de alerta](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Aqui o recurso é o cofre dos Serviços de Recuperação em si. Repita as mesmas etapas para todos os cofres nos quais você deseja ser notificado através de registros de atividades. A condição não terá um limiar, período ou frequência porque este alerta é baseado em eventos. Assim que o registro de atividades relevante é gerado, o alerta é levantado.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Usando o Log Analytics para monitorar em escala

Você pode visualizar todos os alertas criados a partir de registros de atividades e espaços de trabalho do Log Analytics no Azure Monitor. Basta abrir o painel **alertas** à esquerda.

Embora você possa receber notificações através de registros de atividades, recomendamos usar o Log Analytics em vez de registros de atividades para monitoramento em escala. Eis o porquê:

- **Cenários limitados**: Notificações através de registros de atividades aplicam-se apenas aos backups do Azure VM. As notificações devem ser configuradas para cada cofre dos Serviços de Recuperação.
- **Definição fit**: A atividade de backup programada não se encaixa com a definição mais recente de registros de atividades. Em vez disso, ele se alinha com [os registros de recursos](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace). Esse alinhamento causa efeitos inesperados quando os dados que fluem através do canal de log de atividade saem.
- **Problemas com o canal de registro de atividades**: Nos cofres dos Serviços de Recuperação, os registros de atividade que são bombeados do Azure Backup seguem um novo modelo. Infelizmente, essa mudança afeta a geração de registros de atividades no Governo Azure, Azure Alemanha e Azure China 21Vianet. Se os usuários desses serviços em nuvem criarem ou configurarem quaisquer alertas de logs de atividade no Azure Monitor, os alertas não serão acionados. Além disso, em todas as regiões públicas do Azure, se um usuário [coletar logs de atividade dos Serviços de Recuperação em um espaço de trabalho do Log Analytics,](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)esses logs não aparecerão.

Use um espaço de trabalho do Log Analytics para monitorar e alertar em escala para todas as suas cargas de trabalho protegidas pelo Azure Backup.

## <a name="next-steps"></a>Próximas etapas

Para criar consultas personalizadas, consulte [o modelo de dados do Log Analytics](backup-azure-reports-data-model.md).
