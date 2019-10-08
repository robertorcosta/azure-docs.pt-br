---
title: Início rápido do Android para a plataforma de identidade da Microsoft | Azure
description: Saiba como aplicativos Android podem chamar uma API que exige tokens de acesso pelo ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678044"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Entrada de usuários e chamada à API do Microsoft Graph em um aplicativo Android

Este início rápido usa um exemplo de código para demonstrar como um aplicativo Android pode conectar contas pessoais, corporativas ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Captura de tela do aplicativo de exemplo](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * É necessário ter o Android 16+

## <a name="step-1-get-the-sample-app"></a>Etapa 1: Obter o aplicativo de exemplo

[Clone o código](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Etapa 2: Registre seu aplicativo

Para registrar um objeto de aplicativo e adicionar as informações de registro desse objeto de aplicativo ao projeto de exemplo manualmente, siga estas etapas:

1. Vá para o [Portal do Azure](https://aka.ms/MobileAppReg).
1. Abra a folha [Registros de aplicativo](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e clique em **+Novo registro**.
1. Insira um **Nome** para seu registro de aplicativos e, em seguida, sem definir um URI de Redirecionamento, clique em **Registrar**.
1. Na seção **Gerenciar**, selecione **Autenticação** >  **+ Adicionar uma plataforma** > **Android**. (Talvez seja necessário selecionar **Experimentar a nova experiência** ao lado da parte superior da folha para ver esta tela)
1. Insira o **Nome do Pacote** do seu projeto, que é `com.azuresamples.msalandroidapp`.
1. Na seção **Hash de assinatura** da página **Configure seu aplicativo Android**, clique em **Gerar um Hash de Assinatura de desenvolvimento** e copie o comando KeyTool a ser usado para a plataforma que você está usando para desenvolver seu aplicativo Android.

   > [!Note]
   > KeyTool.exe é instalado como parte do JDK (Java Development Kit). Você também precisará instalar a ferramenta OpenSSL para executar o comando KeyTool.  Você precisará do keytool e do diretório OpenSSL\bin em seu caminho.

1. Execute o comando keytool que você copiou do portal em uma janela de terminal.
1. Insira o hash de assinatura gerado no portal em **Hash de assinatura**.
1. Clique em `Configure` e faça uma cópia da **Configuração da MSAL**. Você copiará e colará isso em um arquivo de configuração na próxima etapa. Clique em **Concluído**.

## <a name="step-3-add-your-app-registration"></a>Etapa 3: Adicionar o registro do seu aplicativo

1. Abra o projeto de exemplo no Android Studio.
1. Dentro de **app** > **res** > **raw**, abra **auth_config_multiple_account.json**.  Cole o conteúdo da Configuração da MSAL. Isso adiciona a ID do cliente, a ID do locatário e o redirect_uri do portal. Ele terá uma aparência semelhante a esta, mas com os valores preenchidos para a ID do cliente, ID do locatário e redirect_uri:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. Abra **app** > **res** > **raw**, abra **auth_config_single_account.json** e cole o conteúdo da Configuração da MSAL. Ele se parecerá com o arquivo **auth_config_multiple_account.json** acima.
1. Em **app** > **manifests** > **AndroidManifest.xml**, encontre a atividade `BrowserTabActivity`. Essa entrada permite que a Microsoft faça uma chamada de retorno ao aplicativo após concluir a autenticação:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Substitua o nome do pacote pelo que você registrou no portal do Azure para o valor `android:host=`.  Nesse caso, ele será: `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > O valor **android:path** **deve** ter um caractere "/" à esquerda ou você terá uma linha vermelha embaixo do valor e o aplicativo de exemplo não será executado.
     
1. Substitua o hash de chave que você obteve executando o keytool anteriormente e inseriu no portal do Azure, para o valor `android:path=`. O hash de assinatura não deve ser codificado por URL.

## <a name="step-4-run-the-sample-app"></a>Etapa 4: Executar o aplicativo de exemplo

Selecione seu emulador ou dispositivo da lista suspensa de **dispositivos disponíveis** do Android Studio e execute o aplicativo.

O aplicativo de exemplo é iniciado na tela **Modo de Conta Único**. Um escopo padrão, **user.read**, é fornecido por padrão, usado ao ler seus próprios dados de perfil durante a chamada à API do Microsoft Graph. A URL da chamada à API do Microsoft Graph é fornecida por padrão. Se desejar, você poderá alterar as duas.

![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)

Use o menu do aplicativo para alterar entre modos de conta única e múltipla.

No modo de conta única, entre usando uma conta corporativa ou doméstica:

1. Selecione **Obter dados de grafo interativamente** para solicitar que o usuário insira suas credenciais. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.
2. Após entrar, selecione **Obter dados de grafo silenciosamente** para fazer uma chamada à API do Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada à API do Microsoft Graph na parte inferior da tela.

No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache dessa conta.

## <a name="how-the-sample-works"></a>Como o exemplo funciona

O código é organizado em fragmentos que mostram como escrever um aplicativo MSAL de contas únicas e múltiplas. Os arquivos de código são organizados da seguinte maneira:

| Arquivo  | Demonstra  |
|---------|---------|
| MainActivity | Gerencia a interface do usuário |
| MSGraphRequestWrapper  | Chama a API do Microsoft Graph usando o token fornecido pela MSAL |
| MultipleAccountModeFragment  | Inicializa um aplicativo de conta múltipla, carrega uma conta de usuário e obtém um token para chamar a API do Microsoft Graph |
| SingleAccountModeFragment | Inicializa um aplicativo de conta única, carrega uma conta de usuário e obtém um token para chamar a API do Microsoft Graph |
| res/auth_config_multiple_account.json  | O arquivo de configuração de conta múltipla |
| res/auth_config_single_account.json  | O arquivo de configuração de conta única |
| Gradle Scripts/build.grade (Module:app) | As dependências da biblioteca MSAL são adicionadas aqui |

Agora examinaremos esses arquivos mais detalhadamente e chamaremos o código específico da MSAL em cada um.

### <a name="add-msal-to-the-app"></a>Adicionar MSAL ao aplicativo

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O Gradle 3.0+ instala a biblioteca quando você adiciona o seguinte aos **Scripts do Gradle** > **build.gradle (Módulo: aplicativo)** em **Dependências**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

É possível ver isso no projeto de exemplo em build.gradle (Módulo: aplicativo):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Isso instrui o Gradle a baixar e compilar o MSAL da central do maven.

### <a name="msal-imports"></a>Importações da MSAL

As importações relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*`.  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;` que é o namespace da classe `PublicClientApplication`, que representa seu aplicativo cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Este arquivo demonstra como criar um aplicativo MSAL de conta única e chamar uma API do Microsoft Graph.

Os aplicativos de conta única são usados por um usuário único.  Por exemplo, talvez você só tenha uma conta com a qual você entra em seu aplicativo de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização da MSAL de conta única

No `onCreateView()`, uma conta única `PublicClientApplication` é criada usando as informações de configuração armazenadas no arquivo `auth_config_single_account.json`.  É assim como você inicializa a biblioteca MSAL para que ela seja usada em um aplicativo MSAL de conta única:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Conectar um usuário

O código para conectar um usuário está no `initializeUI()`, no manipulador de cliques `signInButton`.

Chame `signIn()` antes de tentar adquirir tokens. `signIn()` se comporta como se `acquireToken()` foi chamado, resultando em um prompt interativo para o usuário entrar.

Conectar um usuário é uma operação assíncrona. Um retorno de chamada é passado que chama a API do Microsoft Graph e atualiza a interface do usuário depois que o usuário se conecta:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Desconectar um usuário

O código para desconectar um usuário está no `initializeUI()`, no manipulador de cliques `signOutButton`.  Desconectar um usuário é uma operação assíncrona. Desconectar o usuário também apaga o cache de token dessa conta. Um retorno de chamada será criado para atualizar a interface do usuário depois que a conta de usuário for desconectada:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de maneira interativa ou silenciosa

Para representar o menor número de solicitações para o usuário, normalmente você obterá um token silenciosamente. Em seguida, se houver um erro, tente obter o token interativamente. Na primeira vez em que o aplicativo chama `signIn()`, ele funciona efetivamente como uma chamada a `acquireToken()`, que solicitará as credenciais do usuário.

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir credenciais ou dar consentimento às permissões que seu aplicativo solicitou são:

* A primeira vez que o usuário entra no aplicativo
* Se um usuário redefinir a senha, ele precisará inserir as credenciais
* Se o consentimento for revogado
* Se o aplicativo exigir consentimento explicitamente
* Quando seu aplicativo estiver solicitando acesso a um recurso pela primeira vez
* Quando a MFA ou outras políticas de Acesso Condicional forem necessárias

O código para obter um token interativamente, que está com a interface do usuário que envolverá o usuário, está em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que aplicativos solicitem tokens silenciosamente, conforme mostrado no `initializeUI()`, no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está em `loadAccount()`.  Carregar a conta do usuário é uma operação assíncrona. Portanto, retornos de chamada para manipular quando a conta é carregada ou alterada ou quando um erro ocorre são passados para a MSAL.  O código a seguir também manipula `onAccountChanged()`, que ocorre quando uma conta é removida, o usuário é muda para outra conta e assim por diante.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Chamar o Microsoft Graph

Quando um usuário é conectado, a chamada ao Microsoft Graph é feita por meio de uma solicitação HTTP pelo `callGraphAPI()`. Essa função é um wrapper que simplifica o exemplo realizando algumas tarefas como obter o token de acesso do `authenticationResult` e empacotar a chamada para o MSGraphRequestWrapper e exibir os resultados da chamada.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Este é o arquivo de configuração de um aplicativo MSAL que usa uma conta única.

Confira [Noções básicas sobre o arquivo de configuração da MSAL do Android](msal-configuration.md) para obter uma explicação desses campos.

Observe a presença de `"account_mode" : "SINGLE"`, que configura esse aplicativo para usar uma conta única.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Este arquivo demonstra como criar um aplicativo MSAL de conta múltipla e chamar uma API do Microsoft Graph. 

Um exemplo de um aplicativo de conta múltipla é um aplicativo pequeno que permite que você trabalhe com várias contas de usuário, como uma conta corporativa e uma conta pessoal.

#### <a name="multiple-account-msal-initialization"></a>Inicialização da MSAL de conta múltipla

No `onCreateView()`, um objeto de aplicativo de conta múltipla (`IMultipleAccountPublicClientApplication`) é criado usando as informações de configuração armazenadas no `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

O objeto `MultipleAccountPublicClientApplication` criado é armazenado em uma variável de membro de classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir tokens e carregar e remover a conta de usuário.

#### <a name="load-an-account"></a>Carregar uma conta

Aplicativos de conta múltipla geralmente chamam `GetAccounts()` para selecionar a conta a ser usada para operações de MSAL. O código para carregar uma conta está em `loadAccount()`.  Carregar a conta de usuário é uma operação assíncrona. Portanto, um retorno de chamada lida com as situações em que a conta é carregada ou alterada ou quando ocorre um erro.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de maneira interativa ou silenciosa

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir credenciais ou dar consentimento às permissões que seu aplicativo solicitou são:

* A primeira vez que os usuários entram no aplicativo
* Se um usuário redefinir a senha, ele precisará inserir as credenciais 
* Se o consentimento for revogado 
* Se o aplicativo exigir consentimento explicitamente 
* Quando seu aplicativo estiver solicitando acesso a um recurso pela primeira vez
* Quando a MFA ou outras políticas de Acesso Condicional forem necessárias

Os aplicativos de conta múltipla normalmente devem adquirir tokens interativamente, ou seja, com uma interface do usuário que envolva o usuário, com uma chamada para `acquireToken()`.  O código para obter um token interativamente está em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Os aplicativos não devem exigir que o usuário entre sempre que solicitar um token. Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que aplicativos solicitem tokens sem solicitar o usuário, conforme mostrado no `initializeUI()`, no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Remover uma conta

O código para remover uma conta e todos os tokens em cache da conta está em `initializeUI()` no manipulador para o botão remover conta. Antes de remover uma conta, você precisa de um objeto de conta, que você obtém de funções MSAL como `getAccounts()` e `acquireToken()`. Como a remoção de uma conta é uma operação assíncrona, o retorno de chamada `onRemoved` é fornecido para atualizar a interface do usuário.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Este é o arquivo de configuração para um aplicativo MSAL que usa várias contas.

Confira [Noções básicas sobre o arquivo de configuração da MSAL do Android](msal-configuration.md) para obter uma explicação desses campos.

Diferentemente do arquivo de configuração [auth_config_single_account.json](#auth_config_single_accountjson), esse arquivo de configuração tem `"account_mode" : "MULTIPLE"`, em vez de `"account_mode" : "SINGLE"`, porque este é um aplicativo de conta múltipla.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

Experimente o tutorial do Android para um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph para Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL para wiki da biblioteca do Android

Leia mais informações sobre a biblioteca MSAL para Android:

> [!div class="nextstepaction"]
> [MSAL para wiki da biblioteca do Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
