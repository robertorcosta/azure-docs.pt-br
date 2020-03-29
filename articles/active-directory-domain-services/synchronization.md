---
title: Como funciona a sincronização nos serviços de domínio do Azure AD | Microsoft Docs
description: Saiba como o processo de sincronização funciona para objetos e credenciais de um inquilino Azure AD ou no ambiente de serviços de domínio do Active Directory no local para um domínio gerenciado do Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 7e0e904b182a57a51b5d76f0acebc13bce5902b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944428"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Como objetos e credenciais são sincronizados em um domínio gerenciado do Azure AD Domain Services

Objetos e credenciais em um domínio Gerenciado pelo Azure Active Directory Domain Services (AD DS) podem ser criados localmente dentro do domínio ou sincronizados a partir de um inquilino do Azure Active Directory (Azure AD). Quando você implanta o Azure AD DS pela primeira vez, uma sincronização automática unidirecional é configurada e começa a replicar os objetos do Azure AD. Essa sincronização unidirecional continua a ser executada em segundo plano para manter o domínio gerenciado pelo Azure AD DS atualizado com quaisquer alterações do Azure AD. Nenhuma sincronização ocorre do Azure AD DS de volta ao Azure AD.

Em um ambiente híbrido, objetos e credenciais de um domínio AD DS no local podem ser sincronizados com o Azure AD Connect usando o Azure AD Connect. Uma vez que esses objetos são sincronizados com sucesso com o Azure AD, a sincronização automática em segundo plano torna esses objetos e credenciais disponíveis para aplicativos usando o domínio gerenciado pelo Azure AD DS.

O diagrama a seguir ilustra como a sincronização funciona entre o Azure AD DS, o Azure AD e um ambiente AD DS opcional no local:

