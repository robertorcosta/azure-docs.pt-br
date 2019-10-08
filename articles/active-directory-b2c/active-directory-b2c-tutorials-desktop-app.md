---
title: Tutorial – Habilitar autenticação em um aplicativo cliente nativo – Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como usar o Azure Active Directory B2C para fornecer o logon do usuário para um aplicativo da área de trabalho .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326322"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar autenticação em um aplicativo cliente nativo usando o Azure Active Directory B2C

Este tutorial mostra como usar o Azure AD B2C (Azure Active Directory B2C) para inscrever e conectar usuários em um aplicativo da área de trabalho do WPF (Windows Presentation Foundation). O Azure AD B2C permite que seus aplicativos se autentiquem com contas sociais, corporativas e do Azure Active Directory usando protocolos padrão abertos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar um aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com cargas de trabalho de **desenvolvimento de área de trabalho do .NET** e **desenvolvimento Web e do ASP.NET**.

## <a name="add-the-native-client-application"></a>Adicionar um aplicativo cliente nativo

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registre a **ID DO APLICATIVO** para uso em uma etapa posterior.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você deve configurar um exemplo que pode ser baixado do GitHub. O aplicativo da área de trabalho WPF de exemplo demonstra a inscrição e a conexão e chama uma API Web protegida no Azure AD B2C. [Baixe um arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [procure no repositório](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) ou clone o exemplo do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Para alterar as configurações do aplicativo, substitua o `<your-tenant-name>` pelo nome do locatário e substitua`<application-ID`> pela ID do aplicativo que você registrou.

1. Abra a solução `active-directory-b2c-wpf` no Visual Studio.
2. No projeto `active-directory-b2c-wpf`, abra o arquivo **App.xaml.cs** e faça as seguintes atualizações:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Atualize a variável **PolicySignUpSignIn** com o nome do fluxo de usuário que você criou.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Pressione **F5** para criar e executar o exemplo.

### <a name="sign-up-using-an-email-address"></a>Criar conta usando um endereço de email

1. Clique em **Entrar** para inscrever-se como um usuário. Isso usa o fluxo de usuário **B2C_1_signupsignin1**.
2. O Azure AD B2C apresenta uma página de entrada com um link de inscrição. Como você ainda não tem uma conta, clique no link **Inscrever-se agora**.
3. O fluxo de trabalho de inscrição apresenta uma página para coletar e verificar a identidade do usuário usando um endereço de email. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo de usuário.

    Use um endereço de email válido e valide-o usando o código de verificação. Defina uma senha. Insira valores para os atributos necessários.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Clique em **Criar** para criar uma conta local no locatário do Azure AD B2C.

Agora o usuário pode usar seu endereço de email para entrar e usar o aplicativo de desktop.

> [!NOTE]
> Se você clicar no botão **Chamar API**, você receberá um erro "Não autorizado". Você recebe esse erro porque você está tentando acessar um recurso do locatário de demonstração. Como o token de acesso só é válido para seu locatário do Azure AD, a chamada à API não é autorizada. Continue com o próximo tutorial para criar uma API Web protegida para seu locatário.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar um aplicativo cliente nativo
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo de usuário

> [!div class="nextstepaction"]
> [Tutorial: Permitir acesso a uma API Web do Node.js de um aplicativo da área de trabalho usando o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
