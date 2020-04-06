---
title: Use os Serviços de Domínio ad do Azure para autorizar o acesso a dados de arquivos via SMB
description: Saiba como ativar a autenticação baseada em identidade sobre o SMB (Server Message Block, bloco de mensagens do servidor) para arquivos Azure através dos Serviços de Domínio do Diretório Ativo do Azure. Suas máquinas virtuais windows (VMs) unidas por domínio podem acessar os compartilhamentos de arquivos do Azure usando credenciais Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666844"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Habilite a autenticação de serviços de domínio do diretório ativo do Azure em arquivos do Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para obter uma visão geral da autenticação do Azure AD sobre smb para compartilhamentos de arquivos Do Zure, consulte [Visão geral da autenticação do Azure Active Directory sobre SMB para Arquivos Azure](storage-files-active-directory-overview.md). Este artigo está focado em como habilitar a autenticação com o Azure Active Directory Domain Services (Azure AD DS) em Arquivos Azure.

> [!NOTE]
> O Azure Files suporta a autenticação kerberos com o Azure AD DS com criptografia RC4-HMAC. A criptografia AES Kerberos ainda não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de habilitar o Azure AD sobre SMB para compartilhamentos de arquivos Do Azure, certifique-se de ter concluído os seguintes pré-requisitos:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tem um inquilino Azure AD existente, mas deseja criar um novo inquilino para uso com compartilhamentos de arquivos Do Azure, consulte [Criar um inquilino do Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

    Normalmente leva cerca de 15 minutos para que uma implantação do Azure AD DS seja concluída. Verifique se o estado de saúde do Azure AD DS mostra **Running**, com sincronização de hash de senha ativada, antes de prosseguir para a próxima etapa.

1.  **Junte-se a um Azure VM com Azure AD DS.**

    Para acessar um compartilhamento de arquivos usando credenciais Azure AD de uma VM, sua VM deve ser acompanhada de domínio ao Azure AD DS. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > A autenticação Azure AD DS sobre SMB com compartilhamentos de arquivos Do Azure é suportada apenas em VMs do Azure em execução em versões do SISTEMA OPERACIONAL acima do Windows 7 ou Windows Server 2008 R2.

1.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md).
    Para um desempenho ideal, recomendamos que seu compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

1.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho

Antes de ativar a autenticação Azure AD DS sobre smb para compartilhamentos de arquivos Do Zure, verifique se os ambientes azure AD e Azure Storage estão configurados corretamente. Recomendamos que você caminhe pelos [pré-requisitos](#prerequisites) para ter certeza de que completou todas as etapas necessárias.

Em seguida, faça as seguintes coisas para conceder acesso aos recursos do Azure Files com credenciais Ad do Azure:

1. Habilite a autenticação Azure AD DS sobre SMB para sua conta de armazenamento para registrar a conta de armazenamento com a implantação ad DS do Azure associada.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação Azure AD DS sobre SMB para Arquivos Azure.

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. Habilite a autenticação do Azure AD DS para sua conta

Para habilitar a autenticação do Azure AD DS sobre smb para arquivos Azure, você pode definir uma propriedade em contas de armazenamento usando o portal Azure, Azure PowerShell ou Azure CLI. Definir essa propriedade implicitamente "domain joins" da conta de armazenamento com a implantação ad DS do Azure associada. A autenticação Azure AD DS sobre SMB é então habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento.

Tenha em mente que você pode habilitar a autenticação Azure AD DS sobre SMB somente depois de ter implantado com sucesso o Azure AD DS para o seu inquilino Azure AD. Para obter mais informações, consulte os [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar a autenticação Azure AD DS via SMB com o [portal Azure,](https://portal.azure.com)siga estas etapas:

1. No portal Azure, vá para sua conta de armazenamento existente ou [crie uma conta de armazenamento](../common/storage-account-create.md).
1. Na seção **Configurações**, selecione **Configuração**.
1. Em **acesso baseado em identidade para compartilhamentos de arquivos,** alterne o alternador do **Azure Active Directory Domain Service (AAD DS)** para **Habilitado**.
1. Clique em **Salvar**.

A imagem a seguir mostra como ativar a autenticação do Azure AD DS sobre SMB para sua conta de armazenamento.

![Habilite a autenticação Azure AD DS sobre SMB no portal Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Para habilitar a autenticação Azure AD DS sobre SMB com o Azure PowerShell, instale o módulo Az mais recente (2.4 ou mais recente) ou o módulo Az.Storage (1.5 ou mais recente). Para obter mais informações sobre a instalação do PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Para criar uma nova conta de armazenamento, chame [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)e, em seguida, defina o parâmetro **EnableAzureActiveDirectoryDomainServicesForFile** como **verdadeiro**. No exemplo a seguir, lembre-se de substituir os valores do espaço reservado por seus próprios valores. (Se você estivesse usando o módulo de visualização anterior, o parâmetro para habilitação de recursos é **EnableAzureFilesAadIntegrationForSMB**.)

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

Para habilitar a autenticação do Azure AD sobre SMB com o Azure CLI, instale a versão cli mais recente (Versão 2.0.70 ou mais recente). Para obter mais informações sobre a instalação do Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Para criar uma nova conta de armazenamento, chame[a criação de conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)e defina a `--enable-files-aadds` propriedade como **verdadeira.** No exemplo a seguir, lembre-se de substituir os valores do espaço reservado por seus próprios valores. (Se você estivesse usando o módulo de visualização anterior, o parâmetro para habilitação de recursos é **file-aad**.)

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

Agora, você habilitou com sucesso a autenticação Azure AD DS sobre SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos Azure com uma identidade Azure AD. Para conceder aos usuários adicionais acesso ao seu compartilhamento de arquivos, siga as instruções nas [permissões de acesso Atribuir](#2-assign-access-permissions-to-an-identity) para usar uma identidade e [configure permissões NTFS sobre seções SMB](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre arquivos Do Azure e como usar o Azure AD via SMB, consulte esses recursos:

- [Visão geral do suporte de autenticação baseado em identidade do Azure Files para acesso a SMB](storage-files-active-directory-overview.md)
- [Perguntas Frequentes](storage-files-faq.md)
