---
title: Adicionar entrada de OIDC a um aplicativo Web do Node.js – plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a autenticação em um aplicativo Web Node.js usando o OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-javascript
ms.openlocfilehash: 149ed7aa281e50673c86c0bd7339f304aa63914a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552672"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Início Rápido: Adicionar entrada usando o OpenID Connect para um aplicativo Web do Node.js

Neste início rápido, você aprenderá a configurar a autenticação do OpenID Connect em um aplicativo Web criado usando o Node.js com o Express. O exemplo foi projetado para ser executado em qualquer plataforma.

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará do seguinte:

* Instalar o Node.js do http://nodejs.org/

* Uma [conta Microsoft](https://www.outlook.com) ou um [Programa para Desenvolvedores do Microsoft 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registre seu aplicativo
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Se sua conta estiver presente em mais de um locatário do Azure AD:
    - Selecione seu perfil no menu no canto superior direito da página e, em seguida, escolha **Alternar diretório**.
    - Altere a sessão para o locatário do Azure AD no qual você deseja criar seu aplicativo.

1. Navegue até [Azure Active Directory > Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar seu aplicativo.

1. Selecione **Novo registro**.

1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
    - Na seção **Nome**, insira um nome relevante que será exibido aos usuários do aplicativo. Por exemplo:  MyWebApp
    - Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)** .

    Se houver mais de um URI de redirecionamento, você precisará adicioná-los da guia **Autenticação** posteriormente depois que o aplicativo tiver sido criado com êxito.

1. Selecione **Registrar** para criar o aplicativo.

1. Na página **Visão geral** do aplicativo, localize o valor de **ID do aplicativo (cliente)** e registre-o para uso posterior. Você precisará desse valor para configurar o aplicativo posteriormente neste projeto.

1. Na lista de páginas para o aplicativo, selecione **Autenticação**.
    - Na seção **Redirecionamento de URIs**, selecione **Web** na caixa de combinação e insira o seguinte URI de redirecionamento: `http://localhost:3000/auth/openid/return`
    - Na seção **Configurações avançadas**, defina **URL de Saída** como `https://localhost:3000`.
    - Na seção **Configurações avançadas > Concessão implícita**, marque **Tokens de ID**, pois este exemplo requer que o [Fluxo de concessão implícita](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) seja habilitado para conectar o usuário.

1. Clique em **Salvar**.

1. Na página **Certificados e segredos**, na seção **Segredos do cliente**, escolha **Novo segredo do cliente**.
    - Insira uma descrição de chave (para o segredo do aplicativo da instância).
    - Selecione uma duração de chave de **Em 1 ano, Em 2 anos** ou **Nunca expira**.
    - Quando você clicar no botão **Adicionar**, o valor da chave será exibido. Copie o valor da chave e salve-o em um local seguro.

    Você precisará dessa chave posteriormente para configurar o aplicativo. Esse valor de chave não será exibido novamente nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.

## <a name="download-the-sample-application-and-modules"></a>Baixar os módulos e aplicativo de exemplo

Em seguida, clone o repositório de exemplo e instale os módulos NPM.

No shell ou na linha de comando:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

ou

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

No diretório raiz do projeto, execute o comando:

`$ npm install`

## <a name="configure-the-application"></a>Configurar o aplicativo

Forneça os parâmetros em `exports.creds` em config.js conforme instruído.

* Atualize `<tenant_name>` em `exports.identityMetadata` com o nome do locatário do Azure AD do formato \*.onmicrosoft.com.
* Atualize `exports.clientID` com a ID do Aplicativo anotada no registro do aplicativo.
* Atualize `exports.clientSecret` com o Segredo do aplicativo anotado no registro do aplicativo.
* Atualize `exports.redirectUrl` com o URI de Redirecionamento anotado no registro do aplicativo.

**Configuração opcional para aplicativos de produção:**

* Atualize `exports.destroySessionUrl` em config.js se quiser usar um `post_logout_redirect_uri` diferente.

* Defina `exports.useMongoDBSessionStore` em config.js como true se quiser usar [mongoDB](https://www.mongodb.com) ou outros [repositórios de sessão compatíveis](https://github.com/expressjs/session#compatible-session-stores).
O repositório de sessão padrão neste exemplo é `express-session`. O repositório de sessão padrão não é adequado para produção.

* Atualize `exports.databaseUri` se quiser usar o repositório de sessão do mongoDB e um URI de banco de dados diferente.

* Atualizar `exports.mongoDBSessionMaxAge`. Aqui você pode especificar por quanto tempo deseja manter uma sessão no mongoDB. A unidade é segundos (s).

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Inicie o serviço mongoDB. Se você estiver usando o repositório de sessão do mongoDB neste aplicativo, será necessário [instalar o mongoDB](http://www.mongodb.org/) e iniciar o serviço primeiro. Se você estiver usando o repositório de sessão padrão, poderá ignorar esta etapa.

Execute o aplicativo usando o comando a seguir na linha de comando.

```
$ node app.js
```

**A saída do servidor é difícil de entender?:** usamos `bunyan` para registro em log neste exemplo. O console não fará muito sentido para você, a menos que você também instale o Bunyan e execute o servidor, como acima, mas redirecione-o por meio do binário Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>Pronto!

Você terá um servidor em execução com êxito no `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cenário do aplicativo Web compatível com a plataforma de identidade da Microsoft:
> [!div class="nextstepaction"]
> [Aplicativo Web que conecta cenários dos usuários](scenario-web-app-sign-user-overview.md)
