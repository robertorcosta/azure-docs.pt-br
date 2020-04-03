---
title: Habilite a autenticação do Active Directory sobre smb para arquivos Azure
description: Saiba como ativar a autenticação baseada em identidade sobre sMB para compartilhamentos de arquivos Do Azure através do Active Directory. Suas vMs (VMs) com domínio saqueadas do Windows podem acessar os compartilhamentos de arquivos do Azure usando credenciais aD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 0bf8960f1e97de45d5369f69c698311d0b4e3dbb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584507"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Habilitar autenticação do Active Directory sobre SMB para compartilhamentos de arquivos Do Zure

[O Azure Files](storage-files-introduction.md) suporta autenticação baseada em identidade sobre o SMB (Server Message Block) através de dois tipos de Serviços de Domínio: Azure Active Directory Domain Services (Azure AD DS) (GA) e Active Directory (AD) (visualização). Este artigo se concentra no suporte recém-introduzido (visualização) de aproveitar o Active Directory Domain Service para autenticação a compartilhamentos de arquivos Do Zure. Se você estiver interessado em habilitar a autenticação Azure AD DS (GA) para compartilhamentos de arquivos Azure, consulte [nosso artigo sobre o assunto](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Os compartilhamentos de arquivos do Azure só suportam autenticação contra um serviço de domínio, seja o Azure Active Directory Domain Service (Azure AD DS) ou o Active Directory (AD). 
>
> As identidades AD usadas para autenticação de compartilhamento de arquivos do Azure devem ser sincronizadas com o Azure AD. A sincronização de hash de senha é opcional. 
> 
> A autenticação de Anúncios não suporta autenticação contra contas de computador criadas em AD. 
> 
> A autenticação de Anúncios só pode ser suportada contra uma floresta de Anúncios onde a conta de armazenamento está registrada. Você só pode acessar os compartilhamentos de arquivos do Azure com as credenciais de Anúncio de uma única floresta AD por padrão. Se você precisar acessar o compartilhamento de arquivos do Azure de uma floresta diferente, certifique-se de que você tenha a confiança florestal adequada configurada, consulte [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter detalhes.  
> 
> A autenticação de AD para acesso a SMB e persistência da ACL é suportada para compartilhamentos de arquivos Azure gerenciados pelo Azure File Sync.
>
> O Azure Files suporta a autenticação kerberos com criptografia RC4-HMAC. A criptografia AES Kerberos ainda não é suportada.

Quando você habilita o AD para compartilhamentos de arquivos Azure sobre SMB, as máquinas aderidas ao domínio AD podem montar compartilhamentos de arquivos Do Zure usando suas credenciais aD existentes. Esse recurso pode ser habilitado com um ambiente de Anúncio hospedado em máquinas on-prem ou hospedado no Azure.

As identidades AD usadas para acessar os compartilhamentos de arquivos do Azure devem ser sincronizadas com o Azure AD para impor permissões de arquivo de nível de compartilhamento através do modelo [rbac (role-based access control)](../../role-based-access-control/overview.md) padrão. [Os DACLs no estilo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em arquivos/diretórios transportados dos servidores de arquivos existentes serão preservados e aplicados. Esse recurso oferece integração perfeita com a infra-estrutura de domínio aD corporativo. À medida que você substitui servidores de arquivos on-prem por compartilhamentos de arquivos Do Azure, os usuários existentes podem acessar os compartilhamentos de arquivos do Azure de seus clientes atuais com uma única experiência de logon, sem qualquer alteração nas credenciais em uso.  
 
## <a name="prerequisites"></a>Pré-requisitos 

Antes de habilitar a autenticação aD para compartilhamentos de arquivos Do Azure, certifique-se de ter concluído os seguintes pré-requisitos: 

- Selecione ou crie seu ambiente AD e sincronize-o com o Azure AD. 

    Você pode habilitar o recurso em um ambiente AD novo ou existente. As identidades usadas para acesso devem ser sincronizadas com o Azure AD. O inquilino azure AD e o compartilhamento de arquivos que você está acessando devem ser associados à mesma assinatura. 

    Para configurar um ambiente de domínio AD, consulte visão geral do Active [Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se você não tiver sincronizado seu AD com o Seu Azure AD, siga a orientação em [Qual é a identidade híbrida com o Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) 

- Junte-se a uma máquina no local ou a uma VM Azure para AD (também chamada DeD DS). 

    Para acessar um compartilhamento de arquivos usando credenciais ad de uma máquina ou VM, seu dispositivo deve ser acompanhado de domínio ao AD. Para obter informações sobre como participar do domínio para a D., consulte ['Juntar um computador a um domínio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)' 

- Selecione ou crie uma conta de armazenamento Azure em [uma região suportada](#regional-availability). 

    Certifique-se de que a conta de armazenamento que contém seus compartilhamentos de arquivos ainda não está configurada para autenticação Azure AD DS. Se o Azure Files Azure AD DS Authentication estiver habilitado na conta de armazenamento, ele precisa ser desativado antes de mudar para usar a D.A. Isso implica que as ACLs existentes configuradas no ambiente Azure AD DS precisarão ser reconfiguradas para a aplicação adequada da permissão.
    
    Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md).
    
    Para um desempenho ideal, recomendamos que você implante a conta de armazenamento na mesma região da VM da qual você planeja acessar o compartilhamento. 

- Verifique a conectividade ao montar compartilhamentos de arquivos do Azure usando a chave da conta de armazenamento. 

    Para verificar se o compartilhamento de seu dispositivo e arquivo está configurado corretamente, tente montar o compartilhamento de arquivos usando a chave da conta de armazenamento. Para obter mais informações, consulte [Usar um compartilhamento de arquivos Do Zure com o Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação Azure Files AD (preview) está disponível em [todas as regiões em Public Cloud](https://azure.microsoft.com/global-infrastructure/regions/).

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Antes de ativar a autenticação AD sobre smb para compartilhamentos de arquivos Do Zure, recomendamos que você caminhe pelos [pré-requisitos](#prerequisites) e certifique-se de ter completado todas as etapas. Os pré-requisitos validam que seus ambientes AD, Azure AD e Azure Storage estão configurados corretamente. 

Em seguida, conceda acesso aos recursos do Azure Files com credenciais de Anúncios: 

- Habilite a autenticação aD de arquivos Azure em sua conta de armazenamento.  

- Atribua permissões de acesso para um compartilhamento à identidade Azure AD (um usuário, grupo ou diretor de serviço) que esteja em sincronia com a identidade AD de destino. 

- Configure ACLs sobre SMB para diretórios e arquivos. 

- Monte um compartilhamento de arquivos Azure de um domínio AD juntou-se à VM. 

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD sobre sMB para compartilhamentos de arquivos Azure. 

![Diagrama de fluxo de trabalho de Arquivos AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> A autenticação de AD sobre SMB para compartilhamentos de arquivos Azure só é suportada em máquinas ou VMs em execução em versões do SISTEMA OPERACIONAL mais recentes do que o Windows 7 ou o Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Habilite a autenticação de Anúncios para sua conta 

Para habilitar a autenticação de AD sobre smb para compartilhamentos de arquivos Do Azure, você precisa primeiro registrar sua conta de armazenamento com a DA e, em seguida, definir as propriedades de domínio necessárias na conta de armazenamento. Quando o recurso é ativado na conta de armazenamento, ele se aplica a todos os compartilhamentos de arquivos novos e existentes na conta. Use `join-AzStorageAccountForAuth` para ativar o recurso. Você pode encontrar a descrição detalhada do fluxo de trabalho de ponta a ponta na seção abaixo. 

> [!IMPORTANT]
> O `Join-AzStorageAccountForAuth` cmdlet fará modificações no seu ambiente AD. Leia a seguinte explicação para entender melhor o que está fazendo para garantir que você tenha as permissões adequadas para executar o comando e que as alterações aplicadas estejam alinhadas com as políticas de conformidade e segurança. 

O `Join-AzStorageAccountForAuth` cmdlet executará o equivalente a uma adesão de domínio offline em nome da conta de armazenamento indicada. Ele criará uma conta no domínio do Seu D.A., uma conta de [computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) ou uma [conta de logon de serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). A conta AD criada representa a conta de armazenamento no domínio AD. Se a conta AD for criada sob uma Unidade Organizacional AD (OU) que obriga o vencimento da senha, você deve atualizar a senha antes da idade máxima de senha. A não atualização da senha da conta AD resultará em falhas de autenticação ao acessar os compartilhamentos de arquivos do Azure. Para saber como atualizar a senha, consulte [Atualizar a senha da conta AD](#update-ad-account-password).

Você pode usar o script a seguir para executar o registro e habilitar o recurso ou, alternativamente, você pode executar manualmente as operações que o script faria. Essas operações são descritas na seção seguindo o script. Você não precisa fazer as duas coisas.

### <a name="1-check-prerequisites"></a>1. Verifique os pré-requisitos
- [Baixe e desfaça o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Instale e execute o módulo em um dispositivo que é domínio unido ao AD com credenciais de AD que têm permissões para criar uma conta de logon de serviço ou uma conta de computador no AD de destino.
-  Execute o script usando uma credencial AD sincronizada com o seu Azure AD. A credencial AD deve ter o proprietário da conta de armazenamento ou as permissões de função RBAC do contribuinte.
- Certifique-se de que sua conta de armazenamento está em uma [região suportada.](#regional-availability)

### <a name="2-domain-join-your-storage-account"></a>2. Domínio junte-se à sua conta de armazenamento
Lembre-se de substituir os valores do espaço reservado pelos seus nos parâmetros abaixo antes de executá-los no PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

A descrição a seguir resume `Join-AzStorageAccountForAuth` todas as ações realizadas quando o cmdlet é executado. Você pode executar essas etapas manualmente, se preferir não usar o comando:

> [!NOTE]
> Se você já executou o `Join-AzStorageAccountForAuth` script acima com sucesso, vá para a próxima seção "3. Confirme se o recurso está ativado". Você não precisa realizar as operações abaixo novamente.

#### <a name="a-checking-environment"></a>a. Ambiente de verificação

Primeiro, verifica seu ambiente. Especificamente, ele verifica se o [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se o shell está sendo executado com privilégios de administrador. Em seguida, ele verifica se o [módulo de visualização Az.Storage 1.11.1](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) está instalado e o instala se não estiver. Se essas verificações passarem, ele verificará seu AD para ver se há uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou uma conta de [logon de serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) que já foi criada com spn/UPN como "cifs/seu-armazenamento-conta-nome-aqui.file.core.windows.net". Se a conta não existir, ela criará uma conforme descrito na seção b abaixo.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Criando uma identidade representando a conta de armazenamento em seu Anúncio manualmente

Para criar essa conta manualmente, crie uma nova `New-AzStorageAccountKey -KeyName kerb1`chave kerberos para sua conta de armazenamento usando . Em seguida, use essa chave kerberos como a senha para sua conta. Esta chave só é usada durante a configuração e não pode ser usada para qualquer operação de controle ou plano de dados contra a conta de armazenamento.

Depois de ter essa chave, crie uma conta de serviço ou computador sob o seu OU. Use a seguinte especificação: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Senha: Chave Kerberos para sua conta de armazenamento.

Se o seu OU forçar o vencimento da senha, você deve atualizar a senha antes da idade máxima de senha para evitar falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Consulte [a senha da conta Update AD](#update-ad-account-password) para obter detalhes.

Mantenha o SID da conta recém-criada, você precisará dela para o próximo passo. A identidade AD que você acabou de criar que representa a conta de armazenamento não precisa ser sincronizada com o Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Habilite o recurso em sua conta de armazenamento

O script habilitaria o recurso em sua conta de armazenamento. Para executar essa configuração manualmente, forneça alguns detalhes de configuração para as propriedades do domínio no comando a seguir e execute-o. A conta de armazenamento SID necessária no comando a seguir é o SID da identidade que você criou no AD (seção b acima).

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


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Confirme se o recurso está ativado

Você pode verificar se o recurso está ativado em sua conta de armazenamento, você pode usar o seguinte script:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Agora você habilitou com sucesso o recurso em sua conta de armazenamento. Mesmo que o recurso esteja ativado, você ainda precisa concluir ações adicionais para poder usar o recurso corretamente.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Agora, você habilitou com sucesso a autenticação de AD sobre SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos Azure com uma identidade AD. Para conceder aos usuários adicionais acesso ao seu compartilhamento de arquivos, siga as instruções nas [permissões de acesso Atribuir](#assign-access-permissions-to-an-identity) para usar uma identidade e [configure permissões NTFS sobre](#configure-ntfs-permissions-over-smb) seções SMB.

## <a name="update-ad-account-password"></a>Atualizar a senha da conta AD

Se você registrou a identidade/conta aD representando sua conta de armazenamento sob um OU que impõe o tempo de expiração da senha, você deve girar a senha antes da idade máxima de senha. A não atualização da senha da conta AD resultará em falhas de autenticação para acessar os compartilhamentos de arquivos do Azure.  

Para ativar a rotação de `Update-AzStorageAccountADObjectPassword` senhas, você pode executar o comando a partir do módulo AzFilesHybrid. O cmdlet executa ações semelhantes à rotação da chave da conta de armazenamento. Ele recebe a segunda chave Kerberos da conta de armazenamento e usa-a para atualizar a senha da conta registrada em AD. Em seguida, ele regenera a chave Kerberos de destino da conta de armazenamento e atualiza a senha da conta registrada em AD. Você deve executar este cmdlet em um ambiente de adesão ao domínio AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre arquivos Azure e como usar o AD sobre SMB, consulte esses recursos:

- [Visão geral do suporte de autenticação baseado em identidade do Azure Files para acesso a SMB](storage-files-active-directory-overview.md)
- [Perguntas Frequentes](storage-files-faq.md)
