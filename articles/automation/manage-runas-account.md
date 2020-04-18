---
title: Gerenciar contas Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 341db4ffa5b2e0641572f2c9dc011e91fac6a1e9
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617314"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerenciar contas Executar como da Automação do Azure

Execute Como contas no Azure Automation fornecem autenticação para gerenciar recursos no Azure usando os cmdlets do Azure. Quando você cria uma conta Run As, ela cria um novo usuário principal de serviço no Azure Active Directory (AD) e atribui a função Contribuinte a esse usuário no nível de assinatura. Para runbooks que usam Hybrid Runbook Workers em máquinas virtuais do Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Executar como para autenticar para recursos do Azure.

O principal de serviço de uma conta run as não tem permissões para ler a Azure AD por padrão. Se você quiser adicionar permissões para ler ou gerenciar o Azure AD, você precisará conceder as permissões no principal de serviço sob **permissões de API**. Para saber mais, consulte [Adicionar permissões para acessar APIs da Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Este artigo foi atualizado para usar o novo módulo Az do Azure PowerShell. Você ainda pode usar o módulo AzureRM, que continuará a receber as correções de bugs até pelo menos dezembro de 2020. Para saber mais sobre o novo módulo Az e a compatibilidade com o AzureRM, confira [Apresentação do novo módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Para obter instruções de instalação do módulo AZ no trabalhador do runbook híbrido, consulte [Instalar o Módulo PowerShell do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Para sua conta de Automação, você pode atualizar seus módulos para a versão mais recente usando [Como atualizar módulos Azure PowerShell no Azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Tipos de contas de execução como

O Azure Automation usa dois tipos de contas de execução como:

* Azure Run Como conta
* Azure Classic Run Como conta

>[!NOTE]
>As assinaturas do Azure Cloud Solution Provider (CSP) suportam apenas o modelo Azure Resource Manager. Os serviços do Non-Azure Resource Manager não estão disponíveis no programa. Quando você está usando uma assinatura CSP, a conta Azure Classic Run As não é criada, mas a conta Azure Run As é criada. Para saber mais sobre assinaturas de CSP, consulte [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Conta Executar como

A conta Run As gerencia os recursos [do modelo de implantação do Gerenciador de](../azure-resource-manager/management/deployment-models.md) recursos. Ele faz as seguintes tarefas.

* Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de Colaborador à conta na assinatura atual. Você pode alterar a configuração do certificado para Proprietário ou qualquer outra função. Para obter mais informações, consulte [o controle de acesso baseado em função no Azure Automation](automation-role-based-access-control.md).
  
* Cria um ativo `AzureRunAsCertificate` de certificado de automação nomeado na conta de Automação especificada. O ativo certificado possui a chave privada de certificado que o aplicativo Azure AD usa.
  
* Cria um ativo `AzureRunAsConnection` de conexão de automação nomeado na conta de Automação especificada. O ativo de conexão contém o ID do aplicativo, o ID do inquilino, o ID de assinatura e a impressão digital do certificado.

### <a name="azure-classic-run-as-account"></a>Conta Executar como do Azure Clássico

A conta Azure Classic Run As gerencia os recursos [do modelo de implantação classic.](../azure-resource-manager/management/deployment-models.md) Você deve ser um co-administrador na assinatura para criar ou renovar esse tipo de conta.

A conta Azure Classic Run As executa as seguintes tarefas.

  * Cria um certificado de gerenciamento na assinatura.

  * Cria um ativo `AzureClassicRunAsCertificate` de certificado de automação nomeado na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.

  * Cria um ativo `AzureClassicRunAsConnection` de conexão de automação nomeado na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, o ID de assinatura e o nome do ativo do certificado.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Executar como permissões de conta

Esta seção define permissões para contas regulares executar como contas e contas clássicas de execução como.

### <a name="permissions-to-configure-run-as-accounts"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, é necessário ter privilégios e permissões específicos. Um administrador de aplicativos no Azure Active Directory e um Proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tenha separação de deveres, a tabela a seguir mostra uma lista das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Função de desenvolvedor de<sup>aplicativos 1</sup>        |[AD do Azure](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Inscrições de Aplicativos |
|Adicione uma credencial ao aplicativo.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Administrador de aplicativos ou Administrador Global<sup>1</sup>         |[AD do Azure](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Inscrições de Aplicativos|
|Crie e obtenha um principal de serviço azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Administrador de aplicativos ou Administrador Global<sup>1</sup>        |[AD do Azure](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Inscrições de Aplicativos|
|Atribuir ou obter a função RBAC para a entidade especificada|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Administrador ou Proprietário de Acesso ao Usuário, ou ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Página Inicial > Assinaturas > \<nome da assinatura\> – Controle de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[Certificado de automação da New-Az](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Certificado de automação remove-Az](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automação|
|Criar ou remover uma conexão de Automação|[Conexão New-AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Conexão remove-azautomation](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automação|

<sup>1</sup> Os usuários não administradores do seu inquilino Azure AD podem [registrar aplicativos AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se os usuários do inquilino do Azure AD **puderem registrar** a opção de aplicativos na página de configurações do Usuário como **Sim**. Se a configuração de registro do aplicativo **for Não,** o usuário que realizar esta ação deve ser definido nesta tabela.

Se você não for um membro da instância active directory da assinatura antes de ser adicionado à função administrador global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um `You do not have permissions to create…` aviso na página Adicionar Conta de Automação. 

Se você for um membro da instância active directory da assinatura quando a função `You do not have permissions to create…` Administrador Global for atribuída, você também poderá receber um aviso na página Adicionar conta de automação. Neste caso, você pode solicitar a remoção da instância do Active Directory da assinatura e, em seguida, solicitar a re-adição, para que você se torne um usuário completo no Active Directory.

Para verificar se a situação que produz a mensagem de erro foi corrigida:

1. A partir do painel diretório ativo do Azure no portal Azure, selecione **Usuários e grupos**. 
2. Selecione **Todos os usuários**.
3. Escolha seu nome e selecione **Perfil**. 
4. Certifique-se de que o valor do atributo **tipo de Usuário** no perfil do usuário não está definido como **Convidado**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Permissões para configurar contas clássicas de execução como

Para configurar ou renovar contas clássicas de execução como, você deve ter a função de Co-administrador no nível de assinatura. Para saber mais sobre as permissões clássicas de assinatura, consulte [administradores clássicos de assinatura do Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Criando uma conta run as no portal Azure

Execute as seguintes etapas para atualizar sua conta do Azure Automation no portal Azure. Crie as contas Run As e Classic Run As individualmente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.

1. Faça logon no portal do Azure com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.
2. Procure e selecione **Contas de Automação**.
3. Na página Contas de Automação, selecione sua conta de automação na lista.
4. No painel esquerdo, **selecione Executar como contas** na seção configurações da conta.
5. Dependendo da conta de que você precisa, selecione **Conta Executar como do Azure** ou **Conta Executar como Clássica do Azure**. 
6. Dependendo da conta de interesse, use o **painel Adicionar Azure Run As** ou Adicionar o painel **Azure Classic Run As Account.** Depois de revisar as informações da visão geral, clique em **Criar**.
6. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Um banner também é exibido informando que a conta está sendo criada. O processo pode levar alguns minutos para ser concluído.

## <a name="creating-a-run-as-account-using-powershell"></a>Criando uma conta como execução usando o PowerShell

A lista a seguir fornece os requisitos para criar uma conta Run As no PowerShell. Esses requisitos se aplicam a ambos os tipos de contas run as.

* Windows 10 ou Windows Server 2016 com os módulos 3.4.1 e posteriores do Azure Resource Manager. O script PowerShell não suporta versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter informações sobre a versão 1.0 do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Uma conta de Automação, que é `AutomationAccountName` referenciada como o valor para os parâmetros. `ApplicationDisplayName`
* Permissões equivalentes às listadas nas [permissões necessárias para configurar contas run as](#permissions).

Para obter os `SubscriptionId` `ResourceGroupName`valores para , e , que são parâmetros necessários para o script PowerShell, complete os próximos passos.

1. No portal Azure, selecione **Contas de Automação**.
1. Na página Contas de Automação, selecione sua conta de automação.
1. Na seção configurações da conta, selecione **Propriedades**.
1. Observe os valores **de NOME,** **ID de ASSINATURA**e GRUPO DE **RECURSOS** na página Propriedades. Esses valores correspondem `AutomationAccountName`aos `SubscriptionId`valores para os parâmetros de script , e `ResourceGroupName` PowerShell, respectivamente.

   ![Página de propriedades da conta de automação](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script do PowerShell para criar uma conta Run As

Esta seção fornece um script PowerShell para criar uma conta Run As. O script inclui suporte para várias configurações.

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.
* Criar uma conta Executar como e uma conta Clássica Executar como usando um certificado emitido por sua autoridade de certificação corporativa (CA).
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental.

O script usa vários cmdlets do Azure Resource Manager para criar recursos. Para obter os cmdlets e as permissões necessárias, consulte [Permissões para configurar contas run as](#permissions-to-configure-run-as-accounts).

Salve o script no computador usando o nome de arquivo **New-RunAsAccount.ps1**.

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
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

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
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
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

>[!NOTE]
>`Add-AzAccount`e `Add-AzureRMAccount` são pseudônimos para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou atualizar [seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Você pode precisar atualizar seus módulos mesmo se você acabou de criar uma nova conta de Automação.

### <a name="execute-the-powershell-script"></a>Execute o script PowerShell

1. Em sua máquina, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
1. A partir do shell de linha de comando elevado, vá para a pasta que contém o seu script.
1. Execute o script usando os valores de parâmetro para a configuração que você precisa.
1. Se criar uma conta Classic Run As, após a execução do script, carregue o certificado público (extensão de nome de arquivo **.cer)** para a loja de gerenciamento para a assinatura na qual a conta de Automação foi criada.

Depois que o script for executado, você é solicitado a autenticar com o Azure. Faça login com uma conta que é membro da função administradores de assinatura e co-administrador da assinatura.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Crie uma conta Run As usando um certificado auto-assinado

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Se você criou uma conta Classic Run As com um certificado público corporativo (arquivo **.cer),** use este certificado. Consulte [O envio de um certificado de API de gerenciamento para o portal Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se você criou uma conta Classic Run As com um certificado público auto-assinado (arquivo **.cer),** o script cria e salva na pasta de arquivos temporários em seu computador. Ele pode ser encontrado `%USERPROFILE%\AppData\Local\Temp`no perfil do usuário , que você usou para executar a sessão PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Excluindo uma execução como ou executar clássico como conta

Esta seção descreve como excluir uma conta Executar as ou clássica execução como. Quando você executa essa ação, a conta de Automação é mantida. Depois de excluir a conta, você pode recriá-la no portal Azure.

1. No Portal do Azure, abra a Conta de automação.

2. No painel esquerdo, **selecione Executar como contas** na seção configurações da conta.

3. Na página de propriedades de Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. 

4. No painel Propriedades para a conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

6. Depois que a conta for excluída, você poderá recriá-la na página de propriedades Contas Executar como selecionando a opção de criação **Executar como Conta do Azure**.

   ![Recriar a conta de Automação Executar como](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovando um certificado auto-assinado

O certificado auto-assinado que você criou para a conta Run As expira um ano a partir da data de criação. Em algum momento antes de sua conta Executar Como expirar, você deve renovar o certificado. Você pode renová-lo a qualquer momento antes de expirar. 

Quando você renova o certificado auto-assinado, o certificado válido atual é retido para garantir que quaisquer runbooks que estejam na fila ou em execução ativa e que sejam autenticados com a conta Run As não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

>[!NOTE]
>Se você acha que a conta Run As foi comprometida, você pode excluir e recriar o certificado auto-assinado.

>[!NOTE]
>Se você configurou sua conta Run As para usar um certificado emitido pela autoridade de certificado corporativo e usar a opção de renovar uma opção de certificado auto-assinado, o certificado corporativo será substituído por um certificado auto-assinado.

Use as seguintes etapas para renovar o certificado auto-assinado.

1. No Portal do Azure, abra a Conta de automação.

1. Selecione **Executar como contas** na seção configurações da conta.

    ![Painel de propriedades da conta de Automação](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página Executar como contas, selecione a conta Executar como ou a conta Classic Run As para a qual renovar o certificado.

1. No painel de propriedades da conta selecionada, clique **em Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Configuração de renovação automática de certificado com um runbook de automação

Para renovar certificados automaticamente, você pode usar um manual de automação. Este script no [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) habilita essa funcionalidade em sua conta de Automação.

>[!NOTE]
>Você deve ser um administrador global ou administrador de empresa no Azure AD para executar o script.

Este script cria um cronograma semanal para renovar os certificados de conta run as. Ele adiciona um **runbook Update-AutomationRunAsCredential** à sua conta de automação. Você pode visualizar o código do livro de execução no GitHub, no script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Você pode usar o código PowerShell no arquivo para renovar certificados manualmente, conforme necessário.

Use as seguintes etapas para testar o processo de renovação imediatamente.

1. Edite o **manual de atualização-automaçãoRunAsCredential** e coloque um caractere de comentário (#) na linha 122, em frente ao comando **Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Publique o manual.
3. Iniciar o runbook.
4. Verifique a renovação bem sucedida com o seguinte código:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Saída:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Após o teste, edite o manual e remova o caractere de comentário que você adicionou na etapa 1.
6. Publique o manual.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Limitar as permissões da conta Executar como

Para controlar a segmentação de Automação contra recursos no Azure, você pode executar o script [Update-AutomationRunAsAccountRoleAssignments.ps1.](https://aka.ms/AA5hug8) Este script altera o principal de serviço de conta Run As existente para criar e usar uma definição de função personalizada. A função tem permissões para todos os recursos, exceto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1,** os runbooks que acessam o Key Vault através do uso de contas Run As não funcionam mais. Antes de executar o script, você deve revisar os runbooks em sua conta para chamadas para o Azure Key Vault. Para habilitar o acesso ao Key Vault dos runbooks da Azure Automation, você deve [adicionar a conta Run As às permissões do Key Vault](#add-permissions-to-key-vault).

Se você precisar restringir, além do que o diretor de serviço Executar `NotActions` as pode fazer, você pode adicionar outros tipos de recursos ao elemento da definição de função personalizada. O exemplo a seguir `Microsoft.Compute/*`restringe o acesso a . Se você adicionar esse `NotActions` tipo de recurso para a definição da função, a função não poderá acessar qualquer recurso de Computação. Para saber mais sobre definições de função, consulte [Entender definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Você pode determinar se o principal de serviço usado por sua conta Executar As está na definição de função Contribuinte ou uma personalizada. 

1. Vá para sua conta de automação e **selecione Executar como contas** na seção de configurações da conta.
2. Selecione **Azure Run as Account**. 
3. Selecione **Função** para localizar a definição de função que está sendo usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Você também pode determinar a definição de função usada pelas contas Run As para várias assinaturas ou contas de automação. Faça isso usando o [script Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) na Galeria PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões ao Key Vault

Você pode permitir que o Azure Automation verifique se o Key Vault e o seu diretor de serviço de conta Run As estão usando uma definição de função personalizada. Você deve:

* Conceda permissões ao Key Vault.
* Defina a política de acesso.

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) na Galeria PowerShell para dar permissões de conta run as para o Key Vault. Consulte [os aplicativos Grant acessando um cofre de chaves](../key-vault/general/group-permissions-for-apps.md) para obter mais detalhes sobre a definição de permissões no Key Vault.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Resolvendo problemas de configuração incorreta para contas execute como

Alguns itens de configuração necessários para uma conta Run As ou Classic Run As podem ter sido excluídos ou criados incorretamente durante a configuração inicial. As possíveis instâncias de má configuração incluem:

* Ativo de certificado
* Ativo de conexão
* Executar como conta removida da função Contribuinte
* Entidade de serviço ou aplicativo no Azure AD

Para tais instâncias de configuração errada, a conta `Incomplete` Automação detecta as alterações e exibe um status do painel Executar como contas para a conta.

![Status incompleto de configuração de conta Executar como](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando você seleciona a conta Executar como, o painel propriedades da conta exibe a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre os principais do serviço, consulte [Objetos de aplicativo e Objetos Principais de Serviço](../active-directory/develop/app-objects-and-service-principals.md).
* Para saber mais sobre certificados e serviços do Azure, consulte [Visão geral dos certificados para Serviços de Nuvem do Microsoft Azure](../cloud-services/cloud-services-certs-create.md).
