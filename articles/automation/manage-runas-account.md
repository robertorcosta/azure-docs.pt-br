---
title: Gerenciar uma conta Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d85eac7af71f03be384b8fa9d9603525c0807dac
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770781"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gerenciar uma conta Executar como da Automação do Azure

As contas Executar como na Automação do Azure são usadas para fornecer autenticação para o gerenciamento de recursos no Azure com os cmdlets do Azure. Quando você cria uma conta Executar como, ela cria uma nova entidade de serviço no Azure Active Directory (AD) e atribui a função Colaborador a esse usuário no nível da assinatura.

## <a name="types-of-run-as-accounts"></a>Tipos de contas Executar como

A automação do Azure usa dois tipos de contas Executar como:

* Conta Executar como do Azure
* Conta Executar como Clássica do Azure

>[!NOTE]
>As assinaturas do CSP (Provedor de Soluções de Nuvem) do Azure são compatíveis apenas com o modelo do Azure Resource Manager. Serviços que não pertencem ao Azure Resource Manager não estão disponíveis no programa. Quando você estiver usando uma assinatura do CSP, a conta Executar como Clássica do Azure não será criada, mas a conta Executar como do Azure será criada. Para saber mais sobre assinaturas de CSP, consulte [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

A entidade de serviço para uma conta Executar como não tem permissões para ler o Azure AD por padrão. Se desejar adicionar permissões para ler ou gerenciar o Azure AD, você precisará conceder as permissões na entidade de serviço em **permissões de API**. Para saber mais, consulte [Adicionar permissões para acessar APIs Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Conta Executar como

A conta Executar como gerencia recursos do [Modelo de implantação do Resource Manager](../azure-resource-manager/management/deployment-models.md). Ela segue as seguintes etapas.

* Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de Colaborador à conta na assinatura atual. Você pode alterar a configuração de certificado para Proprietário ou qualquer outra função. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
  
* Cria um ativo de certificado de Automação chamado `AzureRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.
  
* Cria um ativo de conexão de Automação chamado `AzureRunAsConnection` na conta de Automação especificada. O ativo de conexão contém a ID do aplicativo, a ID do locatário, a ID da assinatura e a impressão digital do certificado.

### <a name="azure-classic-run-as-account"></a>Conta Executar como do Azure Clássico

A conta Executar como Clássica do Azure gerencia recursos do [Modelo de implantação clássico](../azure-resource-manager/management/deployment-models.md). Você deve ser um coadministrador na assinatura para criar ou renovar esse tipo de conta.

A conta Executar como Clássica do Azure executa as seguintes tarefas.

  * Configurar um certificado de gerenciamento na assinatura.

  * Cria um ativo de certificado de Automação chamado `AzureClassicRunAsCertificate` na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.

  * Cria um ativo de conexão de Automação chamado `AzureClassicRunAsConnection` na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, a ID da assinatura e o nome do ativo de certificado.

>[!NOTE]
>A conta Executar como Clássica do Azure não é criada por padrão ao mesmo tempo em que você cria uma conta de automação. Essa conta é criada individualmente seguindo as etapas descritas posteriormente neste artigo.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Obter as permissões da conta Executar como

Esta seção define permissões para contas Executar como regulares e contas Executar como Clássicas.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, é necessário ter privilégios e permissões específicos. Um administrador de aplicativos no Azure Active Directory e um proprietário em uma assinatura podem concluir todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Função Desenvolvedor de Aplicativos<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Início > Azure AD > Registros do Aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Início > Azure AD > Registros do Aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador do Aplicativo ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Início > Azure AD > Registros do Aplicativo|
|Atribuir ou obter a função RBAC para a entidade especificada|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador ou Proprietário de acesso do usuário ou ter as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Página Inicial > Assinaturas > \<nome da assinatura\> – Controle de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Colaborador no Grupo de Recursos         |Grupo de Recursos da Conta de Automação|
|Criar ou remover uma conexão de Automação|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Colaborador no Grupo de Recursos |Grupo de Recursos da Conta de Automação|

<sup>1</sup> Os usuários não administradores em seu locatário do Azure AD poderão [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se a opção **Os usuários podem registrar aplicativos** do locatário do Azure AD na página Configurações do usuário estiver definida como **Sim**. Se a configuração de registro do aplicativo for **Não**, o usuário que executa essa ação deverá ser conforme definido nesta tabela.

Se você não for um membro da instância do Active Directory da assinatura antes de ser adicionado à função de Administrador Global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um aviso de `You do not have permissions to create…` na página Adicionar Conta de Automação. 

Se você for um membro da instância de Active Directory da assinatura quando a função de Administrador Global for atribuída, também poderá receber um aviso de `You do not have permissions to create…` na página Adicionar Conta de Automação. Nesse caso, você pode solicitar a remoção da instância do Active Directory da assinatura e solicitar que ela seja adicionada novamente, para que você se torne um usuário completo no Active Directory.

Para verificar se a situação que produz a mensagem de erro foi remediada:

1. No painel do Azure Active Directory no portal do Azure, selecione **Usuários e grupos**. 
2. Selecione **Todos os usuários**.
3. Escolha seu nome e, em seguida, selecione **Perfil**. 
4. Verifique se o valor do atributo **Tipo de usuário** no perfil do usuário não está definido como **Convidado**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Obter permissões para configurar contas Executar como Clássicas

Para configurar ou renovar contas Executar como Clássicas, você deve ter a função de coadministrador no nível de assinatura. Para saber mais sobre as permissões de assinatura clássicas, consulte [Administradores de assinatura do Azure Clássico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Criar uma conta Executar como no portal do Azure

Execute as seguintes etapas para atualizar sua conta de Automação do Azure no portal do Azure. Criar as contas Executar como e Executar como Clássicas individualmente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.

1. Faça logon no portal do Azure com uma conta que seja membro da função Administradores de Assinatura e coadministrador da assinatura.
2. Pesquise e escolha **Contas de Automação**.
3. Na página Contas de Automação, escolha sua conta de Automação na lista.
4. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.
5. Dependendo da conta de que você precisa, selecione **Conta Executar como do Azure** ou **Conta Executar como Clássica do Azure**. 
6. Dependendo da conta de interesse, use o painel **Adicionar Executar como do Azure** ou **Adicionar a conta Executar como Clássica do Azure**. Depois de examinar as informações de visão geral, clique em **Criar**.
6. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. O processo pode levar alguns minutos.

## <a name="create-a-run-as-account-using-powershell"></a>Criar conta Executar como usando PowerShell

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell. Esses requisitos se aplicam a ambos os tipos de contas Executar como.

* Windows 10 ou Windows Server 2016 com os módulos 3.4.1 e posteriores do Azure Resource Manager. O script do PowerShell não é compatível com versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter informações sobre a versão 1.0 do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Uma Conta de automação, que é referenciada como o valor para os parâmetros `AutomationAccountName` e `ApplicationDisplayName`.
* Permissões equivalentes às listadas em [Permissões necessárias para configurar contas Executar como](#permissions).

Para obter os valores para `SubscriptionId`, `ResourceGroupName` e , que são parâmetros necessários para o script do PowerShell, conclua as próximas etapas.

1. No portal do Azure, selecione **Contas de Automação**.
1. Na página de Contas de Automação, selecione a sua Conta de automação.
1. Na seção de configurações da conta, selecione **Propriedades**.
1. Anote os valores de **NOME**, **ID DA ASSINATURA** e **GRUPO DE RECURSOS** na página Propriedades. Esses valores correspondem aos valores para os parâmetros de script do PowerShell `AutomationAccountName`, `SubscriptionId` e `ResourceGroupName`, respectivamente.

   ![Painel de propriedades da conta de automação](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Criar o script do PowerShell da conta Executar como

Esta seção fornece o script do PowerShell para criar uma conta Executar como. O script inclui suporte para várias configurações.

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.
* Criar uma conta Executar como e uma conta Clássica Executar como usando um certificado emitido por sua autoridade de certificação corporativa (CA).
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental.

O script usa vários cmdlets do Azure Resource Manager para criar recursos. Para os cmdlets e as permissões que eles exigem, consulte [Obter permissões para configurar contas Executar como](#get-permissions-to-configure-run-as-accounts).

Salve o script em seu computador usando o nome de arquivo **New-RunAsAccount.ps1**.

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
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId | Set-AzContext

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
    $SubscriptionName = $subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount` e `Add-AzureRMAccount` são exemplos de atualização para [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação.

### <a name="execute-the-powershell-script"></a>Execute o script do PowerShell

1. Em sua máquina, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
1. A partir do shell da linha de comando com privilégios elevados, acesse a pasta que contém o script.
1. Execute o script usando os valores de parâmetro para a configuração necessária.
1. Se criar uma conta Executar como Clássica, depois que o script for executado, carregue o certificado público (extensão de nome de arquivo **.cer**) para a assinatura em que a conta de Automação foi criada.

Depois que o script for executado, você deverá se autenticar no Azure. Entre com uma conta que seja membro da função de administradores de assinatura e coadministrador da assinatura.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Crie uma conta Executar como usando um certificado autoassinado

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Se tiver criado uma conta Executar como Clássica com um certificado público corporativo (arquivo **.cer**), use este certificado. Consulte [Carregar um certificado de gerenciamento de API](../azure-api-management-certs.md) no portal do Azure.

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se você tiver criado uma conta Executar como Clássica com um certificado público autoassinado (arquivo **.cer**), o script vai criá-lo e salvá-lo na pasta de arquivos temporários em seu computador. Ele pode ser encontrado no perfil do usuário `%USERPROFILE%\AppData\Local\Temp`, que você usou para executar a sessão do PowerShell.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica

Esta seção descreve como excluir uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Após excluir uma conta, você pode recriá-la no portal do Azure.

1. No Portal do Azure, abra a Conta de automação.

2. No painel do lado esquerdo, selecione **Contas Executar como** na seção Configurações de conta.

3. Na página de propriedades de Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. 

4. No painel Propriedades da conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

5. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

6. Depois que a conta for excluída, você poderá recriá-la na página de propriedades Contas Executar como selecionando a opção de criação **Executar como Conta do Azure**.

   ![Recriar a conta de Automação Executar como](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Renovar um certificado autoassinado

O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Antes da conta Executar como expirar, você deverá renovar o certificado. Você pode renová-lo a qualquer momento antes que ele expire. 

Ao renovar o certificado autoassinado, o certificado válido atual é retido para garantir que todos os runbooks colocados na fila ou em execução ativa e autenticados com a conta Executar como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

>[!NOTE]
>Se achar que a conta Executar como foi comprometida, você poderá excluir e recriar o certificado autoassinado.

>[!NOTE]
>Se você tiver configurado a conta Executar como para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção para renovar uma opção de certificado autoassinado, o certificado da empresa será substituído por um certificado autoassinado.

Use as etapas a seguir para renovar o certificado autoassinado.

1. No Portal do Azure, abra a Conta de automação.

1. Selecione **Contas Executar como** na seção configurações de conta.

    ![Painel de propriedades da conta de Automação](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página de propriedades Contas Executar como, selecione a conta Executar como ou a conta Executar como Clássica para a qual você deseja renovar o certificado.

1. No painel Propriedades da conta selecionada, clique em **Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="set-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Configurar a renovação automática de certificado com um runbook de automação

Para renovar certificados automaticamente, você pode usar um runbook de automação. Esse script em [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) permite essa funcionalidade em sua conta de automação.

>[!NOTE]
>Você deve ser um administrador global ou administrador da empresa no Azure AD para executar o script.

Esse script cria uma agenda semanal para renovar os certificados da conta Executar como. Ele adiciona um runbook **Update-AutomationRunAsCredential** à sua conta de automação. Você pode exibir o código do runbook no GitHub, no script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Você pode usar o código do PowerShell no arquivo para renovar certificados manualmente, conforme necessário.

Use as etapas a seguir para testar o processo de renovação imediatamente.

1. Edite o runbook **Update-AutomationRunAsCredential** e coloque um caractere de comentário (#) na linha 122, na frente do comando **Exit(1)** .

   ```powershell
   #Exit(1)
   ```

2. Publicar o runbook.
3. Iniciar o runbook.
4. Verifique a renovação bem-sucedida com o seguinte código:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Saída:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Após o teste, edite o runbook e remova o caractere de comentário que você adicionou na etapa 1.
6. Publique o runbook.

## <a name="limit-run-as-account-permissions"></a>Limitar as permissões da conta Executar como

Para controlar o direcionamento de automação contra recursos no Azure, você pode executar o script de[Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Esse script altera a entidade de serviço da conta Executar como existente para criar e usar uma definição de função personalizada. A função tem permissões para todos os recursos, exceto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Depois de executar o script **Update-AutomationRunAsAccountRoleAssignments.ps1**, os runbooks que acessam Key Vault por meio do uso de contas Executar como não funcionam mais. Antes de executar o script, você deve examinar os runbooks em sua conta para chamadas para Azure Key Vault. Para habilitar o acesso a Key Vault de runbooks de automação do Azure, você deve [adicionar a conta Executar como a permissões de Key Vault](#add-permissions-to-key-vault).

Se você precisar restringir, além do que a entidade de serviço executar como pode fazer, poderá adicionar outros tipos de recursos ao elemento `NotActions` da definição de função personalizada. O exemplo a seguir restringe o acesso a `Microsoft.Compute/*`. Se você adicionar esse tipo de recurso a `NotActions` para a definição de função, a função não poderá acessar nenhum recurso de computação. Para obter mais informações sobre definições de função, confira [Noções básicas sobre definições de função para recursos do Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Você pode determinar se a entidade de serviço usada pela sua conta Executar como está na definição da função colaborador ou em uma personalizada. 

1. Acesse sua conta de Automação e selecione **Contas Executar como** na seção de configurações de conta.
2. Selecione **Conta Executar como do Azure**. 
3. Selecione **Função** para localizar a definição de função que está sendo usada.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Você também pode determinar a definição de função usada pelas contas Executar como para várias assinaturas ou contas de automação. Faça isso usando o script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) no Galeria do PowerShell.

### <a name="add-permissions-to-key-vault"></a>Adicionar permissões a um Key Vault

Você pode permitir que a Automação do Azure verifique se Key Vault e sua entidade de serviço da conta Executar como estão usando uma definição de função personalizada. Você deve:

* Conceder permissões a um Key Vault.
* Definir a política de acesso.

Você pode usar o script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) no Galeria do PowerShell para conceder permissões de conta Executar como para Key Vault. Consulte [Conceder aos aplicativos acesso a um key vault](../key-vault/general/group-permissions-for-apps.md) para obter mais detalhes sobre como definir permissões no Key Vault.

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Resolver problemas de configuração incorreta para contas Executar como

Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. As possíveis instâncias de configuração incorreta incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Para outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades Contas Executar como para a conta.

![Status incompleto de configuração de conta Executar como](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando você selecionar a conta Executar como, o painel Propriedades da conta exibirá a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximas etapas

* [Objetos de aplicativo e de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* [Visão geral sobre certificados para os Serviços de Nuvem do Azure](../cloud-services/cloud-services-certs-create.md).