![Visão geral de sincronização para um domínio gerenciado do Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronização do Azure AD para o Azure AD DS

Contas de usuário, membros de grupo e hashes de credenciais são sincronizadas de uma maneira do Azure AD ao Azure AD DS. Esse processo de sincronização é automático. Você não precisa configurar, monitorar ou gerenciar esse processo de sincronização. A sincronização inicial pode levar algumas horas a alguns dias, dependendo do número de objetos no diretório Azure AD. Depois que a sincronização inicial for concluída, as alterações feitas no Azure AD, como alterações de senha ou atributo, são sincronizadas automaticamente com o Azure AD DS.

O processo de sincronização é de uma maneira / unidirecional por design. Não há sincronização reversa de alterações do Azure AD DS de volta ao Azure AD. Um domínio gerenciado pelo Azure AD DS é em grande parte somente leitura, exceto para OUs personalizados que você pode criar. Você não pode fazer alterações nos atributos do usuário, senhas de usuário ou membros de grupo dentro de um domínio gerenciado pelo Azure AD DS.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Sincronização e mapeamento de atributos para OZure AD DS

A tabela a seguir lista alguns atributos comuns e como eles são sincronizados com o Azure AD DS.

| Atributo no Azure AD DS | Fonte | Observações |
|:--- |:--- |:--- |
| UPN | Atributo *UPN* do usuário no inquilino Azure AD | O atributo UPN do inquilino Azure AD é sincronizado como está no Azure AD DS. A maneira mais confiável de fazer login em um domínio gerenciado pelo Azure AD DS é usando o UPN. |
| SAMAccountName | E-mail do *usuárioAtributo* de apelido no inquilino Azure AD ou gerado automaticamente | O atributo *SAMAccountName* é originado do atributo *mailNickname* no inquilino Azure AD. Se várias contas de usuário tiverem o mesmo atributo *mailNickname,* o *SAMAccountName* será gerado automaticamente. Se o *e-mail* do usuárioO prefixo ou *UPN* tiver mais de 20 caracteres, o *SAMAccountName* será gerado automaticamente para atender ao limite de 20 caracteres nos atributos *SAMAccountName.* |
| Senhas | Senha do usuário do inquilino Azure AD | Os hashes de senha legados necessários para a autenticação NTLM ou Kerberos são sincronizados do inquilino Azure AD. Se o inquilino Azure AD estiver configurado para sincronização híbrida usando o Azure AD Connect, esses hashes de senha serão originados do ambiente AD DS no local. |
| SID do usuário/grupo primário | Autogerado | O SID principal para contas de usuário/grupo é gerado automaticamente no Azure AD DS. Esse atributo não corresponde ao SID principal do usuário/grupo do objeto em um ambiente AD DS no local. Essa incompatibilidade é porque o domínio Gerenciado pelo Azure AD DS tem um namespace SID diferente do domínio AD DS no local. |
| Histórico de SID para usuários e grupos | SID de usuário e grupo primário local | O atributo *SidHistory* para usuários e grupos no Azure AD DS é definido para corresponder ao usuário principal correspondente ou ao GRUPO SID em um ambiente AD DS local. Esse recurso ajuda a tornar mais fácil o levantamento e a mudança de aplicativos no local para o Azure AD DS, já que você não precisa re-recursos de ACL. |

> [!TIP]
> **Faça login no domínio gerenciado usando o formato UPN** O atributo *SAMAccountName,* como `AADDSCONTOSO\driley`, pode ser gerado automaticamente para algumas contas de usuário em um domínio gerenciado pelo Azure AD DS. O *SAMAccountName* gerado automaticamente dos usuários pode diferir do seu prefixo UPN, por isso nem sempre é uma maneira confiável de fazer login.
>
> Por exemplo, se vários usuários tiverem o mesmo atributo *mailNickname* ou os usuários tiverem prefixos UPN excessivamente longos, o *SAMAccountName* para esses usuários pode ser gerado automaticamente. Use o formato UPN, como, `driley@aaddscontoso.com`para entrar de forma confiável em um domínio gerenciado pelo Azure AD DS.

### <a name="attribute-mapping-for-user-accounts"></a>Mapeamento de atributos para contas de usuário

A tabela a seguir ilustra como atributos específicos para objetos de usuário no Azure AD são sincronizados com atributos correspondentes no Azure AD DS.

| Atributo do usuário no Azure AD | Atributo do usuário no Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (define ou limpa o bit de ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (às vezes pode ser gerado automaticamente) |
| Serviço Móvel |Serviço Móvel |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (define ou limpa o bit de DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| sobrenome |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapeamento de atributo para grupos

A tabela a seguir ilustra como atributos específicos para objetos de grupo no Azure AD são sincronizados com atributos correspondentes no Azure AD DS.

| Atributo de grupo no Azure AD | Atributo de grupo no Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (às vezes pode ser gerado automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronização do AD DS no local para a Azure AD e Azure AD DS

O Azure AD Connect é usado para sincronizar contas de usuários, membros de grupo e hashes de credenciais de um ambiente AD DS no local para o Azure AD. Os atributos de contas de usuário, como o UPN e o sid (on-premises security identifier) são sincronizados. Para fazer login usando o Azure AD DS, os hashes de senha legado necessários para a autenticação NTLM e Kerberos também são sincronizados com o Azure AD.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes do AD DS locais. Não há suporte para instalar o Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta ao Azure AD.

Se você configurar a gravação de volta, as alterações do Azure AD serão sincronizadas de volta ao ambiente AD DS no local. Por exemplo, se um usuário alterar sua senha usando o gerenciamento de senhade autoatendimento do Azure AD, a senha será atualizada de volta no ambiente AD DS local.

> [!NOTE]
> Sempre use a versão mais recente do Azure AD Connect para garantir que você tenha correções para todos os bugs conhecidos.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronização de um ambiente de várias florestas locais

Muitas organizações têm um ambiente AD DS bastante complexo que inclui várias florestas. O Azure AD Connect suporta sincronizar usuários, grupos e hashes de credenciais de ambientes multiflorestais para o Azure AD.

O Azure AD tem um namespace muito mais simples e plano. Para permitir aos usuários acessar aplicativos protegidos pelo Azure AD de forma confiável, resolva os conflitos de UPN entre contas de usuário em diferentes florestas. Os domínios gerenciados do Azure AD DS usam uma estrutura OU plana, semelhante ao Azure AD. Todas as contas e grupos de usuários são armazenados no contêiner *Usuários AADDC,* apesar de serem sincronizados de diferentes domínios ou florestas locais, mesmo se você tiver configurado uma estrutura hierárquica de OU no local. O domínio gerenciado pelo Azure AD DS achata qualquer estrutura hierárquica de Ou.

Como detalhado anteriormente, não há sincronização do Azure AD DS de volta ao Azure AD. Você pode [criar uma Unidade Organizacional (OU) personalizada](create-ou.md) no Azure AD DS e, em seguida, usuários, grupos ou contas de serviço dentro dessas UOs personalizadas. Nenhum dos objetos criados em OUs personalizados são sincronizados de volta ao Azure AD. Esses objetos estão disponíveis apenas no domínio gerenciado pelo Azure AD DS e não são visíveis usando cmdlets AD PowerShell do Azure, API do Microsoft Graph ou usando a UI de gerenciamento ad do Azure.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>O que não está sincronizado com o Azure AD DS

Os seguintes objetos ou atributos não são sincronizados de um ambiente AD DS no local para o Azure AD ou Azure AD DS:

* **Atributos excluídos:** Você pode optar por excluir certos atributos da sincronização com o Azure AD de um ambiente AD DS no local usando o Azure AD Connect. Esses atributos excluídos não estão disponíveis no Azure AD DS.
* **Políticas de grupo:** As políticas de grupo configuradas em um ambiente AD DS local não são sincronizadas com o Azure AD DS.
* **Pasta Sysvol:** O conteúdo da pasta *Sysvol* em um ambiente AD DS no local não está sincronizado com o Azure AD DS.
* **Objetos de computador:** Objetos de computador para computadores unidos a um ambiente AD DS no local não são sincronizados com o Azure AD DS. Esses computadores não têm uma relação de confiança com o domínio gerenciado pelo Azure AD DS e pertencem apenas ao ambiente AD DS no local. No Azure AD DS, apenas objetos de computador para computadores que tenham explicitamente domínio unido ao domínio gerenciado são mostrados.
* **Atributos SidHistory para usuários e grupos:** Os SIDs do usuário principal e do grupo principal de um ambiente AD DS no local são sincronizados com o Azure AD DS. No entanto, os atributos *SidHistory* existentes para usuários e grupos não são sincronizados do ambiente AD DS no local para o Azure AD DS.
* **Estruturas das Unidades de Organização (U):** As unidades organizacionais definidas em um ambiente AD DS no local não sincronizam com o Azure AD DS. Existem duas OUs incorporadas no Azure AD DS - uma para usuários e outra para computadores. O domínio gerenciado pelo Azure AD DS tem uma estrutura OU plana. Você pode optar por [criar um OU personalizado em seu domínio gerenciado](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerações de segurança e sincronização de hash de senha

Quando você habilita o Azure AD DS, são necessários hashes de senha legado para autenticação NTLM + Kerberos. O Azure AD não armazena senhas de texto claro, então esses hashes não podem ser gerados automaticamente para contas de usuário existentes. Uma vez gerados e armazenados, os hashes de senha compatíveis ntlm e kerberos são sempre armazenados de forma criptografada no Azure AD.

As chaves de criptografia são exclusivas para cada inquilino do Azure AD. Esses hashes são criptografados de tal forma que apenas o Azure AD DS tenha acesso às chaves de descriptografia. Nenhum outro serviço ou componente no Azure AD tem acesso às chaves de descriptografia.

Os hashes de senha legado são sincronizados do Azure AD nos controladores de domínio para um domínio gerenciado pelo Azure AD DS. Os discos para esses controladores de domínio gerenciados no Azure AD DS são criptografados em repouso. Esses hashes de senha são armazenados e protegidos nesses controladores de domínio semelhantes à forma como as senhas são armazenadas e protegidas em um ambiente AD DS no local.

Para ambientes Azure AD somente na nuvem, [os usuários devem redefinir/alterar sua senha](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os hashes de senha necessários sejam gerados e armazenados no Azure AD. Para qualquer conta de usuário de nuvem criada no Azure AD depois de habilitar o Azure AD Domain Services, os hashes de senha são gerados e armazenados nos formatos compatíveis com NTLM e Kerberos. Essas novas contas não precisam redefinir ou alterar sua senha gerando os hashes de senha legado.

Para contas de usuário híbridos sincronizadas a partir do ambiente AD DS on-premises usando o Azure AD Connect, você deve [configurar o Azure AD Connect para sincronizar hashes de senha nos formatos compatíveis com NTLM e Kerberos.](tutorial-configure-password-hash-sync.md)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as especificidades da sincronização de senha, consulte [Como a sincronização de hash de senha funciona com o Azure AD Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Para começar com o Azure AD DS, [crie um domínio gerenciado](tutorial-create-instance.md).
