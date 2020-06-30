---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 40bc1c8c3d578e35b6689124f60f82d8ea85f0f2
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112002"
---
### <a name="create-the-xamarinforms-solution"></a>Criar a solução do Xamarin.Forms

1. No **Visual Studio**, crie uma solução do **Xamarin.Forms** usando um **Aplicativo de Formulários em Branco** como o modelo e inserindo *PushDemo* como o **Nome do Projeto**.

    > [!NOTE]
    > Na caixa de diálogo **Configurar o Aplicativo de Formulários em Branco**, verifique se o **Identificador de Organização** corresponde ao valor usado anteriormente e se os destinos **Android** e **iOS** estão marcados.

1. **Control** + **Clique** na solução *PushDemo* e escolha **Atualizar Pacotes NuGet**.
1. **Control** + **Clique** na solução *PushDemo* e escolha **Gerenciar Pacotes NuGet**.
1. Pesquise **Newtonsoft.Json** e verifique se ele está marcado.
1. Clique em **Adicionar Pacotes** e em **Aceitar** quando solicitado a aceitar os termos de licença.
1. Compile e execute o aplicativo em cada plataforma de destino (**Command** + **Enter**) para testar as execuções do aplicativo modelo em seus dispositivos.

### <a name="implement-the-cross-platform-components"></a>Implementar os componentes multiplataforma

1. **Control** + **Clique** no projeto **PushDemo**, escolha **Nova Pasta** no menu **Adicionar** e clique em **Adicionar** usando *Modelos* como o **Nome da Pasta**.

1. **Control** + **Clique** na pasta **Modelos** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **Geral** > **Classe Vazia**, digite *DeviceInstallation.cs* e adicione a implementação a seguir.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Adicione uma **Enumeração Vazia** à pasta **Modelos** chamada *PushDemoAction.cs* com a implementação a seguir.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Adicione uma nova pasta ao projeto **PushDemo** chamada *Serviços* e adicione um **Classe Vazia** a essa pasta, chamada *ServiceContainer.cs* com a implementação a seguir.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Esta é uma versão reduzida da classe [ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) do repositório [XamCAT](https://github.com/xamcat/mobcat-library). Ela será usada como um contêiner leve de IoC (inversão de controle).

1. Adicione uma **Interface Vazia** à pasta **Serviços** chamada *IDeviceInstallationService.cs* e adicione o código a seguir.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Essa interface será implementada e inicializada em cada destino posteriormente para fornecer a funcionalidade específica da plataforma e as informações de **DeviceInstallation** exigidas pelo serviço de back-end.

1. Adicione outra **Interface Vazia** à pasta **Serviços** chamada *INotificationRegistrationService.cs* e adicione o código a seguir.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Isso manipulará a interação entre o cliente e o serviço de back-end.

1. Adicione outra **Interface Vazia** à pasta **Serviços** chamada *INotificationActionService.cs* e adicione o código a seguir.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Isso é usado como um mecanismo simples para centralizar a manipulação de ações de notificação.

1. Adicione uma **Interface Vazia** à pasta **Serviços** chamada *IPushDemoNotificationActionService.cs* que deriva da *INotificationActionService*, com a implementação a seguir.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Esse tipo é específico para o aplicativo **PushDemo** e usa a enumeração **PushDemoAction** para identificar a ação que está sendo disparada de maneira fortemente tipada.

1. Adicione uma **Classe Vazia** à pasta **Serviços** chamada *NotificationRegistrationService.cs* implementando a *INotificationRegistrationService* com o código a seguir.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string CachedTagsKey = "cached_tags";
            const string RequestUrl = "api/notifications/installations";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public Task DeregisterDeviceAsync()
            {
                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                SecureStorage.Remove(CachedTagsKey);

                return SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}");
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                if (deviceInstallation == null)
                    throw new Exception($"Unable to get device installation information.");

                if (string.IsNullOrWhiteSpace(deviceInstallation.InstallationId))
                    throw new Exception($"No {nameof(deviceInstallation.InstallationId)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.Platform))
                    throw new Exception($"No {nameof(deviceInstallation.Platform)} value for {nameof(DeviceInstallation)}");

                if (string.IsNullOrWhiteSpace(deviceInstallation.PushChannel))
                    throw new Exception($"No {nameof(deviceInstallation.PushChannel)} value for {nameof(DeviceInstallation)}");

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                var serializedTags = JsonConvert.SerializeObject(tags);
                await SecureStorage.SetAsync(CachedTagsKey, serializedTags);
            }

            public async Task RefreshRegistrationAsync()
            {
                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(serializedTags))
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > O argumento **apiKey** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).

1. Adicione uma **Classe Vazia** à pasta **Serviços** chamada *PushDemoNotificationActionService.cs* implementando a *IPushDemoNotificationActionService* com o código a seguir.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Adicione uma **Classe Vazia** ao projeto **PushDemo** chamada *Config.cs* com a implementação a seguir.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Isso é usado como uma forma simples de manter os segredos fora do controle do código-fonte. Você pode substituir esses valores como parte de um build automatizado ou substituí-los usando uma classe parcial local. Isso será feito na próxima etapa.
    >
    > O campo **ApiKey** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).

1. Adicione outra **Classe Vazia** ao projeto **PushDemo**, desta vez chamada *Config.local_secrets.cs*, com a implementação a seguir.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Substitua os valores do espaço reservado pelos seus. Você deve tê-los anotado ao criar o serviço de back-end. A URL do **Aplicativo de API** deve ser ``https://<api_app_name>.azurewebsites.net/``. Lembre-se de adicionar ``*.local_secrets.*`` ao arquivo gitignore para evitar confirmar esse arquivo.
    >
    > O campo **ApiKey** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).

1. Adicione uma **Classe Vazia** ao projeto **PushDemo** chamada *Bootstrap.cs* com a implementação a seguir.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > O método **Begin** será chamado em cada plataforma quando o aplicativo iniciar a passagem em uma implementação específica da plataforma de **IDeviceInstallationService**.
    >
    > O argumento construtor da **apiKey** do **NotificationRegistrationService** só será necessário se você optar por concluir a seção [Autenticar clientes usando uma Chave de API](#authenticate-clients-using-an-api-key-optional).

### <a name="implement-the-cross-platform-ui"></a>Implementar a interface do usuário multiplataforma

1. No projeto **PushDemo**, abra **MainPage.xaml** e substitua o controle **StackLayout** pelo seguinte.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Agora, em **MainPage.xaml.cs**, adicione um campo de suporte **readonly** para armazenar uma referência à implementação **INotificationRegistrationService**.

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. No construtor **MainPage**, resolva a implementação de **INotificationRegistrationService** usando o **ServiceContainer** e atribua-o ao campo de suporte *_notificationRegistrationService_*.

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implemente os manipuladores de eventos para os botões **RegisterButton** e **DeregisterButton** dos eventos **Clicados** chamando os métodos **Register**/**Deregister** correspondentes.

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Agora, em **App.xaml.cs**, verifique se os namespaces a seguir estão referenciados.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implemente o manipulador de eventos para o evento **IPushDemoNotificationActionService** **ActionTriggered**.

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. No construtor do **Aplicativo**, resolva a implementação de **IPushNotificationActionService** usando o **ServiceContainer** e assine o evento **IPushDemoNotificationActionService** **ActionTriggered**.

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > Isso serve simplesmente para demonstrar o recebimento e a propagação de ações de notificação por push. Normalmente, elas seriam manipuladas silenciosamente, por exemplo, navegando para uma exibição específica ou atualizando alguns dados em vez de exibir um alerta por meio da **Página** raiz, **MainPage** nesse caso.
