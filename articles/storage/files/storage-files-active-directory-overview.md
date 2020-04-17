---
title: Visão geral - Autorização baseada em identidade do Azure Files
description: O Azure Files suporta autenticação baseada em identidade sobre SMB (Server Message Block) através do Azure Active Directory Domain Services (AD DS) e do Active Directory. As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 7d9f8ccb4273d1378c4826dea420c4edca2f8ac3
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536566"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Visão geral do suporte de autenticação baseado em identidade do Azure Files para acesso a SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como ativar a autenticação de serviços de domínio do Active Directory no local para compartilhamentos de arquivos Do Azure (visualização), consulte [Ativar a autenticação do Active Directory Domain Services no local sobre smb para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md).

Para saber como ativar a autenticação do Azure AD DS para compartilhamentos de arquivos Do Zure, consulte Ativar a autenticação dos [serviços de domínio do diretório ativo do Azure em arquivos Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionados à autenticação do Azure AD Domain Service sobre sMB para compartilhamentos de arquivos Do Zure:

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**

    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Diretório Ativo do Azure (Azure AD)**

    O Azure Active Directory (Azure AD) é o serviço de gerenciamento de identidade e diretório baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. As máquinas virtuais (VMs) do Windows, aderidas pelo Azure, podem acessar os compartilhamentos de arquivos do Azure com suas credenciais azure AD. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    O Azure AD DS fornece serviços de domínio gerenciados, como a desempate de domínio, políticas de grupo, autenticação LDAP e Kerberos/NTLM. Esses serviços são totalmente compatíveis com os Serviços de Domínio do Diretório Ativo. Para obter mais informações, consulte [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Serviços de domínio de diretório ativo no local (AD DS)**

    A integração do Active Directory Domain Services (AD DS) no local com o Azure Files (preview) fornece os métodos para armazenar dados de diretório, ao mesmo tempo em que os disponibiliza para usuários e administradores da rede. A segurança é integrada ao AD DS através da autenticação de logon e controle de acesso a objetos no diretório. Com um único logon de rede, os administradores podem gerenciar dados de diretório e organização em toda a sua rede, e os usuários autorizados da rede podem acessar recursos em qualquer lugar da rede. O AD DS é comumente adotado por empresas em ambientes locais e as credenciais AD DS são usadas como identidade para controle de acesso. Para obter mais informações, consulte [Visão geral dos serviços de domínio do diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **RBAC (Controle de Acesso Baseado em Função) do Azure**

    O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o mínimo de permissões necessárias para que eles realizem seus trabalhos. Para obter mais informações sobre o RBAC, consulte [O que é o controle de acesso baseado em função (RBAC) no Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Casos de uso comuns

A autenticação e o suporte baseados em identidade para ACLs do Windows em Arquivos Azure é melhor aproveitado para os seguintes casos de uso:

### <a name="replace-on-premises-file-servers"></a>Substitua servidores de arquivos no local

Depreciar e substituir servidores de arquivos espalhados no local é um problema comum que todas as empresas encontram em sua jornada de modernização de TI. Os compartilhamentos de arquivos do Azure com autenticação AD DS (preview) no local são os mais adequados aqui, quando você pode migrar os dados para arquivos do Azure. Uma migração completa permitirá que você aproveite os benefícios de alta disponibilidade e escalabilidade, ao mesmo tempo em que minimiza as alterações do lado do cliente. Ele fornece uma experiência de migração perfeita para os usuários finais, para que eles possam continuar a acessar seus dados com as mesmas credenciais usando suas máquinas de domínio existentes.

### <a name="lift-and-shift-applications-to-azure"></a>Aplicativos de elevação e mudança para o Azure

Quando você levanta e muda aplicativos para a nuvem, você deseja manter o mesmo modelo de autenticação para seus dados. À medida que estendemos a experiência de controle de acesso baseada em identidade aos compartilhamentos de arquivos do Azure, ele elimina a necessidade de alterar seu aplicativo para métodos modernos de auth e agilizar a adoção na nuvem. Os compartilhamentos de arquivos do Azure oferecem a opção de integrar-se com o Azure AD DS ou com o AD DS (preview) no local para autenticação. Se o seu plano é ser 100% nativo em nuvem e minimizar os esforços de gerenciamento de infra-estruturas em nuvem, o Azure AD DS seria um melhor ajuste como um serviço de domínio totalmente gerenciado. Se você precisar de compatibilidade total com os recursos de DS, você pode querer considerar estender seu ambiente AD DS para nuvem por controladores de domínio de auto-hospedagem em VMs. De qualquer forma, fornecemos a flexibilidade para escolher os serviços de domínio que atendem às suas necessidades de negócios.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e recuperação de desastres (DR)

Se você estiver mantendo seu armazenamento de arquivos principal no local, os compartilhamentos de arquivos do Azure podem servir como um armazenamento ideal para backup ou DR, para melhorar a continuidade dos negócios. Você pode usar os compartilhamentos de arquivos do Azure para fazer backup de seus dados de servidores de arquivos existentes, preservando os DACLs do Windows. Para cenários de DR, você pode configurar uma opção de autenticação para suportar a aplicação adequada do controle de acesso no failover.

## <a name="supported-scenarios"></a>Cenários com suporte

A tabela a seguir resume os cenários de autenticação de compartilhamentos de arquivos Azure suportados para o Azure AD DS e o AD DS (visualização) no local. Recomendamos selecionar o serviço de domínio que você adotou para o seu ambiente cliente para integração com arquivos Do Zure. Se você tiver AD DS (preview) já configurando no local ou no Azure onde seus dispositivos estão unidos ao seu AD, você deve optar por aproveitar o AD DS (preview) para autenticação de compartilhamentos de arquivos Do Azure. Da mesma forma, se você já adotou o Azure AD DS (GA), você deve usá-lo para autenticação de compartilhamentos de arquivos Do Zure.


|Autenticação Azure AD DS  | autenticação AD DS (preview) no local  |
|---------|---------|
|As máquinas Windows azure AD DS podem acessar os compartilhamentos de arquivos do Azure com credenciais Azure AD sobre SMB.     |As máquinas Windows aderidas por AD No local podem acessar os compartilhamentos de arquivos do Azure com credenciais de Active Directory no local que são sincronizadas com o Azure AD via SMB.         |

### <a name="unsupported-scenarios"></a>Cenários sem suporte

- A autenticação AD DS do Azure AD e a autenticação AD DS no local não suportam autenticação contra contas de computador. Em vez disso, você pode considerar o uso de uma conta de logon de serviço.
- A autenticação Azure AD DS não suporta autenticação contra dispositivos azure ad..

## <a name="advantages-of-identity-based-authentication"></a>Vantagens da autenticação baseada em identidade
A autenticação baseada em identidade para arquivos Azure oferece vários benefícios ao usar a autenticação de chaves compartilhadas:

-   **Amplie a experiência tradicional de acesso ao compartilhamento de arquivos baseado em identidade na nuvem com a DS ad ds e o Azure AD DS no local**  
    Se você planeja levantar e mudar seu aplicativo para a nuvem, substituindo servidores de arquivos tradicionais por compartilhamentos de arquivos do Azure, então você pode querer que seu aplicativo seja autenticado com credenciais AD DS ou AD DS no local para acessar dados de arquivos. O Azure Files suporta o uso de credenciais AD DS ou AD DS no local para acessar os compartilhamentos de arquivos Do Zure sobre SMB de VMs ad DS ou Ad DS no local.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões a uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup das ACLs do Windows (também conhecidas como NTFS) juntamente com seus dados**  
    Você pode usar os compartilhamentos de arquivos do Azure para fazer backup dos compartilhamentos de arquivos existentes no local. O Azure Files preserva suas ACLs juntamente com seus dados quando você faz backup de um compartilhamento de arquivos para compartilhamentos de arquivos do Azure sobre SMB.

## <a name="how-it-works"></a>Como ele funciona

Os compartilhamentos de arquivos do Azure suportam a autenticação do Kerberos para integração com o Azure AD DS ou com o AD DS (visualização) no Local. Antes de habilitar a autenticação nos compartilhamentos de arquivos do Azure, você deve primeiro configurar seu ambiente de domínio. Para autenticação Azure AD DS, você deve habilitar os Serviços de Domínio AD do Azure e o domínio se juntar às VMs das quais você planeja acessar dados de arquivos. Sua VM aderida por domínio deve residir na mesma rede virtual (VNET) que seu Azure AD DS. Da mesma forma, para autenticação AD DS (preview) no local, você precisa configurar seu controlador de domínio e domínio para juntar suas máquinas ou VMs.

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em compartilhamentos de arquivos do Azure, a solicitação é enviada ao Azure AD DS para autenticar a identidade. Se a autenticação for bem sucedida, o Azure AD DS retorna um token Kerberos. O aplicativo envia uma solicitação que inclui o token Kerberos, e os compartilhamentos de arquivos do Azure usam esse token para autorizar a solicitação. As ações de arquivos do Azure recebem apenas o token e não persistem as credenciais Do Azure AD DS. A autenticação AD DS no local funciona de forma semelhante, onde seu DS AD fornece o token Kerberos.

![Diagrama mostrando a captura de tela de autenticação do Azure AD por SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Habilite a autenticação baseada em identidade

Você pode habilitar a autenticação baseada em identidade com o Azure AD DS ou o AD DS (preview) no local para compartilhamentos de arquivos Azure em suas contas de armazenamento novas e existentes. Apenas um serviço de domínio pode ser usado para autenticação de acesso a arquivos na conta de armazenamento, que se aplica a todos os compartilhamentos de arquivos na conta. Orientação detalhada sobre a configuração de seus compartilhamentos de arquivos para autenticação com o Azure AD DS em nosso artigo Habilitar a autenticação do [Azure Active Directory Domain Services em Arquivos Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e orientação para AD DS (visualização) no local em nosso outro artigo, [Habilitar no local a autenticação do Active Directory Domain Services sobre sMB para compartilhamentos de arquivos Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure

Uma vez que a autenticação AD DS do Azure AD ou a ad(preview) no local esteja ativada, você pode usar funções RBAC incorporadas ou configurar funções personalizadas para identidades Azure AD e atribuir direitos de acesso a quaisquer compartilhamentos de arquivos em suas contas de armazenamento. A permissão atribuída permite que a identidade concedida tenha acesso apenas ao compartilhamento, nada mais, nem mesmo o diretório raiz. Você ainda precisa configurar separadamente permissões de diretório ou nível de arquivo para compartilhamentos de arquivos Do Zure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configure permissões de diretório ou nível de arquivo para arquivos Azure

Os compartilhamentos de arquivos do Azure aplicam permissões de arquivo padrão do Windows tanto no diretório quanto no nível do arquivo, incluindo o diretório raiz. A configuração de permissões de diretório ou nível de arquivo é suportada tanto em SMB quanto em REST. Monte o compartilhamento de arquivos de destino da VM e configure permissões usando o Windows File Explorer, o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)ou o comando [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário

Um usuário com a chave da conta de armazenamento pode acessar os compartilhamentos de arquivos do Azure com permissões de superusuário. As permissões de superusuário ignoram todas as restrições de controle de acesso.

> [!IMPORTANT]
> Nossa melhor prática recomendada de segurança é evitar o compartilhamento das chaves da sua conta de armazenamento e aproveitar a autenticação baseada em identidade sempre que possível.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Preservar diretórios e acls de arquivo ao importar dados para compartilhamentos de arquivos do Azure

O Azure Files suporta a preservação de ACLs de nível de diretório ou arquivo ao copiar dados para compartilhamentos de arquivos do Azure. Você pode copiar ACLs em um diretório ou arquivo para compartilhamentos de arquivos do Azure usando o Azure File Sync ou os conjuntos de ferramentas comuns de movimentação de arquivos. Por exemplo, você pode usar `/copy:s` [a robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador para copiar dados, bem como ACLs para um compartilhamento de arquivos Azure. As ACLs são preservadas por padrão, você não é obrigado a habilitar autenticação baseada em identidade em sua conta de armazenamento para preservar ACLs.

## <a name="pricing"></a>Preços
Não há nenhuma taxa de serviço adicional para habilitar a autenticação baseada em identidade sobre SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte [os preços dos arquivos Do Zure](https://azure.microsoft.com/pricing/details/storage/files/) e os preços dos [serviços de domínio do Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre arquivos Azure e autenticação baseada em identidade sobre SMB, consulte esses recursos:

- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Habilite a autenticação de serviços de domínio do Active Directory on-premises sobre smb para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md)
- [Habilite a autenticação de serviços de domínio do diretório ativo do Azure em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Perguntas Frequentes](storage-files-faq.md)
