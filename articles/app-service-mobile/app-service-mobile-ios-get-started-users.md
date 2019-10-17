---
title: Adicionar autenticação no iOS com aplicativos móveis do Azure
description: Aprenda a usar os aplicativos móveis do Azure para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 800d86750f091404ee7f940d7cf8f6631e3fbbeb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388691"
---
# <a name="add-authentication-to-your-ios-app"></a>Adicione autenticação ao seu aplicativo do iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

Neste tutorial, você adicionará a autenticação ao projeto de [início rápido do iOS] usando um provedor de identidade com suporte. Este tutorial baseia-se no tutorial de [início rápido do iOS] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo.  Isso permite que o sistema de autenticação redirecione para seu aplicativo após a conclusão do processo de autenticação.  Neste tutorial, usamos sempre o esquema de URL _appname_.  No entanto, você pode usar o esquema de URL que quiser.  Ele deve ser exclusivo para seu aplicativo móvel.  Para habilitar o redirecionamento no lado do servidor:

1. No [Azure portal], selecione sua conta.

2. Clique na opção de menu **Autenticação/Autorização**.

3. Clique em **Azure Active Directory** na seção **Provedores de Autenticação**.

4. Defina o **Modo de gerenciamento** como **Avançado**.

5. Em **URLs de Redirecionamento Externo Permitidas**, insira `appname://easyauth.callback`.  O _appname_ na cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir as especificações de URL normal para um protocolo (use somente letras e números e inicie com uma letra).  Você deve anotar a cadeia de caracteres escolhida, já que precisará ajustar o código do aplicativo móvel com o esquema de URL em vários lugares.

6. Clique em **OK**.

7. Clique em **Save** (Salvar).

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **Executar** para iniciar o aplicativo. Uma exceção é criada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
**Objective-C**:

1. Em seu Mac, abra *QSTodoListViewController.m* em Xcode e adicione o seguinte método:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, deverá ter a lista de permissões de [domínios do Facebook][1] em seu aplicativo.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externo permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação a fim de voltar para seu aplicativo depois que a solicitação de autenticação é concluída.

2. Substitua `[self refresh]` em `viewDidLoad` em *QSTodoListViewController.m* pelo seguinte código:

    ```Objective-C
    [self loginAndGetData];
    ```

3. Abra o arquivo `QSAppDelegate.h`e adicione o seguinte código:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. Abra o arquivo `QSAppDelegate.m`e adicione o seguinte código:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Adicione o código diretamente antes da linha que tem `#pragma mark - Core Data stack`.  Substitua o _AppName_ pelo valor urlScheme que você usou na etapa 1.

5. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome de seu aplicativo) e adicione o seguinte código:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _appname_ (dentro da matriz de **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor de plist: clique no arquivo `AppName-Info.plist` no XCode para abrir o editor de plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo identificador do pacote Apple.

6. Pressione *Executar* para iniciar o aplicativo e faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

**Swift**:

1. Em seu Mac, abra *ToDoTableViewController.swift* em Xcode e adicione o seguinte método:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Altere *google* para *microsoftaccount*, *twitter*, *facebook* ou *windowsazureactivedirectory* se não estiver usando o Google como seu provedor de identidade. Se você usar o Facebook, deverá ter a lista de permissões de [domínios do Facebook][1] em seu aplicativo.

    Substitua o **urlScheme** por um nome exclusivo para seu aplicativo.  O urlScheme deve ser o mesmo que o protocolo de esquema de URL especificado no campo **URLs de redirecionamento externo permitidas** no portal do Azure. O urlScheme é usado pelo retorno de chamada de autenticação a fim de voltar para seu aplicativo depois que a solicitação de autenticação é concluída.

2. Remova as linhas `self.refreshControl?.beginRefreshing()` e `self.onRefresh(self.refreshControl)` ao final de `viewDidLoad()` em *ToDoTableViewController.swift*. Adicione uma chamada para `loginAndGetData()` em seu lugar:

    ```swift
    loginAndGetData()
    ```

3. Abra o arquivo `AppDelegate.swift` e adicione a seguinte linha à classe `AppDelegate`:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    Substitua o _AppName_ pelo valor urlScheme que você usou na etapa 1.

4. Abra o arquivo `AppName-Info.plist` (substituindo AppName pelo nome de seu aplicativo) e adicione o seguinte código:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Esse código deve ser colocado dentro do elemento `<dict>`.  Substitua a cadeia de caracteres _appname_ (dentro da matriz de **CFBundleURLSchemes**) pelo nome do aplicativo que você escolheu na etapa 1.  Você também pode fazer essas alterações no editor de plist: clique no arquivo `AppName-Info.plist` no XCode para abrir o editor de plist.

    Substitua a cadeia de caracteres `com.microsoft.azure.zumo` para **CFBundleURLName** pelo identificador do pacote Apple.

5. Pressione *Executar* para iniciar o aplicativo e faça logon. Após ter feito o logon você poderá exibir a lista de Tarefas pendentes e fazer atualizações.

A autenticação do Serviço de Aplicativo usa a comunicação interaplicativos da Apple.  Para obter mais detalhes sobre este assunto, consulte a [documentação da Apple][2]
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure portal]: https://portal.azure.com

[Início rápido do iOS]: app-service-mobile-ios-get-started.md

