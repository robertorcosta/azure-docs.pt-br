---
title: Tutorial – Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Active Directory B2C para proteger uma API Web Node.js e chamá-la de um aplicativo de desktop .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679335"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho usando o Azure Active Directory B2C

Este tutorial mostra como chamar um recurso da API Web do Node.js protegido pelo Azure AD B2C (Azure Active Directory B2C) em um aplicativo da área de trabalho do WPF (Windows Presentation Foundation).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [Tutorial: Permitir autenticação de aplicativos da área de trabalho com contas usando o Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adicionar um aplicativo API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. Escopos são usados pela API Web para implementar o controle de acesso com base em escopo. Por exemplo, alguns usuários podem ter acesso de leitura e gravação, enquanto outros usuários podem ter permissões somente leitura. Neste tutorial, você define as permissões de leitura para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, é necessário conceder permissões ao aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *app1*. Use este aplicativo para chamar a API Web.

1. Selecione **Aplicativos** e, em seguida, selecione *nativeapp1*.
1. Selecione **Acesso à API** e, em seguida, selecione **Adicionar**.
1. No menu suspenso **Selecionar API**, selecione *webapi1*.
1. Na lista suspensa **Selecionar Escopos**, selecione os escopos que você definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

Um usuário autentica-se com o Azure AD B2C para usar o aplicativo da área de trabalho do WPF. O aplicativo da área de trabalho obtém uma concessão de autorização do Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar o locatário do Azure AD B2C. Neste tutorial, configure um aplicativo Web Node.js de exemplo que pode ser baixado do GitHub.

[Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o aplicativo Web de exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js usa a biblioteca de Passport.js para habilitar o Azure AD B2C a proteger chamadas à API.

1. Abra o arquivo `index.js` .
2. Configure o exemplo com as informações de registro do locatário do Azure AD B2C. Alterar as seguintes linhas de código:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Execute o exemplo

1. Inicie um prompt de comando de Node.js.
2. Altere o diretório que contém o exemplo de Node.js. Por exemplo `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar o aplicativo da área de trabalho

1. Abra a solução **active-directory-b2c-wpf** no Visual Studio.
2. Pressione **F5** para executar o aplicativo de desktop.
3. Entre usando o endereço de email e a senha usada no [tutorial Autenticar usuários com o Azure Active Directory B2C em um aplicativo de desktop](active-directory-b2c-tutorials-desktop-app.md).
4. Clique no botão **Chamar API**.

O aplicativo da área de trabalho faz uma solicitação para a API Web e obtém uma resposta com o nome de exibição do usuário conectado. Seu aplicativo da área de trabalho protegido está chamando a API Web protegida no seu locatário do Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um aplicativo API Web
> * Configurar escopos para uma API Web
> * Conceder permissões à API Web
> * Atualizar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade aos aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
