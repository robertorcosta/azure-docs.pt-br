---
title: 'Início Rápido: Configurar o SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory)'
description: Este guia de início rápido orienta o processo de configuração do SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory).
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
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038983"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Início Rápido: Configurar o SSO (logon único) para um aplicativo no locatário do Azure AD (Azure Active Directory)

Comece a usar logons de usuário simplificados configurando o SSO para um aplicativo que você adicionou ao seu locatário do Azure AD. Depois de configurar o SSO, os usuários poderão entrar em um aplicativo usando suas credenciais do Azure AD. O SSO está incluído na edição gratuita do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o SSO para um aplicativo que você adicionou ao seu locatário do Azure AD, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uma das seguintes funções: administrador global, administrador de aplicativos de nuvem, administrador de aplicativos ou proprietário da entidade de serviço.
- Um aplicativo compatível com logon único e que já foi pré-configurado e adicionado à galeria do Azure AD. A maioria dos aplicativos pode usar o Azure AD para logon único. Os aplicativos na galeria do Azure AD foram pré-configurados. Se o aplicativo não estiver listado ou for um aplicativo desenvolvido personalizado, você ainda poderá usá-lo com o Azure AD. Confira os tutoriais e outras documentações no sumário. Este início rápido se concentra em aplicativos que foram pré-configurados para SSO e adicionados à galeria do Azure AD pelos desenvolvedores de aplicativos.
- (Opcional: Conclusão de [Exibir seus aplicativos](view-applications-portal.md)).
- (Opcional: Conclusão de [Adicionar um aplicativo](add-application-portal.md)).
- (Opcional: Conclusão de [Configurar um aplicativo](add-application-portal-configure.md)).


>[!IMPORTANT]
>Recomendamos usar um ambiente que não seja de produção para testar as etapas neste início rápido.


## <a name="enable-single-sign-on-for-an-app"></a>Habilitar logon único para um aplicativo

Quando você terminar de adicionar um aplicativo ao seu locatário do Azure AD, verá imediatamente a página de visão geral para ele. Se você estiver configurando um aplicativo que já foi adicionado, examine o primeiro guia de início rápido, que o orientará na exibição dos aplicativos adicionados ao seu locatário. 

Para configurar o logon único para um aplicativo:

1. No portal do Azure AD, selecione **Aplicativos empresariais** e localize e escolha o aplicativo que você deseja configurar para logon único.
2. Na seção Gerenciar, selecione **Logon único** para abrir o painel de propriedades para edição.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Mostra a página de configuração de logon único no portal do Azure AD.":::
3. Selecione SAML para abrir a página de configuração de SSO. Neste exemplo, o aplicativo que estamos configurando para SSO é o GitHub. Após a configuração do GitHub, os usuários poderão entrar no GitHub usando as credenciais deles do seu locatário do Azure AD.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Mostra a página de configuração de logon único no GitHub.":::
4. O processo para configurar um aplicativo para usar o Azure AD para SSO baseado em SAML varia conforme o aplicativo. Observe que há um link para as diretrizes do GitHub. Você pode encontrar guias para outros aplicativos em: https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Siga o guia para configurar o SSO para o aplicativo. Muitos aplicativos têm requisitos de assinatura específicos para a funcionalidade de SSO. Por exemplo, o GitHub requer uma assinatura Enterprise.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Mostra a opção logon único na assinatura Enterprise da página de preços do GitHub.":::


## <a name="next-steps"></a>Próximas etapas

- [Excluir um aplicativo](delete-application-portal.md)
