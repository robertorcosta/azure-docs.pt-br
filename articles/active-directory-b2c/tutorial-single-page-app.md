---
title: 'Tutorial: Habilitar a autenticação em um aplicativo de página única'
titleSuffix: Azure AD B2C
description: Neste tutorial, saiba como usar o Azure Active Directory B2C para fornecer o logon do usuário para um SPA (aplicativo de página única) baseado no JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cd696b6aba96b06a7b84722d61eb40bbfa15c0ff
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84447927"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo de página única com o Azure AD B2C

Este tutorial mostra como usar o Azure AD B2C (Azure Active Directory B2C) para inscrever e conectar usuários em um SPA (aplicativo de página única).

Neste tutorial, o primeiro de uma série de duas partes:

> [!div class="checklist"]
> * Adicione uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixe um exemplo de código do GitHub
> * Modifique o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreva-se usando seu fluxo de inscrição/entrada de usuário

O [próximo tutorial](tutorial-single-page-app-webapi.md) da série habilita a parte da API Web do exemplo de código.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes recursos do Azure AD B2C em vigor antes de continuar com as etapas deste tutorial:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário

Além disso, você precisará do seguinte no ambiente de desenvolvimento local:

* [Visual Studio Code](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar o aplicativo

No segundo tutorial concluído como parte dos pré-requisitos, você registrou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo de código neste tutorial, adicione uma URL de resposta (também chamada de URI de redirecionamento) ao registro de aplicativo.

Para atualizar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **Registros de aplicativo**, selecione a guia **Aplicativos Próprios** e, em seguida, selecione o aplicativo *webapp1*.
1. Em **Web**, selecione o link **Adicionar URI** e insira `http://localhost:6420`.
1. Em **Concessão Implícita**, marque as caixas de seleção para **Tokens de Acesso** e **Tokens de ID** e, em seguida, selecione **Salvar**.
1. Selecione **Visão geral**.
1. Anote a **ID do aplicativo (cliente)** para uso em uma etapa posterior, na qual você atualize o código no aplicativo Web de página única.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal do Azure, pesquise pelo **Azure AD B2C** e selecione-o.
1. Selecione **Aplicativos (Herdado)** e, em seguida, selecione o aplicativo *webapp1*.
1. Em **URL de resposta**, adicione `http://localhost:6420`.
1. Clique em **Salvar**.
1. Na página de propriedades, registre a **ID do Aplicativo**. Você pode usar a ID do aplicativo em uma etapa posterior ao atualizar o código no aplicativo Web de página única.

* * *

## <a name="get-the-sample-code"></a>Obter o código de amostra

Neste tutorial, você configurará um exemplo de código baixado do GitHub para funcionar com o locatário do B2C. A amostra descreve como um aplicativo de página única pode usar o Azure AD B2C para a inscrição e a entrada de usuário e para chamar uma API Web protegida (você habilita a API Web no próximo tutorial da série).

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que você já obteve a amostra, atualize o código com o nome do locatário do Azure AD B2C e a ID do aplicativo registrada em uma etapa anterior.

1. Abra o arquivo *authConfig.js* dentro da pasta *JavaScriptSPA*.
1. No objeto `msalConfig`, atualize:
    * `clientId` com o valor com a **ID de aplicativo (cliente)** que você registrou em uma etapa anterior
    * O URI `authority` com o nome do locatário do Azure AD B2C e o nome do fluxo de inscrição/entrada de usuário criado como parte dos pré-requisitos (por exemplo, *B2C_1_signupsignin1*)

    ```javascript
    const msalConfig = {
        auth: {
          clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
          authority: b2cPolicies.authorities.signUpSignIn.authority,
          validateAuthority: false
        },
        cache: {
          cacheLocation: "localStorage",
          storeAuthStateInCookie: true
        }
    };

    const loginRequest = {
       scopes: ["openid", "profile"],
    };

    const tokenRequest = {
      scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
    };
    ```

## <a name="run-the-sample"></a>Execute o exemplo

1. Abra uma janela do console e altere para o diretório que contém a amostra. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela do console exibe o número da porta do servidor do Node.js em execução localmente:

    ```console
    Listening on port 6420...
    ```
1. Navegue até `http://localhost:6420` para ver o aplicativo Web em execução no computador local.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Navegador da Web mostrando o aplicativo de página única em execução localmente":::

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

Este aplicativo de exemplo é compatível com inscrição, entrada e redefinição de senha. Neste tutorial, você se inscreve usando um endereço de email.

1. Selecione **Entrar** para iniciar o fluxo de usuário *B2C_1_signupsignin1* especificado em uma etapa anterior.
1. O Azure AD B2C apresenta uma página de entrada que inclui um link de inscrição. Como você ainda não tem uma conta, selecione o link **Inscrever-se agora**.
1. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Página de inscrição exibida pelo fluxo de usuário do Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório do Azure AD B2C.

Quando você seleciona **Criar**, o aplicativo mostra o nome do usuário conectado.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Navegador da Web mostrando um aplicativo de página única com o usuário conectado":::

Se você quiser testar a entrada, selecione o botão **Sair** e, em seguida, selecione **Entrar** e entre com o endereço de email e a senha que você inseriu quando se inscreveu.

### <a name="what-about-calling-the-api"></a>E quanto à chamada à API?

Se você selecionar o botão **Chamar API** depois de entrar, será exibida a página de fluxo de inscrição/entrada de usuário em vez dos resultados da chamada à API. Isso é esperado porque você ainda não configurou a parte da API do aplicativo para se comunicar com um aplicativo de API Web registrado em *seu* locatário do Azure AD B2C.

Neste ponto, o aplicativo ainda está tentando se comunicar com a API registrada no locatário de demonstração (fabrikamb2c.onmicrosoft.com) e, como você não está autenticado nesse locatário, a página de inscrição/entrada é exibida.

Vá para o próximo tutorial da série para habilitar a API protegida (consulte a seção [Próximas etapas](#next-steps)).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um aplicativo de página única para trabalhar com um fluxo de usuário em seu locatário do Azure AD B2C para fornecer capacidade de inscrição e entrada. Você concluiu estas etapas:

> [!div class="checklist"]
> * Adicionou uma URL de resposta a um aplicativo registrado em seu locatário do Azure AD B2C
> * Baixou um exemplo de código do GitHub
> * Modificou o código do aplicativo de exemplo para funcionar com seu locatário
> * Inscreveu-se usando o fluxo de inscrição/entrada de usuário

Agora passe para o próximo tutorial da série para permitir acesso a uma API Web protegida por meio do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Proteger e permitir acesso à API Web em um aplicativo de página única >](tutorial-single-page-app-webapi.md)
