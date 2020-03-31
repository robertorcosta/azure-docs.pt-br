---
title: Depurar o logon único baseado em SAML – Azure Active Directory | Microsoft Docs
description: Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ROBOTS: NOINDEX
ms.openlocfilehash: 5d92b43b47a20a75d2c8b6becb69cfee5829e80f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154841"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Depurar o logon único baseado em SAML para aplicativos no Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Saiba como localizar e corrigir problemas de [logon único](../manage-apps/what-is-single-sign-on.md) em aplicativos no Azure AD (Azure Active Directory) compatíveis com o [SAML (Security Assertion Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Antes de começar

É recomendado instalar a [My Apps Secure Sign-in Extension](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Essa extensão do navegador facilita a coleta das informações de solicitação saml e resposta SAML que você precisa para resolver problemas com o único login. Caso você não possa instalar a extensão, este artigo mostrará como resolver problemas com e sem a extensão instalada.

Para baixar e instalar a My Apps Secure Sign-in Extension, use um dos links a seguir.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testar o logon único baseado em SAML

Para testar o login único baseado em SAML entre o Azure AD e um aplicativo de destino:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global ou outro administrador autorizado a gerenciar aplicativos.
1. Na lâmina esquerda, selecione **O Diretório Ativo do Azure**e selecione aplicativos **Corporativos**. 
1. Na lista de aplicativos corporativos, selecione o aplicativo para o qual deseja testar o login único e, em seguida, a partir das opções à esquerda selecione **Único login**.
1. Para abrir a experiência de teste de inscrição única baseada em SAML, vá para **Testar o único sinal** (passo 5). Se o botão **Teste** estiver acinzentado, você precisa preencher e salvar os atributos necessários primeiro na seção **Configuração Básica do SAML.**
1. Na folha **Testar logon único**, use as credenciais corporativas para entrar no aplicativo de destino. Você pode entrar como o usuário atual ou como um usuário diferente. Se você entrar como um usuário diferente, um prompt solicitará que você se autentique.

    ![Captura de tela mostrando a página SSO do teste SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Se você tiver entrado com êxito, o teste terá sido aprovado. Nesse caso, o Azure AD emitiu um token de resposta SAML para o aplicativo. O aplicativo usou o token SAML para você entrar com êxito.

Se houver um erro na página de entrada da empresa ou na página do aplicativo, use uma das seções a seguir para resolver o erro.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Resolver um erro de entrada na página de entrada da empresa

Quando você tenta fazer login, você pode ver um erro na página de login da sua empresa que é semelhante ao exemplo a seguir.

![Exemplo mostrando um erro na página de login da empresa](./media/howto-v1-debug-saml-sso-issues/error.png)

Para depurar esse erro, você precisa da mensagem de erro e da solicitação SAML. A My Apps Secure Sign-in Extension coleta essas informações automaticamente e exibe as diretrizes de resolução no Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Para resolver o erro de login com a extensão de login seguro do Meus aplicativos instalada

1. Quando ocorre um erro, a extensão redireciona-o de volta para a lâmina **de sinalização única** do Teste Azure AD.
1. Na lâmina **de aprovação única** do teste, selecione Baixar a solicitação **SAML**.
1. Serão exibidas diretrizes de resolução específicas com base no erro e nos valores na solicitação SAML.
1. Você verá um botão **Corrigi-lo** para atualizar automaticamente a configuração no Azure AD para resolver o problema. Se você não vir este botão, então o problema de login não será devido a uma configuração errada no Azure AD.

Se não houver nenhuma resolução para o erro de login, sugerimos que você use a caixa de texto de feedback para nos informar.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Para resolver o erro sem instalar a extensão de login seguro do Meus aplicativos

1. Copie a mensagem de erro no canto inferior direito da página. A mensagem de erro inclui:
    - Um CorrelationID e um carimbo de data/hora. Esses valores são importantes quando você cria um caso de suporte com a Microsoft porque eles ajudam os engenheiros a identificar o problema e a fornecer uma resolução precisa do problema.
    - Uma instrução que identifica a causa raiz do problema.
1. Volte para o Azure AD e encontre a folha **Testar logon único**.
1. Na caixa de texto acima de **Obter diretrizes de resolução**, cole a mensagem de erro.
1. Clique em **Obter diretrizes de resolução** para exibir as etapas para resolver o problema. As diretrizes podem exigir informações da solicitação SAML ou da resposta SAML. Se você não estiver usando a extensão de login seguro meus aplicativos, talvez precise de uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para recuperar a solicitação e resposta do SAML.
1. Verifique se o destino na solicitação SAML corresponde à URL de serviço de login único saml obtida do Azure AD.
1. Verifique se o emissor na solicitação SAML é o mesmo identificador que você configurou para o aplicativo no Azure AD. O Azure AD usa o emissor para localizar um aplicativo no diretório.
1. Verificar afirmaçãoConsumerServiceURL é onde o aplicativo espera receber o token SAML do Azure AD. Você pode configurar esse valor no Azure AD, mas não é obrigatório se faz parte da solicitação SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Resolver um erro de entrada na página do aplicativo

Você pode entrar com êxito e, em seguida, encontrar um erro na página do aplicativo. Isso ocorre quando o Azure AD emite um token para o aplicativo, mas o aplicativo não aceita a resposta.

Para resolver o erro, siga estas etapas:

1. Se o aplicativo estiver na Azure AD Gallery, verifique se você seguiu todas as etapas para integrar o aplicativo com o Azure AD. Para obter as instruções de integração do aplicativo, confira a [lista de tutoriais de integração de aplicativos SaaS](../saas-apps/tutorial-list.md).
1. Recupere a resposta de SAML.
    - Se a My Apps Secure Sign-in Extension estiver instalada, na folha **Testar logon único**, clique em **Baixar resposta SAML**.
    - Se a extensão não estiver instalada, use uma ferramenta como a [Fiddler](https://www.telerik.com/fiddler) para recuperar a resposta SAML.
1. Observe estes elementos no token da resposta SAML:
   - Identificador exclusivo do usuário do valor e do formato de NameID
   - Declarações emitidas no token
   - Certificado usado para assinar o token.

     Para obter mais informações sobre a resposta SAML, confira [Protocolo SAML de Logon Único](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Agora que você analisou a resposta SAML, consulte [Erro na página de um aplicativo após fazer login](../manage-apps/application-sign-in-problem-application-error.md) para obter orientação sobre como resolver o problema. 
1. Se você ainda não conseguir fazer login com sucesso, você pode perguntar ao fornecedor de aplicativos o que está faltando na resposta SAML.

## <a name="next-steps"></a>Próximas etapas

Agora que o logon único está funcionando para o seu aplicativo, você pode automatizar o [provisionamento e o desprovisionamento do usuário para aplicativos SaaS](../manage-apps/user-provisioning.md) ou [começar com o Conditional Access](../conditional-access/app-based-conditional-access.md).
