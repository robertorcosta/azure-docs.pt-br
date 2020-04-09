---
title: Gerenciar o acesso de recursos de & de aplicativos usando grupos - Azure AD
description: Saiba mais sobre como gerenciar o acesso aos aplicativos baseados em nuvem da sua organização, aplicativos locais e recursos usando grupos do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89c4fa536994ecc8cecb50c907d43df2110be7df
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982592"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Gerenciar o acesso de aplicativo e recursos usando grupos do Microsoft Azure Active Directory
O Azure Active Directory (Azure AD) permite que você use grupos para gerenciar o acesso aos seus aplicativos baseados na nuvem, aplicativos locais e seus recursos. Seus recursos podem fazer parte da organização Ad do Azure, como permissões para gerenciar objetos através de funções no Azure AD, ou externos à organização, como para aplicativos de Software as a Service (SaaS), serviços Azure, sites SharePoint e recursos locais.

>[!NOTE]
> No portal Azure, você pode ver alguns grupos cujos membros e detalhes de grupo você não pode gerenciar no portal:
>
> - Grupos sincronizados a partir de diretórios ativos no local podem ser gerenciados apenas no Active Directory no local.
> - Outros tipos de grupo, como listas de distribuição e grupos de segurança habilitados por e-mail, são gerenciados apenas no centro de administrados exchange ou no centro de admin microsoft 365. Você deve fazer login no Exchange admin center ou no Microsoft 365 admin center para gerenciar esses grupos.

## <a name="how-access-management-in-azure-ad-works"></a>Como funciona o gerenciamento de acesso no Ad do Azure

O Azure AD ajuda a dar acesso aos recursos da sua organização fornecendo direitos de acesso a um único usuário ou a um grupo do Azure AD inteiro. Usar grupos permite que o recurso proprietário (ou proprietário de diretório do Azure AD), atribua um conjunto de permissões de acesso a todos os membros do grupo, em vez de precisar fornecer os direitos um por um. O proprietário do recurso ou diretório também pode conceder direitos de gerenciamento da lista de membros a outra pessoa, como um gerente de departamento ou um administrador de assistência técnica, permitindo à pessoa adicionar e remover membros, conforme necessário. Para obter mais informações sobre como gerenciar proprietários do grupo, veja [Gerenciar proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)

![Diagrama de gerenciamento de acesso do Active Directory do Azure](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Maneiras de atribuir direitos de acesso

Há quatro maneiras de atribuir direitos de acesso a recursos aos seus usuários:

- **Atribuição direta.** O proprietário do recurso atribui diretamente o usuário ao recurso.

- **Atribuição de grupo.** O proprietário do recurso atribui um grupo do Azure AD ao recurso, que fornece automaticamente a todos os membros do grupo acesso ao recurso. A associação de grupo é gerenciada pelo proprietário do grupo e pelo proprietário do recurso, permitindo que o proprietário adicione ou remova membros do grupo. Para obter mais informações sobre como adicionar ou remover a associação de grupo, veja [Como adicionar ou remover um grupo de outro grupo usando o portal do Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Atribuição baseada em regra.** O proprietário do recurso cria um grupo e usa uma regra para definir quais usuários são atribuídos a um recurso específico. A regra é baseada em atributos designados a usuários individuais. O proprietário do recurso gerencia a regra, determinando quais atributos e valores são necessários para permitir o acesso ao recurso. Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](../users-groups-roles/groups-create-rule.md).

    Você também pode assistir a este breve vídeo para obter uma explicação rápida sobre como criar e usar grupos dinâmicos:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Atribuição de autoridade externa.** Acesso proveniente de uma fonte externa, como um diretório local ou um aplicativo SaaS. Nessa situação, o proprietário do recurso atribui um grupo para fornecer acesso ao recurso e, em seguida, a fonte externa gerencia os membros do grupo.

   ![Visão geral do diagrama de gerenciamento de acesso](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Os usuários podem ingressar em grupos sem serem atribuídos?
O proprietário do grupo pode permitir que os usuários localizem seus próprios grupos para ingressar, em vez de atribuí-los. O proprietário também pode configurar o grupo para aceitar automaticamente todos os usuários que entram ou exigir aprovação.

Depois que um usuário solicitar o ingresso em um grupo, a solicitação será encaminhada para o proprietário do grupo. Se ela for necessária, o proprietário poderá aprovar a solicitação e o usuário será notificado sobre a associação de grupo. No entanto, se você tiver vários proprietários e um deles não aprovar, o usuário será notificado, mas não será adicionado ao grupo. Para obter mais informações e instruções sobre como permitir que os usuários solicitem o ingresso em grupos, veja [Configurar o Azure AD para que os usuários possam solicitar o ingresso em grupos](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Próximas etapas
Agora que você tem algum conhecimento de gerenciamento de acesso usando grupos, começará a gerenciar seus aplicativos e recursos.

- [Criar um novo grupo usando o Azure Active Directory](active-directory-groups-create-azure-portal.md) ou [Criar e gerenciar um novo grupo usando cmdlets do PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Usar grupos para atribuir acesso a um aplicativo de SaaS integrado](../users-groups-roles/groups-saasapps.md)

- [Sincronizar um grupo local ao Azure usando o Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
