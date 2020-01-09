---
title: Visão geral-Azure AD Domain Services autorização-arquivos do Azure
description: Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre SMB (protocolo de mensagens do servidor) por meio dos serviços de domínio do Azure Active Directory (AD do Azure). As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 93db726a2cac14109e542972ce851943b290962f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460292"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Visão geral do suporte à autenticação do Azure AD DS (serviço de domínio Azure Active Directory) de arquivos do Azure para acesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação de AD DS do Azure para arquivos do Azure, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB para arquivos do Azure](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionados à autenticação do serviço de domínio do Azure AD sobre SMB para arquivos do Azure:

-   **Azure Active Directory (Azure AD)**  
    O Azure AD (Azure Active Directory) é o serviço de gerenciamento de diretório e identidade multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Os Serviços de Domínio do Azure AD fornecem serviços de domínio gerenciado, como ingresso no domínio, políticas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [Serviços de Domínio do Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **RBAC (Controle de Acesso Baseado em Função) do Azure**  
    O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o mínimo de permissões necessárias para que eles realizem seus trabalhos. Para obter mais informações sobre o RBAC, consulte [O que é o Controle de acesso baseado em função (RBAC) no Azure?](../../role-based-access-control/overview.md)

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**  
    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Vantagens da autenticação do serviço de domínio do Azure AD
A autenticação do serviço de domínio do Azure AD para arquivos do Azure oferece vários benefícios em relação ao uso da autenticação de chave compartilhada:

-   **Estenda a experiência tradicional de acesso ao compartilhamento de arquivos baseado em identidade para a nuvem com o Azure AD e o serviço de domínio do Azure AD**  
    Se você planeja "aumentar e deslocar" seu aplicativo para a nuvem, substituindo os servidores de arquivos tradicionais pelos arquivos do Azure, convém que seu aplicativo seja autenticado com as credenciais do Azure AD para acessar os dados do arquivo. Os arquivos do Azure dão suporte ao uso de credenciais do Azure AD para acessar os arquivos do Azure via SMB do Azure AD DS VMs do Windows ingressadas no domínio. Você também pode optar por sincronizar todos os seus objetos do Active Directory locais ao Azure AD para preservar os nomes de usuário, senhas e outras atribuições de grupo.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões a uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup das ACLs junto com seus dados**  
    Você pode usar Arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os Arquivos do Azure preservam suas ACLs junto com seus dados ao fazer backup de um compartilhamento de arquivos para os Arquivos do Azure por SMB.

## <a name="how-it-works"></a>Como funciona
Os Arquivos do Azure usam o Azure AD Domain Services para oferecer suporte a autenticação do Kerberos com credenciais do Azure AD das VMs ingressadas por domínio. Antes de poder usar o Azure AD com o Arquivos do Azure, você deve primeiro habilitar o Azure AD Domain Services e ingressar no domínio a partir das VMs das quais você planeja acessar dados de arquivos. Sua VM ingressada no domínio deve residir na mesma rede virtual (VNET) que Azure AD Domain Services. 

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em Arquivos do Azure, a solicitação é enviada ao Azure AD Domain Services para autenticar a identidade. Se a autenticação for bem-sucedida, o Azure AD Domain Services retorna um token do Kerberos. O aplicativo envia uma solicitação que inclui o token do Kerberos e os Arquivos do Azure usam esse token para autorizar a solicitação. Os Arquivos do Azure apenas recebem o token e não persistem em credenciais do Azure AD.

![Diagrama mostrando a captura de tela de autenticação do Azure AD por SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Habilitar a autenticação do serviço de domínio do Azure AD para acesso SMB
Você pode habilitar a autenticação do serviço de domínio do Azure AD para arquivos do Azure em suas contas de armazenamento novas e existentes criadas após 24 de setembro de 2018. 

Antes de habilitar esse recurso, verifique se Azure AD Domain Services foi implantado para o locatário primário do Azure AD ao qual sua conta de armazenamento está associada. Se você ainda não configurou o Azure AD Domain Services, siga as orientações passo a passo fornecidas em [Habilitar o Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/tutorial-create-instance.md).

A implantação do Azure AD Domain Services geralmente leva de 10 a 15 minutos. Após a implantação do Azure AD Domain Services, você pode habilitar a autenticação do Azure AD por SMB para os Arquivos do Azure. Para obter mais informações, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB para arquivos do Azure](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure
Depois que a autenticação do serviço de domínio do Azure AD tiver sido habilitada, você poderá configurar funções RBAC personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivo na conta de armazenamento.

Quando um aplicativo em execução em uma VM ingressada no domínio tenta montar um compartilhamento de arquivos do Azure ou acessar um diretório ou arquivo, as credenciais do aplicativo do Azure AD são verificadas para garantir que as permissões no nível de compartilhamento e de NTFS adequadas. Para obter informações sobre como configurar permissões de nível de compartilhamento, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para Arquivos do Azure 
Os Arquivos do Azure impõem permissões de arquivo NTFS padrão no nível do diretório e do arquivo, incluindo no diretório raiz. A configuração de permissões no nível de diretório ou de arquivo é compatível apenas por SMB. Monte o compartilhamento de arquivos de destino de sua VM e configure permissões usando o explorador de arquivos do Windows, o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou o comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário 
Um usuário que possui a chave de conta de armazenamento pode acessar Arquivos do Azure com permissões de superusuário. As permissões de superusuário ultrapassam todas as restrições de controle de acesso configuradas no nível de compartilhamento com o RBAC e impostas pelo Azure AD. As permissões de superusuário são necessárias para montar um compartilhamento de arquivos do Azure. 

> [!IMPORTANT]
> Como parte das melhores práticas de segurança, evite compartilhar suas chaves de conta de armazenamento e aproveitar as permissões do Azure AD sempre que possível.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Preservar as ACLs de diretório e de arquivo para importação de dados para compartilhamentos de arquivos do Azure
Os arquivos do Azure agora dão suporte à preservação de ACLs de diretório ou arquivo quando você copia dados para compartilhamentos de arquivos do Azure. Você pode copiar as ACLs em um diretório ou arquivo para os arquivos do Azure. Por exemplo, você pode usar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador `/copy:s` para copiar dados e ACLs para um compartilhamento de arquivos do Azure. A preservação da ACL está ativada por padrão e você não precisa habilitar explicitamente o recurso de autenticação do serviço de domínio do Azure AD em sua conta de armazenamento. 

## <a name="pricing"></a>Preços
Não há nenhum custo adicional de serviço para habilitar a autenticação do Azure AD por SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte as páginas [Preços dos Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [Preços do Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre a autenticação de Arquivos do Azure e do Azure AD por SMB, consulte estes recursos:

- [Introdução aos Arquivos do Azure](storage-files-introduction.md)
- [Habilitar a autenticação de Azure Active Directory sobre o SMB para arquivos do Azure](storage-files-active-directory-enable.md)
- [perguntas frequentes](storage-files-faq.md)
