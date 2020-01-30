---
title: Usando o modo de dispositivo compartilhado com o MSAL para Android | Azure
description: Saiba como preparar um dispositivo Android para ser executado no modo compartilhado e executar um aplicativo de trabalhador de contato direto.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 98882ad115ff977cfd8222c6055a436855f50c04
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701239"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Tutorial: Usar o modo de dispositivo compartilhado em seu aplicativo Android

> [!NOTE]
> Esse recurso está em uma versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="developer-guide"></a>Guia do desenvolvedor

Este guia fornece orientação aos desenvolvedores para implementar o modo de dispositivo compartilhado em um aplicativo Android usando o MSAL (Biblioteca de Autenticação da Microsoft). Confira o [tutorial do MSAL para Android](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android) para saber como integrar o MSAL ao seu aplicativo Android, conectar um usuário, chamar o Microsoft Graph e sair de um usuário.

### <a name="download-the-sample"></a>Baixar o exemplo

Clone o [aplicativo de exemplo](https://github.com/Azure-Samples/ms-identity-android-java/) do GitHub. O exemplo tem a capacidade de trabalhar em [modo de conta única ou modo de contas múltiplas](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>Adicionar o SDK do MSAL ao seu repositório Maven local

Se você não estiver usando o aplicativo de exemplo, adicione a biblioteca do MSAL como uma dependência no arquivo build.gradle, desta forma:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Configurar seu aplicativo para usar o modo de dispositivo compartilhado

Veja a [documentação de configuração](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration) para obter mais informações sobre como configurar o arquivo de configuração.

Defina `"shared_device_mode_supported"` como `true` no arquivo de configuração do MSAL.

Talvez você não esteja planejando dar suporte ao modo de várias contas. Isso poderá ocorrer se você não estiver usando um dispositivo compartilhado e o usuário puder entrar no aplicativo com mais de uma conta ao mesmo tempo. Nesse caso, defina `"account_mode"` como `"SINGLE"`. Isso garante que seu aplicativo sempre obterá `ISingleAccountPublicClientApplication` e simplifica significativamente a integração do MSAL. O valor padrão de `"account_mode"` é `"MULTIPLE"`, portanto é importante alterar esse valor no arquivo de configuração se você está usando o modo `"single account"`.

Aqui está um exemplo do arquivo auth_config.json incluído no diretório **aplicativo**>**principal**>**res**>**bruto** do aplicativo de exemplo:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Detectar modo de dispositivo compartilhado

O modo de dispositivo compartilhado permite configurar dispositivos Android para serem compartilhados por vários funcionários enquanto fornece o gerenciamento de Microsoft Identity do dispositivo. Os funcionários podem conectar em seus dispositivos e acessar as informações do cliente rapidamente. Quando eles concluírem seus turnos ou tarefas, eles poderão sair de todos os aplicativos no dispositivo compartilhado com um clique e o dispositivo estará imediatamente pronto para o uso do próximo funcionário.

Use `isSharedDevice()` para determinar se um aplicativo está em execução em um dispositivo que esteja no modo de dispositivo compartilhado. Seu aplicativo poderia usar esse sinalizador para determinar se ele deve modificar o UX adequadamente.

Aqui está um snippet de código que mostra como você poderia usar `isSharedDevice()`.  Está na classe `SingleAccountModeFragment` no aplicativo de exemplo:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Inicializar o objeto PublicClientApplication

Se você definir `"account_mode":"SINGLE"` no arquivo de configuração do MSAL, você poderá converter com segurança o objeto de aplicativo retornado como um `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/ 
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config, 
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});  
```

### <a name="detect-single-vs-multiple-account-mode"></a>Detectar modo único versus modo de várias contas

Se você estiver escrevendo um aplicativo que será usado apenas por trabalhadores de contato direto em um dispositivo compartilhado, recomendamos que você codifique seu aplicativo para dar suporte apenas ao modo de conta única. Isso inclui a maioria dos aplicativos focados em tarefas, como os aplicativos de registros médicos, aplicativos de fatura e a maioria dos aplicativos de linha de negócios. Isso simplifica o desenvolvimento, pois muitos recursos do SDK não precisarão ser acomodados.

Se o aplicativo dá suporte a várias contas, bem como ao modo de dispositivo compartilhado, você deve executar uma verificação de tipo e converter para a interface apropriada, conforme mostrado abaixo.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obter o usuário conectado e determinar se um usuário foi alterado no dispositivo

O método `loadAccount` recupera a conta do usuário conectado. O método `onAccountChanged` determina se o usuário conectado mudou e, nesse caso, limpa:

```java 
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override 
    public void onError(@NonNull Exception exception) 
    {
    }
  }
}  
```

### <a name="globally-sign-in-a-user"></a>Conectar um usuário globalmente

O seguinte conecta um usuário em todo o dispositivo para outros aplicativos que usam o MSAL com o Aplicativo Authenticator:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Desconectar um usuário globalmente

O seguinte remove a conta conectada e limpa os tokens em cache não apenas do aplicativo, mas também do dispositivo que está no modo de dispositivo compartilhado:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Guia do administrador

As etapas a seguir descrevem como configurar seu aplicativo no portal do Azure e como colocar seu dispositivo no modo de dispositivo compartilhado.

### <a name="register-your-application-in-azure-active-directory"></a>Registrar seu aplicativo no Azure Active Directory

Primeiro, registre seu aplicativo em seu locatário organizacional. Em seguida, forneça esses valores abaixo em auth_config.json para que seu aplicativo seja executado corretamente.

Para obter informações sobre como fazer isso, confira [Registrar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application).

> [!NOTE]
> Ao registrar seu aplicativo, use o guia de início rápido no lado esquerdo e, em seguida, selecione **Android**. Isso levará a uma página em que você deverá fornecer o **Nome do Pacote** e o **Hash de Assinatura** para seu aplicativo. Isso é muito importante para garantir que a configuração do aplicativo funcione. Em seguida, você receberá um objeto de configuração que pode ser usado para seu aplicativo e que será recortado e colado em seu arquivo auth_config.json.

![Tela de registro do aplicativo](media/tutorial-v2-shared-device-mode/register-app.png) Você deve selecionar **Fazer essa alteração para mim** e, em seguida, fornecer os valores solicitados pelo início rápido no portal do Azure. Feito isso, geraremos todos os arquivos de configuração necessários.

![Tela de informações de configuração de aplicativo](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>Configurar um locatário

Para fins de teste, configure o seguinte em seu locatário: pelo menos dois funcionários, um Administrador de dispositivo de nuvem e um Administrador global. No portal do Azure, defina o Administrador de Dispositivo de Nuvem modificando Funções Organizacionais. No portal do Azure, acesse suas Funções Organizacionais selecionando **Azure Active Directory** > **Funções e Administradores** > **Administrador de Dispositivo de Nuvem**. Adicione os usuários que poderão colocar um dispositivo no modo compartilhado.

## <a name="set-up-an-android-device-in-shared-mode"></a>Configurar um dispositivo Android para modo compartilhado

### <a name="download-the-authenticator-app"></a>Baixar o Aplicativo Authenticator

Baixe o aplicativo Microsoft Authenticator na Google Play Store. Se o aplicativo já estiver baixado, verifique se é a versão mais recente.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Configurações do aplicativo Authenticator & registrar o dispositivo na nuvem

Inicie o aplicativo Authenticator e navegue até a página da conta principal. Depois de ver a página **Adicionar conta**, você estará pronto para tornar o dispositivo compartilhado.

![Tela de adição de conta do Authenticator](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 Vá para o painel de **Configurações** usando a barra de menus à direita. Selecione **Registro de Dispositivo** em **Contas de Trabalho & Estudo**.
 
 ![Tela de adição de conta do Authenticator](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 Ao clicar nesse botão, será solicitado que você autorize o acesso aos contatos do dispositivo. Isso ocorre devido à integração de conta do Android no dispositivo. Escolha **permitir**.

 ![Tela de adição de conta do Authenticator](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

O Administrador de Dispositivo de Nuvem deve inserir seu email organizacional em **Ou registre-se como um dispositivo compartilhado**. Em seguida, clique no botão **registrar-se como dispositivo compartilhado** e insira suas credenciais.

![tela de registro de dispositivo](media/tutorial-v2-shared-device-mode/register-device.png)

![entrada](media/tutorial-v2-shared-device-mode/sign-in.png)

Agora o dispositivo está no modo compartilhado.

![tela de registro de dispositivo](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 As entradas e saídas no dispositivo serão globais, o que significa que se aplicam a todos os aplicativos integrados ao MSAL e ao Microsoft Authenticator no dispositivo. Agora você pode implantar aplicativos que usam recursos de modo de dispositivo compartilhado.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Exibir dispositivos compartilhados no portal do Azure

Depois de colocar um dispositivo no modo compartilhado, ele se torna conhecido em sua organização e é acompanhado em seu locatário organizacional. Você pode exibir dispositivos compartilhados examinando o **Tipo de Junção** na folha do Azure Active Directory do portal do Azure.

![Folha de todos os dispositivos no portal do Azure](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>Executando o aplicativo de exemplo

O Aplicativo de Exemplo é um aplicativo simples que chamará a API do Graph de sua organização. Na primeira execução você será solicitado a consentir, pois o aplicativo é novo na sua conta de funcionário.

![Tela de informações de configuração de aplicativo](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o modo compartilhado em [Modo de dispositivo compartilhado para dispositivos Android](shared-device-mode.md)