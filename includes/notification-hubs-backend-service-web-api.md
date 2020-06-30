---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095127"
---
### <a name="create-a-web-project"></a>Criar um projeto Web

1. No **Visual Studio**, selecione **Arquivo** > **Nova Solução**.

1. Selecione **.NET Core** > **Aplicativo** > **ASP.NET Core** > **API** > **Avançar**.
  
1. Na caixa de diálogo **Configurar a nova API Web do ASP.NET Core**, selecione **Estrutura de Destino** como **.NET Core 3.1**.

1. Insira *PushDemoApi* como o **Nome do Projeto** e selecione **Criar**.

1. Inicie a depuração (**Comando** + **Enter**) para testar o aplicativo com modelo.

    > [!NOTE]
    > O aplicativo com modelo está configurado para usar o **WeatherForecastController** como a *launchUrl*. Isso é definido em **Propriedades** > **launchSettings.json**.  
    >
    > Se você receber uma solicitação com uma mensagem **Certificado de desenvolvimento inválido encontrado**:
    >
    > 1. Clique em **Sim** para concordar em executar a ferramenta "dotnet dev-certs https" para corrigir isso. A ferramenta "dotnet dev-certs https" solicita que você insira uma senha para o certificado e a senha do seu Conjunto de chaves.
    >
    > 1. Clique em **Sim** quando solicitado a **instalar e confiar no novo**de certificado, insira a senha para seu Conjunto de chaves.

1. Expanda a pasta **Controladores** e exclua **WeatherForecastController.cs**.

1. Excluir **WeatherForecast.cs**.

1. **Control** + **Clique** no projeto **PushDemoApi** e escolha **Novo arquivo...** no menu **Adicionar**.

