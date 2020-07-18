---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 2f8cd5e554980225850780cc474106c3dc69288a
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156261"
---
### <a name="create-the-flutter-solution"></a>Criar a solução Flutter

1. Abra uma nova instância do **Visual Studio Code**.

1. Abra a **Paleta de Comandos** (**Shift** + **Command** + **P**).

1. Selecione o comando **Flutter: New Project** e pressione **Enter**.

1. Insira *push_demo* para o **Nome do Projeto** e selecione um **Localização do projeto**.

1. Quando solicitado a fazer isso, escolha **Obter Pacotes**.

1. Use **Control** + **Clique** na pasta **kotlin** (em **app** > **src** > **main**) e escolha **Revelar no Finder**. Em seguida, renomeie as pastas filho (na pasta **Kotlin**) para ```com```, ```<your_organization>``` e ```pushdemo```, respectivamente.

    > [!NOTE]
    > Ao usar o modelo do **Visual Studio Code**, essas pastas usam **com**, **example**, **<nome_do_projeto>** como padrão. Supondo que **mobcat** seja usado para a **organização**, a estrutura de pastas deve aparecer de modo indicativo como:
    >
    > - kotlin
    >     - com
    >         - mobcat
    >             - pushdemo

1. De volta ao **Visual Studio Code**, atualize o valor de **applicationId** em **android** > **app** > **build.gradle** para `com.<your_organization>.pushdemo`.

    > [!NOTE]
    > Você deve usar o nome da sua organização para o espaço reservado *<sua_organização>* . Por exemplo, usando **mobcat** como a organização resultará em um valor de **nome de pacote** de *com.mobcat.pushdemo*.

1. Atualize o atributo **pacote** nos arquivos **AndroidManifest.xml** em **src** > **debug**, **src** > **main** e **src** > **profile** respectivamente. Verifique se os valores correspondem à **applicationId** usada na etapa anterior.

    ```xml
    <manifest
        xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.<your_organization>.pushdemo>">
        ...
    </manifest>
    ```

1. Atualize o atributo ```android:label``` no arquivo **AndroidManifest.xml** em **src** > **main** para *PushDemo*. Em seguida, adicione o atributo ```android:allowBackup```, diretamente em ```android:label```, definindo seu valor como **false**.

    ```xml
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="PushDemo"
        android:allowBackup="false"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
    ```

1. Abra o arquivo de nível de aplicativo **build.gradle** (**android** > **app** > **build.gradle**) e atualize *compileSdkVersion* (na seção **android**) para usar a API **29**. Em seguida, atualize os valores *minSdkVersion* e *targetSdkVersion* (da seção **defaultConfig**) para **26** e **29**, respectivamente.

    > [!NOTE]
    > Somente os dispositivos que executam **Nível da API 26 e superior** são compatíveis com os fins deste tutorial, no entanto, você pode estendê-lo para compatibilidade com dispositivos que executam versões mais antigas.

1. Use **Control** + **Clique** na pasta **ios** e escolha **Abrir no Xcode**.

1. No **Xcode**, clique em **Executor** (**xcodeproj** na parte superior, não na pasta). Em seguida, selecione o destino **Executor** e selecione a guia **Geral**. Com a configuração de build **Todos** selecionada, atualize o **Identificador de Pacote** para `com.<your_organization>.PushDemo`.

    > [!NOTE]
    > Você deve usar o nome da sua organização para o espaço reservado *<sua_organização>* . Por exemplo, usando **mobcat** como a organização resultará em um valor de **Identificador de Pacote** de *com.mobcat.PushDemo*.

1. Clique em **Info.plist** e atualize o nome do valor **Nome do pacote** para **PushDemo**

1. Feche o **Xcode** e retorne para o **Visual Studio Code**.

