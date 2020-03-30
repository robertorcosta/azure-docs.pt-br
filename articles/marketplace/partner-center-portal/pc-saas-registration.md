---
title: Registre um aplicativo SaaS | Mercado Azure
description: Explica como registrar um aplicativo SaaS usando o portal Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 9c20fe34e108de95a34aabea56390e8a6f0d858f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275706"
---
# <a name="register-a-saas-application"></a>Registrar um aplicativo SaaS

Este artigo explica como registrar um aplicativo SaaS usando o portal Microsoft [Azure](https://portal.azure.com/).  Após um registro bem-sucedido, você receberá um token de segurança Azure Active Directory (Azure AD) que você pode usar para acessar as APIs de cumprimento do SaaS.  Para obter mais informações sobre o Azure AD, consulte [O que é autenticação?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Fluxo de autenticação serviço-serviço

O diagrama a seguir mostra o fluxo de assinatura de um novo cliente e quando essas APIs são usadas:

![Fluxo de API da oferta de SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure não impõe nenhuma restrição quanto à autenticação que o serviço SaaS expõe a seus usuários finais. No entanto, a autenticação com as APIs de cumprimento do SaaS é realizada com um token de segurança Azure AD, normalmente obtido pelo registro do aplicativo SaaS através do portal Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registre um aplicativo protegido pelo Azure

Para que você possa usar os recursos do AD do Azure em qualquer aplicativo desejado, primeiramente é preciso registrá-lo em um locatário do AD do Azure. Esse processo de registro envolve fornecer ao Azure AD detalhes sobre seu aplicativo, como a URL em que ele está localizado, a URL à qual enviar respostas depois que um usuário é autenticado, o URI que identifica o aplicativo, etc.  Para registrar um novo aplicativo usando o portal do Azure, realize as seguintes etapas:

1.  Faça login no [portal Azure](https://portal.azure.com/).
2.  Se sua conta lhe der acesso a mais de uma, clique em sua conta no canto superior direito e defina sua sessão de portal para o inquilino Azure AD desejado.
3.  No painel de navegação esquerdo, clique no serviço **Azure Active Directory**, clique em **Registros do aplicativo** e clique em **Novo registro de aplicativo**.

    ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration-v1.png)

4.  Na página Criar, insira as informações de registro do aplicativo:
    -   **Nome**: insira um nome significativo de aplicativo
    -   **Tipo de aplicação:** 
        - Selecione **Nativo** para [aplicativos clientes instalados](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) localmente em um dispositivo. Essa configuração é usada para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos OAuth.
        - Selecione **o aplicativo Da Web / API** para aplicativos [clientes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) e [aplicativos de recursos/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que estão instalados em um servidor seguro. Esta configuração é usada para [clientes web confidenciais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) OAuth e [clientes públicos baseados em agentes de usuário](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client).
        O mesmo aplicativo também pode expor um cliente e o recurso/API.
    -   **URL de logon**: para aplicativos Web/API, informe a URL base do aplicativo. Por exemplo, **http://localhost:31544** pode ser a URL de um aplicativo web em execução em sua máquina local. Os usuários usariam então essa URL para entrar em um aplicativo cliente Web.
    -   **URI de redirecionamento:** para aplicativos nativos, informe o URI usado pelo Azure AD para retornar respostas de token. Digite um valor específico para **http://MyFirstAADApp**sua aplicação, por exemplo.

        ![Registros de Aplicativo do AD SaaS](./media/saas-offer-app-registration-v1-2.png)

        Para exemplos específicos de aplicativos web ou aplicativos nativos, confira as configurações guiadas quickstart que estão disponíveis na seção *Get Started* do [Azure AD Developers Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Ao terminar, clique em **Criar**. O Azure AD atribui um *ID de* \'aplicativo exclusivo ao\'seu aplicativo, e você é levado para a página principal de registro do aplicativo. Dependendo de se o seu aplicativo é Web ou nativo, diferentes opções serão fornecidas para adicionar mais recursos ao aplicativo.

>[!Note]
>Por padrão, o aplicativo recém-registrado é configurado para permitir que somente usuários do mesmo locatário entrem no seu aplicativo.


## <a name="using-the-azure-ad-security-token"></a>Usando o token de segurança Azure AD

Depois de registrar seu aplicativo, você pode solicitar programáticamente um token de segurança Azure AD.  Espera-se que o editor use esse token e faça um pedido para resolvê-lo.  Ao usar as várias APIs de preenchimento, o parâmetro de consulta de token está na URL quando o usuário é redirecionado para o site SaaS do Azure.  Este token só é válido por uma hora.  Além disso, você deve URL decodificar o valor do token do navegador antes de usá-lo.

Para obter mais informações sobre esses tokens, consulte [tokens de acesso ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obter um token com base no Aplicativo Azure AD

Método HTTP

`POST`

*URL de Solicitação*

**https://login.microsoftonline.com/*{inquilinoId}*/oauth2/token**

*Parâmetro URI*

|  **Nome do parâmetro**  | **Obrigatório**  | **Descrição**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | ID do locatário do aplicativo AAD registrado   |
|  |  |  |


*Cabeçalho da solicitação*

|  **Nome do cabeçalho**  | **Obrigatório** |  **Descrição**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Tipo de conteúdo     | True         | Tipo de conteúdo associado à solicitação. O valor padrão é `application/x-www-form-urlencoded`.  |
|  |  |  |


*Solicitar corpo*

| **Nome da propriedade**   | **Obrigatório** |  **Descrição**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Tipo de concessão. O valor padrão é `client_credentials`.                    |
|  Client_id          | True         |  Identificador do cliente/aplicativo associado ao Aplicativo Azure AD.                  |
|  client_secret      | True         |  Senha associada ao Aplicativo Azure AD.                               |
|  Recurso           | True         |  Recurso de destino para o qual o token é solicitado. O valor padrão é `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Resposta*

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

Seu aplicativo protegido pelo Azure AD agora pode usar a [API SaaS Fulfillment Versão 2](./pc-saas-fulfillment-api-v2.md).
