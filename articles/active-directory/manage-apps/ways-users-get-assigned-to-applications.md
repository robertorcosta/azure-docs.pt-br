---
title: Entenda como os usuários são atribuídos a aplicativos no Azure Active Directory
description: Entenda como os usuários são atribuídos a um aplicativo que está usando Azure Active Directory para gerenciamento de identidade.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604148"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Entenda como os usuários são atribuídos a aplicativos no Azure Active Directory
Este artigo o ajudará a compreender como usuários são atribuídos a um aplicativo em seu locatário.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Como os usuários são atribuídos a um aplicativo no Azure AD?
Para acessar um aplicativo, o usuário precisa primeiro ser atribuído a ele de alguma forma. A atribuição pode ser feita por um administrador, um representante de negócios ou, às vezes, o próprio usuário. Abaixo, descrevemos as formas como os usuários podem ser atribuídos a aplicativos:

*  Um administrador [atribui um usuário](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) diretamente ao aplicativo
*  Um administrador [atribui um grupo](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) do qual o usuário é membro ao aplicativo, incluindo:
    * Um grupo que foi sincronizado do local
    * Um grupo de segurança estático criado na nuvem
    * Um [grupo de segurança dinâmico](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) criado na nuvem
    * Um grupo de Microsoft 365 criado na nuvem
    * O grupo [Todos os Usuários](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Um administrador habilita o [acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um usuário adicione um aplicativo usando [meus aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar** recurso de aplicativo **sem aprovação de negócios**
*  Um administrador habilita o [acesso de aplicativo de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para permitir que um usuário adicione um aplicativo usando [meus aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **Adicionar** recurso de aplicativo, mas somente **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**
*  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído **sem aprovação de negócios**
*  Um administrador habilita o [Gerenciamento de Grupo de Autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) para permitir que um usuário ingresse em um grupo a que um aplicativo está atribuído, mas apenas **com aprovação anterior de um conjunto selecionado de aprovadores de negócios**
*  Um administrador atribui uma licença a um usuário diretamente para um aplicativo de primeira parte, como [Microsoft 365](https://products.office.com/)
*  Um administrador atribui uma licença a um grupo do qual o usuário é membro para um aplicativo de primeira parte, como [Microsoft 365](https://products.office.com/)
*  Um [administrador dá consentimento para um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) ser usado por todos os usuários e, em seguida, um usuário entra no aplicativo
* O próprio usuário [dá consentimento para um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) entrando no aplicativo

## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [O que é gerenciamento de aplicativos?](what-is-application-management.md)
* [O que é logon único?](what-is-single-sign-on.md)