1. De volta ao **Visual Studio Code**, abra **pubspec.yaml**, adicione os **pacotes do Dart** [http](https://pub.dev/packages/http) e [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) como dependências. Em seguida, salve o arquivo e clique em **Obter Pacotes** quando solicitado a fazer isso.

    ```yaml
    dependencies:
      flutter:
        sdk: flutter

      http: ^0.12.1
      flutter_secure_storage: ^3.3.3
    ```

1. Em **Terminal**, altere o diretório para a pasta **ios** (para seu projeto Flutter). Em seguida, execute o comando [**pod install**](https://guides.cocoapods.org/using/getting-started.html#installation) para instalar novos pods (exigidos pelo pacote [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage)).

1. Use **Control** + **Clique** na pasta **lib** e escolha **Novo Arquivo** no menu usando *main_page.dart* como o nome de arquivo. Então adicione o código a seguir.

    ```kotlin
    import 'package:flutter/material.dart';

    class MainPage extends StatefulWidget {
      @override
      _MainPageState createState() => _MainPageState();
    }

    class _MainPageState extends State<MainPage> {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          body: SafeArea(
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.stretch,
              children: <Widget>[],
            )
          )
        );
      }
    }
    ```

1. Em **main.dart**, substitua o código modelo pelo exibido a seguir.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/main_page.dart';

    final navigatorKey = GlobalKey<NavigatorState>();

    void main() => runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey));
    ```

1. Em **Terminal**, compile e execute o aplicativo em cada plataforma de destino para testar as execuções do aplicativo modelo em seus dispositivos. Verifique se os dispositivos com suporte estão conectados.

    ```bash
    flutter run
    ```

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes multiplataforma

1. Use **Control** + **Clique** na pasta **lib** e escolha **Nova Pasta** no menu usando *models* como **Nome da Pasta**.

1. Use **Control** + **Clique** na pasta **models** e escolha **Novo Arquivo** no menu usando *device_installation.dart* como o nome do arquivo. Então adicione o código a seguir.

    ```dart
    class DeviceInstallation {
        final String deviceId;
        final String platform;
        final String token;
        final List<String> tags;

        DeviceInstallation(this.deviceId, this.platform, this.token, this.tags);

        DeviceInstallation.fromJson(Map<String, dynamic> json)
          : deviceId = json['installationId'],
            platform = json['platform'],
            token = json['pushChannel'],
            tags = json['tags'];

        Map<String, dynamic> toJson() =>
        {
          'installationId': deviceId,
          'platform': platform,
          'pushChannel': token,
          'tags': tags,
        };
    }
    ```

1. Adicione um novo arquivo à pasta de **modelos** chamado *push_demo_action.dart* definindo a enumeração das ações com suporte neste exemplo.

    ```dart
    enum PushDemoAction {
      actionA,
      actionB,
    }
    ```

1. Adicione uma nova pasta ao projeto chamada **services** e adicione um novo arquivo à pasta chamado *device_installation_service.dart* com a implementação a seguir.

    ```dart
    import 'package:flutter/services.dart';

    class DeviceInstallationService {
      static const deviceInstallation = const MethodChannel('com.<your_organization>.pushdemo/deviceinstallation');
      static const String getDeviceIdChannelMethod = "getDeviceId";
      static const String getDeviceTokenChannelMethod = "getDeviceToken";
      static const String getDevicePlatformChannelMethod = "getDevicePlatform";

      Future<String> getDeviceId() {
        return deviceInstallation.invokeMethod(getDeviceIdChannelMethod);
      }

      Future<String> getDeviceToken() {
        return deviceInstallation.invokeMethod(getDeviceTokenChannelMethod);
      }

      Future<String> getDevicePlatform() {
        return deviceInstallation.invokeMethod(getDevicePlatformChannelMethod);
      }
    }
    ```

    > [!NOTE]
    > Você deve usar o nome da sua organização para o espaço reservado *<sua_organização>* . Por exemplo, usar **mobcat** como a organização resultará em um nome [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) de *com.mobcat.pushdemo/deviceinstallation*.
    >
    > Essa classe encapsula o trabalho com a plataforma nativa subjacente para adquirir os detalhes de instalação do dispositivo do requisito. Um [MethodChannel](https://api.flutter.dev/flutter/services/MethodChannel-class.html) facilita a comunicação assíncrona bidirecional com as plataformas nativas subjacentes. A contraparte específica da plataforma para esse canal será criada no em etapas posteriores.

1. Adicione outro arquivo a essa pasta chamado *notification_action_service.dart* com a implementação a seguir.

    ```dart
    import 'package:flutter/services.dart';
    import 'dart:async';
    import 'package:push_demo/models/push_demo_action.dart';

    class NotificationActionService {
      static const notificationAction =
          const MethodChannel('com.<your_organization>.pushdemo/notificationaction');
      static const String triggerActionChannelMethod = "triggerAction";
      static const String getLaunchActionChannelMethod = "getLaunchAction";

      final actionMappings = {
        'action_a' : PushDemoAction.actionA,
        'action_b' : PushDemoAction.actionB
      };

      final actionTriggeredController = StreamController.broadcast();

      NotificationActionService() {
        notificationAction
            .setMethodCallHandler(handleNotificationActionCall);
      }

      Stream get actionTriggered => actionTriggeredController.stream;

      Future<void> triggerAction({action: String}) async {

        if (!actionMappings.containsKey(action)) {
          return;
        }

        actionTriggeredController.add(actionMappings[action]);
      }

      Future<void> checkLaunchAction() async {
        final launchAction = await notificationAction.invokeMethod(getLaunchActionChannelMethod) as String;

        if (launchAction != null) {
          triggerAction(action: launchAction);
        }
      }

      Future<void> handleNotificationActionCall(MethodCall call) async {
        switch (call.method) {
          case triggerActionChannelMethod:
            return triggerAction(action: call.arguments as String);
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Isso é usado como um mecanismo simples para centralizar a manipulação de ações de notificação para que elas possam ser tratadas como multiplataforma usando uma enumeração fortemente tipada. O serviço permite que a plataforma nativa subjacente dispare uma ação quando uma é especificada no conteúdo de notificação. Também permite que o código comum verifique de maneira retrospectiva se uma ação foi especificada durante o início do aplicativo quando o Flutter está pronto para processá-la. Por exemplo, quando o aplicativo é iniciado, toque em uma notificação no centro de notificações.

1. Adicione um novo arquivo à pasta **services** chamado *notification_registration_service.dart* com a implementação a seguir.

    ```dart
    import 'dart:convert';
    import 'package:flutter/services.dart';
    import 'package:http/http.dart' as http;
    import 'package:push_demo/services/device_installation_service.dart';
    import 'package:push_demo/models/device_installation.dart';
    import 'package:flutter_secure_storage/flutter_secure_storage.dart';

    class NotificationRegistrationService {
      static const notificationRegistration =
          const MethodChannel('com.<your_organization>.pushdemo/notificationregistration');

      static const String refreshRegistrationChannelMethod = "refreshRegistration";
      static const String installationsEndpoint = "api/notifications/installations";
      static const String cachedDeviceTokenKey = "cached_device_token";
      static const String cachedTagsKey = "cached_tags";

      final deviceInstallationService = DeviceInstallationService();
      final secureStorage = FlutterSecureStorage();

      String baseApiUrl;
      String apikey;

      NotificationRegistrationService(this.baseApiUrl, this.apikey) {
        notificationRegistration
            .setMethodCallHandler(handleNotificationRegistrationCall);
      }

      String get installationsUrl => "$baseApiUrl$installationsEndpoint";

      Future<void> deregisterDevice() async {
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (cachedToken == null || serializedTags == null) {
          return;
        }

        var deviceId = await deviceInstallationService.getDeviceId();

        if (deviceId.isEmpty) {
          throw "Unable to resolve an ID for the device.";
        }

        var response = await http
            .delete("$installationsUrl/$deviceId", headers: {"apikey": apikey});

        if (response.statusCode != 200) {
          throw "Deregister request failed: ${response.reasonPhrase}";
        }

        await secureStorage.delete(key: cachedDeviceTokenKey);
        await secureStorage.delete(key: cachedTagsKey);
      }

      Future<void> registerDevice(List<String> tags) async {
        try {
          final deviceId = await deviceInstallationService.getDeviceId();
          final platform = await deviceInstallationService.getDevicePlatform();
          final token = await deviceInstallationService.getDeviceToken();

          final deviceInstallation =
              DeviceInstallation(deviceId, platform, token, tags);

          final response = await http.put(installationsUrl,
              body: jsonEncode(deviceInstallation),
              headers: {"apikey": apikey, "Content-Type": "application/json"});

          if (response.statusCode != 200) {
            throw "Register request failed: ${response.reasonPhrase}";
          }

          final serializedTags = jsonEncode(tags);

          await secureStorage.write(key: cachedDeviceTokenKey, value: token);
          await secureStorage.write(key: cachedTagsKey, value: serializedTags);
        } on PlatformException catch (e) {
          throw e.message;
        } catch (e) {
          throw "Unable to register device: $e";
        }
      }

      Future<void> refreshRegistration() async {
        final currentToken = await deviceInstallationService.getDeviceToken();
        final cachedToken = await secureStorage.read(key: cachedDeviceTokenKey);
        final serializedTags = await secureStorage.read(key: cachedTagsKey);

        if (currentToken == null ||
            cachedToken == null ||
            serializedTags == null ||
            currentToken == cachedToken) {
          return;
        }

        final tags = jsonDecode(serializedTags);

        return registerDevice(tags);
      }

      Future<void> handleNotificationRegistrationCall(MethodCall call) async {
        switch (call.method) {
          case refreshRegistrationChannelMethod:
            return refreshRegistration();
          default:
            throw MissingPluginException();
            break;
        }
      }
    }
    ```

    > [!NOTE]
    > Essa classe encapsula o uso do **DeviceInstallationService** e as solicitações para o serviço de back-end para executar o registro de requisitos, cancelar registro e atualizar as ações de registro. O argumento **apiKey** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).

1. Adicione um novo arquivo à pasta **lib** chamado *config.dart* com a implementação a seguir.

    ```dart
    class Config {
      static String apiKey = "API_KEY";
      static String backendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
    }
    ```

    > [!NOTE]
    > Isso é usado como uma forma simples de definir segredos do aplicativo. Substitua os valores do espaço reservado pelos seus. Você deve tê-los anotado ao criar o serviço de back-end. A **URL do Aplicativo de API** deve ser `https://<api_app_name>.azurewebsites.net/`. O membro **apiKey** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).
    >
    > Adicione-o ao arquivo gitignore para evitar confirmar esses segredos para o controle do código-fonte.

### <a name="implement-the-cross-platform-ui"></a>Implementar a interface do usuário multiplataforma

1. Em **main_page.dart**, substitua a função **build** pelo seguinte.

    ```dart
    @override
    Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 20.0, vertical: 40.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.end,
            crossAxisAlignment: CrossAxisAlignment.stretch,
            children: <Widget>[
              FlatButton(
                child: Text("Register"),
                onPressed: registerButtonClicked,
              ),
              FlatButton(
                child: Text("Deregister"),
                onPressed: deregisterButtonClicked,
              ),
            ],
          ),
        ),
      );
    }
    ```

1. Adicione as importações de requisito à parte superior do arquivo **main_page.dart**.

    ```dart
    import 'package:push_demo/services/notification_registration_service.dart';
    import 'config.dart';
    ```

1. Adicione um campo à classe **_MainPageState** para armazenar uma referência a **NotificationRegistrationService**.

    ```dart
    final notificationRegistrationService = NotificationRegistrationService(Config.backendServiceEndpoint, Config.apiKey);
    ```

1. Na classe **_MainPageState**, implemente os manipuladores de eventos para eventos **onPressed** dos botões **Registrar** e **Cancelar Registro**. Chame os métodos **Registrar**/**Cancelar registro** correspondentes e mostre um alerta para indicar o resultado.

    ```dart
    void registerButtonClicked() async {
        try {
          await notificationRegistrationService.registerDevice(List<String>());
          await showAlert(message: "Device registered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      void deregisterButtonClicked() async {
        try {
          await notificationRegistrationService.deregisterDevice();
          await showAlert(message: "Device deregistered");
        }
        catch (e) {
          await showAlert(message: e);
        }
      }

      Future<void> showAlert({ message: String }) async {
        return showDialog<void>(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) {
            return AlertDialog(
              title: Text('PushDemo'),
              content: SingleChildScrollView(
                child: ListBody(
                  children: <Widget>[
                    Text(message),
                  ],
                ),
              ),
              actions: <Widget>[
                FlatButton(
                  child: Text('OK'),
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                ),
              ],
            );
          },
        );
      }
    ```

1. Agora em **main.dart**, verifique se as importações a seguir estão presentes na parte superior do arquivo.

    ```dart
    import 'package:flutter/material.dart';
    import 'package:push_demo/models/push_demo_action.dart';
    import 'package:push_demo/services/notification_action_service.dart';
    import 'package:push_demo/main_page.dart';
    ```

1. Declare uma variável para armazenar a referência a uma instância do **NotificationActionService** e inicializá-la.

    ```dart
    final notificationActionService = NotificationActionService();
    ```

1. Adicione funções para manipular a exibição de um alerta quando uma ação for disparada.

    ```dart
    void notificationActionTriggered(PushDemoAction action) {
      showActionAlert(message: "${action.toString().split(".")[1]} action received");
    }

    Future<void> showActionAlert({ message: String }) async {
      return showDialog<void>(
        context: navigatorKey.currentState.overlay.context,
        barrierDismissible: false,
        builder: (BuildContext context) {
          return AlertDialog(
            title: Text('PushDemo'),
            content: SingleChildScrollView(
              child: ListBody(
                children: <Widget>[
                  Text(message),
                ],
              ),
            ),
            actions: <Widget>[
              FlatButton(
                child: Text('OK'),
                onPressed: () {
                  Navigator.of(context).pop();
                },
              ),
            ],
          );
        },
      );
    }
    ```

1. Atualize a função **main** para observar o fluxo **NotificationActionService** **actionTriggered** e verifique se há ações capturadas durante a inicialização do aplicativo.

    ```dart
    void main() async {
      runApp(MaterialApp(home: MainPage(), navigatorKey: navigatorKey,));
      notificationActionService.actionTriggered.listen((event) { notificationActionTriggered(event as PushDemoAction); });
      await notificationActionService.checkLaunchAction();
    }
    ```

    > [!NOTE]
    > Isso serve simplesmente para demonstrar o recebimento e a propagação de ações de notificação por push. Normalmente, elas seriam manipuladas silenciosamente, por exemplo, navegando para uma exibição específica ou atualizando alguns dados em vez de exibir um alerta nesse caso.
