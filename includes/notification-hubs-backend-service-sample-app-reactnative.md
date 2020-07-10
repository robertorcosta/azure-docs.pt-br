---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060394"
---
### <a name="create-the-react-native-solution"></a>Criar a solução do React Native

1. Em `Terminal`, atualize as ferramentas de ambiente, necessárias para trabalhar com o React Native nativamente usando os seguintes comandos:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. Em `Terminal`, execute o comando a seguir, se você tiver a CLI do `React Native` instalada para desinstalá-lo. Use `npx` para acessar automaticamente a última versão disponível da CLI do React Native:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > O React Native tem uma interface de linha de comando interna. Em vez de instalar e gerenciar uma versão específica da CLI globalmente, recomendamos que você acesse a versão atual em runtime usando o `npx`, que é fornecido com o Node.js. Com `npx react-native <command>`, a versão estável atual da CLI será baixada e executada no momento em que o comando for executado.

1. Procure a pasta de projetos na qual deseja criar o aplicativo. Use o modelo baseado em TypeScript especificando o parâmetro `--template`:

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Execute o servidor metro, que cria pacotes JavaScript e monitora as atualizações de código para atualizar os pacotes em tempo real:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Execute o aplicativo do iOS para confirmar a configuração. Verifique se você iniciou um simulador do iOS ou conectou um dispositivo iOS antes de executar o seguinte comando:

    ```bash
    npx react-native run-ios
    ```

1. Execute o aplicativo do Android para confirmar a configuração. São necessárias mais algumas etapas para configurar um emulador do Android ou um dispositivo para acessar o servidor metro do React Native. Os comandos a seguir geram o pacote JavaScript inicial para o Android e o colocam na pasta de ativos.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    Esse script será implantado previamente com a versão inicial do aplicativo. Depois que ele for implantado, configure o emulador ou o dispositivo para acessar o servidor metro especificando o endereço IP do servidor. Execute o seguinte comando para compilar e executar o aplicativo do Android:

    ```bash
    npx react-native run-android
    ```

    Quando estiver no aplicativo, clique em `CMD+M` (emulador) ou agite o dispositivo para preencher as configurações do desenvolvedor, procure `Settings` > `Change Bundle Location` e especifique o endereço IP do servidor metro com a porta padrão: `<metro-server-ip-address>:8081`.

1. No arquivo `App.tsx`, aplique qualquer alteração ao layout da página, salve-o e faça a alteração ser refletida automaticamente nos aplicativos do iOS e do Android.

    > [!NOTE]
    > O guia detalhado de configuração do ambiente de desenvolvimento está disponível na [documentação oficial](https://reactnative.dev/docs/environment-setup)

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Você precisará dos três seguintes pacotes para que esta amostra funcione:

1. [Notificações por push do React Native para iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios) - [Projeto GitHub](https://github.com/react-native-community/push-notification-ios)

    Esse pacote foi criado quando o PushNotificationIOS foi dividido do núcleo do React Native. O pacote implementa nativamente as notificações por push para o iOS e fornece a interface do React Native para acessá-lo. Execute o seguinte comando para instalar o pacote:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Notificações por push do React Native para multiplataforma](https://www.npmjs.com/package/react-native-push-notification)

    Esse pacote implementa notificações locais e remotas no iOS e no Android no modo multiplataforma. Execute o seguinte comando para instalar o pacote:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Pacote de informações do dispositivo](https://www.npmjs.com/package/react-native-device-info) O pacote fornece informações sobre um dispositivo em runtime. Use-o para definir um identificador de dispositivo, que é usado para o registro da notificação por push. Execute o seguinte comando para instalar o pacote:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes multiplataforma

1. Crie e implemente `DemoNotificationHandler`:

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Crie e implemente `DemoNotificationService`:

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Crie e implemente `DemoNotificationRegistrationService`:

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Configure o aplicativo. Abra `package.json` e adicione a seguinte definição de script:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Em seguida, execute este script, que copiará a configuração padrão para a pasta `config`.

    ```bash
    yarn configure
    ```

    A etapa final é atualizar o arquivo de configuração copiado na etapa anterior com as informações de acesso à API. Especifique os parâmetros `apiKey` e `apiUrl`:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>Implementar a interface do usuário multiplataforma

1. Definir o layout da página

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Aplicar estilos

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Inicializar o componente de página

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definir manipuladores de clique de botão

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. Processar as notificações por push e os registros de token recebidos

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```