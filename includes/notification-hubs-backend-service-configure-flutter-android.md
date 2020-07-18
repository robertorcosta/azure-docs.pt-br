---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 11bd05231aa3b5b336400d2434e63e57807a7e69
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156260"
---
### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de Serviços do Google

1. Use **Control** + **Clique** na pasta **android** e escolha **Abrir no Android Studio**. Em seguida, alterne para a exibição de **Projeto** (se ainda não tiver feito isso).

1. Localize o arquivo *google-services.json* que você baixou anteriormente ao configurar o projeto **PushDemo** no [Console do Firebase](https://console.firebase.google.com). Em seguida, arraste-o para o diretório raiz do módulo **app** (**android** > **android** > **app**).

### <a name="configure-build-settings-and-permissions"></a>Definir configurações e permissões de build

1. Alterne a exibição do **Projeto** para **Android**.

1. Abra **AndroidManifest.xml**, adicione as permissões **INTERNET** e **READ_PHONE_STATE** após o elemento **aplicativo** antes da marca de fechamento **</manifest>** .

    ```xml
    <manifest>
        <application>...</application>
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    </manifest>
    ```

### <a name="add-the-firebase-sdks"></a>Adicionar os SDKs do Firebase

1. No **Android Studio**, abra o arquivo **build.gradle** de nível de projeto (**Gradle Scripts** > **build.gradle (Project: android)** ). e verifique se você tem o classpath 'com.google.gms:google-services' no nó ``buildscript`` > **dependências**.

    ```json
    buildscript {

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
      }

      dependencies {
        // ...

        // Add the following line:
        classpath 'com.google.gms:google-services:4.3.3'  // Google Services plugin
      }
    }

    allprojects {
      // ...

      repositories {
        // Check that you have the following line (if not, add it):
        google()  // Google's Maven repository
        // ...
      }
    }
    ```

    > [!NOTE]
    > Referencie a versão mais recente de acordo com as instruções fornecidas no [Console do Firebase](https://console.firebase.google.com) quando você criou o **Projeto do Android**.

1. No arquivo **build.gradle** no nível do aplicativo(**Gradle Scripts** > **build.gradle (Module: app)** ), aplique o **plug-in do Google Services Gradle**. Aplique o plug-in logo acima do nó **android**.

    ```json
    // ...

    // Add the following line:
    apply plugin: 'com.google.gms.google-services'  // Google Services plugin

    android {
      // ...
    }
    ```

1. No mesmo arquivo, no nó **dependências**, adicione a dependência para a biblioteca Android do **Cloud Messaging** Android.

    ```json
    dependencies {
        // ...
        implementation 'com.google.firebase:firebase-messaging:20.2.0'
    }
    ```

    > [!NOTE]
    > Referencie a versão mais recente de acordo com a [documentação do cliente Android do Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/android/client).

1. Salve as alterações e clique no botão **Sincronizar Agora** (no prompt da barra de ferramentas) ou **Sincronizar Projeto com Arquivos Gradle**.

### <a name="handle-push-notifications-for-android"></a>Manipular Notificações por Push para o Android

1. No **Android Studio**, use **Control** + **Clique** na pasta do pacote **com.<sua_organização>.pushdemo** (**app** > **src** > **main** > **kotlin**), escolha **Pacote** no menu **Novo**. Insira **serviços** como o nome e pressione **Voltar**.

1. Use **Control** + **Clique** na pasta **services**, escolha **Arquivo/Classe do Kotlin** no menu **Novo**. Insira **DeviceInstallationService** como o nome e pressione **Voltar**.

1. Implemente o **DeviceInstallationService** usando o código a seguir.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import android.annotation.SuppressLint
    import android.content.Context
    import android.provider.Settings.Secure
    import com.google.android.gms.common.ConnectionResult
    import com.google.android.gms.common.GoogleApiAvailability
    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    @SuppressLint("HardwareIds")
    class DeviceInstallationService {

        companion object {
            const val DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
            const val GET_DEVICE_ID = "getDeviceId"
            const val GET_DEVICE_TOKEN = "getDeviceToken"
            const val GET_DEVICE_PLATFORM = "getDevicePlatform"
        }

        private var context: Context
        private var deviceInstallationChannel : MethodChannel

        val playServicesAvailable
            get() = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context) == ConnectionResult.SUCCESS

        constructor(context: Context, flutterEngine: FlutterEngine) {
            this.context = context
            deviceInstallationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, DEVICE_INSTALLATION_CHANNEL)
            deviceInstallationChannel.setMethodCallHandler { call, result -> handleDeviceInstallationCall(call, result) }
        }

        fun getDeviceId() : String
            = Secure.getString(context.applicationContext.contentResolver, Secure.ANDROID_ID)

        fun getDeviceToken() : String {
            if(!playServicesAvailable) {
                throw Exception(getPlayServicesError())
            }

            // TODO: Revisit once we have created the PushNotificationsFirebaseMessagingService
            val token = "Placeholder_Get_Value_From_FirebaseMessagingService_Implementation"

            if (token.isNullOrBlank()) {
                throw Exception("Unable to resolve token for FCM.")
            }

            return token
        }

        fun getDevicePlatform() : String = "fcm"

        private fun handleDeviceInstallationCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                GET_DEVICE_ID -> {
                    result.success(getDeviceId())
                }
                GET_DEVICE_TOKEN -> {
                    getDeviceToken(result)
                }
                GET_DEVICE_PLATFORM -> {
                    result.success(getDevicePlatform())
                }
                else -> {
                    result.notImplemented()
                }
            }
        }

        private fun getDeviceToken(result: MethodChannel.Result) {
            try {
                val token = getDeviceToken()
                result.success(token)
            }
            catch (e: Exception) {
                result.error("ERROR", e.message, e)
            }
        }

        private fun getPlayServicesError(): String {
            val resultCode = GoogleApiAvailability.getInstance().isGooglePlayServicesAvailable(context)

            if (resultCode != ConnectionResult.SUCCESS) {
                return if (GoogleApiAvailability.getInstance().isUserResolvableError(resultCode)){
                    GoogleApiAvailability.getInstance().getErrorString(resultCode)
                } else {
                    "This device is not supported"
                }
            }

            return "An error occurred preventing the use of push notifications"
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/deviceinstallation`. Isso foi definido na parte do Flutter do aplicativo em **DeviceInstallationService.dart**. Nesse caso, as chamadas são feitas do código comum para o host nativo. Substitua **<sua_organização>** pela sua organização, sempre que ela for usada.
    >
    > Essa classe fornece uma ID exclusiva (usando [Secure.AndroidId](https://developer.android.com/reference/android/provider/Settings.Secure#ANDROID_ID)) como parte do conteúdo de registro do hub de notificação.

1. Adicione outro **Arquivo/Classe do Kotlin** à pasta **services** chamada *NotificationRegistrationService* e adicione o código a seguir.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodChannel

    class NotificationRegistrationService {

        companion object {
            const val NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
            const val REFRESH_REGISTRATION = "refreshRegistration"
        }

        private var notificationRegistrationChannel : MethodChannel

        constructor(flutterEngine: FlutterEngine) {
            notificationRegistrationChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationRegistrationService.NOTIFICATION_REGISTRATION_CHANNEL)
        }

        fun refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, null)
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/notificationregistration`. Isso foi definido na parte Flutter do aplicativo em **NotificationRegistrationService.dart**. Nesse caso, as chamadas são feitas do host nativo para o código comum. Mais uma vez, substitua **<sua_organização>** pela sua organização sempre que isso for usado.

1. Adicione outro **Arquivo/Classe Kotlin** à pasta **services** chamada *NotificationActionService* e o código a seguir.

    ```kotlin
    package com.<your_organization>.pushdemo.services

    import io.flutter.embedding.engine.FlutterEngine
    import io.flutter.plugin.common.MethodCall
    import io.flutter.plugin.common.MethodChannel

    class NotificationActionService {
        companion object {
            const val NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
            const val TRIGGER_ACTION = "triggerAction"
            const val GET_LAUNCH_ACTION = "getLaunchAction"
        }

        private var notificationActionChannel : MethodChannel
        var launchAction : String? = null

        constructor(flutterEngine: FlutterEngine) {
            notificationActionChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, NotificationActionService.NOTIFICATION_ACTION_CHANNEL)
            notificationActionChannel.setMethodCallHandler { call, result -> handleNotificationActionCall(call, result) }
        }

        fun triggerAction(action: String) {
            notificationActionChannel.invokeMethod(NotificationActionService.TRIGGER_ACTION, action)
        }

        private fun handleNotificationActionCall(call: MethodCall, result: MethodChannel.Result) {
            when (call.method) {
                NotificationActionService.GET_LAUNCH_ACTION -> {
                    result.success(launchAction)
                }
                else -> {
                    result.notImplemented()
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/notificationaction`. Isso foi definido na parte Flutter do aplicativo em **NotificationActionService.dart**. As chamadas podem ser feitas em ambas as direções nesse caso. Substitua **<sua_organização>** pela sua organização, sempre que ela for usada.

1. Adicione um novo **Arquivo/Classe do Kotlin** ao pacote **com.<sua_organização>.pushdemo** chamado *PushNotificationsFirebaseMessagingService* e implemente usando o código a seguir.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.os.Handler
    import android.os.Looper
    import com.google.firebase.messaging.FirebaseMessagingService
    import com.google.firebase.messaging.RemoteMessage
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService

    class PushNotificationsFirebaseMessagingService : FirebaseMessagingService() {

        companion object {
            var token : String? = null
            var notificationRegistrationService : NotificationRegistrationService? = null
            var notificationActionService : NotificationActionService? = null
        }

        override fun onNewToken(token: String) {
            PushNotificationsFirebaseMessagingService.token = token
            notificationRegistrationService?.refreshRegistration()
        }

        override fun onMessageReceived(message: RemoteMessage) {
            message.data.let {
                Handler(Looper.getMainLooper()).post {
                    notificationActionService?.triggerAction(it.getOrDefault("action", null))
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe é responsável por lidar com notificações quando o aplicativo está sendo executado em primeiro plano. Ele chamará condicionalmente a **triggerAction** no **NotificationActionService** se uma ação for incluída no conteúdo de notificação que é recebido em **onMessageReceived**. Isso também chamará **refreshRegistration** no **NotificationRegistrationService** quando o token **Firebase** for regenerado substituindo a função **onNewToken**.
    >
    > Mais uma vez, substitua **<sua_organização>** pela sua organização sempre que isso for usado.

1. Em **AndroidManifest.xml** (**app** > **src** > **main**), adicione **PushNotificationsFirebaseMessagingService** à parte inferior do elemento **application** com o filtro de intenção `com.google.firebase.MESSAGING_EVENT`.

    ```xml
    <manifest>
        <application>
            <!-- EXISTING MANIFEST CONTENT -->
             <service
                android:name="com.<your_organization>.pushdemo.PushNotificationsFirebaseMessagingService"
                android:exported="false">
                <intent-filter>
                    <action android:name="com.google.firebase.MESSAGING_EVENT" />
                </intent-filter>
            </service>
        </application>
    </manifest>
    ```

1. De volta ao **DeviceInstallationService**, verifique se as importações a seguir estão presentes na parte superior do arquivo.

    ```kotlin
    package com.<your_organization>.pushdemo
    import com.<your_organization>.pushdemo.services.PushNotificationsFirebaseMessagingService
    ```

    > [!NOTE]
    > Substitua **<sua_organização>** pelo valor da sua organização.

1. Atualize o texto do espaço reservado *Placeholder_Get_Value_From_FirebaseMessagingService_Implementation* para obter o valor do token do **PushNotificationFirebaseMessagingService**.

    ```kotlin
    fun getDeviceToken() : String {
        if(!playServicesAvailable) {
            throw Exception(getPlayServicesError())
        }

        // Get token from the PushNotificationsFirebaseMessagingService.token field.
        val token = PushNotificationsFirebaseMessagingService.token

        if (token.isNullOrBlank()) {
            throw Exception("Unable to resolve token for FCM.")
        }

        return token
    }
    ```

1. Em **MainActivity**, verifique se as importações a seguir estão presentes na parte superior do arquivo.

    ```kotlin
    package com.<your_organization>.pushdemo

    import android.content.Intent
    import android.os.Bundle
    import com.google.android.gms.tasks.OnCompleteListener
    import com.google.firebase.iid.FirebaseInstanceId
    import com.<your_organization>.pushdemo.services.DeviceInstallationService
    import com.<your_organization>.pushdemo.services.NotificationActionService
    import com.<your_organization>.pushdemo.services.NotificationRegistrationService
    import io.flutter.embedding.android.FlutterActivity
    ```

    > [!NOTE]
    > Substitua **<sua_organização>** pelo valor da sua organização.

1. Adicione uma variável para armazenar uma referência ao **DeviceInstallationService**.

    ```kotlin
    private lateinit var deviceInstallationService: DeviceInstallationService
    ```

1. Adicione uma função chamada **processNotificationActions** para verificar se uma **Intenção** tem um valor extra chamado **ação**. Dispare condicionalmente essa ação ou armazene-a para uso posterior se a ação estiver sendo processada durante a inicialização do aplicativo.

    ```kotlin
     private fun processNotificationActions(intent: Intent, launchAction: Boolean = false) {
        if (intent.hasExtra("action")) {
            var action = intent.getStringExtra("action");

            if (action.isNotEmpty()) {
                if (launchAction) {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.launchAction = action
                }
                else {
                    PushNotificationsFirebaseMessagingService.notificationActionService?.triggerAction(action)
                }
            }
        }
    }
    ```

1. Substitua a função **onNewIntent** para chamar **processNotificationActions**.

    ```kotlin
    override fun onNewIntent(intent: Intent) {
        super.onNewIntent(intent)
        processNotificationActions(intent)
    }
    ```

    > [!NOTE]
    > Como **LaunchMode** para **MainActivity** está definido como **SingleTop**, uma **Intent** será enviada para a instância de **Actitivity** existente por meio da função **onNewIntent**, em vez da função **onCreate**, portanto, você deverá manipular uma **Intent** de entrada nas funções **onCreate** e **onNewIntent**.

1. Substitua a função **onCreate**, defina **deviceInstallationService** para uma nova instância de **DeviceInstallationService**.

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        flutterEngine?.let {
            deviceInstallationService = DeviceInstallationService(context, it)
        }
    }
    ```

1. Defina as propriedades **notificationActionService** e **notificationRegistrationService** em **PushNotificationFirebaseMessagingServices**.

    ```kotlin
    flutterEngine?.let {
      deviceInstallationService = DeviceInstallationService(context, it)
      PushNotificationsFirebaseMessagingService.notificationActionService = NotificationActionService(it)
      PushNotificationsFirebaseMessagingService.notificationRegistrationService = NotificationRegistrationService(it)
    }
    ```

1. Na mesma função, chame condicionalmente **FirebaseInstanceId.getInstance().instanceId**. Implemente o **OnCompleteListener** para definir o valor do **token** resultante em **PushNotificationFirebaseMessagingService** antes de chamar **refreshRegistration**.

    ```kotlin
    if(deviceInstallationService?.playServicesAvailable) {
        FirebaseInstanceId.getInstance().instanceId
            .addOnCompleteListener(OnCompleteListener { task ->
                if (!task.isSuccessful)
                    return@OnCompleteListener

                PushNotificationsFirebaseMessagingService.token = task.result?.token
                PushNotificationsFirebaseMessagingService.notificationRegistrationService?.refreshRegistration()
            })
    }
    ```

1. Ainda em **onCreate**, chame **processNotificationActions** no final da função. Use *true* para o argumento *launchAction* para indicar que essa ação está sendo processada durante a inicialização do aplicativo.

    ```kotlin
    processNotificationActions(this.intent, true)
    ```

> [!NOTE]
> Você deve registrar novamente o aplicativo sempre que executá-lo e interrompê-lo em uma sessão de depuração para continuar a receber notificações por push.
