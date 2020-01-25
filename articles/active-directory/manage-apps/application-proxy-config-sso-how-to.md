---
title: Como configurar o logon único para um aplicativo de proxy de aplicativo
description: Como você pode configurar o logon único para um aplicativo de Application Proxy rapidamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712023"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Como configurar o logon único para um aplicativo de Application Proxy

Logon único (SSO) permite que os usuários acessem um aplicativo sem autenticação, várias vezes. Ele permite que a autenticação única que ocorre na nuvem, no Azure Active Directory, e permite que o serviço ou o conector represente o usuário para concluir os desafios de autenticação adicional do aplicativo.

## <a name="how-to-configure-single-sign-on"></a>Como configurar o logon único
Para configurar SSO, primeiro certifique-se de que seu aplicativo está configurado para pré-autenticação através do Azure Active Directory. Para fazer essa configuração, vá para **Azure Active Directory** -&gt; **aplicativos empresariais** -&gt; **todos os aplicativos** -&gt; seu aplicativo-&gt; o **proxy de aplicativo**. Nessa página, consulte o campo "Pré-autenticação" e certifique-se que é definido como "Azure Active Directory". 

Para obter mais informações sobre os métodos de pré-autenticação, consulte a etapa 4 do [documento de publicação de aplicativo](application-proxy-add-on-premises-application.md).

   ![O método de pré-autenticação no portal do Azure](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configurando modos de logon único para aplicativos do Application Proxy
Configure o tipo específico de logon único. Os métodos de logon são classificados com base no tipo de autenticação do aplicativo que o back-end usa. Aplicativos de Application Proxy oferecem suporte a três tipos de logon:

-   **Logon com base em senha**: o logon com base em senha pode ser usado para qualquer aplicativo que usa campos de nome de usuário e senha para logon. As etapas de configuração estão em [Configurar o logon único com senha para um aplicativo da galeria do Azure AD](configure-password-single-sign-on-non-gallery-applications.md).

-   **Autenticação integrada do Windows**: para aplicativos que usam autenticação integrada do Windows (IWA), o logon único está habilitado por meio de delegação de restrita de Kerberos (KCD). Esse método concede permissão de conectores do Application Proxy no Active Directory para representar os usuários e para enviar e receber tokens em seu nome. Detalhes sobre como configurar o KCD podem ser encontrados no [Logon único com documentação de KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Logon com base no cabeçalho**: logon com base no cabeçalho é habilitado por meio de uma parceria e exige alguma configuração adicional. Para obter detalhes sobre a parceria e instruções passo a passo para configurar o logon único para um aplicativo que usa cabeçalhos para autenticação, consulte o [PingAccess para a documentação do Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **Logon único do SAML**: com o logon único do SAML, o Azure AD é autenticado no aplicativo usando a conta do Azure AD do usuário. O Azure AD comunica as informações do logon para o aplicativo por meio de um protocolo de conexão. Com o logon único baseado em SAML, você pode mapear os usuários para funções de aplicativo específicas com base nas regras definidas nas suas declarações SAML. Para obter informações sobre como configurar o logon único do SAML, consulte [SAML para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).

Cada uma dessas opções pode ser encontrada indo em seu aplicativo em "Aplicativos empresariais" e abrindo a página **Logon Único** no menu à esquerda. Observe que, se o aplicativo tiver sido criado no portal antigo, talvez você não veja todas essas opções.

Nessa página, você também visualiza uma opção de logon adicional: logon vinculado. Essa opção também tem suporte do Application Proxy. No entanto, essa opção não adiciona o logon único para o aplicativo. Isso posto, o aplicativo pode já ter logon único implementado usando outro serviço, como Serviços de Federação do Active Directory. 

Essa opção permite que um administrador crie um link para um aplicativo que os usuários encontram ao acessar o aplicativo. Por exemplo, se houver um aplicativo que está configurado para autenticar usuários usando os Serviços de Federação do Active Directory 2.0, um administrador poderá usar a opção "Logon vinculado" para criar um link para ele no painel de acesso.

## <a name="next-steps"></a>Próximos passos
- [Compartimentação de senhas para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Delegação restrita de Kerberos para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
- [Autenticação baseada em cabeçalho para logon único com o Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML para logon único com o proxy de aplicativo](application-proxy-configure-single-sign-on-on-premises-apps.md).
