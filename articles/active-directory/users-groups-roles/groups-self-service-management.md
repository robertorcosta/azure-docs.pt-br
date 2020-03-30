---
title: Configurar o gerenciamento de grupos de autoatendimento – Azure Active Directory | Microsoft Docs
description: Criar e gerenciar grupos de segurança ou grupos do Office 365 no Azure Active Directory e o grupo de segurança de solicitação ou associações de grupo do Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e52c37e293941a767621cf56ef75f8cc83b1925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297996"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configure o gerenciamento de grupo de autoatendimento no Azure Active Directory 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos do Office 365 no Azure Active Directory (Azure AD). O proprietário do grupo pode aprovar ou negar pedidos de associação, e pode delegar o controle da associação do grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança habilitados por e-mail ou listas de distribuição.

## <a name="self-service-group-membership-defaults"></a>Inadimplência de membros do grupo de autoatendimento

Quando grupos de segurança são criados no portal Azure ou usando o Azure AD PowerShell, apenas os proprietários do grupo podem atualizar a adesão. Grupos de segurança criados por autoatendimento no [painel Access](https://account.activedirectory.windowsazure.com/r#/joinGroups) e todos os grupos do Office 365 estão disponíveis para participar de todos os usuários, sejam aprovados pelo proprietário ou aprovados automaticamente. No painel Acesso, você pode alterar as opções de associação ao criar o grupo.

Grupos criados em | Comportamento padrão do grupo de segurança | Comportamento padrão do grupo Office 365
------------------ | ------------------------------- | ---------------------------------
[PowerShell do Azure AD](groups-settings-cmdlets.md) | Apenas os proprietários podem adicionar membros<br>Visível, mas não disponível para participar do painel Access | Aberto para participar para todos os usuários
[Portal Azure](https://portal.azure.com) | Apenas os proprietários podem adicionar membros<br>Visível, mas não disponível para participar do painel Access<br>O proprietário não é atribuído automaticamente na criação do grupo | Aberto para participar para todos os usuários
[Painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Aberto para participar para todos os usuários<br>As opções de adesão podem ser alteradas quando o grupo é criado | Aberto para participar para todos os usuários<br>As opções de adesão podem ser alteradas quando o grupo é criado

## <a name="self-service-group-management-scenarios"></a>Cenários de gerenciamento de grupos de autoatendimento

* **Gestão de grupo delegada** Um exemplo é um administrador que está gerenciando o acesso a um aplicativo SaaS que a empresa está usando. O gerenciamento desses direitos de acesso está se tornando inconveniente, portanto esse administrador solicita ao proprietário de negócios para criar um novo grupo. O administrador atribui acesso ao aplicativo ao novo grupo e adiciona ao grupo todas as pessoas que já acessam o aplicativo. O proprietário da empresa pode então adicionar mais usuários, e os usuários são automaticamente provisionados para o aplicativo. O proprietário da empresa não precisa esperar que o administrador gerencie o acesso para usuários. Se o administrador conceder a mesma permissão a um gerente em um grupo de negócios diferente, essa pessoa também poderá gerenciar o acesso para seus próprios membros do grupo. Nem o dono do negócio nem o gerente podem visualizar ou gerenciar os membros do grupo um do outro. O administrador ainda pode ver todos os usuários que têm acesso ao aplicativo e direitos de acesso de bloco, se necessário.
* **Gerenciamento de grupos de autoatendimento** Um exemplo desse cenário são dois usuários que possuem sites SharePoint Online que eles configuram de forma independente. Eles querem dar às equipes um do outro acesso aos seus sites. Para isso, eles podem criar um grupo no Azure AD e, no SharePoint Online, cada um seleciona esse grupo para fornecer acesso a seus sites. Quando alguém deseja acesso, solicita do painel de acesso, e depois da aprovação obtêm acesso a ambos os sites do SharePoint Online automaticamente. Posteriormente, um deles decide que todas as pessoas que acessam o site também devem obter acesso a determinado aplicativo SaaS. O administrador do aplicativo SaaS pode adicionar direitos de acesso ao aplicativo para o site do SharePoint Online. Depois, as solicitações que forem aprovadas darão acesso aos dois sites do SharePoint Online e também ao aplicativo SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilização de um grupo para autoatendimento do usuário

1. Faça login no centro de administração do [Azure AD](https://aad.portal.azure.com) com uma conta que é um administrador global para o diretório.
1. Selecione **Grupos**e selecione **Configurações Gerais.**
1. **Os proprietários de conjuntos podem gerenciar solicitações de membros de grupo no Painel de acesso** a **Sim**.
1. Definir **restringir o acesso a grupos no painel de acesso** a **não**.
1. Se você definir **Os usuários podem criar grupos de segurança em portais do Azure** ou os usuários podem criar grupos do Office **365 em portais do Azure** para

    - **Sim**: Todos os usuários da sua organização Azure AD podem criar novos grupos de segurança e adicionar membros a esses grupos. Esses novos grupos também aparecem no Painel de Acesso para todos os outros usuários. Se a configuração de diretiva no grupo permitir, outros usuários podem criar solicitações para se juntar a esses grupos
    - **Não:** Os usuários não podem criar grupos e não podem alterar grupos existentes para os quais são proprietários. No entanto, ainda podem gerenciar as associações desses grupos e aprovar solicitações de outros usuários para ingressar em seus grupos.

Você também pode usar **proprietários que podem atribuir membros como proprietários de grupo em portais do Azure** e **Proprietários que podem atribuir membros como proprietários de grupo em portais Azure** para obter mais controle de acesso granular sobre o gerenciamento de grupos de autoatendimento para seus usuários.

Quando os usuários podem criar grupos, todos os usuários da sua organização podem criar novos grupos e, em seguida, podem, como proprietário padrão, adicionar membros a esses grupos. Você não pode especificar indivíduos que podem criar seus próprios grupos. Você pode especificar indivíduos apenas para fazer de outro membro do grupo um proprietário de grupo.

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciar o acesso a recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](groups-settings-cmdlets.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar suas identidades locais com o Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
