---
title: Envie notificações push para aplicativos Swift iOS que usam hubs de notificação do Azure | Microsoft Docs
description: Saiba como empurrar notificações para aplicativos Swift iOS que usam hubs de notificação do Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: a721c519c7a836e20455c6f1887bcfa7b52951f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336632"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-api"></a>Tutorial: Envie notificações push para aplicativos Swift iOS usando a API DO Notification Hubs REST

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, você usa o Azure Notification Hubs para empurrar notificações para um aplicativo iOS baseado em Swift usando a [API REST](/rest/api/notificationhubs/). Você também cria um aplicativo para iOS em branco que recebe notificações push usando o [serviço apple push notification (APNs).](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)

Este tutorial leva você através das seguintes etapas:

> [!div class="checklist"]
> * Gerar o arquivo de solicitação de assinatura de certificado.
> * Solicite seu aplicativo para notificações push.
> * Crie um perfil de provisionamento para o aplicativo.
> * Criar um hub de notificação.
> * Configure o hub de notificação com informações de APNs.
> * Conecte seu aplicativo para iOS a um centro de notificação.
> * Teste a solução.

O código completo deste tutorial pode ser encontrado [no GitHub](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_rest).

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, você precisa:

- Para passar pela [visão geral do Azure Notification Hubs](notification-hubs-push-notification-overview.md) se você não estiver familiarizado com o serviço.
- Para saber mais sobre [inscrições e instalação.](notification-hubs-push-notification-registration-management.md)
- Uma [conta](https://developer.apple.com)ativa do Desenvolvedor Apple .
- Um Mac executando o Xcode juntamente com um certificado de desenvolvedor válido instalado em seu Chaveiro.
- Um dispositivo iPhone físico com o que você pode executar e depurar, já que você não pode testar notificações push com o simulador.
- Seu dispositivo iPhone físico está registrado no Portal da [Apple](https://developer.apple.com) e associado ao seu certificado.
- Uma [assinatura do Azure](https://portal.azure.com) onde você pode criar e gerenciar recursos.

Mesmo que você não tenha experiência prévia com o desenvolvimento do iOS, você deve ser capaz de seguir os passos para criar este exemplo de princípios. No entanto, você se beneficiará da familiaridade com os seguintes conceitos:

- Construindo aplicativos para iOS com Xcode e Swift.
- Configurando [um hub de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para iOS.
- O [Portal de Desenvolvedores da Apple](https://developer.apple.com) e o portal [Azure.](https://portal.azure.com)

> [!NOTE]
> O centro de notificação será configurado apenas para usar o modo de autenticação **Sandbox.** Você não deve usar este modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Conecte seu aplicativo para iOS a um centro de notificação

Nesta seção, você criará o aplicativo para iOS que se conectará ao centro de notificação.  

### <a name="create-an-ios-project"></a>Crie um projeto para iOS

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .

1. Ao definir as opções para o novo projeto:

   1. Especifique o **nome do produto** (PushDemo) e o **identificador de organização** ()`com.<organization>`que você usou ao definir o **Identificador de Pacote sustal** no Portal do Desenvolvedor da Apple.

   1. Escolha a **equipe** para a qual o **ID do aplicativo** foi configurado.

   1. Defina a **linguagem** como **Swift**.

   1. Selecione **Avançar**.

1. Crie uma nova pasta chamada **SupportFiles**.

1. Crie um novo arquivo de lista p chamado **devsettings.plist** na pasta **'Arquivos de suporte'.** Certifique-se de adicionar essa pasta ao seu arquivo **gitignore** para que ele não seja comprometido ao trabalhar com um repo git. Em um aplicativo de produção, você provavelmente estaria condicionando esses segredos como parte de um processo de compilação automatizado. Tais configurações não estão cobertas neste passo a passo.

1. Atualize **devsettings.plist** para incluir as seguintes entradas de configuração usando seus próprios valores do centro de notificação que você provisionou:

   | Chave                            | Type                     | Valor                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<> hubKey                  |
   | notificaçãoHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificaçãoHubNamespace       | String                   | \<hubNamespace>            |

   Você pode encontrar os valores necessários navegando até o recurso do centro de notificação no portal Azure. Em particular, os valores **de notificationHubName** e **notificationHubNamespace** estão no canto superior direito do resumo **Essentials** na página **Visão Geral.**

   ![Resumo do Notification Hubs Essentials](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Você também pode encontrar os valores **de notificaçãoHubKeyName** e **notificationHubKey** navegando para `DefaultFullSharedAccessSignature`políticas de **acesso** e selecionando a respectiva Política de **Acesso,** como . Depois disso, copie da cadeia de **conexão primária** o valor prefixado `SharedAccessKeyName=` com for `notificationHubKeyName` e o valor prefixado com `SharedAccessKey=` o `notificationHubKey`.

   A seqüência de conexões deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para mantê-lo `DefaultFullSharedAccessSignature` simples, especifique para que você possa usar o token para enviar notificações. Na `DefaultListenSharedAccessSignature` prática, seria uma melhor escolha para situações em que você só quer receber notificações.

1. Em **Project Navigator,** selecione o Nome do **projeto** e selecione a guia **Geral.**

1. Encontre **identidade** e defina o valor do **identificador de pacote para** que ele corresponda, `com.<organization>.PushDemo`que é o valor usado para o **ID** do aplicativo de uma etapa anterior.

1. Encontre **recursos de & de assinatura**e selecione a **equipe** apropriada para sua conta de **desenvolvedor apple**. O valor **da Equipe** deve coincidir com o que você criou seus certificados e perfis.

1. O Xcode deve puxar automaticamente o valor apropriado **do perfil de provisionamento** com base no **identificador de pacotes**. Se você não ver o novo valor do **perfil de provisionamento,** tente atualizar os perfis da **Identidade de Assinatura** selecionando a**Conta** **de Preferências** >  **do Xcode** > e selecione o botão **Perfis Manuais de Download** para baixar os perfis.

1. Ainda na guia **Recursos de & de assinatura,** clique no botão + **Capacidade** e toque duas vezes em **Notificações** push da lista para garantir que as **Notificações push** estejam ativadas.

1. Abra seu arquivo **AppDelegate.swift** para implementar o protocolo **UNUserNotificationCenterDelegate** e adicione o seguinte código ao topo da classe:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        
        ...
    }
    ```

    Você vai usar esses membros mais tarde. Especificamente, você usará o membro de **tags** como parte do registro usando um **modelo personalizado**. Para obter mais informações sobre tags, consulte [Tags para inscrições](notification-hubs-tags-segment-push-message.md) e registros de [modelos](notification-hubs-templates-cross-platform-push-messages.md).

1. No mesmo arquivo, adicione o seguinte código à função **didFinishLaunchingWithOptions:**

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Esse código recupera os valores de configuração de **devsettings.plist,** define a classe **AppDelegate** como o delegado **do UNUserNotificationCenter,** solicita autorização para notificações push e, em seguida, chama **o registerForRemoteNotifications**.

    Para simplificar, o código suporta *o iOS 10 e apenas posterior*. Você pode adicionar suporte para versões anteriores do SO usando condicionalmente as respectivas APIs e abordagens como você normalmente faria.

1. No mesmo arquivo, adicione as seguintes funções:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        let keyWindow = UIApplication.shared.windows.filter {$0.isKeyWindow}.first
        keyWindow?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    O código usa os valores **installationId** e **pushChannel** para registrar-se no centro de notificação. Neste caso, você está usando **UIDevice.current.identifierForVendor** para fornecer um valor único para identificar o dispositivo e, em seguida, formatar o **dispositivoToken** para fornecer o valor **pushChannel** desejado. A função **showAlert** existe simplesmente para exibir algum texto de mensagem para fins de demonstração.

1. Ainda no arquivo **AppDelegate.swift,** adicione as funções **willPresent** e **didReceive** no **UNUserNotificationCenterDelegate**. Essas funções exibem um alerta quando são notificadas de que um aplicativo está sendo executado em primeiro plano ou em segundo plano, respectivamente.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Adicione instruções de impressão na parte inferior da função **didRegisterForRemoteNotificationsWithDeviceToken** para verificar se **o Id e** **o pushChannel** estão sendo atribuídos valores.

    ```swift
    print(installationId)
    print(pushChannel)
    ```

1. Crie as **pastas Modelos,** **Serviços**e **Utilitários** para os componentes fundamentais que você adicionará ao projeto mais tarde.

1. Verifique se o projeto é construído e executado em um dispositivo físico. Notificações push não podem ser testadas usando o simulador.

### <a name="create-models"></a>Criar modelos

Nesta etapa, você criará um conjunto de modelos para representar as cargas [de aPI do Notification Hubs REST](/rest/api/notificationhubs/) e armazenar os dados de token de assinatura de acesso compartilhado (SAS) necessários.

1. Adicione um novo arquivo Swift chamado **PushTemplate.swift** à pasta **Modelos.** Este modelo fornece uma estrutura representando o **CORPO** de um modelo individual como parte da carga de instalação do **dispositivo.**

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **DeviceInstallation.swift** à pasta **Models.** Este arquivo define uma estrutura representando a carga útil para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código ao arquivo:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **TokenData.swift** à pasta **Models.** Este modelo será usado para armazenar um token SAS junto com sua expiração.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Gerar um token SAS

Os Hubs de Notificação usam a mesma infra-estrutura de segurança do Azure Service Bus. Para chamar a API REST, você precisará [gerar programáticamente um token SAS](/rest/api/eventhub/generate-sas-token) que pode ser usado no cabeçalho de **autorização** da solicitação.  

O token resultante será no seguinte formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O processo em si envolve os mesmos seis passos-chave:  

1. Calculando o vencimento no formato [de tempo unix época,](https://en.wikipedia.org/wiki/Unix_time) o que significa o número de segundos decorridos desde a meia-noite Horário Universal Coordenado, 1 de janeiro de 1970.
1. Formatação do **ResourceUrl** que representa o recurso que você está tentando acessar para que ele seja codificado por cento e minúsculo. O **ResourceUrl** tem `'https://<namespace>.servicebus.windows.net/<hubName>'`o formulário .
1. Preparando o **StringToSign,** que é `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`formatado como .
1. Computação e Base64 codificando a **Assinatura** usando o hash HMAC-SHA256 do valor **StringToSign.** O valor de hash é usado com a **parte-chave** da **string de conexão** para a respectiva **Regra de Autorização**.
1. Formatação da **Assinatura** codificada Base64 para que seja codificada por cento.
1. Construir o token no formato esperado usando os valores **UrlEncodedSignature,** **ExpiryEpoch,** **KeyName**e **UrlEncodedResourceUrl.**

Consulte a documentação do Ônibus de [Serviço do Azure](../service-bus-messaging/service-bus-sas.md) para obter uma visão mais completa da assinatura de acesso compartilhado e como os Hubs de Notificação e Ônibus de Serviço do Azure a usam.

Para os propósitos deste exemplo Swift, você vai usar a biblioteca **CommonCrypto** de código aberto da Apple para ajudar com o hashing da assinatura. Como é uma biblioteca C, não é acessível em Swift fora da caixa. Você pode disponibilizar a biblioteca usando um cabeçalho de ponte.

Para adicionar e configurar o cabeçalho de ponte:

1. Em Xcode, selecione **Arquivo** > **Novo** > **Arquivo de Cabeçalho****de** > Arquivo . Nomeie o arquivo de cabeçalho **BridgingHeader.h**.

1. Editar o arquivo para importar **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Atualize as **configurações** de compilação do destino para fazer referência ao cabeçalho de ponte:

   1. Toque no projeto **PushDemo** e role até a seção **Compilador Swift.**

   1. Certifique-se de que a opção **Cabeçalho de compatibilidade Objetivo-C** de instalação está definida como **Sim**.

   1. Digite o `'<ProjectName>/BridgingHeader.h'` caminho do arquivo na opção **Cabeçalho de ponte Objetivo-C.** Este é o caminho do arquivo para o nosso cabeçalho de ponte.

   Se você não conseguir encontrar essas opções, certifique-se de que você tenha a exibição **All** selecionada em vez de **Básica** ou **Personalizada**.

   Existem muitas bibliotecas de invólucros de código aberto de terceiros disponíveis que podem tornar o uso **do CommonCrypto** um pouco mais fácil. No entanto, a discussão dessas bibliotecas está além do escopo deste artigo.

1. Adicione um novo arquivo Swift chamado **TokenUtility.swift** na pasta **Utilitários** e adicione o seguinte código:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Este utilitário encapsula a lógica responsável pela geração do token SAS.

   Como descrito anteriormente, a função **getSasToken** orquestra as etapas de alto nível necessárias para preparar o token. A função será chamada pelo serviço de instalação mais tarde neste tutorial.

   As outras duas funções são chamadas pela função **getSasToken:** **sha256HMac** para calcular a assinatura e **urlEncodedString** para codificar a seqüência de URL associada. A função **urlEncodedString** é necessária, pois não é possível alcançar a saída necessária usando a função de adição incorporada **PercentEncoding.**

   O [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar essas operações no Objective-C. Mais informações sobre os tokens SAS do Azure Bus podem ser encontradas na documentação do [Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

1. No **AppDelegate.swift,** adicione o código a seguir à função *didRegisterForRemoteNotificationsWithDeviceToken* para verificar se o **TokenUtility.getSasToken** está gerando um token válido
    
    ```swift
    let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

    let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                                withKeyName: self.notificationHubKeyName!,
                                                andKey: self.notificationHubKey!)
    
    print(tokenData.token)
    ```

    Certifique-se de substituir os valores do espaço reservado na seqüência **baseAddress** por sua própria

### <a name="verify-the-sas-token"></a>Verifique o token SAS

Antes de implementar o serviço de instalação no cliente, verifique se nosso aplicativo está gerando corretamente o token SAS usando seu utilitário HTTP de escolha. Para os propósitos deste tutorial, nossa ferramenta de escolha será **Carteiro**.

Anote os valores de **instalaçãoId** e **token** que estão sendo gerados pelo aplicativo.

Siga estas etapas para chamar as **instalações** de API:

1. Em **Carteiro,** abra uma nova guia.

1. Defina a solicitação para **OBTER** e especifique o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte forma:

   | Chave           | Valor            |
   | ------------- | ---------------- |
   | Tipo de conteúdo  | aplicativo/json |
   | Autorização | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Selecione o botão **Código** que aparece no canto superior direito o botão **Salvar.** A solicitação deve ser semelhante ao seguinte exemplo:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecione o botão **Enviar**.

Não existe registro para a **instalação especificadaId** neste momento. A verificação deve resultar em uma resposta "404 Não Encontrada" em vez de uma resposta "401 Não Autorizada". Este resultado deve confirmar que o token SAS foi aceito.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, você implementará nosso invólucro básico em torno da [API Rest instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo arquivo Swift chamado **NotificationRegistrationService.swift** na pasta **Serviços** e adicione o seguinte código a este arquivo:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    private var tokenExpiryDate : Date? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && 
                        (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            tokenExpiryDate == nil ||
            Date() >= tokenExpiryDate!) {
            
            self.tokenData = TokenUtility.getSasToken(
                forResourceUrl: getBaseAddress(),
                withKeyName: self.keyName,
                andKey: self.key)
            
            self.tokenExpiryDate = Date(timeIntervalSinceNow: -(5 * 60))
                .addingTimeInterval(TimeInterval(tokenData!.expiration))
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Os detalhes necessários são fornecidos como parte da inicialização. As tags e modelos são opcionalmente passados para a função **de registro** para fazer parte da carga útil JSON de instalação do **dispositivo.**  

A função **de registro** chama as outras funções privadas para preparar a solicitação. Após o recebimento de uma resposta, a conclusão é chamada e indica se o registro foi bem sucedido.  

O ponto final da solicitação é construído pela função **getBaseAddress.** A construção utiliza os parâmetros do hub de notificação *namespace* e *nome* que foram fornecidos durante a inicialização.  

A função **getSasToken** verifica se o token armazenado no momento é válido. Se o token não for válido, a função chama **TokenUtility** para gerar um novo token e, em seguida, armazena-o antes de retornar um valor.

Finalmente, **o encodeToJson** converte os respectivos objetos modelo em JSON para uso como parte do corpo de solicitação.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invoque a API REST dos Hubs de Notificação

A última etapa é atualizar **o AppDelegate** para usar **o NotificationRegistrationService** para se registrar no nosso **NotificationHub**.

1. Abra **o AppDelegate.swift** e adicione variáveis de nível de classe para armazenar uma referência ao **NoficiationRegistrationService** e ao **PushTemplate**genérico:

    ```swift
    var registrationService : NotificationRegistrationService?
    let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
    ```

1. No mesmo arquivo, atualize a função **didRegisterForRemoteNotificationsWithDeviceToken** para inicializar o **NotificationRegistrationService** com os parâmetros necessários e, em seguida, chamar a função **de registro.**

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Para simplificar, você vai usar algumas declarações de impressão para atualizar a janela de saída com o resultado da operação de **registro.**

1. Agora construa e execute o aplicativo em um dispositivo físico. Você deve ver "Registrado" na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

Nosso aplicativo nesta fase está cadastrado no **NotificationHub** e pode receber notificações push. No Xcode, pare o depurador e feche o aplicativo se ele estiver em execução no momento. Em seguida, verifique se os detalhes **da instalação** do dispositivo são como esperado e se nosso aplicativo agora pode receber notificações push.  

### <a name="verify-the-device-installation"></a>Verifique a instalação do dispositivo

Agora você pode fazer a mesma solicitação que fez anteriormente usando **o Carteiro** para verificar [o token SAS](#verify-the-sas-token). Supondo que o token SAS não tenha expirado, a resposta agora deve incluir os detalhes de instalação fornecidos, como os modelos e tags.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

Se o **token SAS** anterior expirou, você pode adicionar um ponto de **breakpoint** à **linha 24** da classe **TokenUtility** para obter um novo **token SAS** e atualizar o cabeçalho **autorização** com esse novo valor.

### <a name="send-a-test-notification-azure-portal"></a>Envie uma notificação de teste (portal Azure)

A maneira mais rápida de testar que agora você pode receber notificações é navegar até o hub de notificação no portal azure:

1. No portal Azure, navegue até a guia **Visão Geral** no seu centro de notificação.

1. Selecione **'Enviar teste',** que está acima do resumo **do Essencial** no canto superior esquerdo da janela do portal:

    ![Botão de envio do teste de envio do teste de resumo do Centro de Notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Escolha **Modelo personalizado** na lista **Plataformas.**

1. Digite **12345** para **a expressão Enviar para tag**. Você já havia especificado esta tag em nossa instalação.

1. Editar opcionalmente a **mensagem** na carga útil JSON:

    ![Envio de testes de hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com sucesso ao dispositivo:

    ![Resultados de envio de resultados de envio de resultados de envio de hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supondo que o aplicativo não esteja sendo executado em primeiro plano, você também deve ver uma notificação na **Central de Notificação** em seu dispositivo. Tocar na notificação deve abrir o aplicativo e mostrar o alerta.

    ![Exemplo recebido por notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Envie uma notificação de teste (carteiro)

Você pode enviar notificações através da [API REST](/rest/api/notificationhubs/) usando **o Carteiro,** o que pode ser uma maneira mais conveniente de testar.

1. Abra uma nova guia no **Carteiro.**

1. Defina a solicitação para **POST**e digite o seguinte endereço:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte forma:

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | Tipo de conteúdo                   | aplicação/json;charset=utf-8 |
   | Autorização                  | \<sasToken>                     |
   | Formato de notificação do ServiceBus  | template                       |
   | Marcas                           | “12345”                        |

1. Configure o **CORPO de** solicitação para usar RAW - **JSON (application.json)** com a seguinte carga útil JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecione o botão **Código,** que está o botão **Salvar** no canto superior direito da janela. A solicitação deve ser semelhante ao seguinte exemplo:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Selecione o botão **Enviar**.

Você deve obter um código de status de sucesso **criado em 201** e receber a notificação no dispositivo do cliente..

## <a name="next-steps"></a>Próximas etapas
Agora você tem um aplicativo básico iOS Swift conectado a um hub de notificação através da [API REST](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, consulte os seguintes artigos:

- [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
- [APIs REST dos Hubs de Notificação](/rest/api/notificationhubs/)
- [Hubs de notificação SDK para operações back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Hubs de notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registre-se com o back-end do aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gestão de registro](notification-hubs-push-notification-registration-management.md)
- [Trabalhando com tags](notification-hubs-tags-segment-push-message.md) 
- [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controle de acesso de ônibus de serviço com assinaturas de acesso compartilhadas](../service-bus-messaging/service-bus-sas.md)
- [Programar logicamente gerar tokens SAS](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: cripto comum](https://developer.apple.com/security/)
- [Hora da época unix](https://en.wikipedia.org/wiki/Unix_time)
- [Hmac](https://en.wikipedia.org/wiki/HMAC)
