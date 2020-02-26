---
title: Visão geral-autorização baseada em identidade de arquivos do Azure
description: Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre SMB (protocolo de mensagem de servidor) por meio de Azure Active Directory Domain Services (AD DS) e Active Directory. As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 673bf3be59d72b2cc62b9f92af353fee235c5ddc
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598809"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Visão geral do suporte de autenticação baseada em identidade de arquivos do Azure para acesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação do AD para compartilhamentos de arquivos do Azure, consulte [habilitar a autenticação de Active Directory sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md).

Para saber como habilitar a autenticação de AD DS do Azure para compartilhamentos de arquivos do Azure, consulte [habilitar a autenticação de Azure Active Directory Domain Services em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionados à autenticação do serviço de domínio do Azure AD sobre SMB para compartilhamentos de arquivos do Azure:

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre Kerberos, consulte [Visão geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**

    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [Protocolo SMB da Microsoft e Visão geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    O Azure AD (Azure Active Directory) é o serviço de gerenciamento de diretório e identidade multilocatário baseado em nuvem da Microsoft. O Azure AD combina serviços de diretório principais, gerenciamento de acesso a aplicativos e proteção de identidade em uma única solução. O Azure AD permite que suas VMs (máquinas virtuais) do Windows ingressadas no domínio acessem compartilhamentos de arquivos do Azure com suas credenciais do Azure AD. Para obter mais informações, confira [O que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (AD DS do Azure)**

    O Azure AD Domain Services (GA) fornece serviços de domínio gerenciado, como ingresso no domínio, políticas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [Serviços de Domínio do Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (AD DS, também conhecido como AD)**

    O Active Directory (AD) (versão prévia) fornece os métodos para armazenar dados de diretório ao disponibilizá-los para usuários e administradores de rede. A segurança é integrada com Active Directory por meio de autenticação de logon e controle de acesso a objetos no diretório. Com um único logon de rede, os administradores podem gerenciar dados de diretório e organização em toda a rede, e os usuários de rede autorizados podem acessar recursos em qualquer lugar da rede. O AD é normalmente adotado por empresas no local e usa credenciais do AD como a identidade do controle de acesso. Para obter mais informações, consulte [Active Directory Domain Services visão geral](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **RBAC (Controle de Acesso Baseado em Função) do Azure**

    O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o mínimo de permissões necessárias para que eles realizem seus trabalhos. Para obter mais informações sobre o RBAC, consulte [o que é o RBAC (controle de acesso baseado em função) no Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Casos de uso comuns

A autenticação baseada em identidade e o suporte para ACLs do Windows em arquivos do Azure é melhor aproveitado para os seguintes casos de uso:

### <a name="replace-on-premises-file-servers"></a>Substituir servidores de arquivos locais

Reprovar e substituir servidores de arquivos locais dispersos é um problema comum que cada empresa encontra em sua jornada de modernização de ti. Os compartilhamentos de arquivos do Azure com autenticação do AD (versão prévia) são os mais adequados aqui, quando você pode migrar os dados para os arquivos do Azure. Uma migração completa permitirá que você aproveite os benefícios de alta disponibilidade e escalabilidade, minimizando também as alterações do lado do cliente, especialmente a infraestrutura de domínio do AD complicada. Ele fornece uma experiência de migração direta aos usuários finais, para que eles possam continuar acessando seus dados com as mesmas credenciais usando seus computadores ingressados no domínio existentes.

### <a name="lift-and-shift-applications-to-azure"></a>Migrar e deslocar aplicativos para o Azure

Ao mover os aplicativos para a nuvem, você deseja manter o mesmo modelo de autenticação para seus dados. À medida que estendemos a experiência de controle de acesso com base em identidade para compartilhamentos de arquivos do Azure, ele elimina a necessidade de alterar seu aplicativo para métodos de autenticação modernos e agilizar a adoção da nuvem. Os compartilhamentos de arquivos do Azure fornecem a opção de integração com o Azure AD DS (GA) ou AD (versão prévia) para autenticação. Se seu plano for ser de 100% na nuvem nativa e minimizar os esforços de gerenciamento de infraestruturas de nuvem, o Azure AD DS seria uma melhor opção como um serviço de domínio totalmente gerenciado. Se precisar de compatibilidade total com recursos de AD DS (GA), convém considerar estender seu ambiente do AD para a nuvem por meio da hospedagem automática de controladores de domínio em VMs. De qualquer forma, fornecemos a flexibilidade para escolher os serviços de domínio que atendam às suas necessidades de negócios.

### <a name="backup-and-disaster-recovery-dr"></a>Recuperação de desastres e backup (DR)

Se você estiver mantendo seu armazenamento de arquivos primário local, os compartilhamentos de arquivos do Azure podem servir como um armazenamento ideal para backup ou DR, para melhorar a continuidade dos negócios. Você pode usar compartilhamentos de arquivos do Azure para fazer backup de seus dados de servidores de arquivos existentes, preservando as DACLs do Windows. Para cenários de DR, você pode configurar uma opção de autenticação para dar suporte à imposição de controle de acesso adequada no failover.

## <a name="supported-scenarios"></a>Cenários com suporte

A tabela a seguir resume os cenários de autenticação de compartilhamentos de arquivos do Azure com suporte para o Azure AD DS (GA) e o AD (versão prévia). É recomendável selecionar o serviço de domínio que você adotou para o seu ambiente de cliente para integração com os arquivos do Azure. Se você já tiver o AD (versão prévia) configurado localmente ou no Azure em que seus dispositivos estão ingressados no domínio, você deve optar por aproveitar o AD (versão prévia) para a autenticação de compartilhamentos de arquivos do Azure. Da mesma forma, se você já tiver adotado o Azure AD DS (GA), deverá usá-lo para autenticação de compartilhamentos de arquivos do Azure.


|Autenticação de AD DS do Azure (GA)  |Autenticação do AD (versão prévia)  |
|---------|---------|
|O Azure AD DS computadores Windows ingressados no domínio podem acessar compartilhamentos de arquivos do Azure com as credenciais do Azure AD sobre SMB.     |As máquinas Windows ingressadas no domínio do AD podem acessar compartilhamentos de arquivos do Azure com credenciais do AD que são sincronizadas com o Azure AD por SMB.         |

### <a name="unsupported-scenarios"></a>Cenários sem suporte

- A autenticação do Azure AD DS (GA) e o AD (versão prévia) não dão suporte à autenticação em relação às contas de computador. Em vez disso, você pode considerar o uso de uma conta de logon de serviço.
- A autenticação do Azure AD DS (GA) não oferece suporte à autenticação em dispositivos ingressados na nuvem do Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Vantagens da autenticação baseada em identidade
A autenticação baseada em identidade para arquivos do Azure oferece vários benefícios em relação ao uso da autenticação de chave compartilhada:

-   **Estenda a experiência tradicional de acesso ao compartilhamento de arquivos baseado em identidade para a nuvem com o AD e o Azure AD DS**  
    Se você planeja "aumentar e deslocar" seu aplicativo para a nuvem, substituindo os servidores de arquivos tradicionais por compartilhamentos de arquivos do Azure, convém que seu aplicativo seja autenticado com as credenciais do AD ou do Azure AD para acessar os dados do arquivo. Os arquivos do Azure dão suporte ao uso de credenciais do AD ou do Azure AD para acessar compartilhamentos de arquivos do Azure via SMB do AD ou do Azure AD DS VMs ingressadas no domínio.

-   **Impor o controle de acesso granular em compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões a uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um compartilhamento de arquivos do Azure único para colaboração em projetos. Você pode conceder acesso para todas as equipes a diretórios não confidenciais, ao mesmo tempo em que limita o acesso a diretórios que contenham dados financeiros confidenciais apenas para sua equipe de Finanças. 

-   **Fazer backup de ACLs do Windows (também conhecido como NTFS) junto com seus dados**  
    Você pode usar compartilhamentos de arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os arquivos do Azure preservam suas ACLs junto com seus dados quando você faz backup de um compartilhamento de arquivos para compartilhamentos de arquivos do Azure via SMB.

## <a name="how-it-works"></a>Como ele funciona
Os compartilhamentos de arquivos do Azure dão suporte à autenticação Kerberos para integração com o Azure AD DS (GA) ou AD (versão prévia). Antes de habilitar a autenticação nos compartilhamentos de arquivos do Azure, você deve primeiro configurar o ambiente de domínio. Para a autenticação de AD DS do Azure (GA), você deve habilitar Azure AD Domain Services e ingressar no domínio as VMs das quais você planeja acessar dados de arquivo. Sua VM ingressada no domínio deve residir na mesma rede virtual (VNET) que seu Azure AD Domain Services. Da mesma forma, para a autenticação do AD (versão prévia), você precisa configurar seu Active Directory controlador de domínio e ingressar no domínio em seus computadores ou máquinas virtuais.

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em compartilhamentos de arquivos do Azure, a solicitação é enviada para Azure AD Domain Services para autenticar a identidade. Se a autenticação for bem-sucedida, o Azure AD Domain Services retorna um token do Kerberos. O aplicativo envia uma solicitação que inclui o token Kerberos e os compartilhamentos de arquivos do Azure usam esse token para autorizar a solicitação. Os compartilhamentos de arquivos do Azure recebem apenas o token e não mantêm as credenciais do Azure AD. A autenticação do AD funciona de maneira semelhante, em que o AD fornece o token Kerberos.

![Diagrama mostrando a captura de tela de autenticação do Azure AD por SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Habilitar a autenticação baseada em identidade

Você pode habilitar a autenticação baseada em identidade com o Azure AD DS (GA) ou o AD (versão prévia) para compartilhamentos de arquivos do Azure em suas contas de armazenamento novas e existentes. Somente um serviço de domínio pode ser usado para autenticação de acesso a arquivos na conta de armazenamento, que se aplica a todos os compartilhamentos de arquivos na conta. Orientações passo a passo detalhadas sobre como configurar os compartilhamentos de arquivos para autenticação com o Azure AD DS (GA) em nosso artigo [habilitar a autenticação Azure Active Directory Domain Services em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e orientação para AD (versão prévia) em nosso outro artigo, [habilitar a autenticação Active Directory sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões no nível de compartilhamento para Arquivos do Azure

Quando a autenticação do Azure AD DS (GA) ou do AD (versão prévia) estiver habilitada, você poderá usar funções RBAC internas ou configurar funções personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivos em suas contas de armazenamento. a permissão atribuída permite que a identidade concedida obtenha acesso somente ao compartilhamento, nada mais, nem mesmo o diretório raiz. Você ainda precisa configurar separadamente as permissões no nível do diretório ou do arquivo para compartilhamentos de arquivos do Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para arquivos do Azure

Compartilhamentos de arquivos do Azure impõe permissões padrão de arquivo do Windows no nível do diretório e do arquivo, incluindo o diretório raiz. A configuração de permissões no nível do diretório ou do arquivo é suportada tanto pelo SMB quanto pelo REST. Monte o compartilhamento de arquivos de destino de sua VM e configure permissões usando o explorador de arquivos do Windows, o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)ou o comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de superusuário

Um usuário que possua a chave de conta de armazenamento pode acessar compartilhamentos de arquivos do Azure com permissões de superusuário. As permissões de superusuário ignoram todas as restrições de controle de acesso.

> [!IMPORTANT]
> Nossa prática recomendada de segurança recomendada é evitar compartilhar suas chaves de conta de armazenamento e aproveitar a autenticação baseada em identidade sempre que possível.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Preservar ACLs de diretório e arquivo ao importar dados para compartilhamentos de arquivos do Azure

Os arquivos do Azure dão suporte à preservação de ACLs de nível de diretório ou arquivo ao copiar dados para compartilhamentos de arquivos do Azure. Você pode copiar ACLs em um diretório ou arquivo para compartilhamentos de arquivos do Azure usando Sincronização de Arquivos do Azure ou conjuntos de ferramentas de movimentação de arquivos comuns. Por exemplo, você pode usar o [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador `/copy:s` para copiar dados, bem como ACLs para um compartilhamento de arquivos do Azure. As ACLs são preservadas por padrão, não é necessário habilitar a autenticação baseada em identidade em sua conta de armazenamento para preservar ACLs.

## <a name="pricing"></a>Preços
Não há nenhum encargo de serviço adicional para habilitar a autenticação baseada em identidade sobre SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte páginas de [preços de Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) e preços de [arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) se você estiver procurando informações do AAD DS.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre os arquivos do Azure e a autenticação baseada em identidade sobre o SMB, consulte estes recursos:

- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)
- [Habilitar a autenticação de Active Directory sobre SMB para compartilhamentos de arquivos do Azure](storage-files-identity-auth-active-directory-enable.md)
- [Habilitar a autenticação de Azure Active Directory Domain Services nos arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [perguntas frequentes](storage-files-faq.md)
