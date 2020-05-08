---
title: Registrar um aplicativo SaaS-Azure Marketplace
description: Saiba como usar o portal do Azure para registrar um aplicativo SaaS e receber um token de segurança de Azure Active Directory.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: b3c20d25917d66cba8ae3d811eddaa6455b87722
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792948"
---
# <a name="register-a-saas-application"></a>Registrar um aplicativo SaaS

Este artigo explica como registrar um aplicativo SaaS usando o Microsoft [portal do Azure](https://portal.azure.com/).  Após um registro bem-sucedido, você receberá um token de segurança Azure Active Directory (Azure AD) que pode ser usado para acessar as APIs de preenchimento de SaaS.  Para obter mais informações sobre o Azure AD, consulte [o que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="service-to-service-authentication-flow"></a>Fluxo de autenticação de serviço a serviço

O diagrama a seguir mostra o fluxo de assinatura de um novo cliente e quando essas APIs são usadas:

![Fluxo de API da oferta de SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure não impõe nenhuma restrição quanto à autenticação que o serviço SaaS expõe a seus usuários finais. No entanto, a autenticação com as APIs de preenchimento de SaaS é executada com um token de segurança do Azure AD, normalmente obtido com o registro do aplicativo SaaS por meio do portal do Azure. 

## <a name="register-an-azure-ad-secured-app"></a>Registrar um aplicativo protegido pelo Azure AD

Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao Azure AD detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.  Para registrar um novo aplicativo usando o portal do Azure, realize as seguintes etapas:

1.  Entre no [portal do Azure](https://portal.azure.com/).
2.  Se sua conta fornecer acesso a mais de um, clique em sua conta no canto superior direito e defina a sessão do portal para o locatário do Azure AD desejado.
3.  No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** e clique em **Novo registro de aplicativo**.

    ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na página Criar, insira as informações de registro do aplicativo:
    -   **Nome**: insira um nome significativo de aplicativo
    -   **Tipo de aplicativo**: 
        - Selecione **nativo** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que são instalados localmente em um dispositivo. Essa configuração é usada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos OAuth.
        - Selecione **aplicativo Web/API** para [aplicativos cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [aplicativos de recurso/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que estão instalados em um servidor seguro. Essa configuração é usada para [clientes Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidenciais OAuth e [clientes baseados em agente de usuário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)público.
        O mesmo aplicativo também pode expor um cliente e o recurso/API.
    -   **URL de logon**: para aplicativos Web/API, informe a URL base do aplicativo. Por exemplo, **http://localhost:31544** pode ser a URL para um aplicativo Web em execução no computador local. Os usuários usariam então essa URL para entrar em um aplicativo cliente Web.
    -   **URI de redirecionamento:** para aplicativos nativos, informe o URI usado pelo Azure AD para retornar respostas de token. Insira um valor específico para seu aplicativo, por exemplo **http://MyFirstAADApp**.

        ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration-v1-2.png)

        Para obter exemplos específicos para aplicativos Web ou aplicativos nativos, Confira as configurações guiadas de início rápido que estão disponíveis *na seção Introdução* do [Guia de desenvolvedores do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Ao terminar, clique em **Criar**. O Azure AD atribui uma ID de *aplicativo* exclusiva ao seu aplicativo e você\'voltou para a página de registro\'principal do aplicativo. Dependendo de se o seu aplicativo é Web ou nativo, diferentes opções serão fornecidas para adicionar mais recursos ao aplicativo.

>[!Note]
>Por padrão, o aplicativo recém-registrado é configurado para permitir que somente usuários do mesmo locatário entrem no seu aplicativo.

## <a name="using-the-azure-ad-security-token"></a>Usando o token de segurança do Azure AD

Depois de registrar seu aplicativo, você pode solicitar um token de segurança do Azure AD programaticamente.  O editor deve usar esse token e fazer uma solicitação para resolvê-lo.  Ao usar as várias APIs de preenchimento, o parâmetro de consulta de token está na URL quando o usuário é redirecionado para o site SaaS do Azure.  Esse token só é válido por uma hora.  Além disso, você deve decodificar por URL o valor do token do navegador antes de usá-lo.

Para obter mais informações sobre esses tokens, consulte [Azure Active Directory tokens de acesso](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base no Aplicativo Azure AD

Método HTTP

`POST`

*URL de Solicitação*

**https://login.microsoftonline.com/*{tenantid}*/oauth2/token**

*Parâmetro do URI*

|  **Nome do parâmetro**  | **Necessária**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID do locatário do aplicativo AAD registrado   |
|  |  |  |


*Cabeçalho da solicitação*

|  **Nome do cabeçalho**  | **Necessária** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Tipo de conteúdo     | True         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Corpo da solicitação*

| **Nome da propriedade**   | **Necessária** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Tipo de concessão. O valor padrão é `client_credentials`.                    |
|  Client_id          | True         |  Identificador do cliente/aplicativo associado ao Aplicativo Azure AD.                  |
|  client_secret      | True         |  Senha associada ao Aplicativo Azure AD.                               |
|  Recurso           | True         |  Recurso de destino para o qual o token é solicitado. O valor padrão é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Responde*

|  **Nome**  | **Tipo**       |  **Descrição**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | Solicitação bem-sucedida   |
|  |  |  |

*TokenResponse*

Token de resposta de exemplo:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

## <a name="next-steps"></a>Próximas etapas

Seu aplicativo protegido pelo Azure AD agora pode usar a [API de preenchimento de SaaS versão 2](./pc-saas-fulfillment-api-v2.md).
