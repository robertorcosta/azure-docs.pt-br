---
title: Gerenciar contas Executar como da automação do Azure
description: Este artigo descreve como gerenciar suas contas Executar como com o PowerShell ou no Portal.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fd7e94261d8302224b0e31e5f4ac46978dfa812f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690872"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerenciar contas Executar como da automação do Azure

As contas Executar como na automação do Azure são usadas para fornecer autenticação para gerenciar recursos no Azure com os cmdlets do Azure.

Quando você cria uma conta Executar como, ela cria um novo usuário de entidade de serviço no Azure Active Directory e atribui a função colaborador a esse usuário no nível de assinatura. Para runbooks que usam Hybrid runbook Workers em máquinas virtuais do Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Executar como para autenticar os recursos do Azure.

Há dois tipos de contas Executar como:

* **Conta Executar como do Azure** -essa conta é usada para gerenciar recursos do [modelo de implantação do Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de colaborador para a conta em sua assinatura atual. Você pode alterar essa configuração para proprietário ou qualquer outra função. Para obter mais informações, consulte [controle de acesso baseado em função na automação do Azure](automation-role-based-access-control.md).
  * Cria um ativo de certificado de automação chamado *AzureRunAsCertificate* na conta de automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.
  * Cria um ativo de conexão de automação chamado *AzureRunAsConnection* na conta de automação especificada. O ativo de conexão contém applicationId, tenantid, SubscriptionId e impressão digital do certificado.

* **Conta Executar como clássica do Azure** -essa conta é usada para gerenciar os recursos do [modelo de implantação clássico](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Cria um certificado de gerenciamento na assinatura
  * Cria um ativo de certificado de automação chamado *AzureClassicRunAsCertificate* na conta de automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.
  * Cria um ativo de conexão de automação chamado *AzureClassicRunAsConnection* na conta de automação especificada. O ativo de conexão contém o nome da assinatura, SubscriptionId e o nome do ativo do certificado.
  * Deve ser um coadministrador na assinatura para criar ou renovar

  > [!NOTE]
  > As assinaturas do provedor de soluções na nuvem do Azure (CSP do Azure) dão suporte apenas ao modelo de Azure Resource Manager, os serviços não Azure Resource Manager não estão disponíveis no programa. Ao usar uma assinatura do CSP, a conta Executar como clássica do Azure não é criada. A conta Executar como do Azure ainda é criada. Para saber mais sobre assinaturas do CSP, confira [serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > A entidade de serviço para uma conta Executar como não tem permissões para ler Azure Active Directory por padrão. Se você quiser adicionar permissões para ler ou gerenciar o Azure Active Directory, será necessário conceder essa permissão na entidade de serviço sob permissões de **API**. Para saber mais, consulte [adicionar permissões para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, você deve ter privilégios e permissões específicas. Um administrador de aplicativos no Azure Active Directory e um proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Função de desenvolvedor de aplicativo<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrador de aplicativos ou administrador GLOBAL<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrador de aplicativos ou administrador GLOBAL<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>> Página inicial Azure Active Directory > registros de aplicativo|
|Atribuir ou obter a função de RBAC para a entidade de segurança especificada|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Você deve ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Ou ser um:</br></br>Administrador ou proprietário de acesso do usuário        | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Home > assinaturas > nome do \<subscription \>-controle de acesso (IAM)|
|Criar ou remover um certificado de automação|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Colaborador no grupo de recursos         |Grupo de recursos da conta de automação|
|Criar ou remover uma conexão de automação|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Colaborador no grupo de recursos |Grupo de recursos da conta de automação|

<sup>1</sup> os usuários não administradores em seu locatário do Azure ad podem [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se a opção os usuários do locatário do Azure ad **podem registrar aplicativos** na página **configurações do usuário** estiver definida como **Sim**. Se a configuração registros do aplicativo estiver definida como **não**, o usuário que executa essa ação deverá ser o que está definido na tabela anterior.

Se você não for um membro da instância de Active Directory da assinatura antes de ser adicionado à função de **administrador global** da assinatura, você será adicionado como convidado. Nessa situação, você receberá uma `You do not have permissions to create…` aviso na página **adicionar conta de automação** . Os usuários que foram adicionados à função de **administrador global** primeiro podem ser removidos da instância de Active Directory da assinatura e adicionados novamente para torná-los um usuário completo no Active Directory. Para verificar essa situação, no painel de **Azure Active Directory** no portal do Azure, selecione **usuários e grupos**, selecione **todos os usuários** e, depois de selecionar o usuário específico, selecione **perfil**. O valor do atributo **Tipo de usuário** sob o perfil de usuários não deve ser igual a **Convidado**.

## <a name="permissions-classic"></a>Permissões para configurar contas Executar como clássicas

Para configurar ou renovar contas Executar como clássicas, você deve ter a função de **coadministrador** no nível de assinatura. Para saber mais sobre permissões clássicas, confira [Administradores de assinatura clássica do Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Criar uma conta Executar como no portal

Nesta seção, execute as etapas a seguir para atualizar sua conta de automação do Azure no portal do Azure. Você cria as contas Executar como e executar como clássicas individualmente. Se você não precisar gerenciar recursos clássicos, poderá criar apenas a conta Executar como do Azure.

1. Entre no portal do Azure com uma conta que seja membro da função Administradores de assinatura e coadministrador da assinatura.
2. Na portal do Azure, clique em **todos os serviços**. Na lista de recursos, digite **automação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Automação**.
3. Na página **contas de automação** , selecione sua conta de automação na lista de contas de automação.
4. No painel esquerdo, selecione **contas Executar como** na seção **configurações de conta**.
5. Dependendo de qual conta você precisa, selecione a **conta Executar como do Azure** ou a **conta Executar como clássica do Azure**. Depois de selecionar o painel **Adicionar Azure executar como** ou **adicionar conta Executar como clássica do Azure** aparece e depois de examinar as informações de visão geral, clique em **criar** para continuar com a criação da conta Executar como.
6. Enquanto o Azure cria a conta Executar como, você pode acompanhar o progresso em **notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. Esse processo pode levar alguns minutos para ser concluído.

## <a name="create-run-as-account-using-powershell"></a>Criar conta Executar como usando o PowerShell

## <a name="prerequisites"></a>Pré-requisitos

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell:

* Windows 10 ou Windows Server 2016 com módulos Azure Resource Manager 3.4.1 e posterior. O script do PowerShell não oferece suporte a versões anteriores do Windows.
* Azure PowerShell 1,0 e posterior. Para obter informações sobre a versão 1,0 do PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Uma conta de automação, que é referenciada como o valor para os parâmetros *– AutomationAccountName* e *-ApplicationDisplayName* .
* Permissões equivalentes ao que está listado em [permissões necessárias para configurar contas Executar como](#permissions)

Para obter os valores de *SubscriptionId*, *resourcegroup*e *AutomationAccountName*, que são parâmetros necessários para o script, conclua as seguintes etapas:

1. Na portal do Azure, clique em **todos os serviços**. Na lista de recursos, digite **automação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Automação**.
1. Na página conta de automação, selecione sua conta de automação e, em **configurações de conta** , selecione **Propriedades**.
1. Observe os valores de **ID da assinatura**, **nome**e **grupo de recursos** na página **Propriedades** .

   ![A página de "Propriedades" da conta de automação](media/manage-runas-account/automation-account-properties.png)

Este script do PowerShell inclui suporte para as seguintes configurações:

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como clássica usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como clássica usando um certificado emitido pela autoridade de certificação (CA) corporativa.
* Crie uma conta Executar como e uma conta Executar como clássica usando um certificado autoassinado na nuvem do Azure governamental.

>[!NOTE]
> Se você selecionar uma das opções para criar uma conta Executar como clássica, depois que o script for executado, carregue o certificado público (extensão de nome de arquivo. cer) no repositório de gerenciamento da assinatura na qual a conta de automação foi criada.

1. Salve o script a seguir no seu computador. Neste exemplo, salve-o com o nome de arquivo *New-RunAsAccount. ps1*.

   O script usa vários cmdlets Azure Resource Manager para criar recursos. A tabela de [permissões](#permissions) precedentes mostra os cmdlets e suas permissões necessárias.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-AzureRmAccount** agora é um alias para **Connect-AzureRmAccount**. Ao pesquisar os itens de biblioteca, se você não vir **Connect-AzureRMAccount**, poderá usar **Add-AzureRMAccount**ou poderá [atualizar seus módulos](automation-update-azure-modules.md) na sua conta de automação.

1. Em sua máquina, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
1. No Shell de linha de comando com privilégios elevados, vá para a pasta que contém o script que você criou na etapa 1.
1. Execute o script usando os valores de parâmetro para a configuração necessária.

    **Criar uma conta Executar como usando um certificado autoassinado**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Criar uma conta Executar como e uma conta Executar como clássica usando um certificado autoassinado**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Criar uma conta Executar como e uma conta Executar como clássica usando um certificado corporativo**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Criar uma conta Executar como e uma conta Executar como clássica usando um certificado autoassinado na nuvem do Azure governamental**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Depois que o script for executado, você será solicitado a autenticar com o Azure. Entre com uma conta que seja membro da função Administradores de assinatura e coadministrador da assinatura.

Depois que o script for executado com êxito, observe o seguinte:

* Se você criou uma conta Executar como clássica com um certificado público autoassinado (arquivo. cer), o script cria e salva-o na pasta arquivos temporários em seu computador sob o perfil de usuário *%USERPROFILE%\AppData\Local\Temp*, que você usou para executar a sessão do PowerShell.

* Se você criou uma conta Executar como clássica com um certificado público corporativo (arquivo. cer), use este certificado. Siga as instruções para [carregar um certificado de API de gerenciamento para o portal do Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou executar como clássica

Esta seção descreve como excluir e recriar uma conta Executar como ou executar como clássica. Quando você executa essa ação, a conta de automação é mantida. Depois de excluir uma conta Executar como ou executar como clássica, você poderá recriá-la no portal do Azure.

1. Na portal do Azure, abra a conta de automação.

2. Na página **conta de automação** , selecione **contas Executar como**.

3. Na página de propriedades **contas Executar como** , selecione a conta Executar como ou a conta Executar como clássica que você deseja excluir. Em seguida, no painel **Propriedades** da conta selecionada, clique em **excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

1. Enquanto a conta está sendo excluída, você pode acompanhar o progresso em **notificações** no menu.

1. Depois que a conta tiver sido excluída, você poderá recriá-la na página de propriedades **contas Executar como** selecionando a opção criar **conta Executar como do Azure**.

   ![Recriar a conta Executar como de automação](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Renovação de certificado autoassinado

Em algum momento antes de a conta Executar como expirar, você precisará renovar o certificado. Se você acredita que a conta Executar como foi comprometida, você pode excluí-la e recriá-la. Esta seção discute como executar essas operações.

O certificado autoassinado que você criou para a conta Executar como expira um ano a partir da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Ao renová-lo, o certificado válido atual é mantido para garantir que todos os runbooks que estiverem na fila ou que estejam em execução ativamente, e que sejam autenticados com a conta Executar como, não sejam afetados negativamente. O certificado permanece válido até sua data de expiração.

> [!NOTE]
> Se você tiver configurado sua conta Executar como de automação para usar um certificado emitido por sua autoridade de certificação corporativa e usar essa opção, o certificado corporativo será substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. Na portal do Azure, abra a conta de automação.

1. Selecione **contas Executar como** em **configurações da conta**.

    ![Painel de propriedades da conta de automação](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página de propriedades **contas Executar como** , selecione a conta Executar como ou a conta Executar como clássica para a qual você deseja renovar o certificado.

1. No painel **Propriedades** da conta selecionada, clique em **renovar certificado**.

    ![Renovar certificado para conta Executar como](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado está sendo renovado, você pode acompanhar o progresso em **notificações** no menu.

## <a name="auto-cert-renewal"></a>Configurar a renovação automática de certificado com um runbook de automação

Para renovar certificados automaticamente, você pode usar um runbook de automação. O script a seguir no [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) habilita essa funcionalidade em sua conta de automação.

- O script de `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` cria uma agenda semanal para renovar os certificados da conta Executar como.
- O script adiciona um runbook **Update-AutomationRunAsCredential** à sua conta de automação.
  - Você também pode exibir o código do runbook no GitHub, no script: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - Você também pode usar o código do PowerShell no arquivo para renovar certificados manualmente, conforme necessário.

Para testar o processo de renovação imediatamente, use as seguintes etapas:

1. Edite o runbook **Update-AutomationRunAsCredential** e coloque um caractere de comentário (`#`) na linha 122, na frente do comando `Exit(1)`, conforme mostrado abaixo.

   ```powershell
   #Exit(1)
   ```

2. Publique o runbook.
3. Inicie o runbook.
4. Verifique a renovação bem-sucedida com o seguinte código:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Após o teste, edite o runbook e remova o caractere de comentário que você adicionou na **etapa 1**.
6. **Publique** o runbook.

> [!NOTE]
> Você precisa ser um administrador **global** ou **administrador da empresa** no Azure Active Directory, para executar o script.

## <a name="limiting-run-as-account-permissions"></a>Limitando permissões de conta Executar como

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) na galeria do PowerShell para alterar a entidade de serviço da conta Executar como existente para criar e usar uma função personalizada defini. Essa função terá permissões para todos os recursos, exceto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Depois de executar o script de `Update-AutomationRunAsAccountRoleAssignments.ps1`, os runbooks que acessam o keyvault por meio do uso de contas Executar como não funcionarão mais. Você deve examinar os runbooks em sua conta para chamadas para o Azure keyvault.
>
> Para habilitar o acesso ao keyvault por meio de runbooks de automação do Azure, você precisaria [Adicionar a conta Executar como às permissões do keyvault](#add-permissions-to-key-vault).

Se você precisar restringir o que a entidade de serviço RunAs pode fazer mais, você pode adicionar outros tipos de recursos ao `NotActions` da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute`. Se você adicionar isso às minhas **ações** da definição de função, essa função não poderá acessar nenhum recurso de computação. Para saber mais sobre definições de função, confira [noções básicas sobre definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Para determinar se a entidade de serviço usada pela sua conta Executar como está no **colaborador** ou em uma definição de função personalizada, vá para sua conta de automação e, em **configurações de conta**, selecione **contas Executar como**  > **conta Executar como do Azure**. Em **função** , você encontrará a definição de função que está sendo usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Para determinar a definição de função usada pelas contas Executar como de automação para várias assinaturas ou contas de automação, você pode usar o script [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a Key Vault

Se você quiser permitir que a automação do Azure gerencie Key Vault e sua entidade de serviço da conta Executar como esteja usando uma definição de função personalizada, você precisará executar etapas adicionais para permitir esse comportamento:

* Conceder permissões para o Key Vault
* Definir a política de acesso

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para dar permissões à conta Executar como ao keyvault ou visitar [conceder aplicativos acesso a um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md) para obter mais detalhes sobre as configurações permissões no keyvault.

## <a name="misconfiguration"></a>Configuração incorreta

Alguns itens de configuração necessários para que a conta Executar como ou executar como clássica funcione corretamente podem ter sido excluídos ou criados incorretamente durante a configuração inicial. Os itens incluem:

* Ativo de certificado
* Ativo de conexão
* A conta Executar como foi removida da função colaborador
* Entidade de serviço ou aplicativo no Azure AD

No anterior e em outras instâncias de configuração incorreta, a conta de automação detecta as alterações e exibe um status *incompleto* no painel de propriedades **contas Executar como** para a conta.

![Status de configuração da conta Executar como incompleto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando você seleciona a conta Executar como, o painel **Propriedades** da conta exibe a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre entidades de serviço, consulte [objetos de aplicativo e objetos de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* Para obter mais informações sobre certificados e serviços do Azure, consulte [visão geral de certificados para serviços de nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
