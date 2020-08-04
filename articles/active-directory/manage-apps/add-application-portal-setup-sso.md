---
title: 'Início Rápido: Configurar o SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory)'
description: Este início rápido orienta pelo processo de configuração do SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f8f19e6b98143bb48430decdd51f5626e72d422
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387278"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Configurar o SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory)

Comece a usar logons de usuário simplificados configurando o SSO (logon único) para um aplicativo que você adicionou ao seu locatário do Azure AD (Azure Active Directory). Depois de configurar o SSO, os usuários poderão entrar em um aplicativo usando as respectivas credenciais do Azure AD. O SSO está incluído na edição gratuita do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o SSO para um aplicativo que você adicionou ao seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Um aplicativo compatível com SSO e que já foi pré-configurado e adicionado à galeria do Azure AD. A maioria dos aplicativos pode usar o Azure AD para SSO. Os aplicativos na galeria do Azure AD foram pré-configurados. Se o aplicativo não estiver listado ou for um aplicativo desenvolvido personalizado, você ainda poderá usá-lo com o Azure AD. Confira os tutoriais e outras documentações no sumário. Este início rápido se concentra em aplicativos que foram pré-configurados para SSO e adicionados à galeria do Azure AD pelos desenvolvedores de aplicativos.
- Opcional: conclusão de [Exibir seus aplicativos](view-applications-portal.md).
- Opcional: conclusão de [Adicionar um aplicativo](add-application-portal.md).
- Opcional: conclusão de [Configurar um aplicativo](add-application-portal-configure.md).


>[!IMPORTANT]
>Use um ambiente de não produção para testar as etapas deste início rápido.


## <a name="enable-single-sign-on-for-an-app"></a>Habilitar logon único para um aplicativo

Depois de concluir a adição de um aplicativo ao locatário do Azure AD, a página de visão geral é exibida. Se você estiver configurando um aplicativo que já tenha sido adicionado, confira o primeiro início rápido. Ele orienta você na exibição dos aplicativos adicionados ao seu locatário. 

Para configurar o logon único para um aplicativo:

1. No portal do Azure AD, selecione **Aplicativos empresariais**. Em seguida, encontre e selecione o aplicativo para o qual você deseja configurar o logon único.
1. Na seção **Gerenciar**, selecione **Logon único** para abrir o painel **Logon único** para edição.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Captura de tela mostrando a página de configuração de logon único no portal do Azure AD.":::

1. Selecione **SAML** para abrir a página de configuração de SSO. Neste exemplo, o aplicativo que estamos configurando para SSO é o GitHub. Após a configuração do GitHub, os usuários poderão entrar no GitHub usando as credenciais deles por meio do seu locatário do Azure AD.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Captura de tela mostrando a página de configuração de logon único no GitHub.":::

1. O processo para configurar um aplicativo para usar o Azure AD para SSO baseado em SAML varia conforme o aplicativo. Há um link para as diretrizes do GitHub. Para encontrar guias para outros aplicativos, confira [Tutoriais para a integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/).
1. Siga o guia para configurar o SSO para o aplicativo. Muitos aplicativos têm requisitos de assinatura específicos para a funcionalidade de SSO. Por exemplo, o GitHub requer uma assinatura Enterprise.
    > [!TIP]
    > Para saber mais sobre as opções de configuração do SAML, confira [Configurar o logon único baseado em SAML](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Captura de tela mostrando a opção logon único na assinatura Enterprise da página de preços do GitHub.":::


## <a name="next-step"></a>Próxima etapa

- [Excluir um aplicativo](delete-application-portal.md)
