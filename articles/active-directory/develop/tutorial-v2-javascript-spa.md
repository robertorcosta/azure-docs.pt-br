---
title: Guia de aplicativo de página única do JavaScript da plataforma de identidade da Microsoft | Azure
description: Como aplicativos SPA JavaScript podem chamar uma API que exige tokens de acesso pelo Ponto de Extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3576c015fcca7eb9c390f38c6527d76353efe06
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919385"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript

Este guia demonstra como um aplicativo SPA (aplicativo de página única) JavaScript pode:
- Entrar em contas pessoais, bem como contas corporativas e de estudante 
- Adquirir um token de acesso
- Chamar a API do Microsoft Graph ou outras APIs que requerem tokens de acesso do *ponto de extremidade da plataforma de identidade da Microsoft*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade da plataforma de identidade da Microsoft. Nesse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia usa a seguinte biblioteca:

|Biblioteca|DESCRIÇÃO|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Versão prévia da Biblioteca de Autenticação da Microsoft para JavaScript|

> [!NOTE]
> *Msal.js* tem como destino o ponto de extremidade da plataforma de identidade da Microsoft, que permite que contas pessoais, corporativas e de estudante entrem e adquiram tokens. O ponto de extremidade da plataforma de identidade da Microsoft tem [algumas limitações](azure-ad-endpoint-comparison.md#limitations).
> Para entender as diferenças entre os pontos de extremidade v1.0 e v2.0, confira o [guia de comparação de pontos de extremidade](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Configurar o servidor Web ou o projeto

> Prefere baixar este projeto de exemplo? Faça uma das opções a seguir:
> 
> - [Baixe os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) para executar o projeto com um servidor Web local, como Node.js.
>
> - (Opcional) Para executar o projeto usando o servidor do IIS (Serviços de Informações da Internet) da Microsoft, [baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Pule para a [etapa de configuração](#register-your-application) a fim de configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, é necessário um servidor Web local como [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) ou a integração do IIS Express com o [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se estiver usando o Node.js para executar o projeto, instale um ambiente de desenvolvimento integrado (IDE), como o [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos de projeto.

* As instruções neste guia são baseadas no Node.js e no Visual Studio 2017, mas você pode usar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar seu projeto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opção 1: Node.js ou outros servidores Web
> Verifique se você tem o [Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar seu aplicativo.
>
> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se você estiver usando o Visual Studio e for criar um novo projeto, siga estas etapas:
> 1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
> 1. Em **Visual C#\Web**, selecione **Aplicativo Web ASP.NET (.NET Framework)** .
> 1. Insira um nome para o aplicativo e selecione **OK**.
> 1. Em **Novo aplicativo Web do ASP.NET**, selecione **Vazio**.

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA
1. Crie um arquivo *index.html* para seu JavaScript SPA. Se estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto). Clique com o botão direito do mouse e selecione **Adicionar** > **Novo Item** > **Página HTML** e dê ao arquivo o nome *index.html*.

1. No arquivo *index.html*, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Você pode substituir a versão de MSAL.js no script anterior pela versão mais recente em [versões de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Use a MSAL (Biblioteca de Autenticação da Microsoft) para conectar o usuário

Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Depois que um usuário selecione o botão **Entrar** pela primeira vez, o método `signIn` chama `loginPopup` para conectar o usuário. Esse método abre uma janela pop-up com o *ponto de extremidade da plataforma de identidade da Microsoft* para solicitar e validar as credenciais do usuário. Após entrar com êxito, o usuário é redirecionado de volta para a página *index.html* original. Um token é recebido, processado por `msal.js` e as informações contidas no token são armazenadas em cache. Esse token é conhecido como o *token de ID* e contém informações básicas sobre o usuário, como o nome de exibição do usuário. Se você planeja usar os dados fornecidos por esse token para qualquer finalidade, é necessário certificar-se de que esse token seja validado pelo servidor de back-end para ter certeza de que o token foi emitido para um usuário válido para o seu aplicativo.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações de perfil do usuário. Se você precisar de um exemplo que valide o token da ID, examine [este](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Exemplo do GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2") aplicativo de exemplo no GitHub. Esta amostra usa uma ASP.NET Web API para validação do token.

#### <a name="getting-a-user-token-interactively"></a>Obtendo um token de usuário interativamente

Depois da entrada inicial, você não deseja solicitar aos usuários que autentiquem novamente sempre que precisam solicitar um token para acessar um recurso. Portanto, *acquireTokenSilent* deve ser usado na maioria das vezes para adquirir tokens. Entretanto, há situações em que é necessário forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft. Os exemplos incluem:

- Os usuários precisam reinserir suas credenciais, pois a senha expirou.
- Seu aplicativo está solicitando acesso a um recurso e você precisa do consentimento do usuário.
- A autenticação de dois fatores é necessária.

Chamar *acquireTokenPopup* abre uma janela pop-up (ou *acquireTokenRedirect* redireciona os usuários para o ponto de extremidade da plataforma de identidade da Microsoft). Nessa janela, os usuários precisam interagir confirmando suas credenciais, concedendo consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="getting-a-user-token-silently"></a>Obtendo um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula a aquisição e a renovação de tokens sem nenhuma interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método usado normalmente para obter tokens usados para acessar recursos protegidos nas próximas chamadas. (Chamadas para solicitar ou renovar tokens são feitas de modo silencioso.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a senha do usuário pode ter expirado. O aplicativo pode tratar essa exceção de duas maneiras:

1. Fazer uma chamada para `acquireTokenPopup` imediatamente, o que dispara um prompt de entrada do usuário. Esse padrão é usado frequentemente em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação guiada usa esse padrão.

2. Os aplicativos também podem fazer uma indicação visual para o usuário de que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Normalmente, isso é usado quando o usuário pode usar outra funcionalidade do aplicativo sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível no aplicativo. Nessa situação, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este início rápido usa os métodos `loginRedirect` e `acquireTokenRedirect` quando o Internet Explorer é o navegador usado. Seguimos essa prática devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à maneira como o Internet Explorer lida com janelas pop-up.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chamar a API do Microsoft Graph usando o token adquirido recentemente

Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

No aplicativo de exemplo criado por este guia, o método `callMSGraph()` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para o aplicativo de exemplo criado por esse guia, o recurso é o ponto de extremidade *me* da API do Microsoft Graph, que exibe as informações de perfil do usuário.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Adicione o seguinte código no arquivo `index.html` dentro da marca `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Registre seu aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Se a conta fornecer acesso a mais de um locatário, selecione a conta no canto superior direito e defina sua sessão do portal para o locatário Azure AD que deseja usar.
1. Vá até a página [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) da plataforma de identidade da Microsoft para desenvolvedores.
1. Quando a página **Registrar um aplicativo** aparecer, insira um nome para o seu aplicativo.
1. Em **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **URI de Redirecionamento**, selecione a plataforma **Web** na lista suspensa e defina o valor para a URL do aplicativo com base em seu servidor Web.

   Para saber como definir e obter a URL de redirecionamento para o Node.js e o Visual Studio, confira a seção "Definir uma URL de redirecionamento para o Node.js" a seguir e confira [Configurar a URL de redirecionamento para o Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Selecione **Registrar**.
1. Na página **Visão geral** do aplicativo, anote o valor de **ID do aplicativo (cliente)** para uso posterior.
1. Este início rápido requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo do aplicativo registrado, selecione **Autenticação**.
1. Em **Configurações avançadas**, em **Concessão implícita**, marque as caixas de seleção **Tokens de ID** e **Tokens de Acesso**. Os tokens de ID e tokens de acesso são necessários porque esse aplicativo precisa conectar usuários e chamar uma API.
1. Clique em **Salvar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Configurar a URL de redirecionamento para o Node.js
> Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 30662, mas você pode usar qualquer outra porta disponível.
>
> Para configurar uma URL de redirecionamento nas informações de registro do aplicativo, volte para o painel **Registro de Aplicativo** e siga um destes procedimentos:
>
> - Defina *`http://localhost:30662/`* como a **URL de Redirecionamento**.
> - Se você estiver usando uma porta TCP personalizada, use *`http://localhost:<port>/`* (onde *\<porta>* é o número de porta TCP personalizada).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Configurar a URL de redirecionamento para o Visual Studio
> Para obter a URL de redirecionamento para o Visual Studio, siga estas etapas:
> 1. No Gerenciador de Soluções, selecione o projeto.
>
>    A janela **Propriedades** é aberta. Se isso não acontecer, pressione F4.
>
>    ![A janela de propriedades do projeto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copie o valor da **URL**.
> 1. Volte para o painel **Registro de Aplicativo** e cole o valor copiado como uma **URL de Redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

1. No arquivo *index.html* criado durante a configuração do projeto, inclua as informações de registro do aplicativo. Na parte superior do arquivo, dentro das marcações `<script></script>`, adicione o seguinte código:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Em que:
    - *\<Insira_Informações_da_Conta_com_Suporte_Aqui>* é a **ID do Aplicativo (cliente)** para o aplicativo que você registrou.
    - *\<Insira_as_informações_de_Locatário_aqui>* é definido para uma das seguintes opções:
       - Se o seu aplicativo é compatível com *Contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **Nome do locatário** (por exemplo, *Contoso.microsoft.com*).
       - Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**.
       - Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**.

## <a name="test-your-code"></a>Testar seu código

Teste seu código usando qualquer um dos ambientes a seguir.

### <a name="test-with-nodejs"></a>Testar com Node.js

Se você não estiver usando o Visual Studio, verifique se o servidor Web está iniciado.

1. Configure o servidor para escutar uma porta TCP com base no local do seu arquivo *index.html*. Para o Node.js, inicie o servidor Web para escutar a porta executando os comandos a seguir em um prompt de linha de comando da pasta de aplicativo:

    ```bash
    npm install
    node server.js
    ```
1. No seu navegador, digite **http://\<span>\</span>localhost:30662** ou **http://\<span>\</span>localhost:{port}** , em que *port* é a porta que o seu servidor Web está escutando. Você deve ver o conteúdo do seu arquivo *index.html* e o botão **Entrar**.

### <a name="test-with-visual-studio"></a>Testar com Visual Studio

Se estiver usando o Visual Studio, verifique se selecionou a solução de projeto e pressione F5 para executar o projeto. O navegador abre para o local http://<span></span>localhost:{port} e o botão **Entrar** deve estar visível.

## <a name="test-your-application"></a>Teste seu aplicativo

Depois que o navegador carregar seu arquivo *index.html*, selecione **Entrar**. Você será solicitado a entrar com o ponto de extremidade da plataforma de identidade da Microsoft:

![A janela de entrada do conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que entrar no aplicativo, você será solicitado a conceder acesso ao seu perfil e a conectá-lo:

![A janela de "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Após você entrar, suas informações de perfil do usuário são retornadas na resposta da API do Microsoft Graph exibida:

![Resultados esperados da chamada da API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Por padrão, este escopo é adicionado automaticamente a cada aplicativo registrado no portal de registro. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Calendars.Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *Calendars.Read* às informações de registro do aplicativo. Em seguida, adicione o escopo *Calendars.Read* à chamada `acquireTokenSilent`.

>[!NOTE]
>Talvez o usuário precise fornecer autorizações adicionais à medida que o número de escopos aumentar.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar a *clientId* como o escopo nas chamadas para obter tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
