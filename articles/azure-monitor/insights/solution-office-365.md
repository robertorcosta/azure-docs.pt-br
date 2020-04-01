---
title: Solução de gerenciamento do Office 365 no Microsoft Azure | Microsoft Docs
description: Este artigo apresenta detalhes sobre a configuração e o uso da solução Office 365 no Microsoft Azure.  Ele inclui uma descrição detalhada dos registros do Office 365 criados no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 5aa025fb366634e796abfb2eb9c0035d9b87dc3c
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437037"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solução de gerenciamento do Office 365 no Microsoft Azure | (Versão prévia)

![Logotipo do Office 365](media/solution-office-365/icon.png)


> [!IMPORTANT]
> ## <a name="solution-update"></a>Atualização de soluções
> Esta solução foi substituída pela solução [Office 365](../../sentinel/connect-office-365.md) General Availability no [Azure Sentinel](../../sentinel/overview.md) e pela [solução de relatórios e monitoramento do Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Juntos, eles fornecem uma versão atualizada da solução anterior do Azure Monitor Office 365 com uma melhor experiência de configuração. Você pode continuar a usar a solução existente até 30 de julho de 2020.
> 
> O Azure Sentinel é uma solução nativa de segurança nativa da nuvem que ingere logs e fornece funcionalidades adicionais de SIEM, incluindo detecções, investigações, insights orientados para caça e aprendizado de máquina. O uso do Azure Sentinel agora fornecerá a você a ingestão de registros de gerenciamento de atividades do Office 365 SharePoint e do Exchange.
> 
> O relatório Azure AD fornece uma visão mais abrangente dos logs da atividade do Azure AD em seu ambiente, incluindo eventos de login, eventos de auditoria e alterações em seu diretório. Para conectar os logs Ad do Azure, você pode usar o [conector Azure Sentinel Azure AD](../../sentinel/connect-azure-active-directory.md) ou configurar a [integração de logs Azure AD com o Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> A coleção de log Azure AD é submetida aos preços do Azure Monitor.  Consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter mais informações.
>
> Para usar a solução Azure Sentinel Office 365:
> 1. O uso do conector Office 365 no Azure Sentinel afeta os preços do seu espaço de trabalho. Para obter mais informações, consulte [os preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Se você já estiver usando a solução Azure Monitor Office 365, primeiro deve desinstalá-la usando o script na [seção Desinstalar abaixo](#uninstall).
> 3. [Habilite a solução do Azure Sentinel](../../sentinel/quickstart-onboard.md) em seu espaço de trabalho.
> 4. Acesse a página **de conectores de dados** no Azure Sentinel e ative o conector Office **365.**
>
> ## <a name="frequently-asked-questions"></a>Perguntas frequentes
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-april-30th"></a>P: É possível embarcar na solução do Office 365 Azure Monitor entre agora e 30 de abril?
> Não, os scripts de onboarding da solução Azure Monitor Office 365 não estão mais disponíveis. A solução será removida em 30 de abril.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: As tabelas e esquemas serão alterados?
> O nome e o esquema da tabela **OfficeActivity** permanecerão os mesmos da solução atual. Você pode continuar usando as mesmas consultas na nova solução, excluindo consultas que fazem referência aos dados do Azure AD.
> 
> Os novos registros [de soluções de relatórios e monitoramento do Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) serão ingeridos nas [tabelas SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) e [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) em vez do **OfficeActivity**. Para obter mais informações, veja [como analisar os logs Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), o que também é relevante para os usuários do Azure Sentinel e do Azure Monitor.
> 
> A seguir estão amostras para converter consultas do **OfficeActivity** para **SigninLogs**:
> 
> **Consulta falha nos logins, pelo usuário:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Veja as operações do Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: Como posso embarcar no Azure Sentinel?
> O Azure Sentinel é uma solução que você pode habilitar no novo ou existente espaço de trabalho do Log Analytics. Para saber mais, consulte [a documentação de embarque do Azure Sentinel](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: Preciso do Azure Sentinel para conectar os logs AD do Azure?
> Você pode configurar [a integração de logs Azure AD com o Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), que não está relacionado à solução do Azure Sentinel. O Azure Sentinel fornece um conector nativo e conteúdo fora da caixa para logs AD do Azure. Para obter mais informações, consulte a pergunta abaixo sobre conteúdo orientado para segurança fora da caixa.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: Quais são as diferenças ao conectar logs Azure AD do Azure Sentinel e do Azure Monitor?
> O Azure Sentinel e o Azure Monitor conectam-se aos logs Azure AD com base na mesma [solução de relatórios e monitoramento do Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). O Azure Sentinel fornece um conector nativo de um clique que conecta os mesmos dados e fornece informações de monitoramento.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: O que eu preciso mudar ao mudar para as novas tabelas de relatórios e monitoramento do Azure AD?
> Todas as consultas que usam dados AD do Azure, incluindo consultas em alertas, dashboards e qualquer conteúdo que você criou usando dados do Office 365 Azure AD, devem ser recriadas usando as novas tabelas.
>
> O Azure Sentinel e o Azure AD fornecem conteúdo embutido que você pode usar ao mover-se para a solução de emissão de relatórios e monitoramento do Azure AD. Para obter mais informações, consulte a próxima pergunta sobre conteúdo orientado para segurança fora da caixa e como usar as carteiras de [trabalho do Azure Monitor para relatórios do Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: Como posso usar o conteúdo orientado para segurança do Azure Sentinel?
> O Azure Sentinel fornece painéis orientados para segurança, consultas de alerta personalizadas, consultas de caça, investigação e recursos de resposta automatizados com base nos logs do Office 365 e do Azure AD. Explore o Azure Sentinel GitHub e tutoriais para saber mais:
>
> - [Detectar ameaças fora da caixa](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Criar regras analíticas personalizadas para detectar ameaças suspeitas](../../sentinel/tutorial-detect-threats-custom.md)
> - [Monitorar seus dados](../../sentinel/tutorial-monitor-your-data.md)
> - [Investigar incidentes com o Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Configurar respostas de ameaças automatizadas no Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Comunidade Azure Sentinel GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: O Azure Sentinel fornece conectores adicionais como parte da solução?
> Sim, veja [o Azure Sentinel conectar fontes de dados](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-april-30-do-i-need-to-offboard-beforehand"></a>P: O que acontecerá em 30 de abril? Preciso sair do mar antes?
> 
> - Você não poderá receber dados da solução **Office365.** A solução não estará mais disponível no Marketplace
> - Para os clientes do Azure Sentinel, a solução de espaço de trabalho Log Analytics **Office365** será incluída na solução Azure Sentinel **SecurityInsights.**
> - Se você não desligar sua solução manualmente, seus dados serão desconectados automaticamente em 30 de abril.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: Minha transferência de dados para a nova solução?
> Sim. Quando você remover a solução **office 365** do seu espaço de trabalho, seus dados ficarão temporariamente indisponíveis porque o esquema é removido. Quando você habilita o novo conector **Office 365** no Sentinel, o esquema é restaurado no espaço de trabalho e todos os dados já coletados serão disponibilizados. 
 

A solução de gerenciamento do Office 365 permite que você monitore o ambiente do Office 365 no Azure Monitor.

- Monitore atividades do usuário em suas contas do Office 365 para analisar padrões de uso, bem como identificar tendências de comportamentos. Por exemplo, você pode extrair os cenários de uso específicos, como arquivos que são compartilhados fora da sua organização ou os sites do SharePoint mais populares.
- Monitore atividades do administrador para controlar as alterações de configuração ou operações com privilégios elevados.
- Detecte e investigue comportamento indesejado do usuário, o que pode ser personalizado para suas necessidades organizacionais.
- Demonstre auditoria e conformidade. Por exemplo, você pode monitorar as operações de acesso a arquivos em arquivos confidenciais, o que pode ajudá-lo com o processo de conformidade e auditoria.
- Execute a solução de problemas operacionais usando [consultas de log](../log-query/log-query-overview.md) com base nos dados da atividade do Office 365 de sua organização.


## <a name="uninstall"></a>Desinstalar

Remova a solução de gerenciamento do Office 365 usando o processo em [Remover uma solução de gerenciamento](solutions.md#remove-a-monitoring-solution). No entanto, isso não interromperá a coleta de dados do Office 365 pelo Azure Monitor. Siga o procedimento abaixo para cancelar a assinatura do Office 365 e parar de coletar dados.

1. Salve o script a seguir como *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Execute o script com o seguinte comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Você será solicitado para credenciais. Forneça as credenciais para o espaço de trabalho do Log Analytics.

## <a name="data-collection"></a>Coleta de dados

Talvez demore algumas horas para coletar os dados pela primeira vez. Após o início da coleta, o Office 365 envia uma [notificação webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) com dados detalhados para o Azure Monitor sempre que um registro é criado. O registro fica disponível no Azure Monitor alguns minutos após o recebimento.

## <a name="using-the-solution"></a>Usando a solução

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Quando você adicionar a solução Office 365 ao espaço de trabalho do Log Analytics, o bloco **Office 365** será adicionado ao seu painel do OMS. Esse bloco exibe uma contagem e representação gráfica do número de computadores em seu ambiente e sua conformidade de atualização.<br><br>
![Bloco de Resumo do Office 365](media/solution-office-365/tile.png)  

Clique no bloco **Office 365** para abrir o painel **Office 365**.

![Painel do Office 365](media/solution-office-365/dashboard.png)  

O painel inclui as colunas na tabela a seguir. Cada coluna lista os dez principais alertas por contagem que correspondem aos critérios da coluna para o escopo e intervalo de tempo especificados. É possível executar uma pesquisa de log que fornece a lista inteira clicando em Ver todos na parte inferior da coluna ou clicando no cabeçalho de coluna.

| Coluna | Descrição |
|:--|:--|
| Operações | Fornece informações sobre os usuários ativos de todas as suas assinaturas do Office 365 monitoradas. Você também poderá ver o número de atividades que ocorrem ao longo do tempo.
| Exchange | Mostra a análise das atividades do Exchange Server, como a permissão Add-Mailbox ou Set-Mailbox. |
| SharePoint | Mostra as principais atividades que os usuários executam em documentos do SharePoint. Quando você faz drill down desse bloco, a página de pesquisa mostra os detalhes dessas atividades, como o documento de destino e o local dessa atividade. Por exemplo, para um evento acessado por arquivo, você poderá ver o documento que está sendo acessado, o nome da conta associada e o endereço IP. |
| Azure Active Directory | Inclui as principais atividades do usuário, como Tentativas de Logon e de Redefinição de Senha do Usuário. Quando você fizer o drill down, poderá ver os detalhes dessas atividades como o Status do Resultado. Isso é mais útil se você desejar monitorar atividades suspeitas no Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Registros de log do Azure Monitor

Todos os registros criados no espaço de trabalho do Log Analytics no Azure Monitor pela solução do Office 365 têm um **Tipo** de **OfficeActivity**.  A propriedade **OfficeWorkload** determina a qual serviço Office 365 o registro se refere: Exchange, AzureActiveDirectory, SharePoint ou OneDrive.  A propriedade **RecordType** especifica o tipo de operação.  As propriedades variam para cada tipo de operação e são mostradas nas tabelas a seguir.

### <a name="common-properties"></a>Propriedades comuns

As propriedades a seguir são comuns a todos os registros do Office 365.

| Propriedade | Descrição |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | O endereço IP do dispositivo que foi usado quando a atividade foi registrada. O endereço IP é exibido no formato de endereço IPv4 ou IPv6. |
| OfficeWorkload | Serviço Office 365 ao qual o registro se refere.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operação | O nome da atividade do usuário ou administrador.  |
| OrganizationId | O GUID do locatário do Office 365 da sua organização. Esse valor sempre será o mesmo para a sua organização, independentemente do serviço do Office 365 em que ele ocorre. |
| RecordType | Tipo de operação executada. |
| ResultStatus | Indica se a ação (especificada na propriedade Operation) foi bem-sucedida ou não. Os valores possíveis são Succeeded, PartiallySucceeded ou Failed. Para a atividade de administração do Exchange, o valor é True ou False. |
| UserId | O nome UPN do usuário que executou a ação que resultou em o registro ser incluído em log. Por exemplo, my_name@my_domain_name. Observe que os registros para a atividade realizada por contas do sistema (como SHAREPOINT\system ou NTAUTHORITY\SYSTEM) também são incluídos. | 
| UserKey | Uma ID alternativa para o usuário identificado na propriedade UserId.  Por exemplo, essa propriedade é preenchida com a PUID (ID exclusiva do passport) para eventos executadas por usuários no SharePoint, no OneDrive for Business e no Exchange. Essa propriedade também pode especificar o mesmo valor que a propriedade UserID para eventos que ocorrem em outros serviços e eventos executados por contas do sistema|
| UserType | O tipo de usuário que realizou a operação.<br><br>Admin<br>Aplicativo<br>DcAdmin<br>Regular<br>Reservado<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Base do Azure Active Directory

As propriedades a seguir são comuns a todos os registros do Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | O tipo de evento do Azure AD. |
| ExtendedProperties | As propriedades estendidas do evento do Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Logon na Conta do Azure Active Directory

Esses registros são criados quando um usuário do Active Directory tenta fazer logon.

| Propriedade | Descrição |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | O aplicativo que dispara o evento de logon de conta, como Office 15. |
| `Client` | Detalhes sobre o dispositivo cliente, o SO do dispositivo e o navegador do dispositivo que foi usado para o evento de logon na conta. |
| `LoginStatus` | Esta propriedade é diretamente de OrgIdLogon.LoginStatus. O mapeamento de várias falhas de logon interessantes pode ser feito por algoritmos de alerta. |
| `UserDomain` | As TII (informações de identidade de locatário). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Esses registros são criados quando adições ou alterações são feitas aos objetos do Azure Active Directory.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | O usuário em que a ação (identificada pela propriedade Operation) foi executada. |
| Ator | O usuário ou a entidade de serviço que executou a ação. |
| ActorContextId | O GUID da organização à qual pertence o ator. |
| ActorIpAddress | O endereço IP do ator no formato de endereço IPV4 ou IPV6. |
| InterSystemsId | O GUID que controla as ações entre componentes no serviço do Office 365. |
| IntraSystemId |     O GUID que é gerado pelo Azure Active Directory para controlar a ação. |
| SupportTicketId | A ID do tíquete de suporte ao cliente para a ação em situações "agir em nome de". |
| TargetContextId | O GUID da organização à qual o usuário de destino pertence. |


### <a name="data-center-security"></a>Segurança do Data Center

Esses registros são criados de dados de auditoria de Segurança do Data Center.  

| Propriedade | Descrição |
|:--- |:--- |
| EffectiveOrganization | O nome do locatário ao qual o cmdlet \elevation foi direcionado. |
| ElevationApprovedTime | O carimbo de data/hora de quando a elevação foi aprovada. |
| ElevationApprover | O nome de um gerente Microsoft. |
| ElevationDuration | A duração pela qual a elevação ficou ativa. |
| ElevationRequestId |     Um identificador exclusivo para a solicitação de elevação. |
| ElevationRole | A função para a qual a elevação foi solicitada. |
| ElevationTime | A hora de início da elevação. |
| Start_Time | A hora de início da execução do cmdlet. |


### <a name="exchange-admin"></a>Exchange Admin

Esses registros são criados quando são feitas alterações à configuração do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Especifica se o cmdlet foi executado por um usuário em sua organização, por uma conta de serviço do datacenter ou pela equipe do datacenter da Microsoft ou por um administrador delegado. O valor False indica que o cmdlet foi executado por alguém de sua organização. O valor True indica que o cmdlet foi executado pela equipe do datacenter, uma conta de serviço do datacenter ou um administrador delegado. |
| ModifiedObjectResolvedName |     Esse é o nome amigável de usuário do objeto modificado pelo cmdlet. Isso é registrado apenas se o cmdlet modificar o objeto. |
| OrganizationName | O nome do locatário. |
| OriginatingServer | O nome do servidor do qual o cmdlet foi executado. |
| parâmetros | O nome e o valor para todos os parâmetros que foram usados com o cmdlet identificado na propriedade Operations. |


### <a name="exchange-mailbox"></a>Caixa de correio do Exchange

Esses registros são criados quando alterações ou adições são feitas às caixas de correio do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Informações sobre o cliente de email que foi usado para executar a operação, como uma versão do navegador, versão do Outlook e informações do dispositivo móvel. |
| Client_IPAddress | O endereço IP do dispositivo que foi usado quando a operação foi registrada. O endereço IP é exibido no formato de endereço IPv4 ou IPv6. |
| ClientMachineName | O nome do computador que hospeda o cliente do Outlook. |
| ClientProcessName | O cliente de email usado para acessar a caixa de correio. |
| ClientVersion | A versão do cliente de email. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica o tipo de usuário que acessou a caixa de correio e executou a operação que foi registrada. |
| LogonUserDisplayName |     O nome amigável do usuário que realizou a operação. |
| LogonUserSid | O SID do usuário que realizou a operação. |
| MailboxGuid | O GUID do Exchange da caixa de correio que foi acessada. |
| MailboxOwnerMasterAccountSid | O SID da conta mestre da conta do proprietário da caixa de correio. |
| MailboxOwnerSid | O SID do proprietário da caixa de correio. |
| MailboxOwnerUPN | O endereço de email da pessoa que detém a caixa de correio acessada. |


### <a name="exchange-mailbox-audit"></a>Auditoria de Caixa de Correio do Exchange

Esses registros são criados quando é criada uma entrada de auditoria de caixa de correio.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | Representa o item no qual a operação foi executada | 
| SendAsUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar email como ela. |
| SendAsUserSmtp | Endereço SMTP do usuário que está sendo representado. |
| SendonBehalfOfUserMailboxGuid | O GUID do Exchange da caixa de correio acessada para enviar emails em nome dela. |
| SendOnBehalfOfUserSmtp | Endereço SMTP do usuário em cujo nome o email é enviado. |


### <a name="exchange-mailbox-audit-group"></a>Grupo de Auditoria da Caixa de Correio do Exchange

Esses registros são criados quando alterações ou adições são feitas a grupos do Exchange.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Informações sobre cada item no grupo. |
| CrossMailboxOperations | Indica se a operação envolveu mais de uma caixa de correio. |
| DestMailboxId | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o GUID da caixa de correio de destino. |
| DestMailboxOwnerMasterAccountSid | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o SID para o SID da conta mestra do proprietário de caixa de correio de destino. |
| DestMailboxOwnerSid | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o SID da caixa de correio de destino. |
| DestMailboxOwnerUPN | Definido somente se o parâmetro CrossMailboxOperations for True. Especifica o UPN do proprietário da caixa de correio de destino. |
| DestFolder | A pasta de destino, para operações como Mover. |
| Pasta | A pasta na qual se encontra um grupo de itens. |
| Pastas |     Obter informações sobre as pastas de origem envolvidas em uma operação; por exemplo, se as pastas são selecionadas e então, excluídas. |


### <a name="sharepoint-base"></a>Base do SharePoint

Essas propriedades são comuns a todos os registros do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica o que ocorreu um evento no SharePoint. Os valores possíveis são SharePoint ou ObjectModel. |
| ItemType | O tipo de objeto acessado ou modificado. Consulte a tabela ItemType para obter detalhes sobre os tipos de objetos. |
| MachineDomainInfo | Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| MachineId |     Obter informações sobre operações de sincronização do dispositivo. Essas informações só serão relatadas se estiverem presentes na solicitação. |
| Site_ | O GUID do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| Source_Name | A entidade que disparou a operação auditada. Os valores possíveis são SharePoint ou ObjectModel. |
| UserAgent | Informações sobre o cliente ou o navegador do usuário. Essas informações são fornecidas pelo cliente ou pelo navegador. |


### <a name="sharepoint-schema"></a>Esquema do SharePoint

Esses registros são criados quando são feitas alterações de configuração do SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadeia de caracteres opcional para eventos personalizados. |
| Event_Data |     Conteúdo opcional para eventos personalizados. |
| ModifiedProperties | A propriedade é incluída para os eventos de administrador, como adicionar um usuário como um membro de um site ou um grupo de administração do conjunto de sites. A propriedade inclui o nome da propriedade que foi modificada (por exemplo, o grupo de Administrador do Site), o novo valor da propriedade modificada (como o usuário que foi adicionado como administrador do site) e o valor anterior do objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operações de arquivo do SharePoint

Esses registros são criados em resposta às operações de arquivo no SharePoint.

| Propriedade | Descrição |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | A extensão de arquivo de um arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationFileName | O nome do arquivo que é copiado ou movido. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| DestinationRelativeUrl | A URL da pasta de destino em que um arquivo é copiado ou movido. A combinação dos valores de parâmetros SiteURL, DestinationRelativeURL e DestinationFileName é igual ao valor da propriedade ObjectID, que é o nome de caminho completo para o arquivo copiado. Essa propriedade é exibida apenas para eventos FileCopied e FileMoved. |
| SharingType | O tipo de permissões de compartilhamento atribuídas ao usuário com o qual o recurso foi compartilhado. Esse usuário é identificado pelo parâmetro UserSharedWith. |
| Site_Url | A URL do site em que está localizado o arquivo ou a pasta acessado pelo usuário. |
| SourceFileExtension | A extensão de arquivo do arquivo que foi acessado pelo usuário. Essa propriedade ficará em branco se o objeto acessado for uma pasta. |
| SourceFileName |     O nome do arquivo ou pasta acessado pelo usuário. |
| SourceRelativeUrl | A URL da pasta que contém o arquivo acessado pelo usuário. A combinação de valores para os parâmetros SiteURL, SourceRelativeURL e SourceFileName é a mesma que o valor para a propriedade ObjectID, que é o nome de caminho completo do arquivo acessado pelo usuário. |
| UserSharedWith |     O usuário com o qual um recurso foi compartilhado. |




## <a name="sample-log-queries"></a>Consultas de registro de amostra

A tabela a seguir fornece consultas de registro de amostra para registros de atualização coletados por esta solução.

| Consulta | Descrição |
| --- | --- |
|Contagem de todas as operações em sua assinatura do Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso de sites do SharePoint|OfficeActivity &#124; onde officeworkload =~ "sharepoint" &#124; \| resumir contagem() por siteUrl classificar por Contagem asc|
|Operações de acesso de arquivos por tipo de usuário | OfficeActivity &#124; resumir contagem() por UserType |
|Monitorar de ações externas no Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Próximas etapas

* Use [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para exibir dados detalhados das atualizações.
* [Crie seus próprios painéis](../learn/tutorial-logs-dashboards.md) para exibir suas consultas de pesquisa favoritas do Office 365.
* [Crie alertas](../platform/alerts-overview.md) para ser notificado proativamente das atividades importantes do Office 365.  
