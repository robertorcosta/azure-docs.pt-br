---
title: Guia de migração ADAL para MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda as diferenças entre o MSAL para iOS/macOS e a Biblioteca de Autenticação AD do Azure para ObjectiveC (ADAL). ObjC) e como migrar para o MSAL para iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085183"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrar aplicativos para MSAL para iOS e macOS

A Biblioteca de Autenticação de Diretório Ativo do Azure ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) foi criada para trabalhar com contas do Azure Active Directory através do ponto final v1.0.

A Biblioteca de Autenticação da Microsoft para iOS e macOS (MSAL) foi construída para trabalhar com todas as identidades da Microsoft, como contas do Azure Active Directory (Azure AD), contas pessoais da Microsoft e contas AD B2C do Azure através da plataforma de identidade Microsoft (formalmente o ponto final Azure AD v2.0).

A plataforma de identidade da Microsoft tem algumas diferenças importantes com o Azure Active Directory v1.0. Este artigo destaca essas diferenças e fornece orientações para migrar um aplicativo de ADAL para MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Diferenças de capacidade do aplicativo ADAL e MSAL

### <a name="who-can-sign-in"></a>Quem pode entrar

* O ADAL só suporta contas de trabalho e escola - também conhecidas como contas Ad do Azure.
* A MSAL suporta contas pessoais da Microsoft (contas MSA) como Hotmail.com, Outlook.com e Live.com.
* A MSAL suporta contas de trabalho e escola, e contas Azure AD B2C.

### <a name="standards-compliance"></a>Conformidade com as normas

* O ponto final da plataforma de identidade da Microsoft segue os padrões OAuth 2.0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

* O ponto final do Azure Active Directory v1.0 exige que todas as permissões sejam declaradas antecipadamente durante o registro da inscrição. Isso significa que essas permissões são estáticas.
* A plataforma de identidade da Microsoft permite que você solicite permissões dinamicamente. Os aplicativos podem pedir permissões apenas conforme necessário e solicitar mais conforme o aplicativo precisar deles.

