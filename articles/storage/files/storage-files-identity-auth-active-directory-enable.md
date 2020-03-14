---
title: Habilitar a autenticação de Active Directory sobre o SMB para arquivos do Azure
description: Saiba como habilitar a autenticação baseada em identidade sobre SMB para compartilhamentos de arquivos do Azure por meio de Active Directory. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando credenciais do AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: rogarana
ms.openlocfilehash: d9d2e06cc3beae8a7bb8ea1b4eee15fb1641ddd4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255217"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Habilitar a autenticação de Active Directory sobre SMB para compartilhamentos de arquivos do Azure

[Os arquivos do Azure](storage-files-introduction.md) dão suporte à autenticação baseada em identidade sobre o protocolo SMB por meio de dois tipos de serviços de domínio: Azure Active Directory Domain Services (Azure AD DS) (GA) e Active Directory (AD) (versão prévia). Este artigo se concentra no suporte introduzido recentemente (versão prévia), aproveitando o serviço Domínio do Active Directory para autenticação em compartilhamentos de arquivos do Azure. Se você estiver interessado em habilitar a autenticação do Azure AD DS (GA) para compartilhamentos de arquivos do Azure, consulte [nosso artigo sobre o assunto](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Os compartilhamentos de arquivos do Azure dão suporte apenas à autenticação em um serviço de domínio, seja Azure Active Directory serviço de domínio (AD DS do Azure) ou Active Directory (AD). 
>
> As identidades do AD usadas para a autenticação de compartilhamento de arquivos do Azure devem ser sincronizadas com o Azure AD. A sincronização de hash de senha é opcional. 
> 
> A autenticação do AD não dá suporte à autenticação em relação às contas de computador criadas no AD. 
> 
> A autenticação do AD só pode ser suportada em uma floresta do AD na qual a conta de armazenamento está registrada. Você só pode acessar compartilhamentos de arquivos do Azure com as credenciais do AD de uma única floresta do AD por padrão. Se você precisar acessar o compartilhamento de arquivos do Azure de uma floresta diferente, verifique se você tem a relação de confiança de floresta apropriada configurada, consulte as [perguntas frequentes](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) para obter detalhes.  
> 
> Há suporte para a autenticação do AD para acesso SMB e persistência de ACL em compartilhamentos de arquivos do Azure gerenciados pelo Sincronização de Arquivos do Azure.
>
> Os arquivos do Azure dão suporte à autenticação Kerberos com o AD com a criptografia RC4-HMAC. A criptografia Kerberos AES ainda não tem suporte.

Quando você habilita o AD para compartilhamentos de arquivos do Azure via SMB, seus computadores ingressados no domínio do AD podem montar compartilhamentos de arquivos do Azure usando suas credenciais existentes do AD. Esse recurso pode ser habilitado com um ambiente do AD hospedado em computadores locais ou hospedado no Azure.

As identidades do AD usadas para acessar compartilhamentos de arquivos do Azure devem ser sincronizadas com o Azure AD para impor permissões de arquivo de nível de compartilhamento por meio do modelo [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) padrão. As [DACLs de estilo do Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em arquivos/diretórios transferidos de servidores de arquivos existentes serão preservadas e impostas. Esse recurso oferece integração direta com sua infraestrutura de domínio do AD corporativo. Ao substituir servidores de arquivos locais por compartilhamentos de arquivos do Azure, os usuários existentes podem acessar compartilhamentos de arquivos do Azure de seus clientes atuais com uma experiência de logon único, sem nenhuma alteração nas credenciais em uso.  
 
## <a name="prerequisites"></a>Prerequisites 

Antes de habilitar a autenticação do AD para compartilhamentos de arquivos do Azure, verifique se você concluiu os seguintes pré-requisitos: 

- Selecione ou crie seu ambiente do AD e sincronize-o com o Azure AD. 

    Você pode habilitar o recurso em um ambiente novo ou existente do AD. As identidades usadas para o acesso devem ser sincronizadas com o Azure AD. O locatário do Azure AD e o compartilhamento de arquivos que você está acessando devem ser associados à mesma assinatura. 

    Para configurar um ambiente de domínio do AD, consulte [Active Directory Domain Services visão geral](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Se você não tiver sincronizado seu AD com o Azure AD, siga as orientações em [o que é a identidade híbrida com Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) para determinar o método de autenticação preferencial e Azure ad Connect opção de instalação. 

- Domínio-ingresse em um computador local ou em uma VM do Azure para AD (também conhecida como AD DS). 

    Para acessar um compartilhamento de arquivos usando credenciais do AD de um computador ou VM, seu dispositivo deve estar ingressado no domínio no AD. Para obter informações sobre como ingressar em domínio no AD, consulte [ingressar um computador em um domínio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Selecione ou crie uma conta de armazenamento do Azure em [uma região com suporte](#regional-availability). 

    Certifique-se de que a conta de armazenamento que contém seus compartilhamentos de arquivos ainda não esteja configurada para autenticação de AD DS do Azure. Se a autenticação de AD DS do Azure for habilitada na conta de armazenamento, ela precisará ser desabilitada antes da alteração para usar o AD. Isso implica que as ACLs existentes configuradas no ambiente de AD DS do Azure precisarão ser reconfiguradas para a imposição de permissão adequada.
    
    Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [criar um compartilhamento de arquivos em arquivos do Azure](storage-how-to-create-file-share.md).
    
    Para obter um desempenho ideal, recomendamos que você implante a conta de armazenamento na mesma região da VM da qual você planeja acessar o compartilhamento. 

- Verifique a conectividade montando compartilhamentos de arquivos do Azure usando sua chave de conta de armazenamento. 

    Para verificar se o dispositivo e o compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [usar um compartilhamento de arquivos do Azure com o Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação do AD do Azure Files (versão prévia) está disponível na [maioria das regiões públicas](https://azure.microsoft.com/global-infrastructure/regions/).

A autenticação do AD do Azure files não está disponível em:
- Oeste dos EUA
- Oeste dos EUA 2
- Leste dos EUA
- Leste dos EUA 2
- Europa Ocidental
- Norte da Europa

## <a name="workflow-overview"></a>Visão geral do fluxo de trabalho

Antes de habilitar a autenticação do AD via SMB para compartilhamentos de arquivos do Azure, recomendamos que você percorra os [pré-requisitos](#prerequisites) e certifique-se de concluir todas as etapas. Os pré-requisitos validam que o AD, o Azure AD e os ambientes de armazenamento do Azure estão configurados corretamente. 

Em seguida, conceda acesso aos recursos de arquivos do Azure com credenciais do AD: 

- Habilite a autenticação do AD dos arquivos do Azure em sua conta de armazenamento.  

- Atribua permissões de acesso para um compartilhamento para a identidade do Azure AD (um usuário, grupo ou entidade de serviço) que esteja em sincronia com a identidade do AD de destino. 

- Configure ACLs sobre SMB para diretórios e arquivos. 

- Monte um compartilhamento de arquivos do Azure de uma VM ingressada no domínio do AD. 

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD sobre SMB para compartilhamentos de arquivos do Azure. 

![Diagrama de fluxo de trabalho do AD de arquivos](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> A autenticação do AD sobre o SMB para compartilhamentos de arquivos do Azure tem suporte apenas em máquinas ou VMs em execução em versões do sistema operacional mais recentes do que o Windows 7 ou o Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Habilitar a autenticação do AD para sua conta 

Para habilitar a autenticação do AD via SMB para compartilhamentos de arquivos do Azure, primeiro você precisa registrar sua conta de armazenamento com o AD e, em seguida, definir as propriedades de domínio necessárias na conta de armazenamento. Quando o recurso é habilitado na conta de armazenamento, ele se aplica a todos os compartilhamentos de arquivos novos e existentes na conta. Use `join-AzStorageAccountForAuth` para habilitar o recurso. Você pode encontrar a descrição detalhada do fluxo de trabalho de ponta a ponta na seção abaixo. 

> [!IMPORTANT]
> O cmdlet `join-AzStorageAccountForAuth` fará modificações no seu ambiente do AD. Leia a explicação a seguir para entender melhor o que está fazendo para garantir que você tenha as permissões adequadas para executar o comando e que as alterações aplicadas se alinhem às políticas de conformidade e segurança. 

O cmdlet `join-AzStorageAccountForAuth` executará o equivalente a um ingresso no domínio offline em nome da conta de armazenamento indicada. Ele criará uma conta em seu domínio do AD, uma conta de [computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) ou uma [conta de logon de serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). A conta do AD criada representa a conta de armazenamento no domínio do AD. Se a conta do AD for criada em uma UO (unidade organizacional) do AD que impõe a expiração da senha, você deverá atualizar a senha antes da duração máxima da senha. Falha ao atualizar a senha da conta do AD resultará em falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Para saber como atualizar a senha, consulte [Atualizar senha da conta do AD](#update-ad-account-password).

Você pode usar o script a seguir para executar o registro e habilitar o recurso ou, como alternativa, você pode executar manualmente as operações que o script faria. Essas operações são descritas na seção após o script. Você não precisa fazer as duas.

### <a name="1-check-prerequisites"></a>1. verificar pré-requisitos
- [Baixar e descompactar o módulo AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Instale e execute o módulo em um dispositivo ingressado no domínio no AD com credenciais do AD que têm permissões para criar uma conta de logon de serviço ou uma conta de computador no AD de destino.
-  Execute o script usando uma credencial do AD que é sincronizada com o Azure AD. A credencial do AD deve ter as permissões de proprietário da conta de armazenamento ou da função de RBAC do colaborador.
- Verifique se sua conta de armazenamento está em uma [região com suporte](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. executar script de habilitação do AD
Lembre-se de substituir os valores de espaço reservado pelos seus próprios nos parâmetros abaixo antes de executá-los no PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU (for example: "OU=ComputersOU,DC=prod,DC=corp,DC=contoso,DC=com")
#You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationalUnitName "<ou-name-here>"
```

A descrição a seguir resume todas as ações executadas quando o cmdlet `join-AzStorageAccountForAuth` é executado. Você pode executar essas etapas manualmente, se preferir não usar o comando:

> [!NOTE]
> Se você já tiver executado o script Join-AzStorageAccountForAuth acima com êxito, vá para a próxima seção "3. Confirme se o recurso está habilitado ". Você não precisa executar as operações abaixo novamente.

#### <a name="a-checking-environment"></a>a. Verificando o ambiente

Primeiro, ele verifica seu ambiente. Especificamente, ele verifica se o [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) está instalado e se o Shell está sendo executado com privilégios de administrador. Em seguida, ele verifica se o [módulo AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) está instalado e instala-o se não estiver. Se essas verificações forem aprovadas, ele verificará seu anúncio para ver se há uma [conta de computador](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou [conta de logon de serviço](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) que já foi criada com o SPN/UPN como "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net". Se a conta não existir, ela criará uma, conforme descrito na seção b abaixo.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Criando uma identidade que representa a conta de armazenamento em seu AD manualmente

Para criar essa conta manualmente, crie uma nova chave Kerberos para sua conta de armazenamento usando `New-AzStorageAccountKey -KeyName kerb1`. Em seguida, use essa chave Kerberos como a senha para sua conta. Essa chave só é usada durante a instalação e não pode ser usada para nenhuma operação de controle ou plano de dados na conta de armazenamento.

Depois de ter essa chave, crie uma conta de serviço ou de computador em sua UO. Use a seguinte especificação: SPN: "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net" Password: chave Kerberos para sua conta de armazenamento.

Se sua UO impõe a expiração de senha, você deve atualizar a senha antes da duração máxima da senha para evitar falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Consulte [atualizar a senha da conta do AD](#update-ad-account-password) para obter detalhes.

Mantenha o SID da conta recém-criada, você precisará dela para a próxima etapa. A identidade do AD que você acabou de criar que representa a conta de armazenamento não precisa ser sincronizada com o Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Habilitar o recurso em sua conta de armazenamento

O script, em seguida, habilitaria o recurso em sua conta de armazenamento. Para executar essa configuração manualmente, forneça alguns detalhes de configuração para as propriedades de domínio no comando a seguir e, em seguida, execute-a. O SID da conta de armazenamento necessário no comando a seguir é o SID da identidade que você criou no AD (seção b acima).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServicesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Confirme se o recurso está habilitado

Você pode verificar para confirmar se o recurso está habilitado em sua conta de armazenamento, pode usar o seguinte script:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Agora você ativou com êxito o recurso em sua conta de armazenamento. Embora o recurso esteja habilitado, você ainda precisa concluir ações adicionais para poder usar o recurso corretamente.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Agora você habilitou com êxito a autenticação do AD via SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos do Azure com uma identidade do AD. Para conceder a usuários adicionais acesso ao seu compartilhamento de arquivos, siga as instruções nas seções [atribuir permissões de acesso](#assign-access-permissions-to-an-identity) para usar uma identidade e [configurar permissões NTFS em relação ao SMB](#configure-ntfs-permissions-over-smb) .

## <a name="update-ad-account-password"></a>Atualizar senha da conta do AD

Se você registrou a conta/identidade do AD que representa sua conta de armazenamento em uma UO que impõe o tempo de expiração da senha, você deve girar a senha antes da duração máxima da senha. A falha ao atualizar a senha da conta do AD resultará em falhas de autenticação para acessar compartilhamentos de arquivos do Azure.  

Para disparar a rotação de senha, você pode executar o comando `Update-AzStorageAccountADObjectPassword` do módulo AzFilesHybrid. O cmdlet executa ações semelhantes à rotação de chaves da conta de armazenamento. Ele obtém a segunda chave Kerberos da conta de armazenamento e a usa para atualizar a senha da conta registrada no AD. Em seguida, ele regenera a chave Kerberos de destino da conta de armazenamento e atualiza a senha da conta registrada no AD. Você deve executar esse cmdlet em um ambiente ingressado no domínio do AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure e como usar o AD sobre SMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseada em identidade de arquivos do Azure para acesso SMB](storage-files-active-directory-overview.md)
- [perguntas frequentes](storage-files-faq.md)
