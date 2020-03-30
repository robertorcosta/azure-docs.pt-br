---
title: Liste seu aplicativo na galeria de aplicativos do Azure AD | Microsoft Docs
description: Saiba como listar um aplicativo que oferece suporte a logon único na galeria de aplicativos do Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154960"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar seu aplicativo na galeria de aplicativos do Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Este artigo mostra como listar um aplicativo na galeria de aplicativos Do Azure Active Directory (Azure AD), implementar o SSO (Single Sign-on) e gerenciar a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam o Sistema de Gerenciamento de Identidade Cross-domain ([SCIM)](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML/WS-Fed), o aplicativo deve suportar o modelo de software como serviço (SaaS) para ser listado na galeria de aplicativos Azure AD. Os aplicativos da galeria corporativa devem suportar várias configurações do cliente e não qualquer cliente específico.
- Para o Open ID Connect, o aplicativo deve ser multilocatário e a [estrutura de consentimento Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) deve ser implementada adequadamente para o aplicativo. O usuário pode enviar a solicitação de login para um ponto final comum para que qualquer cliente possa fornecer consentimento ao aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.
- Para saml 2.0/WS-Fed, seu aplicativo deve ter a capacidade de fazer a integração SAML/WS-Fed SSO no modo SP ou IDP. Certifique-se de que esse recurso está funcionando corretamente antes de enviar a solicitação.
- Para senha SSO, certifique-se de que seu aplicativo suporte a autenticação do formulário para que o cofre de senhas possa ser feito para que o login único funcione como esperado.
- Você precisa de uma conta permanente para testes com pelo menos dois usuários registrados.

**Como obter a Azure AD para desenvolvedores?**