1. Configure os valores da configuração local usando a [ferramenta Gerenciador de Segredos](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager). O desacoplamento dos segredos da solução garante que eles não acabem no controle do código-fonte. Abra o **Terminal**, acesse o diretório do arquivo de projeto e execute os seguintes comandos:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Substitua os valores de espaço reservado pelos seus valores do nome do hub de notificação e cadeia de conexão. Você os anotou na seção [criar um hub de notificação](#create-a-notification-hub). Caso contrário, procure-os no [Azure](https://portal.azure.com).

    **NotificationsHub:Name**:  
    Confira *Nome* no resumo dos **Fundamentos** na parte superior de **Visão geral**.  

    **NotificationHub:ConnectionString**:  
    Veja *DefaultFullSharedAccessSignature* em **Políticas de Acesso**

    > [!NOTE]
    > Para cenários de produção, examine opções como o [Azure KeyVault](https://azure.microsoft.com/services/key-vault) para armazenar a cadeia de conexão com segurança. Para simplificar, os segredos serão adicionados às configurações do aplicativo do [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/).

### <a name="authenticate-clients-using-an-api-key-optional"></a>Autenticar clientes usando uma chave de API (opcional)

As chaves de API não são tão seguras como tokens, mas serão suficientes para os fins deste tutorial. Uma chave de API pode ser configurada facilmente por meio do [Middleware do ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Adicione a **chave de API** aos valores de configuração local.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Você deve substituir o valor do espaço reservado pelo seu valor e anotá-lo.

1. **Control** + **Clique** no projeto **PushDemoApi**, escolha **Nova Pasta** no menu **Adicionar** e clique em **Adicionar** usando *Autenticação* como o **Nome da Pasta**.

1. **Control** + **Clique** na pasta **Autenticação** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **Geral** > **Classe Vazia**, digite *ApiKeyAuthOptions.cs* como o **Nome** e clique em **Novo**, adicionando a implementação a seguir.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **Autenticação** chamada *ApiKeyAuthHandler.cs* e adicione a implementação a seguir.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > Um [Manipulador de Autenticação](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) é um tipo que implementa o comportamento de um esquema, neste caso, um esquema de chave de API personalizado.

1. Adicione outra **Classe Vazia** à pasta **Autenticação** chamada *ApiKeyAuthenticationBuilderExtensions.cs* e adicione a implementação a seguir.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Esse método de extensão simplifica o código de configuração do middleware em **Startup.cs** tornando-o mais legível e geralmente mais fácil de seguir.

1. No **Startup.cs**, atualize o método **ConfigureServices** para configurar a autenticação da chave de API abaixo da chamada para ao método **services.AddControllers**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Ainda no **Startup.cs**, atualize o método **Configure** para chamar os métodos de extensão **UseAuthentication** e **UseAuthorization** no **IApplicationBuilder** do aplicativo. Verifique se esses métodos são chamados depois de **UseRouting** e antes de **app.UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Chamar **UseAuthentication** registra o middleware que usa os esquemas de autenticação registrados anteriormente (de **ConfigureServices**). Isso deve ser chamado antes de qualquer middleware que dependa de usuários serem autenticados.

### <a name="add-dependencies-and-configure-services"></a>Adicionar dependências e configurar serviços

O ASP.NET Core é compatível com o padrão de design de software de [DI (injeção de dependência)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1), que é uma técnica para alcançar a [IoC (Inversão de Controle)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.  

O uso do hub de notificação e do [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) é encapsulado dentro de um serviço. O serviço é registrado e disponibilizado por meio de uma abstração adequada.

1. **Control** + **Clique** na pasta **Dependências** e escolha **Gerenciar Pacotes NuGet...** .

1. Pesquise **Microsoft.Azure.NotificationHubs** e verifique se ele está marcado.

1. Clique em **Adicionar Pacotes** e em **Aceitar** quando solicitado a aceitar os termos de licença.

1. **Control** + **Clique** no projeto **PushDemoApi**, escolha **Nova Pasta** no menu **Adicionar** e clique em **Adicionar** usando *Modelos* como o **Nome da Pasta**.

1. **Control** + **Clique** na pasta **Modelos** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **Geral** > **Classe Vazia**, digite *PushTemplates.cs* como o **Nome** e clique em **Novo**, adicionando a implementação a seguir.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe contém o conteúdo de notificação indexado para as notificações genéricas e silenciosas exigidas por esse cenário. Os conteúdos são definidos fora da [Instalação](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) para permitir a experimentação sem a necessidade de atualizar as instalações existentes por meio do serviço. O tratamento de alterações nas instalações dessa forma está fora do escopo deste tutorial. Para produção, considere [modelos personalizados](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Selecione **Geral** > **Classe Vazia**, digite *DeviceInstallation.cs* como o **Nome** e clique em **Novo**, adicionando a implementação a seguir.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **Modelos** chamada *NotificationRequest.cs* e adicione a implementação a seguir.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Adicione outra **Classe Vazia** à pasta **Modelos** chamada *NotificationHubOptions.cs* e adicione a implementação a seguir.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Adicione uma nova pasta ao projeto **PushDemoApi** chamada *Serviços*.

1. Adicione uma **Interface Vazia** à pasta **Serviços** chamada *INotificationService.cs* e adicione a implementação a seguir.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Adicione uma **Classe Vazia** à pasta **Serviços** chamada *NotificationHubsService.cs* e adicione o seguinte código para implementar a interface **INotificationService**:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > A expressão de marcação fornecida para **SendTemplateNotificationAsync** é limitada a 20 marcações. Ela é limitada a 6 para a maioria dos operadores, mas a expressão contém apenas ORs (||) nesse caso. Se houver mais de 20 marcações na solicitação, elas deverão ser divididas em várias solicitações. Confira a documentação [Expressões de marca e Roteamento](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) para obter mais detalhes.

1. No **Startup.cs**, atualize o método **ConfigureServices** para adicionar o **NotificationHubsService** como uma implementação de singleton de **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Criar a API de notificações

1. **Control** + **Clique** na pasta **Controladores** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **ASP.NET Core** > **Classe do Controlador da API Web**, digite *NotificationsController* como o **Nome** e clique em **Novo**.

1. Adicione os namespaces a seguir à parte superior do arquivo.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Atualize o controlador modelo para que derive de **ControllerBase** e seja decorado com o atributo **ApiController**.

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > A classe base **Controller** é compatível com exibições, mas isso não é necessário nesse caso e, portanto, a **ControllerBase** pode ser usada.

1. Se você optou por concluir a seção [Autenticar clientes usando uma chave de API](#authenticate-clients-using-an-api-key-optional), decore a **NotificationsController** com o atributo **Authorize** também.

    ```cs
    [Authorize]
    ```

1. Atualize o construtor para aceitar a instância registrada de **INotificationService** como um argumento e atribua a instância a um membro ReadOnly.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. No **launchSettings.json** (dentro da pasta **Propriedades**), altere a **launchUrl** de `weatherforecast` para *api/notifications* para corresponder à URL especificada no atributo **Route** de **RegistrationsController**.

1. Inicie a depuração (**Command** + **Enter**) para validar se o aplicativo está funcionando com o novo **NotificationsController** e retorna um status **401 Não autorizado**.

    > [!NOTE]
    > O Visual Studio pode não iniciar automaticamente o aplicativo no navegador. Você usará o [Postman](https://www.postman.com/downloads) para testar a API deste ponto em diante.

1. Em uma nova guia do **[Postman](https://www.postman.com/downloads)** , defina a solicitação como **GET** e insira o endereço abaixo.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > O endereço do localhost deve corresponder ao valor de **applicationUrl** encontrado em **Propriedades** > **launchSettings.json**. O padrão deve ser `https://localhost:5001;http://localhost:5000`, no entanto, isso é algo que deve ser verificado se você receber uma resposta 404.

1. Se você optou por concluir a seção [Autenticar clientes usando uma chave de API](#authenticate-clients-using-an-api-key-optional), configure os cabeçalhos de solicitação para incluir o valor de **apikey**.

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Clique no botão **Enviar**.

    > [!NOTE]
    > Você deve receber um status **200 OK** com algum conteúdo em **JSON**.
    >
    > Se você receber um aviso de **verificação de certificado SSL**, poderá desativar a configuração da solicitação de verificação de certificado SSL do **[Postman](https://www.postman.com/downloads)** nas **Configurações**.

1. Substitua os métodos de classe do modelo pelo código a seguir.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Criar o Aplicativo de API

Agora você criará um [aplicativo de API](https://azure.microsoft.com/services/app-service/api/) no [Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) para hospedar o serviço de back-end.  

1. Entre no [portal do Azure](https://portal.azure.com).

1. Clique em **Criar um recurso**, pesquise e escolha **Aplicativo de API** e clique em **Criar**.

1. Atualize os campos a seguir e clique em **Criar**.

    **Nome do aplicativo:**  
    Insira um nome globalmente exclusivo para o **Aplicativo de API**

    **Assinatura:**  
    Escolha a mesma **Assinatura** de destino em que você criou o hub de notificação.

    **Grupo de recursos:**  
    Escolha o mesmo **Grupo de Recursos** em que você criou o hub de notificação.

    **Localização/Plano do Serviço de Aplicativo:**  
    Criar um **Plano do Serviço de Aplicativo**  

    > [!NOTE]
    > Altere da opção padrão para um plano que inclua compatibilidade com **SSL**. Caso contrário, você precisará seguir as etapas apropriadas ao trabalhar com o aplicativo móvel para impedir que solicitações **HTTP** sejam bloqueadas.

    **Application Insights:**  
    Mantenha a opção sugerida (um recurso será criado usando esse nome) ou selecione um recurso existente.

1. Quando o **Aplicativo de API** tiver sido provisionado, navegue até esse recurso.

1. Anote a propriedade **URL** no resumo dos **Fundamentos** na parte superior da **Visão Geral**. Essa URL é o *ponto de extremidade de back-end* que será usado posteriormente neste tutorial.

    > [!NOTE]
    > A URL usa o nome do aplicativo de API especificado anteriormente, com o formato `https://<app_name>.azurewebsites.net`.

1. Selecione **Configuração** na lista (em **Configurações**).  

1. Para cada uma das configurações abaixo, clique em **Nova configuração de aplicativo** para inserir o **Nome** e um **Valor** e clique em **OK**.

   | Nome                               | Valor                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Essas são as mesmas configurações definidas anteriormente nas configurações do usuário. Você deve ser capaz de copiá-las. A configuração **Authentication:ApiKey** será necessária somente se você optar por concluir a seção [Autenticar clientes usando uma chave de API](#authenticate-clients-using-an-api-key-optional). Para cenários de produção, examine opções como o [Azure KeyVault](https://azure.microsoft.com/services/key-vault). Elas foram adicionadas como configurações de aplicativo para simplificar, nesse caso.

1. Depois que todas as configurações do aplicativo forem adicionadas, clique em **Salvar** e **Continuar**.

### <a name="publish-the-backend-service"></a>Publicar o serviço de back-end

Em seguida, você implanta o aplicativo no Aplicativo de API para torná-lo acessível a todos os dispositivos.  

1. Altere a configuração de **Depuração** para **Versão** se você ainda não tiver feito isso.

1. **Control** + **Clique** no projeto **PushDemoApi** e escolha **Publicar no Azure...** no menu **Publicar**.

1. Siga o fluxo de autenticação se solicitado. Use a conta que você usou na seção anterior [criar o Aplicativo de API](#create-the-api-app).

1. Selecione na lista o **Aplicativo de API do Serviço de Aplicativo do Azure** criado anteriormente como o destino de publicação e clique em **Publicar**.

Ao concluir, o assistente publica o aplicativo no Azure e abre o aplicativo. Anote a **URL** se você ainda não tiver feito isso. Essa URL é o *ponto de extremidade de back-end* que é usado posteriormente neste tutorial.

### <a name="validating-the-published-api"></a>Validando a API publicada

1. No **[Postman](https://www.postman.com/downloads)** , abra uma nova guia, defina a solicitação como **POST** e insira o endereço abaixo. Substitua o espaço reservado pelo endereço básico que você anotou na seção anterior: [publicar o serviço de back-end](#publish-the-backend-service).

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > O endereço básico deve estar no formato ``https://<app_name>.azurewebsites.net/``

1. Se você optou por concluir a seção [Autenticar clientes usando uma chave de API](#authenticate-clients-using-an-api-key-optional), configure os cabeçalhos de solicitação para incluir o valor de **apikey**.

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Escolha a opção **bruto** para o **Corpo**, escolha **JSON** na lista de opções de formato e inclua algum conteúdo de espaço reservado **JSON**:

    ```json
    {}
    ```

1. Clique em **Enviar**.

    > [!NOTE]
    > Você deve receber um status **400 Solicitação Incorreta** do serviço.

1. Execute as etapas 1 a 4 novamente, mas desta vez especificando o ponto de extremidade de solicitações para validar se você receberá a mesma resposta **400 Solicitação Incorreta**.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Ainda não é possível testar a API usando dados de solicitação válidos, pois isso exigirá informações específicas da plataforma do aplicativo móvel cliente.
