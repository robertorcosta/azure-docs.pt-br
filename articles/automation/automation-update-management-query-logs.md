---
title: Registros de gerenciamento de atualização do Azure de consulta
description: Este artigo descreve como consultar os logs de gerenciamento de atualizações em seu espaço de trabalho do Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617424"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Registros de atualização de consulta para gerenciamento de atualizações no Azure Monitor Logs

Além dos detalhes fornecidos na solução de Gerenciamento de Atualizações, você pode pesquisar contra os logs armazenados no espaço de trabalho do Log Analytics. Na página de solução, no painel esquerdo, selecione **Logs**. A página Pesquisa de log é aberta.

Você também pode aprender como personalizar as consultas ou usá-las de diferentes clientes. Consulte [a documentação da API de pesquisa do Log Analytics](https://dev.loganalytics.io/).

## <a name="update-records"></a>Registros de atualização

O Update Management coleta registros para VMs Windows e Linux e os tipos de dados que aparecem nos resultados de pesquisa de log. As seções a seguir descrevem esses registros.

### <a name="required-updates"></a>Atualizações necessárias

Um registro com `RequiredUpdate` um tipo de é criado que representa atualizações exigidas por uma máquina. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de relatórios. |
| KBID | ID do artigo base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gerenciamento do Gerente de Operações ou espaço de trabalho do Log Analytics. | 
| Produto | Os produtos para os quais a atualização é aplicável. | 
| PublishDate | A data em que a atualização está pronta para ser baixada e instalada a partir do Windows Update. |
| Servidor | | 
| SourceHealthServiceId | Identificador exclusivo representando o ID do agente do Log Analytics Windows. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificador exclusivo representando a instância de suas organizações do Azure Active Directory. | 
| TimeGenerated | Data e hora de criação do registro. | 
| Type | *Atualizar* | 
| UpdateClassification | Indica o tipo de atualizações que podem ser aplicadas. Para Windows:<br> *Atualizações críticas*<br> *Atualizações de segurança*<br> *Pacotes cumulativos de atualização*<br> *Feature packs*<br> *Service packs*<br> *Atualizações de definição*<br> *Ferramentas*<br> *Atualizações*. Para Linux:<br> *Atualizações críticas ou de segurança*<br> *Outros* |
| AtualizaçãoSeverity | Classificação de gravidade para a vulnerabilidade. Os valores são:<br> *Crítico*<br> *Importante*<br> *Moderado*<br> *Baixo* |
| UpdateTitle | O título da atualização.|

### <a name="update"></a>Atualizar

Um registro com `Update` um tipo de é criado que representa atualizações disponíveis e seu status de instalação para uma máquina. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição | 
|----------|-------------|
| Fonte de aprovação | Aplica-se apenas ao sistema operacional Windows. Fonte de aprovação para o registro. O valor é o Microsoft Update. |
| Aprovado | Verdade se o registro for aprovado, ou falso de outra forma. |
| classificação | Classificação de aprovação. O valor é Atualizações. |
| Computador | Nome de domínio totalmente qualificado da máquina de relatórios. |
| Ambiente de computador | Ambiente. Os valores possíveis são Azure ou Non-Azure. |
| MSRCBulletinID | Número de identidade do boletim de segurança. | 
| MSRCSeverity | Classificação de gravidade para a vulnerabilidade. Os valores são:<br> Crítico<br> Importante<br> Moderado<br> Baixo |  
| KBID | ID do artigo base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gerenciamento do Gerente de Operações ou do espaço de trabalho log analytics. |
| UpdateID | Identificador exclusivo da atualização de software. |
| RevisionNumber | O número de revisão de uma revisão específica de uma atualização. |
| Opcional | Verdade se o registro for opcional, ou Falso de outra forma. | 
| RebootBehavior | O comportamento de reinicialização após a instalação/desinstalação de uma atualização. |
| _ResourceId | Identificador exclusivo para o recurso associado ao registro. |
| Type | Tipo de registro. O valor é Atualizar. |
| VMUUID | Identificador exclusivo para a máquina virtual. |
| MG | Identificador exclusivo para o grupo de gerenciamento ou espaço de trabalho log analytics. | 
| TenantId | Identificador exclusivo representando a instância da sua organização do Azure Active Directory. | 
| SourceSystem | O sistema de origem para registro. O valor é `OperationsManager`. | 
| TimeGenerated | Data e hora de criação de discos. | 
| SourceComputerId | Identificador exclusivo representando o computador de origem. | 
| Title | O título da atualização. |
| Data de publicação (UTC) | A data em que a atualização está pronta para ser baixada e instalada a partir do Windows Update.  |
| UpdateState | O estado atual da atualização. | 
| Produto | Os produtos para os quais a atualização é aplicável. |
| SubscriptionId | Identificador exclusivo da assinatura do Azure. | 
| ResourceGroup | Nome do grupo de recursos ao qual o recurso pertence. | 
| ResourceProvider | O provedor de recursos. | 
| Recurso | Nome do recurso. | 
| ResourceType | O tipo de recurso. | 

### <a name="update-agent"></a>Atualizar Agente

Um registro com `UpdateAgent` um tipo de é criado que fornece detalhes do agente de atualização na máquina. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computador | Nome de domínio totalmente qualificado da máquina de relatórios. |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Nome do grupo de gerenciamento do Gerente de Operações ou espaço de trabalho do Log Analytics. |
| OSVersion | A versão do sistema operacional. |
| Servidor | |
| SourceHealthServiceId | Identificador exclusivo representando o ID do agente do Log Analytics Windows. |
| SourceSystem | O sistema de origem para registro. O valor é `OperationsManager`. | 
| TenantId | Identificador exclusivo representando a instância da sua organização do Azure Active Directory. |
| TimeGenerated | Data e hora de criação de discos. |
| Type | Tipo de registro. O valor é Atualizar. | 
| WindowsUpdateAgentVersion | Versão do agente Windows Update. |
| WSUSServer | Erros se o agente do Windows Update tiver um problema, para ajudar na solução de problemas. |

### <a name="update-deployment-status"></a>Status de implantação de atualização 

Um registro com `UpdateRunProgress` um tipo de é criado que fornece o status de implantação de atualização de uma implantação programada por máquina. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de relatórios. |
| Ambiente de computador | Ambiente. Os valores são Azure ou Non-Azure. | 
| CorrelationId | Identificador exclusivo do runbook job run para a atualização. |
| EndTime | O tempo em que o processo de sincronização terminou. | 
| ErrorResult | Código de erro do Windows Update gerado se uma atualização não for instalada. | 
| Status de instalação | Os possíveis estados de instalação de uma atualização no computador cliente,<br> `NotStarted`- trabalho ainda não acionado.<br> `FailedToStart`- incapaz de começar o trabalho na máquina.<br> `Failed`- o trabalho começou, mas falhou com uma exceção.<br> `InProgress`- trabalho em andamento.<br> `MaintenanceWindowExceeded`- se a execução fosse restante, mas o intervalo da janela de manutenção alcançado.<br> `Succeeded`- o trabalho foi bem sucedido.<br> `InstallFailed`- a atualização não conseguiu instalar com sucesso.<br> `NotIncluded`<br> `Excluded` |
| KBID | ID do artigo base de conhecimento para a atualização do Windows. | 
| ManagementGroupName | Nome do grupo de gerenciamento do Gerente de Operações ou espaço de trabalho do Log Analytics. |
| OSType | Tipo do sistema operacional. Os valores são Windows ou Linux. | 
| Produto | Os produtos para os quais a atualização é aplicável. |
| Recurso | Nome do recurso. | 
| ResourceId | Identificador exclusivo para o recurso associado ao registro. |
| ResourceProvider | O provedor de recursos. | 
| ResourceType | Tipo de recurso. | 
| SourceComputerId | Identificador exclusivo representando o computador de origem. | 
| SourceSystem | Sistema de origem para registro. O valor é `OperationsManager`. |
| StartTime | Hora em que a atualização está programada para ser instalada. |
| SubscriptionId | Identificador exclusivo da assinatura do Azure. | 
| SucessoOnRetry | Valor indicando se a execução de atualização falhou na primeira tentativa e a operação atual é uma tentativa de repetição. |
| TimeGenerated | Data e hora de criação de discos. |
| Title | O título da atualização. |
| Type | O tipo de atualização. O valor é `UpdateRunProgress`. |
| UpdateId | Identificador exclusivo da atualização de software. |
| VMUUID | Identificador exclusivo para a máquina virtual. |
| ResourceId | Identificador exclusivo para o recurso associado ao registro. |

### <a name="update-summary"></a>Resumo da Atualização 

Um registro com `UpdateSummary` um tipo de é criado que fornece resumo de atualização por máquina. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de relatórios. |
| Ambiente de computador | Ambiente. Os valores são Azure ou Non-Azure. | 
| CriticalUpdatesMissing | Número de atualizações críticas aplicáveis que estão faltando. | 
| ManagementGroupName | Nome do grupo de gerenciamento do Gerente de Operações ou espaço de trabalho do Log Analytics. |
| NETRuntimeVersion | Versão do .NET Framework instalado no computador Windows. |
| OldestMissingSecurityUpdateBucket | Especificador do balde de segurança mais antigo desaparecido. Os valores são:<br> Valor recente se for inferior a 30 dias<br> há 30 dias<br> há 60 dias<br> há 90 dias<br> há 120 dias<br> há 150 dias<br> há 180 dias<br> Mais velho quando o valor é maior que 180 dias. | 
| OldestMissingSecurityUpdateInDays | Número total de dias para a atualização mais antiga detectada conforme aplicável que não foi instalado. |
| OsVersion | A versão do sistema operacional. |
| OtherUpdatesMissing | Contagem de atualizações detectadas faltando. |
| Recurso | Nome do recurso para registro. | 
| ResourceGroup | Nome do grupo de recursos que contém o recurso. |
| ResourceId | Identificador exclusivo para o recurso associado ao registro. |
| ResourceProvider | O provedor de recursos. |
| ResourceType | Tipo de recurso. |
| ReinicializaçãoPendente | Verdade se uma reinicialização estiver pendente, ou Falsa de outra forma. |
| SecurityUpdatesMissing | Contagem de atualizações de segurança ausentes que são aplicáveis.| 
| SourceComputerId | Identificador exclusivo para a máquina virtual. |
| SourceSystem | Sistema de origem para registro. O valor é `OpsManager`. | 
| SubscriptionId | Identificador exclusivo da assinatura do Azure. |
| TimeGenerated | Data e hora de criação de discos. |
| TotalUpdatesMissing | Número total de atualizações ausentes que são aplicáveis. | 
| Type | Tipo de registro. O valor é `UpdateSummary`. |
| VMUUID | Identificador exclusivo para a máquina virtual. |
| WindowsUpdateAgentVersion | Versão do agente Windows Update. |
| WindowsUpdateSetting | Status do agente windows update. Os valores possíveis são:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Erros se o agente do Windows Update tiver um problema, para ajudar na solução de problemas. |
| _ResourceId | Identificador exclusivo para o recurso associado ao registro. |

## <a name="sample-queries"></a>Consultas de exemplo

As seções a seguir fornecem consultas de registro de amostra para registros de atualização coletados para o Gerenciamento de Atualizações.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirmar se computadores não Azure estão integrados

Para confirmar que as máquinas diretamente conectadas estão se comunicando com os registros do Azure Monitor, execute uma das seguintes pesquisas de log.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Em um computador Windows, você pode revisar as seguintes informações para verificar a conectividade do agente com os registros do Monitor Do Azure:

1. No painel de controle, abra o **Microsoft Monitoring Agent**. Na guia **Log Analytics do Azure**, o agente exibe a seguinte mensagem: **O Microsoft Monitoring Agent se conectou com êxito ao Log Analytics**.
2. Abra o Log de Eventos do Windows. Navegue até **Logs de Aplicativos e Serviços\Operations Manager** e procure as IDs de Evento 3000 e 5002 do **Conector de Serviço** de origem. Esses eventos indicam que o computador foi registrado com o espaço de trabalho do Log Analytics e está recebendo a configuração.

Se o agente não puder se comunicar com os logs do Azure Monitor e o agente estiver configurado para se comunicar com a internet através de um firewall ou servidor proxy, confirme se o firewall ou servidor proxy está configurado corretamente. Para saber como verificar se o firewall ou o servidor proxy está configurado corretamente, veja [Configuração de rede para agente do Windows](../azure-monitor/platform/agent-windows.md) ou [Configuração de rede para agente do Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os seus sistemas Linux estiverem configurados para se comunicar com um proxy ou com o Log Analytics Gateway e você estiver integrando essa solução, atualize as permissões *proxy.conf* para conceder ao grupo omiuser permissão de leitura no arquivo usando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Agentes do Linux recém-adicionados mostram um status de **Atualizado** após ter sido realizada uma avaliação. Esse processo pode levar até seis horas.

Para confirmar se um grupo de gerenciamento do Gerente de Operações está se comunicando com os logs do Azure Monitor, consulte [Validar a integração do Operations Manager com os logs do Monitor do Azure](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas de Avaliação de VM única do Azure (Windows)

Substitua o valor VMUUID pelo GUID da VM da máquina virtual que você está consultando. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos registros do Monitor do Azure:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação de VM única do Azure (Linux)

Para algumas distros do Linux, há uma incompatibilidade [de endianness](https://en.wikipedia.org/wiki/Endianness) com o valor VMUUID que vem do Azure Resource Manager e o que é armazenado nos logs do Monitor do Azure. A consulta a seguir procura uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato big-endian e little-endian do GUID para retornar corretamente os resultados. Você pode encontrar o VMUUID que deve ser usado executando a seguinte consulta nos registros do Monitor do Azure:`Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação de várias VMs

#### <a name="computers-summary"></a>Resumo de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Resumo das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lista das atualizações ausentes

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Próximas etapas

* Use pesquisas de log nos [logs do Azure Monitor](../log-analytics/log-analytics-log-searches.md) para visualizar dados de atualização detalhados.
* [Criar alertas](automation-tutorial-update-management.md#configure-alerts) para status de implantação de atualização.
