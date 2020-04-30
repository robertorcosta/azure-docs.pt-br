---
title: Entrar e sair com os usuários e chamar o Microsoft Graph (Android) – Plataforma de identidade da Microsoft | Azure
description: Obtenha um token de acesso e chame o Microsoft Graph ou as APIs que exigem tokens de acesso por meio da plataforma de identidade da Microsoft (Android)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b899e1d651f41c9c1e1e54af1b5ec19162dfc28d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380052"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Tutorial: Entrar com os usuários e chamar o Microsoft Graph em um aplicativo Android 

>[!NOTE]
>Este tutorial demonstra exemplos simplificados de como trabalhar com o MSAL para Android. Para simplificar, este tutorial usa apenas o modo de conta única. Você também pode exibir o repositório e clonar [o aplicativo de exemplo pré-configurado](https://github.com/Azure-Samples/ms-identity-android-java/) para explorar cenários mais complexos. Exiba o [Início Rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) para obter mais informações sobre o aplicativo de exemplo, a configuração e o registro. 

Neste tutorial, você aprenderá a integrar seu aplicativo Android com a plataforma de identidade da Microsoft usando a Biblioteca de Autenticação da Microsoft para Android. Você aprenderá a entrar e sair com um usuário, obter um token de acesso para chamar a API do Microsoft Graph e fazer uma solicitação à API do Graph. 

> [!div class="checklist"]
> * Integrar seu aplicativo Android à Plataforma de Identidade da Microsoft 
> * Conectar um usuário 
> * Obter um token de acesso para chamar a API do Microsoft Graph 
> * Chamar a API do Microsoft Graph 
> * Desconectar um usuário 

Após concluir este tutorial, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras), bem como contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-this-tutorial-works"></a>Como este tutorial funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

O aplicativo deste tutorial conectará usuários e obterá dados no nome deles. Esses dados serão acessados por meio de uma API protegida (API do Microsoft Graph) que exige autorização e é protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator e pelo Portal da Empresa do Intune.
* O usuário final aceitará as permissões solicitadas por seu aplicativo.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph.

Esta amostra usa a MSAL (Biblioteca de Autenticação da Microsoft) para Android para implementar a Autenticação: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 A MSAL automaticamente renovará tokens, fornecerá o SSO (logon único) entre outros aplicativos no dispositivo e ajudará a gerenciar as contas.

### <a name="prerequisites"></a>Pré-requisitos

* Este tutorial exige o Android Studio versão 3.5+

## <a name="create-a-project"></a>Criar um projeto
Se você ainda não tiver um aplicativo Android, siga estas etapas para configurar um novo projeto. 

