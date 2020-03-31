---
title: Habilite auditorias de segurança para serviços de domínio Ad do Azure | Microsoft Docs
description: Saiba como habilitar auditorias de segurança para centralizar o registro de eventos para análise e alertas nos Serviços de Domínio ad do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2138818a9092999dd54b14664f7146f087c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328639"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Habilite auditorias de segurança para serviços de domínio do diretório ativo do Azure

As auditorias de segurança do Azure Active Directory Domain Services (Azure AD DS) permitem que os eventos de segurança do fluxo do Azure sejam direcionados aos recursos direcionados. Esses recursos incluem o Azure Storage, o Azure Log Analytics ou o Azure Event Hub. Depois de habilitar eventos de auditoria de segurança, o Azure AD DS envia todos os eventos auditados para a categoria selecionada para o recurso direcionado.

Você pode arquivar eventos no armazenamento do Azure e transmitir eventos em software siem (ou equivalente) de gerenciamento de informações de segurança e gerenciamento de eventos (SIEM) usando o Azure Event Hubs, ou fazer sua própria análise e usando espaços de trabalho do Azure Log Analytics a partir do portal Azure.

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS só estão disponíveis para instâncias baseadas no Azure Resource Manager. Para obter informações sobre como migrar, consulte [Migrate Azure AD DS do modelo de rede virtual Classic para Resource Manager][migrate-azure-adds].

## <a name="audit-event-categories"></a>Categorias de eventos de auditoria

As auditorias de segurança Do Azure AD DS estão alinhadas com a auditoria tradicional para controladores de domínio AD DS tradicionais. Em ambientes híbridos, você pode reutilizar padrões de auditoria existentes para que a mesma lógica possa ser usada ao analisar os eventos. Dependendo do cenário que você precisa para solucionar ou analisar, as diferentes categorias de eventos de auditoria precisam ser direcionadas.

As seguintes categorias de eventos de auditoria estão disponíveis:

