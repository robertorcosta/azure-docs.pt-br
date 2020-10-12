---
title: Exibindo aplicativos usando seu locatário Azure Active Directory para gerenciamento de identidades
description: Entenda como exibir todos os aplicativos usando seu locatário de Azure Active Directory para o gerenciamento de identidades.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 5229b123986149903d562bc0b3d6902c0136f647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707876"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Exibindo aplicativos usando seu locatário do Azure AD para o gerenciamento de identidades
A [série de guias de início rápido sobre o gerenciamento de aplicativos](view-applications-portal.md) orienta os conceitos básicos. Nele, você aprende a exibir todos os aplicativos usando seu locatário do Azure AD para o gerenciamento de identidades. Este artigo mergulha um pouco mais sobre os tipos de aplicativos que você encontrará.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Por que um aplicativo específico aparece em minha lista de todos os aplicativos?
Quando filtrada para **Todos os Aplicativos**, a **Lista** **Todos os Aplicativos** mostra todos os objetos de Entidade de Serviço em seu locatário. Objetos de Entidade de Serviço podem aparecer nessa lista de uma várias maneiras:
- Quando você adiciona qualquer aplicativo da galeria de aplicativos, incluindo:
   - **Azure ad-aplicativos corporativos** – aplicativos adicionados ao seu locatário usando a opção **aplicativos empresariais** no portal do Azure AD. Normalmente, aplicativos integrados usando o padrão SAML.
   - **Azure ad-registros de aplicativo** – aplicativos adicionados ao seu locatário usando a opção **registros de aplicativo** no portal do Azure AD. Normalmente, aplicativos desenvolvidos personalizados usando os padrões do Open ID Connect e do OAuth.
   - **Aplicativos do Proxy de Aplicativo** – um aplicativo em execução em seu ambiente local para o qual você deseja fornecer logon único seguro externamente
- Ao se inscrever ou entrar no, um aplicativo de terceiros integrado ao Azure Active Directory. Um exemplo é o [Smartsheet](https://app.smartsheet.com/b/home) ou o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Aplicativos da Microsoft, como o Microsoft 365.
- Quando você adiciona um novo registro de aplicativo criando um aplicativo personalizado usando o [Registro de Aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)
- Quando você adiciona um novo registro de aplicativo criando um aplicativo personalizado usando o [Portal de Registro de Aplicativos V2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)
- Ao adicionar um aplicativo, você está desenvolvendo usando os [métodos de autenticação ASP.net](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) ou os [Serviços conectados](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) do Visual Studio
- Quando você cria um objeto de entidade de serviço usando o [Módulo do PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)
- Quando você [dá consentimento a um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) como administrador para usar dados em seu locatário
- Quando um [usuário dá consentimento para um aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) usar dados no seu locatário
- Quando você habilita determinados serviços que armazenam dados em seu locatário. Um exemplo é a Redefinição de Senha, que é modelada como uma entidade de serviço para armazenar sua senha de redefinição de senha com segurança.

Saiba mais sobre como e por que os aplicativos são adicionados ao diretório, consulte [como os aplicativos são adicionados ao Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="next-steps"></a>Próximas etapas
[Gerenciamento de aplicativos com o Active Directory do Azure](what-is-application-management.md)
