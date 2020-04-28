---
title: Permissões de usuário padrão - Azure Active Directory | Microsoft Docs
description: Saiba mais sobre as permissões de usuário diferentes disponíveis no Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366250"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Quais são as permissões de usuário padrão no Azure Active Directory?
No Azure Active Directory (Azure AD), a todos os usuários é concedido um conjunto de permissões padrão. Um acesso do usuário é composto por tipo de usuário, suas [atribuições de função](active-directory-users-assign-role-azure-portal.md) e sua posse de objetos individuais. Este artigo descreve as permissões padrão e contém uma comparação entre os padrões do usuário membro e convidado. As permissões de usuário padrão podem ser alteradas somente nas configurações do usuário no Azure AD.

## <a name="member-and-guest-users"></a>Usuários membro e convidado
O conjunto de permissões padrão recebido depende de se o usuário é um membro nativo do locatário (usuário membro) ou se o usuário é trazido de outro diretório como um convidado de colaboração B2B (usuário convidado). Veja [O que é colaboração B2B do Azure AD?](../b2b/what-is-b2b.md) para obter mais informações sobre como adicionar usuários convidados.
* Usuários membro podem registrar aplicativos, gerenciar seu próprio número de celular e foto perfil, alterar sua senha e convidar pessoas B2B. Além disso, os usuários podem ler todas as informações de diretório (com algumas poucas exceções). 
* Usuários convidados têm permissões de diretório restritas. Por exemplo, os usuários convidados não podem procurar informações do locatário além de suas próprias informações de perfil. No entanto, um usuário convidado pode recuperar informações sobre outro usuário, fornecendo o Nome Principal do Usuário ou o objectId. Um usuário convidado pode ler as propriedades dos grupos aos quais pertencem, incluindo a associação ao grupo, independentemente da configuração de **As permissões de usuários convidados são limitadas**. Um convidado não pode exibir informações sobre nenhum outro objeto de locatário.

Permissões padrão para convidados são restritivas por padrão. Convidados podem ser adicionados a funções de administrador, o que lhes concedem permissões totais de leitura e gravação contidas na função. Há uma restrição adicional disponível, a capacidade de convidados convidarem outras pessoas. Ao definir a opção **Convidados podem convidar** como **Não**, impede-se que convidados convidem outras pessoas. Consulte [Delegar convites para colaboração B2B](../b2b/delegate-invitations.md) para saber como. Para conceder a usuários convidados as mesmas permissões de usuários membros por padrão, defina **Permissões a usuários convidados são limitadas** para **Não**. Essa configuração concede permissões completas de usuários membros para usuários convidados por padrão, bem como permite que convidados sejam adicionados a funções administrativas.

## <a name="compare-member-and-guest-default-permissions"></a>Comparar permissões padrão de membro e convidado

**Área** | **Permissões de usuário membro** | **Permissões de usuário convidado**
------------ | --------- | ----------
Usuários e contatos | Ler todas as propriedades públicas de usuários e contatos<br>Convidar pessoas<br>Alterar a própria senha<br>Gerenciar o próprio número de celular<br>Gerenciar a própria foto<br>Invalidar os próprios tokens de atualização | Ler as próprias propriedades<br>Ler nome de exibição, email, nome de entrada, foto, nome principal de usuário e propriedades de tipo de usuário de outros usuários e contatos<br>Alterar a própria senha
Grupos | Criar grupos de segurança<br>Criar grupos do Office 365<br>Ler todas as propriedades de grupos<br>Ler associações de grupos não ocultos<br>Ler associações de grupo oculto do Office 365 para grupo associado<br>Gerenciar propriedades, posse e associação de grupos pertencentes ao usuário<br>Adicionar convidados a grupos próprios<br>Gerenciar configurações de associação dinâmica<br>Excluir grupos próprios<br>Restaurar grupos próprios do Office 365 | Ler todas as propriedades de grupos<br>Ler associações de grupos não ocultos<br>Ler associações de grupo do Office 365 oculto para grupos associados<br>Gerenciar grupos próprios<br>Adicionar convidados a grupos próprios (se permitido)<br>Excluir grupos próprios<br>Restaurar grupos próprios do Office 365<br>Ler propriedades dos grupos aos quais pertencem, incluindo associação.
Aplicativos | Registrar (criar) novo aplicativo<br>Ler propriedades de aplicativos registrados e corporativos<br>Gerenciar propriedades do aplicativo, atribuições e credenciais para aplicativos próprios<br>Criar ou excluir senha de aplicativo do usuário<br>Excluir aplicativos próprios<br>Restaurar aplicativos próprios | Ler propriedades de aplicativos registrados e corporativos<br>Gerenciar propriedades do aplicativo, atribuições e credenciais para aplicativos próprios<br>Excluir aplicativos próprios<br>Restaurar aplicativos próprios
Dispositivos | Ler todas as propriedades de dispositivos<br>Gerenciar todas as propriedades de dispositivos próprios<br> | Nenhuma permissão<br>Excluir dispositivos próprios<br>
Diretório | Ler todas as informações da empresa<br>Ler todos os domínios<br>Ler todos os contratos de parceiro | Ler nome de exibição e domínios verificados
Funções e escopos | Ler todas as funções e associações administrativas<br>Ler todas as propriedades e associação de unidades administrativas | Nenhuma permissão 
Assinaturas | Ler todas as assinaturas<br>Habilitar Membro de plano de serviço | Nenhuma permissão
Políticas | Ler todas as propriedades de políticas<br>Gerenciar todas as propriedades de política própria | Nenhuma permissão

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Para restringir as permissões padrão para usuários membro