Você pode obter uma conta de teste gratuita com todos os recursos premium do Azure AD - 90 dias grátis e pode ser estendido desde que você trabalhe com ele:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Depois de testar que a integração do aplicativo funciona com o Azure AD, envie sua solicitação de acesso no [portal da Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso assim, use sua conta microsoft, como Outlook ou Hotmail, para fazer login.

Se a página seguinte aparecer após o login, entre em contato com a [equipe de integração Do Ad AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de e-mail que você deseja usar para enviar a solicitação. A equipe do Azure AD adicionará a conta no portal Microsoft Application Network.

![Mensagem de solicitação de acesso no portal SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois que a conta for adicionada, você pode entrar no portal da Microsoft Application Network.

Se a página seguinte aparecer após o login, forneça uma justificativa comercial para a necessidade de acesso na caixa de texto. Em seguida, selecione **'Solicitar acesso '**.

  ![Caixa de justificativa de negócios no portal SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nossa equipe analisa os detalhes e fornece-lhe o acesso adequadamente. Depois que sua solicitação for aprovada, você pode entrar no portal e enviar a solicitação selecionando o bloco **DE Solicitação de Envio (ISV)** na página inicial.

![Enviar solicitação (ISV) na página inicial](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problemas sobre login no portal

Se você está vendo esse erro ao fazer login, então aqui estão os detalhes sobre o problema e é assim que você pode corrigi-lo.

* Se o seu login foi bloqueado como mostrado abaixo:

  ![problemas de resolução de aplicação na galeria](./media/howto-app-gallery-listing/blocked.png)

**O que está acontecendo:**

O usuário convidado é federado para um inquilino doméstico que também é um Azure AD. O usuário convidado está em alto risco. A Microsoft não permite que usuários de alto risco acessem seus recursos. Todos os usuários de alto risco (funcionários ou convidados / fornecedores) devem remediar / encerrar seu risco de acessar os recursos da Microsoft. Para usuários convidados, esse risco de usuário vem do inquilino doméstico e a política vem do inquilino de recursos (Microsoft neste caso).
 
**Soluções seguras:**

* Os usuários convidados registrados da MFA remediam seu próprio risco de usuário. Isso pode ser feito pelo usuário convidado realizando umahttps://aka.ms/sspr) mudança de senha segura ou reset ( em seu inquilino doméstico (isso precisa de MFA e SSPR no inquilino da casa). A alteração ou redefinição de senha segura deve ser iniciada no Azure AD e não no prem.

* Os usuários convidados têm seus admins para remediar seu risco. Neste caso, o admin realizará uma redefinição de senha (geração temporária de senha). Isso não precisa de Proteção de Identidade. O admin do usuário convidado https://aka.ms/RiskyUsers pode ir e clicar em 'Redefinir senha'.

* Os usuários convidados têm seus admins próximos / descartam seu risco. Mais uma vez, isso não precisa de Proteção de Identidade. O admin pode https://aka.ms/RiskyUsers ir e clicar em 'Descartar risco de usuário'. No entanto, o admin deve fazer a due diligence para garantir que esta era uma avaliação de risco falso positivo antes de fechar o risco do usuário. Caso contrário, eles estão colocando seus recursos e da Microsoft em risco, suprimindo uma avaliação de risco sem investigação.

> [!NOTE]
> Se você tiver algum problema com acesso, entre em contato com a [equipe de integração Do Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementar O SSO usando o protocolo da federação

Para listar um aplicativo na galeria de aplicativos do Azure Active Directory, primeiro é necessário implementar um dos seguintes protocolos de federação com suporte pelo Azure Active Directory. Você também precisa concordar com os termos e condições da galeria de aplicativos Azure AD. Leia os termos e condições da galeria de aplicativos Azure AD [neste site](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Para integrar seu aplicativo com o Azure AD usando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](v1-authentication-scenarios.md).

    ![Listando um aplicativo OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você quiser adicionar seu aplicativo à lista na galeria usando o OpenID Connect, selecione **OpenID Connect & OAuth 2.0** como mostrado.
    * Se você tiver algum problema com acesso, entre em contato com a [equipe de integração Do Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **SAML 2.0** ou **WS-Fed**: Se o aplicativo suportar o SAML 2.0, você pode integrá-lo diretamente com um inquilino AD do Azure seguindo as [instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

  ![Listando um aplicativo SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

  * Se você quiser adicionar seu aplicativo à lista na galeria usando **SAML 2.0** ou **WS-Fed,** selecione **SAML 2.0/WS-Fed** como mostrado.

  * Se você tiver algum problema com acesso, entre em contato com a [equipe de integração Do Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementar SSO usando a senha SSO

Crie um aplicativo da Web que tenha uma página de login HTML para configurar a [conexão única baseada em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como nas contas de aplicativos de mídia social da sua organização.

![Listando um aplicativo SSO de senha na galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você quiser adicionar seu aplicativo para listar na galeria usando senha SSO, selecione **SSO de senha** como mostrado.
* Se você tiver algum problema com acesso, entre em contato com a [equipe de integração Do Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Solicitação de provisionamento de usuários

Siga o processo mostrado na imagem a seguir para solicitar o provisionamento do usuário.

   ![Solicitação de provisionamento de usuários](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Para atualizar ou remover um aplicativo existente na galeria de aplicativos Azure AD, primeiro você precisa enviar a solicitação no [portal da Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso assim, use sua conta microsoft, como Outlook ou Hotmail, para fazer login.

- Selecione a opção apropriada conforme mostrado na imagem a seguir.

    ![Listando um aplicativo SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Para atualizar um aplicativo existente, selecione a opção apropriada conforme sua exigência.
    * Para remover um aplicativo existente da galeria de aplicativos Azure AD, **selecione Remover minha lista de aplicativos da galeria**.
    * Se você tiver algum problema com acesso, entre em contato com a [equipe de integração Do Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Pedidos de lista por clientes

Os clientes podem enviar uma solicitação para listar um aplicativo selecionando **solicitações de aplicativos por clientes** > **Enviar nova solicitação**.

![Mostra o azulejo de aplicativos solicitado pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Aqui está o fluxo de aplicativos solicitados pelo cliente.

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Linhas do tempo

O cronograma para o processo de listagem de um aplicativo SAML 2.0 ou WS-Fed na galeria é de 7 a 10 dias úteis.

  ![Cronograma para listar um aplicativo SAML na galeria](./media/howto-app-gallery-listing/timeline.png)

O cronograma para o processo de listagem de um aplicativo OpenID Connect na galeria é de 2 a 5 dias úteis.

  ![Cronograma para listar um aplicativo OpenID Connect na galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para quaisquer escalações, envie um e-mail para a SaaSApplicationIntegrations@service.microsoft.comequipe de [integração do Azure AD SSO](mailto:SaaSApplicationIntegrations@service.microsoft.com) em , e responderemos o mais rápido possível.
