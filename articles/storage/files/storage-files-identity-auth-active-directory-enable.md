---
title: Habilitar a autenticação de Active Directory sobre o SMB para arquivos do Azure
description: Saiba como habilitar a autenticação baseada em identidade sobre SMB para compartilhamentos de arquivos do Azure por meio de Active Directory. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando credenciais do AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: rogarana
ms.openlocfilehash: b2dd501344e1ea799db58ea749395aaed05d05f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82106536"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Habilitar a autenticação de Active Directory Domain Services local sobre SMB para compartilhamentos de arquivos do Azure

[Os arquivos](storage-files-introduction.md) do Azure oferecem suporte à autenticação baseada em identidade sobre o protocolo SMB por meio de dois tipos de serviços de domínio: Azure Active Directory Domain Services (Azure AD DS) e Active Directory Domain Services local (AD DS) (visualização). Este artigo se concentra no suporte introduzido recentemente (versão prévia), aproveitando o serviço Domínio do Active Directory para autenticação em compartilhamentos de arquivos do Azure. Se você estiver interessado em habilitar a autenticação do Azure AD DS (GA) para compartilhamentos de arquivos do Azure, consulte [nosso artigo sobre o assunto](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Os compartilhamentos de arquivos do Azure dão suporte apenas à autenticação em um serviço de domínio, seja Azure Active Directory serviço de domínio (AD DS do Azure) ou Active Directory Domain Services local (AD DS). 
>
> AD DS identidades usadas para a autenticação de compartilhamento de arquivos do Azure devem ser sincronizadas com o Azure AD. A sincronização de hash de senha é opcional. 
> 
> A autenticação de AD DS local não oferece suporte à autenticação em relação às contas de computador criadas no AD DS. 
> 
> A autenticação de AD DS local só pode ser suportada em uma floresta do AD na qual a conta de armazenamento está registrada. Você só pode acessar compartilhamentos de arquivos do Azure com as credenciais de AD DS de uma única floresta por padrão. Se você precisar acessar o compartilhamento de arquivos do Azure de uma floresta diferente, verifique se você tem a relação de confiança de floresta apropriada configurada, consulte as [perguntas frequentes](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter detalhes.  
> 
> Há suporte para a autenticação AD DS para acesso SMB e persistência de ACL em compartilhamentos de arquivos do Azure gerenciados pelo Sincronização de Arquivos do Azure.
>
> Os arquivos do Azure dão suporte à autenticação Kerberos com o AD com a criptografia RC4-HMAC. A criptografia Kerberos AES ainda não tem suporte.

Quando você habilita AD DS para compartilhamentos de arquivos do Azure via SMB, seus computadores ingressados AD DS podem montar compartilhamentos de arquivos do Azure usando suas credenciais existentes do AD. Esse recurso pode ser habilitado com um ambiente de AD DS hospedado em computadores locais ou hospedado no Azure.

As identidades usadas para acessar os compartilhamentos de arquivos do Azure devem ser sincronizadas com o Azure AD para impor permissões de arquivo de nível de compartilhamento por meio do modelo [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) . As [DACLs de estilo do Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em arquivos/diretórios transferidos de servidores de arquivos existentes serão preservadas e impostas. Esse recurso oferece integração direta com o ambiente de AD DS empresarial. Ao substituir servidores de arquivos locais por compartilhamentos de arquivos do Azure, os usuários existentes podem acessar compartilhamentos de arquivos do Azure de seus clientes atuais com uma experiência de logon único, sem nenhuma alteração nas credenciais em uso.  

> [!NOTE]
> Para ajudá-lo a configurar a autenticação do AD dos arquivos do Azure para alguns casos de uso comuns, publicamos [dois vídeos](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) com orientações passo a passo:
> - Substituindo servidores de arquivos locais por arquivos do Azure (incluindo a instalação no link privado para arquivos e autenticação do AD)
> - Usando os arquivos do Azure como o contêiner de perfil para a área de trabalho virtual do Windows (incluindo a configuração na autenticação do AD e FsLogix)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de habilitar a autenticação AD DS para compartilhamentos de arquivos do Azure, verifique se você concluiu os seguintes pré-requisitos: 

- Selecione ou crie seu ambiente de AD DS e [Sincronize-o com o Azure ad](../../active-directory/hybrid/how-to-connect-install-roadmap.md). 

    Você pode habilitar o recurso em um ambiente de AD DS local novo ou existente. As identidades usadas para o acesso devem ser sincronizadas com o Azure AD. O locatário do Azure AD e o compartilhamento de arquivos que você está acessando devem ser associados à mesma assinatura. 

    Para configurar um ambiente de domínio do AD, consulte [Active Directory Domain Services visão geral](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se você não tiver sincronizado seu AD com o Azure AD, siga as orientações em [Azure ad Connect e Azure ad Connect Health roteiro de instalação](../../active-directory/hybrid/how-to-connect-install-roadmap.md) para configurar e configurar o Azure ad Connect. 

- Domínio-ingresse em um computador local ou em uma VM do Azure para AD DS local. 

    Para acessar um compartilhamento de arquivos usando credenciais do AD de um computador ou VM, seu dispositivo deve estar ingressado no domínio para AD DS. Para obter informações sobre como ingressar no domínio, consulte [ingressar um computador em um domínio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selecione ou crie uma conta de armazenamento do Azure em [uma região com suporte](#regional-availability). 

    Certifique-se de que a conta de armazenamento que contém seus compartilhamentos de arquivos ainda não esteja configurada para autenticação de AD DS do Azure. Se a autenticação de AD DS do Azure for habilitada na conta de armazenamento, ela precisará ser desabilitada antes da alteração para usar o AD DS local. Isso implica que as ACLs existentes configuradas no ambiente de AD DS do Azure precisarão ser reconfiguradas para a imposição de permissão adequada.
    
    Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [criar um compartilhamento de arquivos em arquivos do Azure](storage-how-to-create-file-share.md).
    
    Para obter um desempenho ideal, recomendamos que você implante a conta de armazenamento na mesma região da VM da qual você planeja acessar o compartilhamento. 

- Verifique a conectividade montando compartilhamentos de arquivos do Azure usando sua chave de conta de armazenamento. 

    Para verificar se o dispositivo e o compartilhamento de arquivos estão configurados corretamente, tente [montar o compartilhamento de arquivos](storage-how-to-use-files-windows.md) usando sua chave de conta de armazenamento. Se você tiver problemas ao conectar-se aos arquivos do Azure, consulte [a ferramenta de solução de problemas que publicamos para erros de montagem de arquivos do Azure no Windows](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5). Também fornecemos [orientações](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) para contornar cenários quando a porta 445 é bloqueada. 

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação de arquivos do Azure com AD DS (versão prévia) está disponível em [todas as regiões públicas e regiões do Azure gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Antes de habilitar a autenticação AD DS sobre o SMB para compartilhamentos de arquivos do Azure, recomendamos que você leia e conclua a seção de [pré-requisitos](#prerequisites) . Os pré-requisitos validam que o AD, o Azure AD e os ambientes de armazenamento do Azure estão configurados corretamente. 

Se você planeja habilitar qualquer configuração de rede em seu compartilhamento de arquivos, recomendamos que você avalie a [consideração de rede](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e conclua a configuração relacionada primeiro antes de habilitar a autenticação de AD DS.

Em seguida, siga as etapas abaixo para configurar os arquivos do Azure para autenticação do AD: 

1. Habilite os arquivos do Azure AD DS a autenticação em sua conta de armazenamento. 

2. Atribua permissões de acesso para um compartilhamento para a identidade do Azure AD (um usuário, grupo ou entidade de serviço) que esteja em sincronia com a identidade do AD de destino. 

3. Configure ACLs sobre SMB para diretórios e arquivos. 
 
4. Monte um compartilhamento de arquivos do Azure em uma VM ingressada em seu AD DS. 

5. Atualize a senha da sua identidade de conta de armazenamento no AD DS.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD sobre SMB para compartilhamentos de arquivos do Azure. 

![Diagrama de fluxo de trabalho do AD de arquivos](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Só há suporte para a autenticação AD DS sobre SMB para compartilhamentos de arquivos do Azure em máquinas ou VMs em execução em versões do sistema operacional mais recentes do que o Windows 7 ou o Windows Server 2008 R2. 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. habilitar a autenticação do AD para sua conta 

Para habilitar a autenticação de AD DS sobre o SMB para compartilhamentos de arquivos do Azure, primeiro você precisa registrar sua conta de armazenamento com AD DS e, em seguida, definir as propriedades de domínio necessárias na conta de armazenamento. Quando o recurso é habilitado na conta de armazenamento, ele se aplica a todos os compartilhamentos de arquivos novos e existentes na conta. Use `join-AzStorageAccountForAuth` para habilitar o recurso. Você pode encontrar a descrição detalhada do fluxo de trabalho de ponta a ponta no script dentro desta seção. 

> [!IMPORTANT]
> O `Join-AzStorageAccountForAuth` cmdlet fará modificações no seu ambiente do AD. Leia a explicação a seguir para entender melhor o que está fazendo para garantir que você tenha as permissões adequadas para executar o comando e que as alterações aplicadas se alinhem às políticas de conformidade e segurança. 

O `Join-AzStorageAccountForAuth` cmdlet executará o equivalente a um ingresso no domínio offline em nome da conta de armazenamento indicada. O script usa o cmdlet para criar uma conta em seu domínio do AD, seja uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou uma conta de logon de [serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Se você optar por fazer isso manualmente, deverá selecionar a conta mais adequada para o seu ambiente.

A conta de AD DS criada pelo cmdlet representa a conta de armazenamento no domínio do AD. Se a conta de AD DS for criada em uma UO (unidade organizacional) que impõe a expiração da senha, você deverá atualizar a senha antes da duração máxima da senha. A falha ao atualizar a senha da conta resultará em falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Para saber como atualizar a senha, consulte [atualizar a senha da conta do AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds).

Você pode usar o script a seguir para executar o registro e habilitar o recurso ou, como alternativa, você pode executar manualmente as operações que o script faria. Essas operações são descritas na seção após o script. Você não precisa fazer as duas.

### <a name="11-script-prerequisites"></a>pré-requisitos de script do 1,1
- [Baixar e descompactar o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Instale e execute o módulo em um dispositivo que esteja ingressado no domínio no local AD DS com credenciais de AD DS que tenham permissões para criar uma conta de logon de serviço ou uma conta de computador no AD de destino.
-  Execute o script usando uma credencial de AD DS local que é sincronizada com o Azure AD. A credencial de AD DS local deve ter as permissões de proprietário da conta de armazenamento ou da função de RBAC do colaborador.
- Verifique se sua conta de armazenamento está em uma [região com suporte](#regional-availability).

### <a name="12-domain-join-your-storage-account"></a>1,2 domínio ingressar em sua conta de armazenamento
Lembre-se de substituir os valores de espaço reservado pelos seus próprios nos parâmetros abaixo antes de executá-los no PowerShell.
> [!IMPORTANT]
> O cmdlet ingresso no domínio criará uma conta do AD para representar a conta de armazenamento (compartilhamento de arquivos) no AD. Você pode optar por se registrar como uma conta de computador ou conta de logon de serviço, consulte [perguntas frequentes](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter detalhes. Para contas de computador, há uma duração de expiração de senha padrão definida no AD em 30 dias. Da mesma forma, a conta de logon do serviço pode ter uma duração de expiração de senha padrão definida no domínio do AD ou na UO (unidade organizacional).
> Para ambos os tipos de conta, é altamente recomendável verificar qual é a idade de expiração de senha configurada no seu ambiente do AD e planejar [atualizar a senha da sua identidade de conta de armazenamento no AD](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) da conta do AD abaixo antes da duração máxima da senha. Falha ao atualizar a senha da conta do AD resultará em falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Você pode considerar [criar uma nova ou (unidade organizacional) do AD no AD](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e desabilitar a política de expiração de senha em [contas de computador](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) ou contas de logon de serviço de acordo. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

A descrição a seguir resume todas as ações executadas quando `Join-AzStorageAccountForAuth` o cmdlet é executado. Você pode executar essas etapas manualmente, se preferir não usar o comando:

> [!NOTE]
> Se você já executou o `Join-AzStorageAccountForAuth` script acima com êxito, vá para a próxima seção "1,3 confirmar que o recurso está habilitado". Você não precisa executar as operações abaixo novamente.

#### <a name="a-checking-environment"></a>a. Verificando o ambiente

Primeiro, o script verifica seu ambiente. Especificamente, ele verifica se [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se o Shell está sendo executado com privilégios de administrador. Em seguida, ele verifica se o [módulo AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) está instalado e instala-o se não estiver. Se essas verificações forem aprovadas, ele verificará sua AD DS para ver se há uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou [conta de logon de serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) que já foi criada com o SPN/UPN como "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net". Se a conta não existir, ela criará uma, conforme descrito na seção b abaixo.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Criando uma identidade que representa a conta de armazenamento em seu AD manualmente

Para criar essa conta manualmente, crie uma nova chave Kerberos para sua conta de armazenamento `New-AzStorageAccountKey -KeyName kerb1`usando. Em seguida, use essa chave Kerberos como a senha para sua conta. Essa chave só é usada durante a instalação e não pode ser usada para nenhuma operação de controle ou plano de dados na conta de armazenamento.

Depois de ter essa chave, crie uma conta de serviço ou de computador em sua UO. Use a seguinte especificação: SPN: "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net" Password: chave Kerberos para sua conta de armazenamento.

Se sua UO impõe a expiração de senha, você deve atualizar a senha antes da duração máxima da senha para evitar falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Consulte [Atualizar senha de sua identidade de conta de armazenamento no AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) para obter detalhes.

Mantenha o SID da conta recém-criada, você precisará dela para a próxima etapa. A identidade que você criou que representa a conta de armazenamento não precisa ser sincronizada com o Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Habilitar o recurso em sua conta de armazenamento

O script, em seguida, habilitaria o recurso em sua conta de armazenamento. Para executar essa configuração manualmente, forneça alguns detalhes de configuração para as propriedades de domínio no comando a seguir e, em seguida, execute-a. O SID da conta de armazenamento necessário no comando a seguir é o SID da identidade que você criou em seu AD DS na [seção anterior](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1,3 confirmar que o recurso está habilitado

Você pode verificar para confirmar se o recurso está habilitado em sua conta de armazenamento, pode usar o seguinte script:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Agora você ativou com êxito o recurso em sua conta de armazenamento. Agora que o recurso está habilitado, há mais etapas que você deve executar para usar o recurso.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Agora você habilitou com êxito AD DS autenticação sobre o SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos do Azure com uma identidade de AD DS. Para conceder a usuários adicionais acesso ao seu compartilhamento de arquivos, siga as instruções nas seções [atribuir permissões de acesso](#2-assign-access-permissions-to-an-identity) para usar uma identidade e [configurar permissões NTFS em relação ao SMB](#3-configure-ntfs-permissions-over-smb) .

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. atualize a senha da sua identidade de conta de armazenamento no AD DS

Se você registrou o AD DS identidade/conta que representa sua conta de armazenamento em uma UO que impõe o tempo de expiração da senha, você deve girar a senha antes da duração máxima da senha. A falha ao atualizar a senha da conta de AD DS resultará em falhas de autenticação para acessar compartilhamentos de arquivos do Azure.  

Para disparar a rotação de senha, você pode `Update-AzStorageAccountADObjectPassword` executar o comando do módulo AzFilesHybrid. O cmdlet executa ações semelhantes à rotação de chaves da conta de armazenamento. Ele obtém a segunda chave Kerberos da conta de armazenamento e a usa para atualizar a senha da conta registrada no AD DS. Em seguida, ele regenera a chave Kerberos de destino da conta de armazenamento e atualiza a senha da conta registrada no AD DS. Você deve executar esse cmdlet em um ambiente ingressado no domínio AD DS local.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure e como usar o AD sobre SMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseada em identidade de arquivos do Azure para acesso SMB](storage-files-active-directory-overview.md)
- [Perguntas frequentes](storage-files-faq.md)
