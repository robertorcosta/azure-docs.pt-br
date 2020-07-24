---
title: Usar Azure AD Domain Services para autorizar o acesso a dados de arquivo via SMB
description: Saiba como habilitar a autenticação baseada em identidade sobre o protocolo SMB para arquivos do Azure por meio de Azure Active Directory Domain Services. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fe2bbc1d6f42819354f48812a34371a49e5acbac
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999625"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Habilitar a autenticação de Azure Active Directory Domain Services nos arquivos do Azure

[Arquivos](storage-files-introduction.md)   do Azure oferece suporte à autenticação baseada em identidade sobre o protocolo SMB por meio de dois tipos de serviços de domínio: Active Directory Domain Services local (AD DS) e Azure Active Directory Domain Services (Azure AD DS). É altamente recomendável que você examine a [seção como ele funciona](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) para selecionar o serviço de domínio certo para authentcation. A instalação é diferente dependendo do serviço de domínio que você escolher. Este artigo se concentra na habilitação e configuração da AD DS do Azure para autenticação com compartilhamentos de arquivos do Azure.

Se você for novo nos compartilhamentos de arquivos do Azure, é recomendável ler nosso [Guia de planejamento](storage-files-planning.md) antes de ler a série de artigos a seguir.

> [!NOTE]
> Os arquivos do Azure dão suporte à autenticação Kerberos com o Azure AD DS com a criptografia RC4-HMAC. A criptografia Kerberos AES ainda não tem suporte.
> Os arquivos do Azure dão suporte à autenticação para o Azure AD DS com sincronização completa com o Azure AD. Se você tiver habilitado a sincronização com escopo no Azure AD DS que apenas sincronizar um conjunto limitado de identidades do Azure AD, não haverá suporte para autenticação e autorização.

## <a name="prerequisites"></a>Pré-requisitos

Antes de habilitar o Azure AD sobre SMB para compartilhamentos de arquivos do Azure, verifique se você concluiu os seguintes pré-requisitos:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tiver um locatário existente do Azure AD, mas quiser criar um novo locatário para uso com compartilhamentos de arquivos do Azure, consulte [criar um locatário Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Normalmente, leva cerca de 15 minutos para que uma implantação de AD DS do Azure seja concluída. Verifique se o status de integridade do Azure AD DS mostra **em execução**, com a sincronização de hash de senha habilitada, antes de prosseguir para a próxima etapa.

1.  **Ingressar no domínio de uma VM do Azure com o Azure AD DS.**

    Para acessar um compartilhamento de arquivos usando as credenciais do Azure AD de uma VM, sua VM deve estar ingressada no domínio para o Azure AD DS. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação do Azure AD DS sobre SMB com compartilhamentos de arquivos do Azure tem suporte apenas em VMs do Azure em execução nas versões do sistema operacional acima do Windows 7 ou do Windows Server 2008 R2.

1.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md).
    Para obter um desempenho ideal, recomendamos que o compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

1.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação de arquivos do Azure com o Azure AD DS está disponível em [todas as regiões públicas do Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de habilitar a autenticação do Azure AD DS sobre o SMB para compartilhamentos de arquivos do Azure, verifique se os ambientes do Azure AD e do armazenamento do Azure estão configurados corretamente. Recomendamos que você percorra os [pré-requisitos](#prerequisites) para ter certeza de que concluiu todas as etapas necessárias.

Em seguida, faça o seguinte para conceder acesso aos recursos de arquivos do Azure com as credenciais do Azure AD:

1. Habilite a autenticação de AD DS do Azure sobre SMB para sua conta de armazenamento para registrar a conta de armazenamento com a implantação de AD DS do Azure associada.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação de AD DS do Azure sobre SMB para arquivos do Azure.

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. habilitar a autenticação de AD DS do Azure para sua conta

Para habilitar a autenticação de AD DS do Azure sobre o SMB para arquivos do Azure, você pode definir uma propriedade em contas de armazenamento usando o portal do Azure, Azure PowerShell ou CLI do Azure. Definir essa propriedade implicitamente "ingressa no domínio" é a conta de armazenamento com a implantação de AD DS do Azure associada. A autenticação do Azure AD DS sobre o SMB é então habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento.

Tenha em mente que você pode habilitar a autenticação AD DS do Azure somente por SMB depois de ter implantado com êxito o Azure AD DS em seu locatário do Azure AD. Para obter mais informações, consulte os [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar a autenticação de AD DS do Azure sobre SMB com o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. Na portal do Azure, vá para sua conta de armazenamento existente ou [crie uma conta de armazenamento](../common/storage-account-create.md).
1. Na seção **Configurações**, selecione **Configuração**.
1. Em **acesso baseado em identidade para compartilhamentos de arquivos** , alterne a alternância para o **serviço de domínio Azure Active Directory (AAD DS)** para **habilitado**.
1. Clique em **Salvar**.

A imagem a seguir mostra como habilitar a autenticação de AD DS do Azure sobre SMB para sua conta de armazenamento.

![Habilitar a autenticação de AD DS do Azure sobre SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Para habilitar a autenticação de AD DS do Azure sobre SMB com Azure PowerShell, instale o módulo AZ mais recente (2,4 ou mais recente) ou o módulo AZ. Storage (1,5 ou mais recente). Para obter mais informações sobre como instalar o PowerShell, consulte [instalar Azure PowerShell no Windows com PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Para criar uma nova conta de armazenamento, chame [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)e, em seguida, defina o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** como **true**. No exemplo a seguir, lembre-se de substituir os valores de espaço reservado pelos seus próprios valores. (Se você estava usando o módulo de visualização anterior, o parâmetro para a habilitação de recursos é **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Para habilitar esse recurso em contas de armazenamento existentes, use o seguinte comando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>CLI do Azure

Para habilitar a autenticação do Azure AD em SMB com o CLI do Azure, instale a versão mais recente da CLI (versão 2.0.70 ou mais recente). Para obter mais informações sobre como instalar o CLI do Azure, consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Para criar uma nova conta de armazenamento, chame [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e defina a `--enable-files-aadds` propriedade como **true**. No exemplo a seguir, lembre-se de substituir os valores de espaço reservado pelos seus próprios valores. (Se você estava usando o módulo de visualização anterior, o parâmetro para habilitação de recurso é **File-AAD**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Para habilitar esse recurso em contas de armazenamento existentes, use o seguinte comando:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Agora você habilitou com êxito a autenticação do Azure AD DS sobre o SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos do Azure com uma identidade do Azure AD. Para conceder a usuários adicionais acesso ao seu compartilhamento de arquivos, siga as instruções nas seções [atribuir permissões de acesso](#2-assign-access-permissions-to-an-identity) para usar uma identidade e [configurar permissões NTFS em relação ao SMB](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure e como usar o Azure AD em SMB, consulte estes recursos:

- [Visão geral do suporte de autenticação baseada em identidade de arquivos do Azure para acesso SMB](storage-files-active-directory-overview.md)
- [perguntas frequentes](storage-files-faq.md)
