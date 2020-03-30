---
title: Adicionar suporte do Key Vault ao projeto do ASP.NET usando o Visual Studio - Azure Key Vault | Microsoft Docs
description: Use este tutorial para aprender a adicionar suporte do Key Vault para um aplicativo Web ASP.NET ou ASP.NET Core.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457006"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Adicione o Key Vault ao seu aplicativo Web usando os Serviços Conectados do Visual Studio

Neste tutorial, você aprenderá como adicionar facilmente tudo o que você precisa para começar a usar o Azure Key Vault para gerenciar seus segredos para projetos Web no Visual Studio, quer esteja usando o ASP.NET Core ou qualquer outro tipo de projeto do ASP.NET. Usando o recurso Connected Services no Visual Studio, você pode adicionar automaticamente todos os pacotes e configurações do NuGet que você precisa para se conectar ao Key Vault no Azure.

Para obter detalhes sobre as alterações que os Serviços Conectados realizam em seu projeto para habilitar o Key Vault, confira [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET 4.7.1](#how-your-aspnet-framework-project-is-modified) ou [Serviço Conectado do Key Vault — O que aconteceu com meu projeto do ASP.NET Core](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Pré-requisitos

- **Uma assinatura do Azure.** Se você não tem uma assinatura, inscreva-se em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 versão 16.3** ou posterior, ou **Visual Studio 2017 versão 15.7** com a carga de trabalho **web development** instalada. [Baixe agora](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Para ASP.NET (não Core) com o Visual Studio 2017, você precisa do .NET Framework 4.7.1 ou posterior de Ferramentas de Desenvolvimento, que não são instalados por padrão. Para instalá-las, inicie o Instalador do Visual Studio, escolha **Modificar** e, em seguida, selecione **Componentes Individuais**. Em seguida, do lado direito, expanda **Desenvolvimento Web e do ASP.NET** e selecione **Ferramentas de Desenvolvimento do .NET Framework 4.7.1**.
- Um ASP.NET 4.7.1 ou posterior, ou ASP.NET projeto web Core 2.0 ou posterior aberto.

## <a name="add-key-vault-support-to-your-project"></a>Como adicionar suporte do Key Vault ao seu projeto

Antes de começar, certifique-se de que você está conectado ao Visual Studio. Faça login com a mesma conta que você usa para sua assinatura do Azure. Em seguida, abra um ASP.NET 4.7.1 ou posterior, ou ASP.NET projeto web Core 2.0 e faça as seguintes etapas:

1. No **Solution Explorer,** clique com o botão direito do mouse no projeto ao qual deseja adicionar o suporte ao Key Vault e escolha **Adicionar** > **serviço conectado**.
   A página do Serviço Conectado aparece com os serviços que você pode adicionar ao seu projeto.
1. No menu de serviços disponíveis, selecione **Proteger Segredos com o Azure Key Vault**.

   ![Selecione "Proteger Segredos com o Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecione a assinatura que deseja usar e escolha um Cofre de Chaves novo ou existente. Se você escolher o novo Cofre de Chaves, um link **Editar** será exibido. Selecione-o para configurar seu novo Cofre de Chaves.

   ![Selecione sua assinatura](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Em **Editar o Cofre de Chaves do Azure,** digite o nome que deseja usar para o Cofre de Chaves.

1. Selecione um **grupo de recursos**existente ou escolha criar um novo com um nome único gerado automaticamente.  Se você quiser criar um novo grupo com um nome diferente, você pode usar o [portal Azure](https://portal.azure.com)e, em seguida, fechar a página e reiniciar para recarregar a lista de grupos de recursos.
1. Escolha o **local** para criar o Cofre de Chaves. Se seu aplicativo Web estiver hospedado no Azure, escolha a região que hospeda o aplicativo Web para um desempenho ideal.
1. Escolha um **nível de preços .** Para obter mais detalhes, confira [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Escolha **OK** para aceitar as opções de configuração.
1. Depois de selecionar um Cofre de Chaves existente ou configurar um novo Cofre de Chaves, na guia **Azure Key Vault** do Visual Studio, selecione **Adicionar** para adicionar o Serviço Conectado.
1. Selecione os **segredos gerenciar armazenados neste** link do Cofre de Chaves para abrir a página **Segredos** para o seu Cofre de Chaves. Se você fechou a página ou o projeto, você pode navegar até ela no [portal Azure](https://portal.azure.com) escolhendo **Todos os Serviços** e, em **Segurança,** escolhendo **Key Vault,** e então escolha seu Cofre-chave.
1. Na seção Key Vault para o Cofre de Chaves criado, escolha **Segredos**e depois **Gere/Importe.**

   ![Gerar/Importar um segredo](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Digite um segredo, como *MySecret* e dê-lhe qualquer valor de seqüência como teste, em seguida, selecione o botão **Criar.**

   ![Criar um segredo](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (opcional) Insira outro segredo, mas, desta vez, coloque-o em uma categoria nomeando-o como *Segredos--MySecret*. Esta sintaxe especifica uma categoria "Segredos" que contém um segredo "MySecret".

Agora, você pode acessar seus segredos no código. As próximas etapas são diferentes dependendo se você estiver usando o ASP.NET 4.7.1 ou ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Acesse seus segredos em código (ASP.NET Core)

1. No Solution Explorer, clique com o botão direito do mouse no projeto e **selecione Gerenciar pacotes NuGet**. Na guia **Procurar,** localize e instale esses dois pacotes NuGet: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e para .NET Core 2, adicione [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) ou para .NET Core 3, adicione[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core).

1. Para .NET Core 2, selecione a `Program.cs` guia e altere a `BuildWebHost` definição na classe Programa para o seguinte:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Para .NET Core 3, use o seguinte código.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Em seguida, abra um dos arquivos da página, como *Index.cshtml.cs* e escreva o seguinte código:
   1. Inclua uma `Microsoft.Extensions.Configuration` referência a isso usando diretiva:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Adicione a variável de configuração.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Adicione este construtor ou substitua o construtor existente por isso:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Atualize o método `OnGet`. Atualize o valor do espaço reservado mostrado aqui com o nome secreto que você criou nos comandos acima.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Para confirmar o valor em tempo `ViewData["Message"]` de execução, adicione código para exibir ao arquivo *.cshtml* para exibir o segredo em uma mensagem.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Você pode executar o aplicativo localmente para verificar se o segredo é obtido com sucesso no Key Vault.

## <a name="access-your-secrets-aspnet"></a>Acesse seus segredos (ASP.NET)

Você pode configurar a configuração para que o arquivo Web.config `appSettings` tenha um valor falso no elemento que é substituído pelo valor real em tempo de execução. Em seguida, você `ConfigurationManager.AppSettings` pode acessar isso através da estrutura de dados.

1. Edite seu arquivo web.config.  Encontre a tag Configurações de `configBuilders="AzureKeyVault"`aplicativos, adicione um atributo e adicione uma linha:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Edite `About` o método em *HomeController.cs,* para exibir o valor para confirmação.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Execute o aplicativo localmente o depurador, mude para a guia **Sobre** e verifique se o valor do Cofre de Chaves é exibido.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos. Isso exclui o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. No **tipo Nome do grupo de recursos:** caixa, digite o nome do grupo de recursos e **selecione Excluir**.

## <a name="troubleshooting"></a>Solução de problemas

Se o seu Key Vault estiver sendo executado em uma conta Microsoft diferente da que você está logado no Visual Studio (por exemplo, o Key Vault está sendo executado em sua conta de trabalho, mas o Visual Studio está usando sua conta privada), você tem um erro em seu arquivo Program.cs , que o Visual Studio não pode ter acesso ao Key Vault. Para corrigir esse problema:

1. Vá para o [portal Azure](https://portal.azure.com) e abra seu Cofre de Chaves.

1. Escolha **políticas de acesso,** depois **adicione política de acesso**e escolha a conta com a qual você está conectado como Principal.

1. No Visual Studio, escolha**Configurações da conta de** **arquivo** > .
Selecione **Adicionar uma conta** na seção **Toda a conta.** Faça login com a conta que você escolheu como Principal de sua política de acesso.

1. Escolha **opções** > **de ferramentas**e procure a **autenticação do serviço Azure.** Em seguida, selecione a conta que você acabou de adicionar ao Visual Studio.

Agora, quando você depura seu aplicativo, o Visual Studio se conecta à conta em que seu Key Vault está localizado.

## <a name="how-your-aspnet-core-project-is-modified"></a>Como seu projeto ASP.NET Core é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referências adicionadas para ASP.NET Core

Afeta as referências do arquivo de projeto .NET e as referências do pacote NuGet.

| Type | Referência |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Arquivos adicionados para ASP.NET Core

- `ConnectedService.json`adicionado, que registra algumas informações sobre o provedor de serviços conectados, versão e um link da documentação.

### <a name="project-file-changes-for-aspnet-core"></a>Alterações de arquivo de projeto para ASP.NET Core

- Adicionado o Connected Services `ConnectedServices.json` ItemGroup e o arquivo.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json alterações para ASP.NET Core

- Adição das seguintes entradas de variável de ambiente ao perfil do IIS Express e ao perfil que coincide com o nome do seu projeto da Web:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Alterações no Azure para ASP.NET Core

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Como seu projeto ASP.NET Framework é modificado

Esta seção identifica as alterações exatas feitas em um projeto ASP.NET ao adicionar o serviço conectado Key Vault usando o Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referências adicionadas para ASP.NET Framework

Afeta o arquivo do projeto `packages.config` .NET referências e (referências NuGet).

| Type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Arquivos adicionados para ASP.NET Framework

- `ConnectedService.json`adicionado, que registra algumas informações sobre o provedor de serviços conectados, versão e um link para a documentação.

### <a name="project-file-changes-for-aspnet-framework"></a>Alterações de arquivo de projeto para ASP.NET Framework

- Adicione o ItemGroup dos Serviços Conectados e o arquivo ConnectedServices.json.
- Referências aos assemblies .NET descritos na seção [Referências adicionadas](#added-references-for-aspnet-framework).

### <a name="webconfig-or-appconfig-changes"></a>alterações de web.config ou app.config

- Adicionadas as seguintes entradas de configuração:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Alterações no Azure para ASP.NET Framework

- Criação de um grupo de recursos (ou uso de um existente).
- Criação de um Key Vault no grupo de recursos especificado.

## <a name="next-steps"></a>Próximas etapas

Se você seguiu este tutorial, suas permissões do Key Vault serão configuradas para serem executadas com sua própria assinatura do Azure, mas isso pode não ser desejável para um cenário de produção. Você pode criar uma identidade gerenciada para gerenciar o acesso do Key Vault para o seu aplicativo. Consulte [Fornecer autenticação do Key Vault com uma identidade gerenciada](/azure/key-vault/managed-identity).

Saiba mais sobre o desenvolvimento do Key Vault lendo o [Guia do Desenvolvedor do Key Vault](key-vault-developers-guide.md).