Permissões padrão para usuários membro podem ser restringidas das seguintes maneiras.

Permissão | Configurar explicação
---------- | ------------
Os usuários podem registrar o aplicativo | Definir essa opção como não impede que os usuários criem registros de aplicativo. A capacidade pode então ser concedida de volta a indivíduos específicos, adicionando-os à função de desenvolvedor de aplicativos.
Permitir que os usuários se conectem à conta corporativa ou de estudante com o LinkedIn | Definir essa opção como não impede que os usuários conectem sua conta corporativa ou de estudante com sua conta do LinkedIn. Para obter mais informações, consulte o [consentimento e compartilhamento de dados de conexões de contas do LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Capacidade de criar grupos de segurança | Definir essa opção como Não impede que os usuários criem grupos de segurança. Administradores globais e administradores de usuários ainda podem criar grupos de segurança. Consulte [Cmdlets do Active Directory do Azure para definir as configurações de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Capacidade de criar grupos do Office 365 | Definir essa opção como Não impede que os usuários criem grupos do Office 365. Definir essa opção para Alguns permite que um conjunto selecionado de usuários crie grupos do Office 365. Administradores globais e administradores de usuários ainda poderão criar grupos do Office 365. Consulte [Cmdlets do Active Directory do Azure para definir as configurações de grupo](../users-groups-roles/groups-settings-cmdlets.md) para saber como.
Restringir o acesso ao portal de administração do Azure AD | Definir essa opção como não permite que os não administradores usem o portal de administração do Azure AD para ler e gerenciar recursos do Azure AD. Sim restringe todos os não-administradores de acessar dados do Azure AD no portal de administração. Importante Observação: essa configuração não restringe o acesso aos dados do Azure AD usando o PowerShell ou outros clientes, como o Visual Studio. Quando definido como Sim, para conceder a um usuário não administrador específico a capacidade de usar o portal de administração do Azure AD, atribua qualquer função administrativa, como a função leitores de diretório. Essa função permite ler informações básicas do diretório, que os usuários Membros têm por padrão (convidados e entidades de serviço não).
Capacidade de ler outros usuários | Essa configuração só está disponível no PowerShell. Definir esse sinalizador como $false impede que todos os não-administradores leiam informações do usuário do diretório. Esse sinalizador não impede a leitura de informações do usuário em outros serviços da Microsoft, como o Exchange Online. Essa configuração destina-se a circunstâncias especiais e a definição desse sinalizador como $false não é recomendada.

## <a name="object-ownership"></a>Propriedade do objeto

### <a name="application-registration-owner-permissions"></a>Permissões de proprietário de registro de aplicativo
Quando um usuário registra um aplicativo, ele é automaticamente adicionado como um proprietário do aplicativo. Como proprietário, ele pode gerenciar os metadados do aplicativo, como o nome e as permissões que o aplicativo solicita. Eles também podem gerenciar a configuração de locatário específico do aplicativo, como configuração de SSO e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Diferentemente dos Administradores globais, proprietários só podem gerenciar os aplicativos que eles possuem.

### <a name="enterprise-application-owner-permissions"></a>Permissões do proprietário do aplicativo empresarial
Quando um usuário adiciona um novo aplicativo empresarial, ele é adicionado automaticamente como um proprietário. Como proprietário, eles podem gerenciar a configuração específica do locatário do aplicativo, como a configuração de SSO, o provisionamento e as atribuições de usuário. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais, os proprietários podem gerenciar somente os aplicativos que eles possuem.

### <a name="group-owner-permissions"></a>Permissões de proprietário do grupo
Quando um usuário cria um grupo, ele é automaticamente adicionado como um proprietário desse grupo. Como proprietário, eles podem gerenciar as propriedades do grupo, como o nome, bem como gerenciar a associação ao grupo. Um proprietário também pode adicionar ou remover outros proprietários. Ao contrário dos administradores globais e administradores de usuários, os proprietários só podem gerenciar os grupos que eles possuem. Para atribuir um proprietário do grupo, consulte [Gerenciando proprietários para um grupo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Permissões de propriedade
As tabelas a seguir descrevem as permissões específicas em Azure Active Directory usuários Membros têm objetos de propriedade. O usuário só tem essas permissões em objetos de sua propriedade.

#### <a name="owned-application-registrations"></a>Registros de aplicativo de propriedade
Os usuários podem executar as seguintes ações em registros de aplicativo de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. Directory/Applications/Audience/Update | Atualize a propriedade applications.audience no Azure Active Directory. |
| Microsoft. Directory/aplicativos/autenticação/atualização | Atualize a propriedade applications.authentication no Azure Active Directory. |
| Microsoft. Directory/Applications/Basic/Update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| Microsoft. Directory/aplicativos/credenciais/atualização | Atualize a propriedade applications.credentials no Azure Active Directory. |
| Microsoft. Directory/Applications/Delete | Excluir aplicativos no Active Directory do Azure. |
| Microsoft. Directory/Applications/Owners/Update | Atualize a propriedade applications.owners no Azure Active Directory. |
| Microsoft. Directory/Applications/Permissions/Update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| Microsoft. Directory/Applications/Policies/Update | Atualize a propriedade applications.policies no Azure Active Directory. |
| Microsoft. Directory/Applications/Restore | Restaure aplicativos no Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Aplicativos empresariais de propriedade
Os usuários podem executar as seguintes ações em aplicativos corporativos de propriedade. Um aplicativo empresarial é composto de entidade de serviço, uma ou mais políticas de aplicativo e, às vezes, um objeto de aplicativo no mesmo locatário que a entidade de serviço.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. Directory/auditLogs/myproperties/Read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| Microsoft. Directory/Policies/Basic/Update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft. Directory/Policies/Delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| Microsoft. Directory/políticas/proprietários/atualização | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/appRoleAssignedTo/Update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/appRoleAssignments/Update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/público/atualização | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/autenticação/atualização | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/Basic/Update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| Microsoft. Directory/servicePrincipalName/Credentials/Update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/Delete | Excluir servicePrincipals em Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/Owners/Update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/Permissions/Update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| Microsoft. Directory/servicePrincipalName/Policies/Update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| Microsoft. Directory/signInReports/myproperties/Read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivos de propriedade
Os usuários podem executar as seguintes ações em dispositivos de propriedade.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. Directory/Devices/bitLockerRecoveryKeys/Read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| Microsoft. Directory/Devices/Disable | Desabilite dispositivos no Azure Active Directory. |

#### <a name="owned-groups"></a>Grupos de propriedade
Os usuários podem executar as seguintes ações em grupos próprios.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. Directory/groups/appRoleAssignments/Update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft. Directory/groups/Basic/Update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft. Directory/groups/Delete | Exclua grupos no Azure Active Directory. |
| Microsoft. Directory/groups/dynamicMembershipRule/Update | Atualize a propriedade dynamicmembershiprule no Azure Active Directory. |
| Microsoft. Directory/groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft. Directory/groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft. Directory/groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft. Directory/groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir funções de administrador do Microsoft Azure Active Directory consulte [Atribuir um usuário a funções de administrador no Microsoft Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerenciar usuários](add-users-azure-active-directory.md)
