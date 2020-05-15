---
title: Tutorial de aplicativo de página única JavaScript 2.0 – plataforma de identidade da Microsoft | Azure
description: Como aplicativos SPA JavaScript podem usar o fluxo de código de autenticação para chamar uma API que exige tokens de acesso pelo Ponto de Extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f664f5fa4219a8bcc32230b352e90cc2516faceb
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82890373"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript – MSAL.js 2.0

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem ser alterados antes da GA (disponibilidade geral).

Este tutorial usa uma versão do MSAL.js que usa o fluxo de código de autorização OAuth 2.0 com PKCE. Para ler mais sobre esse protocolo, bem como as diferenças entre o fluxo implícito e o fluxo de código de autorização, confira a [documentação](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Se você estiver procurando um tutorial que usa o fluxo implícito, confira o [tutorial do MSAL.js v1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Essa versão do MSAL.js aprimora a biblioteca msal-core atual e usa o fluxo de código de autorização no navegador. A maioria dos recursos disponíveis na biblioteca antiga está disponível nesta versão, mas há pequenas diferenças entre o fluxo de autenticação de cada uma delas. Esta versão **NÃO** é compatível com o fluxo implícito.

Este guia demonstra como um aplicativo SPA (aplicativo de página única) JavaScript pode:
- Entrar em contas pessoais, bem como contas corporativas e de estudante
- Adquirir um token de acesso
- Chamar a API do Microsoft Graph ou outras APIs que requerem tokens de acesso do *ponto de extremidade da plataforma de identidade da Microsoft*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Mais informações

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API do Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade da plataforma de identidade da Microsoft. Nesse cenário, depois que um usuário se conecta, um token de acesso é adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (Biblioteca de Autenticação da Microsoft).

### <a name="libraries"></a>Bibliotecas

Este guia usa a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Biblioteca de Autenticação da Microsoft para o pacote de navegador do JavaScript|

## <a name="set-up-your-web-server-or-project"></a>Configurar o servidor Web ou o projeto

Prefere baixar este projeto de exemplo? Para executar os arquivos de projeto usando um servidor Web local (por exemplo, o Node.js), clone os arquivos de projeto:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Pule para a [etapa de configuração](#register-your-application) a fim de configurar o exemplo de código antes de executá-lo.

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, você precisa de um servidor Web local, como [Node.js](https://nodejs.org/en/download/) ou [.NET Core](https://www.microsoft.com/net/core).

* Se estiver usando o Node.js para executar o projeto, instale um IDE (ambiente de desenvolvimento integrado), como o [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos de projeto.

* As instruções neste tutorial se baseiam no Node.js.

## <a name="create-your-project"></a>Criar seu projeto

Verifique se você tem o [Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar seu aplicativo. Em seguida, implemente um servidor Web pequeno do [Express](https://expressjs.com/) para atender ao seu arquivo `index.html`.

1. Primeiro, navegue até a pasta do projeto em seu terminal e execute os comandos do NPM a seguir.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Em seguida, crie um arquivo .js chamado *server.js* e, depois, adicione o seguinte código:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Agora você tem um servidor simples para atender ao seu SPA. A estrutura de pastas pretendida no final deste tutorial é a seguinte:

![uma representação de texto da estrutura de pastas do SPA desejada](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA

1. Crie um arquivo *index.html* para seu JavaScript SPA na pasta *aplicativo*. Esse arquivo implementa uma interface do usuário criada com o **Bootstrap 4 Framework** e importa arquivos de script para configuração, autenticação e chamadas à API.

   No arquivo *index.html*, adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > Você pode substituir a versão de MSAL.js no script anterior pela versão mais recente em [versões de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Agora, crie um arquivo .js chamado *ui.js* que acessará e atualizará os elementos DOM, depois adicione a ele o seguinte código:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Registre seu aplicativo

Siga as [instruções para registrar um novo aplicativo de página única](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Configurar a URL de redirecionamento para o Node.js

Para o Node.js, você pode definir a porta do servidor Web no arquivo *server.js*. Este tutorial usa a porta 3000, mas você pode usar qualquer outra porta disponível.

Para configurar uma URL de redirecionamento nas informações de registro de aplicativo, volte para o painel **Registro de Aplicativo** e registre um novo **SPA** com um dos seguintes procedimentos:

- Defina *`http://localhost:3000/`* como a **URL de Redirecionamento**.
- Se você estiver usando uma porta TCP personalizada, use *`http://localhost:<port>/`* (onde *\<porta>* é o número de porta TCP personalizada).

### <a name="configure-your-javascript-spa"></a>Configurar o JavaScript SPA

Crie um arquivo .js chamado *authConfig.js* que conterá os parâmetros de configuração para autenticação, depois adicione a ele o seguinte código:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Em que:
 - *\<Insira_a_ID_do_Aplicativo_Aqui>* é a **ID do Aplicativo (cliente)** do aplicativo registrado.
 - *\<Insira_a_ID_da_Instância_de_Nuvem_Aqui>* é a instância da nuvem do Azure. Para a nuvem principal ou global do Azure, basta inserir *https://login.microsoftonline.com* . Para nuvens **nacionais** (por exemplo, China), confira [Nuvens nacionais](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
 - *\<Insira_as_informações_de_Locatário_aqui>* é definido para uma das seguintes opções:
   - Se o seu aplicativo for compatível com as *contas neste diretório organizacional*, substitua esse valor pela **ID do locatário** ou **Nome do locatário** (por exemplo, *contoso.microsoft.com*).
   - Se o aplicativo for compatível com as *contas em qualquer diretório organizacional*, substitua esse valor por **organizações**.
   - Se o seu aplicativo for compatível com as *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte a *contas pessoais da Microsoft*, substitua esse valor por **consumidores**.
- A *\Enter_the_Redirect_Uri_Here>* é a porta que você registrou no portal ( *`http://localhost:3000/`* )


Crie um arquivo .js chamado `graphConfig.js`, que conterá os parâmetros de configuração chamando a API do Microsoft Graph, depois adicione a ele o seguinte código:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- *\<Insira_o_ponto_de_extremidade_do_Graph_aqui>* é a instância da API do MS Graph. Para o ponto de extremidade global da API do MS Graph, basta substituir essa cadeia de caracteres por `https://graph.microsoft.com`. Para implantações de nuvem nacional, confira [Documentação da API do Graph](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Use a MSAL (Biblioteca de Autenticação da Microsoft) para conectar o usuário

### <a name="popup"></a>Pop-up
Crie um arquivo .js chamado `authPopup.js`, que conterá sua lógica de aquisição de token e autenticação para um pop-up de logon, depois adicione a ele o seguinte código:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Redirecionar
Crie um arquivo .js chamado `authRedirect.js`, que conterá sua lógica de aquisição de token e autenticação para redirecionamento de logon, depois adicione a ele o seguinte código:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Mais informações

Depois que um usuário selecione o botão **Entrar** pela primeira vez, o método `signIn` chama `loginPopup` para conectar o usuário. Esse método abre uma janela pop-up com o *ponto de extremidade da plataforma de identidade da Microsoft* para solicitar e validar as credenciais do usuário. Após uma entrada bem-sucedida, *msal.js* inicia o [fluxo de código de autorização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

Neste ponto, um código de autorização protegido por PKCE é enviado para o ponto de extremidade de token protegido por CORS e é trocado por tokens. Um token de ID, um token de acesso e um token de atualização são recebidos, processados por *msal.js* e as informações contidas no token são armazenadas em cache.

O token de ID contém informações básicas sobre o usuário, como o seu nome de exibição. Se você planeja usar os dados fornecidos por esse token, ele precisa ser validado pelo servidor de back-end para ter certeza de que o token foi emitido para um usuário válido para o seu aplicativo. O token de atualização tem um tempo de vida limitado e expira após 24 horas. O token de atualização pode ser usado para obter silenciosamente novos tokens de acesso.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API do Microsoft Graph para obter informações de perfil do usuário. Se você precisar de uma amostra que valida o token de ID, confira o aplicativo de exemplo [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) no GitHub. Esta amostra usa um ASP.NET Web API para validação de token.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Depois da entrada inicial, não se deve solicitar aos usuários que autentiquem novamente sempre que precisam solicitar um token para acessar um recurso. Para evitar essas solicitações de reautenticação, use `acquireTokenSilent`. Entretanto, há algumas situações em que talvez seja necessário forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft. Por exemplo: 

- Os usuários precisam reinserir suas credenciais, pois a senha expirou.
- Seu aplicativo está solicitando acesso a um recurso e você precisa do consentimento do usuário.
- A autenticação de dois fatores é necessária.

Chamar `acquireTokenPopup` abre uma janela pop-up (ou `acquireTokenRedirect` redireciona os usuários para o ponto de extremidade da plataforma de identidade da Microsoft). Nessa janela, os usuários precisam interagir confirmando suas credenciais, concedendo consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

O método `acquireTokenSilent` manipula a aquisição e a renovação de tokens sem nenhuma interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método usado normalmente para obter tokens usados para acessar recursos protegidos nas próximas chamadas. (Chamadas para solicitar ou renovar tokens são feitas de modo silencioso.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a senha do usuário pode ter expirado. O aplicativo pode tratar essa exceção de duas maneiras:

1. Fazer uma chamada para `acquireTokenPopup` imediatamente, o que dispara um prompt de entrada do usuário. Esse padrão é usado frequentemente em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação guiada usa esse padrão.

1. Indicar visualmente ao usuários que uma conexão interativa é necessária e, portanto, o usuário pode escolher o momento certo para se conectar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Essa técnica normalmente é usada quando o usuário pode usar outra funcionalidade do aplicativo sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível no aplicativo. Nessa situação, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este guia de início rápido usa os métodos `loginPopup` e `acquireTokenPopup` por padrão. Se você estiver usando o Internet Explorer como seu navegador, é recomendável usar os métodos `loginRedirect` e `acquireTokenRedirect`, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à maneira como o Internet Explorer lida com janelas pop-up. Se você quiser ver como obter o mesmo resultado usando métodos de redirecionamento, confira [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chamar a API do Microsoft Graph usando o token adquirido recentemente


 Crie um arquivo .js chamado *graph.js*, que fará uma chamada REST para a API do Microsoft Graph, depois adicione a ele o seguinte código:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

No aplicativo de exemplo criado por este guia, o método `callMSGraph()` é usado para fazer uma solicitação HTTP `GET` em um recurso protegido que exige um token. Depois, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de Autorização HTTP*. Para o aplicativo de exemplo criado por esse guia, o recurso é o ponto de extremidade *me* da API do Microsoft Graph, que exibe as informações de perfil do usuário.

## <a name="test-your-code"></a>Testar seu código

1. Para o Node.js, inicie o servidor Web executando os seguintes comandos de dentro da pasta do aplicativo:

   ```bash
   npm install
   npm start
   ```
1. No navegador, digite **http://localhost:3000** ou **http://localhost:{port}** , em que *port* é a porta na qual o servidor Web está escutando. Você deve ver o conteúdo do seu arquivo *index.html* e o botão **Entrar**.

## <a name="test-your-application"></a>Teste seu aplicativo

Depois que o navegador carregar seu arquivo *index.html*, selecione **Entrar**. Você será solicitado a entrar com o ponto de extremidade da plataforma de identidade da Microsoft:

![A janela de entrada do conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer autorização para acesso de aplicativo

Na primeira vez que entrar no aplicativo, você será solicitado a conceder acesso ao seu perfil e a conectá-lo:

![A janela de "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Veja os resultados de aplicativo

Após você entrar, suas informações de perfil do usuário são retornadas na resposta da API do Microsoft Graph exibida:

![Resultados esperados da chamada da API do Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API do Microsoft Graph requer o escopo *user.read* para ler o perfil do usuário. Por padrão, este escopo é adicionado automaticamente a cada aplicativo registrado no portal do Azure. Outras APIs do Microsoft Graph, bem como APIs personalizadas do servidor de back-end, podem exigir escopos adicionais. Por exemplo, a API do Microsoft Graph requer o escopo *Mail.Read* para listar os emails do usuário.

> [!NOTE]
> Talvez o usuário precise fornecer consentimento adicional à medida que você adicionar escopos.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar a *clientId* como o escopo nas chamadas para obter tokens.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

O [repositório GitHub do MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) contém documentação adicional da biblioteca, perguntas frequentes e oferece suporte a problemas.