Para saber mais sobre as diferenças entre o Azure Active Directory v1.0 e a plataforma de identidade da Microsoft, consulte [Por que atualizar para a plataforma de identidade microsoft (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="adal-and-msal-library-differences"></a>Diferenças de biblioteca SDAL e MSAL

A API pública MSAL reflete algumas diferenças importantes entre o Azure AD v1.0 e a plataforma de identidade Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication em vez de ADAuthenticationContext

`ADAuthenticationContext`é o primeiro objeto que um aplicativo ADAL cria. Representa uma instanciação de ADAL. Os aplicativos criam `ADAuthenticationContext` uma nova instância para cada combinação de nuvem e inquilino (autoridade) do Azure Active Directory. O `ADAuthenticationContext` mesmo pode ser usado para obter tokens para vários aplicativos públicos de clientes.

No MSAL, a interação `MSALPublicClientApplication` principal é através de um objeto, que é modelado após [OAuth 2.0 Cliente Público](https://tools.ietf.org/html/rfc6749#section-2.1). Uma instância `MSALPublicClientApplication` de pode ser usada para interagir com várias nuvens AAD e inquilinos, sem precisar criar uma nova instância para cada autoridade. Para a maioria `MSALPublicClientApplication` dos aplicativos, uma instância é suficiente.

### <a name="scopes-instead-of-resources"></a>Escopos em vez de recursos

No ADAL, um aplicativo *resource* teve que `https://graph.microsoft.com` fornecer um identificador de recursos como adquirir tokens do ponto final do Azure Active Directory v1.0. Um recurso pode definir uma série de escopos, ou oAuth2Permissões no manifesto do aplicativo, que ele entende. Isso permitiu que os aplicativos clientes solicitassem tokens desse recurso para um determinado conjunto de escopos pré-definidos durante o registro do aplicativo.

No MSAL, em vez de um único identificador de recursos, os aplicativos fornecem um conjunto de escopos por solicitação. Um escopo é um identificador de recursos seguido de um nome de permissão no recurso/permissão do formulário. Por exemplo, `https://graph.microsoft.com/user.read`

Existem duas maneiras de fornecer escopos no MSAL:

* Forneça uma lista de todas as permissões que seus aplicativos precisam. Por exemplo:  

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Neste caso, o aplicativo `directory.read` `directory.write` solicita as permissões. O usuário será solicitado a consentir com essas permissões se eles não tiverem consentido com elas antes para este aplicativo. O aplicativo também pode receber permissões adicionais que o usuário já consentiu para o aplicativo. O usuário só será solicitado a consentir com novas permissões ou permissões que não tenham sido concedidas.

* O escopo de `/.default`.

Este é o escopo embutido para cada aplicação. Refere-se à lista estática de permissões configuradas quando o aplicativo foi registrado. Seu comportamento é semelhante `resource`ao de . Isso pode ser útil ao migrar para garantir que um conjunto semelhante de escopos e experiência do usuário seja mantido.

Para usar `/.default` o escopo, adere `/.default` ao identificador de recursos. Por exemplo: `https://graph.microsoft.com/.default`. Se o seu recurso`/`terminar com uma `/.default`barra ( ), você ainda deve anexar , incluindo a`//`barra de avanço principal, resultando em um escopo que tem uma barra dupla para a frente ( ) nele.

Você pode ler mais informações sobre o uso do escopo "/.default" [aqui](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Suporte a diferentes tipos de WebView & navegadores

O ADAL só suporta UIWebView/WKWebView para iOS e WebView para macOS. O MSAL para iOS suporta mais opções para exibir conteúdo da Web `UIWebView`ao solicitar um código de autorização e não suporta mais; que pode melhorar a experiência e a segurança do usuário.

Por padrão, o MSAL no iOS usa [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), que é o componente web que a Apple recomenda para autenticação em dispositivos iOS 12+. Ele fornece benefícios de SSO de participação única através do compartilhamento de cookies entre aplicativos e o navegador Safari.

Você pode optar por usar um componente web diferente, dependendo dos requisitos do aplicativo e da experiência do usuário final que deseja. Consulte [os tipos de visualização da Web suportados](customize-webviews.md) para obter mais opções.

Ao migrar de ADAL para `WKWebView` MSAL, fornece ao usuário a experiência mais semelhante ao ADAL no iOS e macOS. Nós encorajamos `ASWebAuthenticationSession` você a migrar para o iOS, se possível. Para macOS, encorajamos `WKWebView`você a usar .

### <a name="account-management-api-differences"></a>Diferenças de API de gerenciamento de contas

Quando você chama os `acquireToken()` métodos ADAL ou `acquireTokenSilent()`, você recebe um `ADUserInformation` objeto contendo uma lista de reclamações do `id_token` que representa a conta que está sendo autenticada. Além disso, `ADUserInformation` `userId` retorna um `upn` com base na reivindicação. Após a aquisição inicial de tokens `userId` interativos, a ADAL espera que o desenvolvedor forneça todas as chamadas silenciosas.

A ADAL não fornece uma API para recuperar identidades de usuários conhecidas. Ele conta com o aplicativo para salvar e gerenciar essas contas.

A MSAL fornece um conjunto de APIs para listar todas as contas conhecidas pelo MSAL sem ter que adquirir um token.

Assim como a ADAL, a MSAL retorna `id_token`informações da conta que detém uma lista de reclamações do . É parte do `MSALAccount` objeto dentro `MSALResult` do objeto.

A MSAL fornece um conjunto de APIs para remover contas, tornando as contas removidas inacessíveis ao aplicativo. Depois que a conta for removida, chamadas posteriores de aquisição de tokens solicitarão ao usuário que faça a aquisição interativa de tokens. A remoção da conta só se aplica ao aplicativo cliente que o iniciou e não remove a conta dos outros aplicativos em execução no dispositivo ou no navegador do sistema. Isso garante que o usuário continue a ter uma experiência de SSO no dispositivo mesmo depois de sair de um aplicativo individual.

Além disso, o MSAL também retorna um identificador de conta que pode ser usado para solicitar um token silenciosamente mais tarde. No entanto, o identificador da `MSALAccount` conta (acessível através da `identifier` propriedade no objeto) não pode ser exibido e você não pode assumir em que formato ele está nem deve tentar interpretá-lo ou analisá-lo.

### <a name="migrating-the-account-cache"></a>Migrando o cache da conta

Ao migrar de ADAL, os aplicativos `userId`normalmente armazenam ADAL's, que não tem o `identifier` exigido pelo MSAL. Como uma etapa de migração única, um aplicativo pode consultar uma conta MSAL usando o UserId da ADAL com a seguinte API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Esta API lê o cache do MSAL e do ADAL para encontrar a conta pelo Usuário De usuário ADAL (UPN).

Se a conta for encontrada, o desenvolvedor deve usar a conta para fazer a aquisição silenciosa do token. A primeira aquisição silenciosa de token atualizará efetivamente a conta, e o desenvolvedor obterá um identificador de conta compatível com MSAL no resultado MSAL (`identifier`). Depois disso, `identifier` só deve ser usado para procurar contas usando a seguinte API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Embora seja possível continuar usando ADAL's `userId` para todas as `userId` operações no MSAL, já que é baseado na UPN, ele está sujeito a múltiplas limitações que resultam em uma má experiência do usuário. Por exemplo, se o UPN mudar, o usuário tem que fazer login novamente. Recomendamos que todos os aplicativos `identifier` usem a conta não exibida para todas as operações.

Leia mais sobre [migração de estado de cache](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Alterações na aquisição de tokens

A MSAL introduz algumas alterações de chamada de aquisição de tokens:

* Como a `acquireTokenSilent` ADAL, sempre resulta em um pedido silencioso.
* Ao contrário `acquireToken` do ADAL, sempre resulta em iU acionável do usuário, seja através da visualização da Web ou do aplicativo Microsoft Authenticator. Dependendo do estado SSO dentro da Webview/Microsoft Authenticator, o usuário pode ser solicitado a inserir suas credenciais.
* No `acquireToken` ADAL, `AD_PROMPT_AUTO` com a primeira tentativa de aquisição silenciosa de token, e só mostra a UI se a solicitação silenciosa falhar. No MSAL, essa lógica pode `acquireTokenSilent` ser alcançada `acquireToken` pela primeira chamada e apenas chamando se a aquisição silenciosa falhar. Isso permite que os desenvolvedores personalizem a experiência do usuário antes de iniciar a aquisição interativa de tokens.

### <a name="error-handling-differences"></a>Diferenças de manuseio de erros

A MSAL fornece mais clareza entre os erros que podem ser tratados pelo seu aplicativo e aqueles que requerem intervenção do usuário. Há um número limitado de erros que o desenvolvedor deve lidar:

* `MSALErrorInteractionRequired`: O usuário deve fazer uma solicitação interativa. Isso pode ser causado por várias razões, como uma sessão de autenticação expirada, a política de acesso condicional foi alterada, um token de atualização expirado ou foi revogado, não há tokens válidos no cache, e assim por diante.
* `MSALErrorServerDeclinedScopes`: A solicitação não foi totalmente concluída e alguns escopos não tiveram acesso. Isso pode ser causado por um usuário que declina o consentimento para um ou mais escopos.

O manuseio de todos [ `MSALError` ](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) os outros erros da lista é opcional. Você pode usar as informações nesses erros para melhorar a experiência do usuário.

Consulte [Manipulação de exceções e erros usando msal](msal-handling-exceptions.md) para obter mais informações sobre o manuseio de erros MSAL.

### <a name="broker-support"></a>Suporte a corretores

A MSAL, a partir da versão 0.3.0, oferece suporte para autenticação intermediada usando o aplicativo Microsoft Authenticator. O Microsoft Authenticator também permite o suporte para cenários de Acesso Condicional. Exemplos de cenários de acesso condicional incluem políticas de conformidade de dispositivos que exigem que o usuário inscreva o dispositivo através do Intune ou registre-se com o AAD para obter um token. E políticas de acesso condicional de gerenciamento de aplicativos móveis (MAM), que exigem prova de conformidade antes que seu aplicativo possa obter um token.

Para habilitar o corretor para sua aplicação:

1. Registre um formato URI compatível com corretor para a aplicação. O formato URI de `msauth.<app.bundle.id>://auth`redirecionamento compatível com corretor é . Substitua pelo `<app.bundle.id>` ID do pacote do seu aplicativo. Se você está migrando da ADAL e sua aplicação já era capaz de corretora, não há nada extra que você precisa fazer. Seu URI de redirecionamento anterior é totalmente compatível com o MSAL, para que você possa pular para o passo 3.

2. Adicione o esquema URI de redirecionamento do aplicativo ao seu arquivo info.plist. Para o URI de redirecionamento `msauth.<app.bundle.id>`msal padrão, o formato é . Por exemplo: 

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adicione os seguintes esquemas ao Info.plist do seu aplicativo em LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adicione o seguinte ao seu arquivo AppDelegate.m para lidar com retornos de chamadas: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Entre empresas (B2B)

No ADAL, você cria `ADAuthenticationContext` instâncias separadas de cada inquilino para o que o aplicativo solicita tokens. Isso não é mais um requisito no MSAL. No MSAL, você pode criar `MSALPublicClientApplication` uma única instância e usá-la para qualquer nuvem e organização AAD especificando uma autoridade diferente para adquirirToken e adquirir chamadasTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO em parceria com outros SDKs

O MSAL para iOS pode alcançar o SSO através de um cache unificado com os seguintes SDKs:

- Objetivo ADAL-C 2.7.x+
- MSAL.NET para Xamarin 2.4.x+
- ADAL.NET para Xamarin 4.4.x+

O SSO é alcançado através do compartilhamento de chaveiros iOS e só está disponível entre aplicativos publicados a partir da mesma conta do Apple Developer.

O SSO através do compartilhamento de chaveiros iOS é o único tipo silencioso de SSO.

No macOS, o MSAL pode alcançar o SSO com outros MSAL para aplicativos baseados em iOS e macOS e aplicativos baseados em ADAL Objective-C.

O MSAL no iOS também suporta dois outros tipos de SSO:

* SSO através do navegador da web. MSAL para suporte `ASWebAuthenticationSession`siOS , que fornece SSO através de cookies compartilhados entre outros aplicativos no dispositivo e especificamente no navegador Safari.
* SSO através de um corretor de autenticação. Em um dispositivo iOS, o Microsoft Authenticator atua como o corretor de autenticação. Ele pode seguir políticas de acesso condicional, como exigir um dispositivo compatível, e fornece SSO para dispositivos registrados. Os SDKs MSAL que começam com a versão 0.3.0 suportam um corretor por padrão.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

O [Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) suporta MSAL para iOS a partir da versão [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL no mesmo aplicativo

A versão 2.7.0 do ADAL, e acima, não pode coexistir com o MSAL no mesmo aplicativo. A principal razão é por causa do código comum do submódulo compartilhado. Como o Objective-C não suporta namespaces, se você adicionar estruturas ADAL e MSAL à sua aplicação, haverá duas instâncias da mesma classe. Não há garantia de qual deles é escolhido em tempo de execução. Se ambos os SDKs estiverem usando a mesma versão da classe conflitante, seu aplicativo ainda pode funcionar. No entanto, se for uma versão diferente, seu aplicativo pode sofrer falhas inesperadas que são difíceis de diagnosticar.

A execução de ADAL e MSAL no mesmo aplicativo de produção não é suportada. No entanto, se você estiver apenas testando e migrando seus usuários do ADAL Objective-C para o MSAL para iOS e macOS, você pode continuar usando [o ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). É a única versão que funciona com MSAL no mesmo aplicativo. Não haverá novas atualizações de recursos para esta versão ADAL, por isso deve ser usada apenas para fins de migração e teste. Seu aplicativo não deve contar com a convivência ADAL e MSAL a longo prazo.

A coexistência ADAL e MSAL no mesmo aplicativo não é suportada.
A coexistência ADAL e MSAL entre vários aplicativos é totalmente suportada.

## <a name="practical-migration-steps"></a>Etapas práticas de migração

### <a name="app-registration-migration"></a>Migração de registro de aplicativos

Você não precisa alterar seu aplicativo AAD existente para mudar para MSAL e habilitar contas AAD. No entanto, se o seu aplicativo baseado em ADAL não suportar autenticação intermediada, você precisará registrar um novo URI de redirecionamento para o aplicativo antes de poder mudar para O MSAL.

O URI de redirecionamento `msauth.<app.bundle.id>://auth`deve estar neste formato: . Substitua pelo `<app.bundle.id>` ID do pacote do seu aplicativo. Especifique o URI de redirecionamento no [portal Azure](https://aka.ms/MobileAppReg).

Apenas para iOS, para suportar a autenticação baseada em cert, um URI de redirecionamento adicional precisa `msauth://code/<broker-redirect-uri-in-url-encoded-form>`ser registrado em seu aplicativo e no portal Azure no seguinte formato: . Por exemplo, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Recomendamos que todos os aplicativos registrem URIs de redirecionamento.

Se você deseja adicionar suporte para consentimento incremental, selecione as APIs e permissões que seu aplicativo está configurado para solicitar acesso no registro do aplicativo na guia permissões da **API.**

Se você está migrando da ADAL e deseja dar suporte a contas AAD e MSA, seu registro de aplicativo existente precisa ser atualizado para suportar ambos. Não recomendamos que você atualize seu aplicativo de produção existente para suportar tanto a AAD quanto a MSA imediatamente. Em vez disso, crie outro ID do cliente que suporte tanto a AAD quanto o MSA para testes, e depois de verificar que todos os cenários funcionam, atualize o aplicativo existente.

### <a name="add-msal-to-your-app"></a>Adicione MSAL ao seu aplicativo

Você pode adicionar o MSAL SDK ao seu aplicativo usando sua ferramenta de gerenciamento de pacotes preferida. Veja [instruções detalhadas aqui](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Atualize o arquivo Info.plist do seu aplicativo

Somente para iOS, adicione o esquema URI de redirecionamento do aplicativo ao seu arquivo info.plist. Para aplicativos compatíveis com corretores ADAL, ele já deve estar lá. O esquema DE URI de redirecionamento `msauth.<app.bundle.id>`msal padrão estará no formato: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adicione os seguintes esquemas ao Info.plist do seu aplicativo em `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Atualize seu código AppDelegate

Somente para iOS, adicione o seguinte ao seu arquivo AppDelegate.m:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Se você estiver usando xcode 11,** você deve `SceneDelegate` colocar a chamada msal de volta no arquivo em vez disso.
Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois arquivos.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Isso permite que a MSAL manuseie respostas do corretor e do componente web.
Isso não era necessário no ADAL, uma vez que ele "girava" métodos de delegado de aplicativos automaticamente. Adicioná-lo manualmente é menos propenso a erros e dá ao aplicativo mais controle.

### <a name="enable-token-caching"></a>Ativar cache de token

Por padrão, a MSAL armazena em cache os tokens do seu aplicativo nos conjuntos de chaves do iOS ou macOS. 

Para habilitar o cache de token:
1. Verifique se seu aplicativo está assinado corretamente
2. Vá para a sua **guia** > Configurações do projeto Xcode > Recursos**Habilitar o compartilhamento de chaveiros**
3. Clique **+** e insira a seguinte entrada de Grupos `com.microsoft.adalcache` de **Chaveiros:** 3.a Para iOS, digite 3.b Para entrar no macOS`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Crie o MSALPublicClientApplication e mude para o seu Token adquirido e adquira chamadasTokeSilent

Você pode `MSALPublicClientApplication` criar usando o seguinte código:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Em seguida, ligue para a API de gerenciamento de contas para ver se há alguma conta no cache:

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



ou ler todas as contas:

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Se uma conta for encontrada, `acquireTokenSilent` ligue para a API Do MSAL:

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
