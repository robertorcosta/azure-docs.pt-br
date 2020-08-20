---
title: Configurar o logon vinculado no Azure Active Directory
description: Configurar o logon vinculado no Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: bad4fe7f0cf090e5d61506e775fccf677df45fa5
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641969"
---
# <a name="configure-linked-sign-on"></a>Configurar o logon vinculado

Na [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos, você aprendeu a usar o Azure ad como o IDP (provedor de identidade) para um aplicativo. No guia de início rápido, você configura o SSO baseado em SAML. Outra opção está **vinculada**. Este artigo apresenta mais detalhes sobre a opção vinculada.

A opção **vinculada** permite configurar o local de destino quando um usuário seleciona o aplicativo no portal [meus aplicativos](https://myapps.microsoft.com/) ou do Office 365 da sua organização.

Alguns cenários comuns em que a opção link é valiosa incluem:
- Adicione um link a um aplicativo Web personalizado que usa atualmente a Federação, como Serviços de Federação do Active Directory (AD FS) (AD FS).
- Adicione links profundos a páginas específicas do SharePoint ou a outras páginas da Web que você só deseja que apareçam nos painéis de acesso do usuário.
- Adicione um link a um aplicativo que não exija autenticação. 
 
 A opção **vinculada** não fornece funcionalidade de logon por meio de credenciais do Azure AD. Mas, você ainda pode usar alguns dos outros recursos dos **aplicativos empresariais**. Por exemplo, você pode usar os logs de auditoria e adicionar um logotipo personalizado e o nome do aplicativo.

## <a name="before-you-begin"></a>Antes de começar

Para aumentar rapidamente, percorra a [série de guias de início rápido](view-applications-portal.md) sobre o gerenciamento de aplicativos. No guia de início rápido, onde você configura o logon único, você também encontrará a opção **vinculada** . 

A opção **vinculada** não fornece funcionalidade de logon por meio do Azure AD. A opção simplesmente define o local ao qual os usuários serão enviados quando selecionam o aplicativo em [meus aplicativos](https://myapps.microsoft.com/) ou o iniciador de aplicativo Microsoft 365.

> [!IMPORTANT] 
> Há alguns cenários em que a opção de **logon único** não estará na navegação de um aplicativo em **aplicativos empresariais**. 
>
> Se o aplicativo tiver sido registrado usando **registros de aplicativo** , o recurso de logon único será configurado para usar o OIDC OAuth por padrão. Nesse caso, a opção de **logon único** não aparecerá na navegação em **aplicativos empresariais**. Ao usar **registros de aplicativo** para adicionar seu aplicativo personalizado, você configura opções no arquivo de manifesto. Para saber mais sobre o arquivo de manifesto, consulte [Azure Active Directory manifesto do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Para saber mais sobre os padrões de SSO, consulte [autenticação e autorização usando a plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Outros cenários em que o **logon único** ficará ausente da navegação incluem quando um aplicativo é hospedado em outro locatário ou se sua conta não tem as permissões necessárias (administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço). As permissões também podem causar um cenário em que você pode abrir o **logon único** , mas não poderá salvar. Para saber mais sobre as funções administrativas do Azure AD, consulte ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Configurar link

Para definir um link para um aplicativo, selecione **vinculado** na página **logon único** . Em seguida, insira o link e selecione **salvar**. Precisa de um lembrete sobre onde encontrar essas opções? Confira a [série de início rápido](view-applications-portal.md).
 
Depois de configurar um aplicativo, atribua usuários e grupos a ele. Ao atribuir usuários, você pode controlar quando o aplicativo aparece em [meus aplicativos](https://myapps.microsoft.com/) ou no iniciador de aplicativo Microsoft 365.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir usuários e grupos ao aplicativo](methods-for-assigning-users-and-groups.md)
- [Configurar o provisionamento automático de conta de usuário](../app-provisioning/configure-automatic-user-provisioning-portal.md)