| Nome da categoria de auditoria | Descrição |
|:---|:---|
| Logon de contas|Auditorias tentam autenticar dados de conta em um controlador de domínio ou em um SAM (Security Accounts Manager, gerente de contas de segurança local).</p>As configurações de diretiva de logon e logoff e os eventos rastreiam tentativas de acessar um computador específico. As configurações e eventos nesta categoria se concentram no banco de dados de conta que é usado. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da validação de credenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditoria do serviço de autenticação Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditoria das operações do tíquete de serviço Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditoria de outros eventos de logon/logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gerenciamento de Conta|Auditorias alterações em contas e grupos de usuários e computadores. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do gerenciamento de grupo de aplicativos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditoria do gerenciamento da conta de computador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditoria do gerenciamento do grupo de distribuição](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditoria Outra gestão de contas](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditoria de gerenciamento do grupo de distribuição](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditoria de gerenciamento de conta de usuário](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rastreamento de detalhes|Audita atividades de aplicativos individuais e usuários nesse computador, e para entender como um computador está sendo usado. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da atividade DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Atividade PNP de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditoria do processo de criação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditoria do encerramento do processo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditoria de eventos de RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso aos serviços de diretório|As auditorias tentam acessar e modificar objetos no AD DS (Active Directory Domain Services, serviços de domínio de diretório ativo). Esses eventos de auditoria são registrados apenas em controladores de domínio. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de replicação detalhada do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditoria de acesso do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditoria de mudanças do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditoria de replicação do serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|As auditorias tentam fazer logon em um computador de forma interativa ou em uma rede. Esses eventos são úteis para rastrear a atividade do usuário e identificar potenciais ataques aos recursos da rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de bloqueio de conta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditoria das declarações de dispositivo/usuário](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditoria do modo IPsec estendido](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Auditoria da associação a um grupo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditoria do modo IPsec principal](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditoria do modo IPsec rápido](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditoria de logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditoria de logon](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditoria do Servidor de Políticas de Rede](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditoria de outros eventos de logon/logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditoria de logon especial](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a objetos| Auditorias tentam acessar objetos ou tipos específicos de objetos em uma rede ou computador. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria de aplicativo gerado](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditoria de serviços de certificação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditoria de compartilhamento de arquivos detalhado](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditoria de compartilhamento de arquivos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Sistema de arquivos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditoria de conexão de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditoria de descarte de pacote de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulação do cabo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Objeto do kernel de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditoria de outros eventos de acesso ao objeto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registro de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Armazenamento removível de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditoria de SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditoria do preparo da política de acesso central](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Mudança de política|As auditorias mudam para políticas de segurança importantes em um sistema ou rede local. As políticas são normalmente estabelecidas pelos administradores para ajudar a proteger os recursos da rede. Monitorar mudanças ou tentativas de mudar essas políticas pode ser um aspecto importante da gestão de segurança para uma rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria da mudança na política de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditoria da mudança na política de autenticação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditoria da mudança na política de autorização](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditoria da mudança na política de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditoria da mudança na política de nível de regra MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditar outras mudanças de políticas](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso privilegiado| Audita o uso de certas permissões em um ou mais sistemas. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do uso de privilégios não confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditoria do uso de privilégios confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditoria de outros eventos de uso de privilégios](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Audita alterações no nível do sistema para um computador não incluído em outras categorias e que têm potenciais implicações de segurança. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditoria do driver IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditoria de outros eventos do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditoria da mudança no estado de segurança](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditoria da extensão do sistema de segurança](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditoria da integridade do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de eventos por categoria

 As auditorias de segurança do Azure AD DS registram os seguintes IDs de evento quando a ação específica desencadeia um evento auditável:

| Nome da categoria do evento | IDs de eventos |
|:---|:---|
|Segurança do Logon da conta|4767, 4774, 4775, 4776, 4777|
|Segurança de gerenciamento de contas|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de rastreamento de detalhes|Nenhum|
|Segurança do Acesso DS|5136, 5137, 5138, 5139, 5141|
|Segurança logon-logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de acesso a objetos|Nenhum|
|Segurança de mudança de política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Segurança do Uso de Privilégios|4985|
|Segurança do sistema|4612, 4621|

## <a name="security-audit-destinations"></a>Destinos de auditoria de segurança

Você pode usar os espaços de trabalho Do Azure Storage, Azure Event Hubs ou Azure Log Analytics como um recurso de destino para auditorias de segurança Do Azure AD DS. Esses destinos podem ser combinados. Por exemplo, você pode usar o Azure Storage para arquivar eventos de auditoria de segurança, mas um espaço de trabalho do Azure Log Analytics para analisar e relatar as informações no curto prazo.

A tabela a seguir descreve cenários para cada tipo de recurso de destino.

> [!IMPORTANT]
> Você precisa criar o recurso de destino antes de ativar auditorias de segurança Do Azure AD DS. Você pode criar esses recursos usando o portal Azure, o Azure PowerShell ou o Azure CLI.

| Recurso de destino | Cenário |
|:---|:---|
|Armazenamento do Azure| Esse alvo deve ser usado quando sua principal necessidade é armazenar eventos de auditoria de segurança para fins de arquivamento. Outros alvos podem ser usados para fins de arquivamento, no entanto, esses alvos fornecem recursos além da necessidade primária de arquivamento. <br /><br />Antes de ativar eventos de auditoria de segurança Do Azure AD, crie primeiro [uma conta de armazenamento Azure](../storage/common/storage-account-create.md).|
|Hubs de eventos do Azure| Esse alvo deve ser usado quando sua principal necessidade é compartilhar eventos de auditoria de segurança com softwares adicionais, como software de análise de dados ou software de gerenciamento de & de eventos (SIEM).<br /><br />Antes de ativar eventos de auditoria de segurança do Azure AD DS, [crie um hub de eventos usando o portal Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Espaço de trabalho do Azure Log Analytics| Esse alvo deve ser usado quando sua principal necessidade é analisar e revisar auditorias seguras diretamente do portal Azure.<br /><br />Antes de ativar eventos de auditoria de segurança do Azure AD DS, [crie um espaço de trabalho do Log Analytics no portal Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Habilite eventos de auditoria de segurança usando o portal Azure

Para habilitar eventos de auditoria de segurança Do Azure AD DS usando o portal Azure, complete as seguintes etapas.

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS não são retroativas. Você não pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorrem após a ativação das auditorias de segurança.

1. Entre no Portal do Azure em https://portal.azure.com.
1. No topo do portal Azure, procure e selecione **Azure AD Domain Services**. Escolha seu domínio gerenciado, como *aaddscontoso.com*.
1. Na janela Azure AD DS, selecione **Configurações de diagnóstico** no lado esquerdo.
1. Nenhum diagnóstico é configurado por padrão. Para começar, selecione **Adicionar configuração de diagnóstico**.

    ![Adicionar uma configuração de diagnóstico para serviços de domínio Azure AD](./media/security-audit-events/add-diagnostic-settings.png)

1. Digite um nome para a configuração de diagnóstico, como *aadds-auditing*.

    Verifique a caixa para o destino da auditoria de segurança desejada. Você pode escolher entre uma conta do Azure Storage, um hub de eventos Do Zure ou um espaço de trabalho do Log Analytics. Esses recursos de destino já devem existir na sua assinatura do Azure. Você não pode criar os recursos de destino neste assistente.

    ![Habilite o destino e o tipo de eventos de auditoria necessários para capturar](./media/security-audit-events/diagnostic-settings-page.png)

    * **Armazenamento azure**
        * Selecione **'Arquivamento' para uma conta de armazenamento**e, em seguida, escolha **Configurar**.
        * Selecione a **assinatura** e a **conta de armazenamento** que você deseja usar para arquivar eventos de auditoria de segurança.
        * Quando estiver pronto, escolha **OK**.
    * **Hubs de eventos do Azure**
        * Selecione **'Transmitir para um hub de eventos'** e, em seguida, escolha **Configurar**.
        * Selecione o **espaço de nome da Assinatura** e do centro de **eventos**. Se necessário, escolha também um **nome de hub de eventos** e, em seguida, nome da política do hub de **eventos**.
        * Quando estiver pronto, escolha **OK**.
    * **Espaços de trabalho do Azure Log Analytic**
        * Selecione **Enviar para o Log Analytics**e escolha o espaço de trabalho de **assinatura** e de análise **de log** que deseja usar para armazenar eventos de auditoria de segurança.

1. Selecione as categorias de log que você deseja incluídas para o recurso de destino em particular. Se você enviar os eventos de auditoria para uma conta do Azure Storage, você também poderá configurar uma política de retenção que define o número de dias para reter dados. Uma configuração padrão de *0* retém todos os dados e não gira eventos após um período de tempo.

    Você pode selecionar diferentes categorias de log para cada recurso direcionado em uma única configuração. Essa habilidade permite que você escolha quais logs categorias você deseja manter no Log Analytics e quais categorias de logs você deseja arquivar, por exemplo.

1. Quando estiver pronto, **selecione Salvar** para cometer suas alterações. Os recursos-alvo começam a receber eventos de auditoria de segurança Do Azure AD DS logo após a configuração ser salva.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Habilite eventos de auditoria de segurança usando o Azure PowerShell

Para habilitar eventos de auditoria de segurança AD DS do Azure usando o Azure PowerShell, complete as seguintes etapas. Se necessário, instale primeiro [o módulo Azure PowerShell e conecte-se à sua assinatura do Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> As auditorias de segurança do Azure AD DS não são retroativas. Você não pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorrem após a ativação das auditorias de segurança.

1. Autenticar a sua assinatura do Azure usando o cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Quando solicitado, digite as credenciais de sua conta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Crie o recurso de destino para os eventos de auditoria de segurança.

    * **Armazenamento do Azure** - [Crie uma conta de armazenamento usando o Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Hubs de eventos do Azure** - [Crie um hub de eventos usando o Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Você também pode precisar usar o [cmdlet New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) para criar uma regra de autorização que concede permissões Azure AD DS para o *namespace*do hub de eventos . A regra de autorização deve incluir os direitos **gerenciar,** **ouvir**e **enviar.**

        > [!IMPORTANT]
        > Certifique-se de definir a regra de autorização no namespace do hub de eventos e não no hub de eventos em si.

    * **Os espaços** - de trabalho do Azure Log Analytic[criam um espaço de trabalho do Log Analytics com o Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Obtenha o ID de recurso para o domínio Gerenciado pelo Azure AD DS usando o cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Crie uma variável chamada *$aadds. ResourceId* para manter o valor:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configure as configurações do Diagnóstico do Azure usando o [cmdlet Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para usar o recurso de destino para eventos de auditoria de segurança do Azure AD Domain Services. Nos exemplos a seguir, a variável *$aadds. ResourceId* é usado da etapa anterior.

    * **Armazenamento azure** - Substitua *o armazenamentoAccountId* pelo nome da sua conta de armazenamento:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Hubs de eventos do Azure** - Substitua o *eventHubName* pelo nome do seu hub de eventos e *eventHubRuleId* com o seu ID de regra de autorização:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Espaços de trabalho do Azure Log Analytic** - Substitua o *workspaceId* pelo ID do espaço de trabalho do Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Consultar e visualizar eventos de auditoria de segurança usando o Azure Monitor

Os espaços de trabalho do Log Analytic permitem visualizar e analisar os eventos de auditoria de segurança usando o Azure Monitor e a linguagem de consulta Kusto. Esta linguagem de consulta foi projetada para uso somente leitura que possui recursos analíticos de energia com uma sintaxe fácil de ler. Para obter mais informações para começar com os idiomas de consulta do Kusto, consulte os seguintes artigos:

* [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Introdução ao Log Analytics no Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Introdução às consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Criar e compartilhar dashboards de dados do Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

As seguintes consultas de amostra podem ser usadas para começar a analisar eventos de auditoria de segurança do Azure AD DS.

### <a name="sample-query-1"></a>Consulta de amostra 1

Veja todos os eventos de bloqueio de conta dos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Consulta de amostra 2

Veja todos os eventos de bloqueio da conta (*4740*) entre 3 de fevereiro de 2020 às 9:00. e 10 de fevereiro de 2020 à meia-noite, ordenado ascender pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Consulta de amostra 3

Exibir eventos de login de conta há sete dias (a partir de agora) para o usuário nomeado da conta:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Consulta de amostra 4

Veja os eventos de login da conta sete dias atrás para o usuário chamado conta que tentou entrar usando uma senha ruim (*0xC00000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Consulta de amostra 5

Exibir eventos de login da conta sete dias atrás para o usuário nomeado da conta que tentou entrar enquanto a conta estava bloqueada (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Consulta de amostra 6

Veja o número de eventos de login da conta sete dias atrás para todas as tentativas de login que ocorreram para todos os usuários bloqueados:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações específicas sobre Kusto, consulte os seguintes artigos:

* [Visão geral](/azure/kusto/query/) da linguagem de consulta kusto.
* [Tutorial de Kusto](/azure/kusto/query/tutorial) para familiarizá-lo com o básico da consulta.
* [Consultas de exemplo](/azure/kusto/query/samples) que ajudam você a aprender novas maneiras de ver seus dados.
* As [melhores práticas da](/azure/kusto/query/best-practices) Kusto para otimizar suas consultas para o sucesso.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
