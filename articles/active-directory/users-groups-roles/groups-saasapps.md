---
title: Use um grupo para gerenciar o acesso aos aplicativos SaaS - Azure AD | Microsoft Docs
description: Como usar grupos no Azure Active Directory para atribuir acesso a aplicativos SaaS que estão integrados ao Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026848"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Usar um grupo para gerenciar o acesso a aplicativos SaaS

Usando o Azure Active Directory (Azure AD) com um plano de licença Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS integrado ao Azure AD. Por exemplo, se você quiser atribuir acesso ao departamento de marketing usar cinco aplicativos SaaS diferentes, você pode criar um grupo que contém os usuários no departamento de marketing e atribuir esse grupo a esses cinco aplicativos SaaS que são necessários para o departamento de marketing. Dessa forma, você pode poupar tempo gerenciando a associação no departamento de marketing em um único lugar. Os usuários são atribuídos ao aplicativo quando eles são adicionados como membros do grupo de marketing, e sua atribuição é removida do aplicativo quando eles são removidos do grupo de marketing. Esse recurso pode ser usado com centenas de aplicativos que você adiciona na Galeria de aplicativos do Azure AD.

> [!IMPORTANT]
> Você só pode usar esse recurso depois de iniciar uma avaliação azure AD Premium ou comprar o plano de licença Azure AD Premium.
> A atribuição baseada em grupo é suportada apenas para grupos de segurança.
> No momento, as associações de grupo aninhadas não têm suporte para atribuição com base em grupo de aplicativos.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Para atribuir acesso de um usuário ou um grupo a um aplicativo SaaS

1. No [Centro de administração do Azure AD](https://aad.portal.azure.com), selecione **Aplicativos empresariais**.
2. Selecione um aplicativo que você adicionou da Galeria de Aplicativos para abri-lo.
3. Selecione **Usuários e grupos** e, em seguida, selecione **Adicionar usuário**.
4. Em **Adicionar atribuição**, selecione **Usuários e grupos** para abrir a lista de seleção **usuários e grupos**.
6. Selecione quantos grupos ou usuários desejar, em seguida, clique ou toque em **Selecione** para adicioná-los à lista de **Adicionar atribuição**. Você também pode atribuir uma função a um usuário neste estágio.
7. Selecione **Atribuir** para atribuir usuários ou grupos ao aplicativo empresarial selecionado.

## <a name="next-steps"></a>Próximas etapas
Esses artigos fornecem mais informações sobre o Active Directory do Azure.

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md)
* [Gerenciamento de aplicativos no Microsoft Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Cmdlets do Azure Active Directory para definir configurações de grupo](groups-settings-cmdlets.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrando suas identidades locais ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
