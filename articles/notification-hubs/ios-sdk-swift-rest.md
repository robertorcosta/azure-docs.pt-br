---
title: Enviar notificações por push para aplicativos iOS Swift usando os Hubs de Notificação do Azure e as APIs REST
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure e as APIs REST para enviar notificações por push para dispositivos iOS.
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/03/2020
ms.openlocfilehash: 861011874c63f4c5d18ce33b107bbe9a80359045
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126942"
---
# <a name="tutorial-send-push-notifications-to-swift-ios-apps-using-notification-hubs-rest-apis"></a>Tutorial: Enviar notificações por push para aplicativos iOS Swift usando os Hubs de Notificação e as APIs REST

Este tutorial descreve como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo iOS usando APIs REST.

Este tutorial cobre as seguintes etapas:

- Criar um aplicativo iOS de exemplo.
- Conectar seu aplicativo iOS aos Hubs de Notificação do Azure.
- Enviar notificações por push de teste.
- Verificar se seu aplicativo recebe notificações.

O código completo deste tutorial pode ser baixado no [GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.

- Um iPhone ou iPad executando o iOS versão 10 ou posterior.

- Seu dispositivo físico registrado no [portal da Apple](https://developer.apple.com/) e associado ao seu certificado.

Antes de prosseguir, siga o tutorial anterior sobre como começar a usar os [Hubs de Notificação do Azure para aplicativos iOS](https://go.microsoft.com/fwlink/?linkid=2129801) para então preparar e configurar as credenciais de push em seu hub de notificação. Mesmo que você não tenha nenhuma experiência anterior com o desenvolvimento do iOS, você será capaz de seguir estas etapas.

> [!NOTE]
> Devido aos requisitos de configuração das notificações por push, você precisa implantá-las e testá-las em um dispositivo iOS físico (iPhone ou iPad), em vez de usar o emulador de iOS.

Nas seções a seguir, você cria um aplicativo iOS que se conecta ao hub de notificação.

## <a name="create-an-ios-project"></a>Criar um projeto do iOS

1. No Xcode, crie um projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .

2. Ao definir as opções para o novo projeto:
   - Especifique o **Nome do Produto** (PushDemo) e **Identificador da Organização** (`com.<organization>`) que você usou ao definir o **Identificador do Pacote** no Apple Developer Portal.
   - Escolha a **Equipe** para a qual a **ID do Aplicativo** foi configurada.
   - Defina a **linguagem de programação** para **Swift**.
   - Selecione  **Avançar**.

3. Crie uma pasta chamada **SupportingFiles**.

4. Crie um arquivo p-list chamado **devsettings.plist** na pasta **SupportingFiles** . Adicione essa pasta ao seu arquivo **gitignore** para que ela não seja confirmada quando você trabalhar com um repositório git. Em um aplicativo de produção, talvez você esteja definindo condicionalmente esses segredos como parte de um processo de build automatizado. Essas configurações não são abordadas neste tutorial.

5. Atualize **devsettings.plist** para incluir as seguintes entradas de configuração usando os valores do hub de notificação que você provisionou:

   | **Chave**                  | **Tipo** | **Valor**        |
   | ------------------------ | -------- | ---------------- |
   | notificationHubKey       | String   | `<hubKey>`       |
   | notificationHubKeyName   | String   | `<hubKeyName>`   |
   | notificationHubName      | String   | `<hubName>`      |
   | notificationHubNamespace | String   | `<hubNamespace>` |

   Você pode encontrar os valores necessários navegando até o recurso do hub de notificação no portal do Azure. Em particular, os valores **notificationHubName** e **notificationHubNamespace** estão no canto superior direito do resumo **Fundamentos** na página **Visão geral** .

   :::image type="content" source="media/ios-sdk-swift-rest/image1.png" alt-text="Valores necessários":::

   Você também pode encontrar os valores **notificationHubKeyName** e **notificationHubKey** navegando até **Políticas de Acesso** e selecionando a respectiva **Política de Acesso**, como **DefaultFullSharedAccessSignature**. Depois disso, copie da **Cadeia de conexão Primária** o valor prefixado com `SharedAccessKeyName=` para **notificationHubKeyName**, bem como o valor prefixado com `SharedAccessKey=` para a **notificationHubKey**. A cadeia de conexão estará no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para simplificar, especifique **DefaultFullSharedAccessSignature**, permitindo a você usar o token para enviar notificações. Na prática, o **DefaultListenSharedAccessSignature** é uma opção melhor para situações em que você deseja apenas receber notificações.

6. Em **Navegador de Projeto**, selecione o **Nome do Projeto** e escolha a guia **Geral** .

7. Localize **Identidade** e defina o valor de **Identificador do Pacote** para que ele corresponda a `com.<organization>.PushDemo`, que é o valor usado para a **ID do Aplicativo** de uma etapa anterior.

8. Encontre **Assinatura e Funcionalidades** e selecione a **Equipe** apropriada para sua **Conta de Desenvolvedor da Apple**. O valor de **Equipe** deve corresponder àquela sob a qual você criou seus certificados e perfis.

9. O Xcode deve baixar automaticamente o valor de **Perfil de Provisionamento** apropriado com base no **Identificador do Pacote**. Se você não vir o novo valor de **Perfil de Provisionamento** , tente atualizar os perfis para a **Identidade de Assinatura** selecionando **Xcode**, depois **Preferências**, **Conta** e clique no botão **Baixar Perfis Manuais** para baixar os perfis.

10. Ainda na guia **Assinatura e Funcionalidades** , clique no botão **+ Funcionalidade** e toque duas vezes em **Notificações por Push** na lista para assegurar que as **Notificações por Push** estejam habilitadas.

11. Abra o arquivo **AppDelegate.swift** para implementar o protocolo **UNUserNotificationCenterDelegate** e adicione o seguinte código à parte superior da classe:

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

    Você usará esses membros posteriormente. Especificamente, você usará o membro **tags** como parte do registro usando um **modelo personalizado**. Para obter mais informações sobre marcas, confira [Marcas para registros](notification-hubs-tags-segment-push-message.md) e [Registros de modelo](notification-hubs-templates-cross-platform-push-messages.md).

12. No mesmo arquivo, adicione o seguinte código à função **didFinishLaunchingWithOptions**:

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

    Esse código recupera as configurações de **devsettings.plist**, define a classe **AppDelegate** como o delegado **UNUserNotificationCenter** , solicita autorização para notificações por push e chama **registerForRemoteNotifications**.

    Para simplificar, o código só dá suporte ao iOS 10 e posterior. Você pode adicionar suporte a versões anteriores do iOS usando condicionalmente as respectivas APIs e abordagens, como faria normalmente.

13. No mesmo arquivo, adicione o seguinte código:

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

    Esse código usa os valores **installationId** e **pushChannel** para se registrar com o hub de notificação. Nesse caso, você está usando **UIDevice.current.identifierForVendor** a fim de fornecer um valor exclusivo para identificar o dispositivo e formatar o **deviceToken** para fornecer o valor **pushChannel** desejado. A função **showAlert** existe simplesmente para exibir um texto de mensagem para fins de demonstração.

14. Ainda no arquivo **AppDelegate.swift** , adicione as funções **willPresent** e **didReceive** para **UNUserNotificationCenterDelegate**. Essas funções exibem um alerta quando são notificadas de que um aplicativo está sendo executado em primeiro ou segundo plano.

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

15. Adicione instruções `print` à parte inferior da função **didRegisterForRemoteNotificationsWithDeviceToken** para verificar se valores estão sendo atribuídos a **installationId** e **pushChannel** :

    ```swift
    print(installationId)
    print(pushChannel)
    ```

16. Crie as pastas **Models**, **Services**e **Utilities** para os componentes fundamentais que você adicionará ao projeto posteriormente.

17. Verifique se o projeto é compilado e executado em um dispositivo físico. As notificações por push não podem ser testadas usando o simulador.

## <a name="create-models"></a>Criar modelos

Nesta etapa, você cria um conjunto de modelos para representar os conteúdos da [API REST dos Hubs de Notificação](/rest/api/notificationhubs/) e para armazenar os dados de token SAS (assinatura de acesso compartilhado) necessários.

1. Adicione um novo arquivo Swift chamado **PushTemplate.swift** à pasta **Models** . Esse modelo define uma estrutura que representa o **CORPO** de um modelo individual como parte do conteúdo de **DeviceInstallation** .

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Adicione um novo arquivo Swift chamado **DeviceInstallation.swift** à pasta **Models** . Esse arquivo define uma estrutura que representa o conteúdo para a criação ou atualização de uma **Instalação de Dispositivo**. Adicione o seguinte código ao arquivo:

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

3. Adicione um novo arquivo Swift chamado **TokenData.swift** à pasta **Models** . Esse modelo é usado para armazenar um token SAS, juntamente com a respectiva expiração. Adicione o seguinte código ao arquivo:

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

## <a name="generate-a-sas-token"></a>Gerar um token SAS

Os Hubs de Notificação usam a mesma infraestrutura de segurança que o Barramento de Serviço do Azure. Para chamar a API REST, [gere programaticamente um token SAS](/rest/api/eventhub/generate-sas-token) que possa ser usado no cabeçalho de **Autorização** da solicitação.

O token resultante terá o seguinte formato:

```xml
SharedAccessSignature sig=\<UrlEncodedSignature\>\&se=\<ExpiryEpoch\>\&skn=\<KeyName\>\&sr=\<UrlEncodedResourceUri\> |
```

O processo propriamente dito envolve as mesmas seis etapas:

1. Computar a expiração no formato de [Hora da época do UNIX](https://en.wikipedia.org/wiki/Unix_time) , o que significa o número de segundos decorridos desde o horário coordenado universal de meia-noite, 1º de janeiro de 1970.

2. Formatar o **ResourceUrl** que representa o recurso que você está tentando acessar, de modo que ele seja codificado por percentual e representado em minúsculas. O **ResourceUrl** tem o formato `https://<namespace>.servicebus.windows.net/<hubName>`.

3. Prepare o **StringToSign**, que é formatado como `<UrlEncodedResourceUrl>\n<ExpiryEpoch>`.

4. Compute a **Assinatura** e codifique-a em Base64 usando o hash HMAC-SHA256 do valor **StringToSign** . O valor do hash é usado com a parte de **Chave** da **Cadeia de Conexão** para a respectiva **Regra de Autorização**.

5. Formate a **Assinatura** codificada em Base64 de maneira que ela seja codificada por porcentagem.

6. Crie o token no formato esperado usando os valores **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**e **UrlEncodedResourceUrl** .

Confira a [documentação do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-sas.md) para obter uma visão geral mais completa das assinaturas de acesso compartilhado e de como elas são utilizadas pelos Hubs de Notificação e pelo Barramento de Serviço do Azure.

Para os fins deste exemplo do Swift, use a biblioteca **CommonCrypto** de software livre da Apple para ajudar com o hash da assinatura. Como se trata de uma biblioteca C, ela não está imediatamente acessível no Swift. Você pode tornar a biblioteca disponível usando um cabeçalho de ponte.

Para adicionar e configurar o cabeçalho de ponte:

1. No Xcode, selecione **Arquivo**,  **Novo**,  **Arquivo** e escolha  **Arquivo de Cabeçalho**. Nomeie o arquivo de cabeçalho **BridgingHeader.h**.

2. Edite o arquivo para importar **CommonHMAC.h**:

   ```swift
   #import <CommonCrypto/CommonHMAC.h>

   #ifndef BridgingHeader_h
   #define BridgingHeader_h

   #endif /* BridgingHeader_h */
   ```

3. Atualize as **Configurações de Build** de destino para referenciar o cabeçalho de ponte:

   1. Selecione o projeto **PushDemo** e role para baixo até a seção **Compilador Swift** .

   2. Verifique se a opção **Instalar o Cabeçalho de Compatibilidade do Objective-C** está definida como **Sim**.

   3. Insira o caminho do arquivo `<ProjectName>/BridgingHeader.h` na opção **Cabeçalho de Ponte Objective-C** . Esse é o caminho do arquivo para o cabeçalho de ponte.

   Se você não conseguir encontrar essas opções, verifique o modo de exibição **Todos** está selecionado, em vez de **Básico** ou **Personalizado**.

   Há muitas bibliotecas de wrappers de software livre de terceiros disponíveis que podem facilitar um pouco o uso do **CommonCrypto** . No entanto, uma análise completa dessas bibliotecas está além do escopo deste artigo.

4. Adicione um novo arquivo Swift chamado **TokenUtility.swift** na pasta **Utilities** e adicione o seguinte código:

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

   Esse utilitário encapsula a lógica responsável por gerar o token SAS.

   Conforme descrito anteriormente, a função **getSasToken** orquestra as etapas de alto nível necessárias para preparar o token. A função será chamada pelo serviço de instalação posteriormente neste tutorial.

   As outras duas funções são chamadas pela função **getSasToken** : **sha256HMac** para calcular a assinatura e **urlEncodedString** para codificar a cadeia de caracteres da URL associada. A função **urlEncodedString** é necessária, pois não é possível obter a saída necessária usando a função interna **addingPercentEncoding** .

   O [SDK do iOS do Armazenamento do Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar essas operações em Objective-C. Mais informações sobre tokens SAS do Barramento de Serviço do Azure podem ser encontradas na [documentação do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-sas.md).

5. Em **AppDelegate.swift**, adicione o código a seguir à função `didRegisterForRemoteNotificationsWithDeviceToken` para verificar se o **TokenUtility.getSasToken** está gerando um token válido

   ```swift
   let baseAddress = "https://<notificaitonHubNamespace>.servicebus.windows.net/<notifiationHubName>"

   let tokenData = TokenUtility.getSasToken(forResourceUrl: baseAddress,
                                            withKeyName: self.notificationHubKeyName!,
                                            andKey: self.notificationHubKey!)

   print(tokenData.token)
   ```

   Substitua os espaços reservados na cadeia de caracteres **baseAddress** por seus valores.

## <a name="verify-the-sas-token"></a>Verificar o token SAS

Antes de implementar o serviço de instalação no cliente, use um utilitário HTTP para verificar se o aplicativo está gerando o token SAS corretamente. Para os fins deste tutorial, a ferramenta que escolhemos é o **Postman**.

Anote os valores de **installationId** e **token** gerados pelo aplicativo.

Siga estas etapas para chamar a API de **instalações** :

1. Abra uma nova guia no **Postman**.
2. Defina a solicitação como **Get** e especifique o seguinte endereço:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
   ```

3. Configure os cabeçalhos da solicitação conforme determinado abaixo:

   | **Chave**       | **Valor**        |
   | ------------- | ---------------- |
   | Tipo de conteúdo  | aplicativo/json |
   | Autorização | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Selecione o botão **Code** que aparece no canto superior direito no botão **Salvar** . A solicitação terá aparência semelhante à do seguinte exemplo:

   ```xml
   GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
   Host: <namespace>.servicebus.windows.net
   Content-Type: application/json
   Authorization: <sasToken>
   x-ms-version: 2015-01
   Cache-Control: no-cache
   Postman-Token: <postmanToken>
   ```

5. Selecione o botão **Enviar** .

Neste momento, não existe registro para a **installationId** especificada. A verificação deve resultar em uma resposta "404: Não Encontrado" em vez de uma resposta "401: Não Autorizado". Esse resultado confirma que o token SAS foi aceito.

## <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, implemente um wrapper básico encapsulando a [API REST de Instalações](/rest/api/notificationhubs/create-overwrite-installation).

Adicione um novo arquivo Swift chamado **NotificationRegistrationService.swift** na pasta **Services** e adicione o seguinte código a esse arquivo:

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

Os detalhes do requisito são fornecidos como parte da inicialização. As marcas e os modelos são, opcionalmente, passados para a função **register** para formar parte do conteúdo JSON da **instalação do dispositivo** .

A função **register** chama as outras funções privadas para preparar a solicitação. Depois que uma resposta é recebida, a conclusão é chamada e indica se o registro foi bem-sucedido.

O ponto de extremidade de solicitação é criado pela função **getBaseAddress** . A construção usa os parâmetros *namespace* e *name* do hub de notificação que foram fornecidos durante a inicialização.

A função **getSasToken** verifica se o token armazenado no momento é válido. Se o token não for válido, a função chamará **TokenUtility** para gerar um novo token e o armazenará antes de retornar um valor.

Por fim, **encodeToJson** converte os objetos de modelo respectivos em JSON para uso como parte do corpo da solicitação.

## <a name="invoke-the-notification-hubs-rest-api"></a>Invocar a API REST dos Hubs de Notificação

A última etapa é atualizar **AppDelegate** a fim de usar **NotificationRegistrationService** para registrar-se com o **NotificationHub**.

1. Abra **AppDelegate.swift** e adicione variáveis em nível de classe para armazenar uma referência ao **NoficiationRegistrationService** e ao **PushTemplate**genérico:

   ```swift
   var registrationService : NotificationRegistrationService?
   let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
   ```

2. No mesmo arquivo, atualize a função **didRegisterForRemoteNotificationsWithDeviceToken** para inicializar o **NotificationRegistrationService** com os parâmetros necessários e chame a função **register** .

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

   Para simplificar, o código usa instruções `print` para atualizar a Janela de Saída com o resultado da operação **register** .

3. Compile e execute o aplicativo em um dispositivo físico. Você verá a mensagem **Registrado** na Janela de Saída.

## <a name="test-the-solution"></a>Testar a solução

Neste ponto, o aplicativo é registrado com o **NotificationHub** e pode receber notificações por push. No Xcode, pare o depurador e feche o aplicativo se ele estiver em execução no momento. Em seguida, verifique se os detalhes da **Instalação do Dispositivo** aparecem conforme o esperado e se o aplicativo agora pode receber notificações por push.

### <a name="verify-the-device-installation"></a>Verificar a instalação do dispositivo

Agora você pode fazer a mesma solicitação que fazia anteriormente, usando o **Postman** para [verificar o token SAS](notification-hubs-ios-push-notifications-swift-apps-get-started.md#verify-the-sas-token). Se o token SAS não tiver expirado, a resposta agora incluirá os detalhes da instalação fornecidos, como os modelos e as marcas.

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

Se o token SAS anterior tiver expirado, você poderá adicionar um ponto de interrupção à linha 24 da classe **TokenUtility** para obter um novo token SAS e atualizar o cabeçalho de **Autorização** com esse novo valor.

### <a name="send-a-test-notification-azure-portal"></a>Enviar uma notificação de teste (portal do Azure)

A maneira mais rápida de testar que agora você pode receber notificações é navegar até o hub de notificação no portal do Azure:

1. Na portal do Azure, navegue até a guia **Visão geral** no hub de notificação.

2. Selecione **Envio de Teste**, que está acima do resumo **Fundamentos** no canto superior esquerdo da janela do portal:

   :::image type="content" source="media/ios-sdk-swift-rest/image2.png" alt-text="Envio de teste de resumo dos fundamentos dos Hubs de Notificação":::

3. Escolha **Modelo Personalizado** na lista **Plataformas** .

4. Insira **12345** para a **Expressão Enviar para Marca**. Você já especificou essa marca em sua instalação.

5. Opcionalmente, edite a **mensagem** no conteúdo JSON:

   :::image type="content" source="media/ios-sdk-swift-rest/image3.png" alt-text="Envio de teste dos Hubs de Notificação":::

6. Selecione **Enviar**. O portal indica se a notificação foi enviada com êxito para o dispositivo:

   :::image type="content" source="media/ios-sdk-swift-rest/image4.png" alt-text="Resultado do envio de teste":::

   Se o aplicativo não estiver sendo executado em primeiro plano, você verá uma notificação no **Centro de Notificação** em seu dispositivo. Tocar na notificação abrirá o aplicativo e mostrará o alerta.

   :::image type="content" source="media/ios-sdk-swift-rest/image5.png" alt-text="Notificação de teste":::

### <a name="send-a-test-notification-mail-carrier"></a>Enviar uma notificação de teste (operadora de email)

Você pode enviar notificações por meio da [API REST](/rest/api/notificationhubs/) usando o **Postman**, que é uma forma mais prática de testar.

1. Abra uma nova guia no **Postman**.

2. Defina a solicitação como **POST**e insira o seguinte endereço:

   ```xml
   https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
   ```

3. Configure os cabeçalhos da solicitação conforme determinado abaixo:

   | Chave                           | Valor                          |
   | ----------------------------- | ------------------------------ |
   | Tipo de conteúdo                  | application/json;charset=utf-8 |
   | Autorização                 | \<sasToken\>                   |
   | ServiceBusNotification-Format | template                       |
   | Marcas                          | “12345”                        |

4. Configure o **corpo** da solicitação para usar **JSON bruto (application.json)**  com o seguinte conteúdo JSON:

   ```json
   {
   "message" : "Hello from Postman!"
   }
   ```

5. Selecione o botão **Código** , que fica sob o botão  **Salvar** no canto superior direito da janela. A solicitação terá aparência semelhante à do seguinte exemplo:

   ```xml
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

6. Selecione o botão **Enviar** .

Você obterá um código de status de êxito **201: Criado** e receberá a notificação no dispositivo cliente.

## <a name="next-steps"></a>Próximas etapas

Agora você tem um aplicativo Swift básico do iOS básico conectado a um hub de notificação por meio da [API REST dos Hubs de Notificação](/rest/api/notificationhubs/)e pode enviar e receber notificações. Para saber mais sobre como enviar notificações a dispositivos específicos, avance para o seguinte tutorial:

- [Tutorial: Enviar notificações por push para dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
- [APIs REST dos Hubs de Notificação](/rest/api/notificationhubs/)
- [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [SDKs dos Hubs de Notificação do GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrar com o back-end de aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gerenciamento de registros](notification-hubs-push-notification-registration-management.md)
- [Como trabalhar com marcas](notification-hubs-tags-segment-push-message.md)
- [Como trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controle de acesso do Barramento de Serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md)
- [Gerar tokens SAS programaticamente](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: criptografia comum](https://developer.apple.com/security/)
- [Hora da época do UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