1. Abra o Android Studio e selecione **Iniciar um novo projeto do Android Studio**.
2. Selecione **Atividade Básica** e, em seguida, **Avançar**.
3. Nome do seu aplicativo.
4. Guarde o nome do pacote. Você o inserirá mais tarde no portal do Azure.
5. Altere a linguagem de **Kotlin** para **Java**.
6. Defina o **Nível mínimo da API** como **API 19** ou superior e clique em **Concluir**.
7. Na exibição do projeto, escolha **Projeto** na lista suspensa para exibir os arquivos do projeto que são e que não são a fonte, abra **app/build.gradle** e defina `targetSdkVersion` como `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integrar com a Biblioteca de Autenticação da Microsoft 

### <a name="register-your-application"></a>Registre seu aplicativo

1. Vá para o [Portal do Azure](https://aka.ms/MobileAppReg).
2. Abra a folha [Registros de aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) e clique em **+Novo registro**.
3. Insira um **Nome** para seu aplicativo e, em seguida, **sem** definir um URI de Redirecionamento, clique em **Registrar**.
4. Na seção **Gerenciar** do painel que aparece, selecione **Autenticação** >  **+ Adicionar uma plataforma** > **Android**. (Talvez seja necessário selecionar "Alternar para a nova experiência" perto da parte superior da folha para ver esta seção)
5. Insira o nome do pacote do seu projeto. Se você baixou o código, esse valor é `com.azuresamples.msalandroidapp`.
6. Na seção **Hash de assinatura** da página **Configurar seu aplicativo Android**, clique em **Gerar um hash de assinatura de desenvolvimento.** e copie o comando KeyTool para usar para sua plataforma.

   > [!Note]
   > KeyTool.exe é instalado como parte do JDK (Java Development Kit). Você também precisará instalar a ferramenta OpenSSL para executar o comando KeyTool. Veja a [documentação do Android sobre como gerar uma chave](https://developer.android.com/studio/publish/app-signing#generate-key) para obter mais informações. 

7. Insira o **Hash de assinatura** gerado por KeyTool.
8. Clique em `Configure` e salve a **Configuração da MSAL** exibida na página **Configuração do Android** para que você possa inseri-la quando configurar o aplicativo mais tarde.  Clique em **Concluído**.

### <a name="configure-your-application"></a>Configurar seu aplicativo 

1. No painel do projeto do Android Studio, navegue até **app\src\main\res**.
2. Clique com o botão direito do mouse em **res** e escolha **Novo** > **Diretório**. Insira `raw` como o nome do novo diretório e clique em **OK**.
3. Em **app** > **src** > **main** > **res** > **raw**, crie um arquivo JSON chamado `auth_config_single_account.json` e cole a Configuração da MSAL salva anteriormente. 

    Abaixo do URI de Redirecionamento, cole: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Seu arquivo de configuração deve ser semelhante a este exemplo: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
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
    
   >[!NOTE]
   >Este tutorial demonstra apenas como configurar um aplicativo no modo de conta única. Exiba a documentação para obter mais informações sobre o [modo de conta única versus várias contas](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) e [como configurar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. Em **app** > **src** > **main** > **AndroidManifest.xml**, adicione a atividade `BrowserTabActivity` abaixo ao corpo do aplicativo. Essa entrada permite que a Microsoft faça uma chamada de retorno ao aplicativo após concluir a autenticação:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Substitua o nome do pacote que você registrou no portal do Azure para o valor `android:host=`.
    Substitua o hash da chave que você registrou no portal do Azure para o valor `android:path=`. O hash de assinatura **não** deve ser codificado por URL. Verifique se há um `/` inicial no começo do seu hash de assinatura. 
    >[!NOTE]
    >O "Nome do pacote" com o qual você substituirá o valor de `android:host` deverá ser semelhante a: "com.azuresamples.msalandroidapp". O "Hash de assinatura" com o qual você que substituirá o valor de `android:path` deverá ser semelhante a: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=". Você também será capaz de encontrar esses valores na folha de autenticação do registro do aplicativo. Observe que o URI de redirecionamento será semelhante a: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Embora o Hash de assinatura seja codificado por URL ao final desse valor, o Hash de assinatura **não** deve ser codificado por URL no valor de `android:path`. 

## <a name="use-msal"></a>Usar a MSAL 

### <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. Na janela do projeto do Android Studio, navegue até **app** > **src** > **gradle** e adicione o seguinte: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Mais sobre o SDK do Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Importações necessárias 

Adicione o seguinte à parte superior do **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Instanciar o PublicClientApplication
#### <a name="initialize-variables"></a>Inicializar variáveis 
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Dentro da classe `MainActivity`, consulte o método onCreate() a seguir para instanciar a MSAL usando o `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initializeUI
Ouça os botões e chame os métodos ou erros de log correspondentes. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> A saída com a MSAL remove todas as informações conhecidas sobre um usuário deste aplicativo, mas o usuário ainda terá uma sessão ativa em seu dispositivo. Se o usuário tentar entrar novamente, ele poderá ver a interface do usuário de entrada, mas talvez não precise inserir novamente suas credenciais, pois a sessão do dispositivo ainda estará ativa. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Retorno de chamada usado para solicitações interativas.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Retorno de chamada usado para solicitações silenciosas 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Chamar a API do Microsoft Graph 

O código a seguir demonstra como chamar o GraphAPI usando o Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Adicionar a interface do usuário
### <a name="activity"></a>Atividade 
Se você quiser modelar a interface do usuário com base nesse tutorial, os métodos a seguir fornecerão um guia para atualizar o texto e ouvir os botões.

#### <a name="updateui"></a>updateUI
Habilitar/Desabilitar botões com base no estado de entrada e definir textos.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Método para atualizar textos na interface do usuário a fim de refletir a saída. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout 

Arquivo `activity_main.xml` de exemplo para exibir botões e caixas de texto. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testar seu aplicativo

### <a name="run-locally"></a>Executar localmente

Compilar e implantar o aplicativo em um emulador ou dispositivo de teste. Você deve conseguir conectar e obter tokens para o Azure AD ou contas Microsoft pessoais.

Após entrar, este aplicativo exibirá os dados retornados do ponto de extremidade `/me` do Microsoft Graph.

### <a name="consent"></a>Consentimento

Na primeira vez que um usuário entrar no seu aplicativo, ele será solicitado pela identidade da Microsoft a consentir com as permissões solicitadas. Alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores forneçam consentimento em nome de todos os usuários. Para dar suporte a esse cenário, você precisará criar seu próprio locatário ou receber o consentimento do administrador. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o objeto de aplicativo criado na etapa [Registrar seu aplicativo](#register-your-application).

## <a name="get-help"></a>Obter ajuda

Visite [Ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver algum problema com este tutorial ou com a plataforma de identidade da Microsoft.
