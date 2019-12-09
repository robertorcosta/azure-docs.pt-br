---
title: 'Tutorial: ASP.NET Core com o Banco de Dados SQL'
description: Saiba como executar um aplicativo .NET Core no Serviço de Aplicativo do Azure, com uma conexão com um Banco de Dados SQL.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7f444ad9b32ca5da923ce5ac711c9947971c4d1e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672008"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Tutorial: Criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Windows. Para implantar no Serviço de Aplicativo no _Linux_, confira [Criar um aplicativo .NET Core e com Banco de Dados SQL no Serviço de Aplicativo do Azure no Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

O [Serviço de Aplicativo](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches no Azure. Este tutorial mostra como criar um aplicativo .NET Core e conectá-lo a um Banco de Dados SQL. Quando terminar, você terá um aplicativo MVC .NET Core em execução no Serviço de Aplicativo.

![aplicativo em execução no Serviço de Aplicativo](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Você aprenderá a:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs de diagnóstico do Azure
> * Gerenciar o aplicativo no portal do Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Instalar o Git](https://git-scm.com/)
* [Instalar o .NET Core](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>Criar um aplicativo .NET Core local

Nesta etapa, você configura o projeto .NET Core local.

### <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Na janela do terminal, `cd` para um diretório de trabalho.

Execute os comandos a seguir para clonar o repositório de exemplo e alterar sua raiz.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Esse projeto de exemplo contém um aplicativo CRUD (create-read-update-delete) básico usando o [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Executar o aplicativo

Execute os comandos a seguir para instalar os pacotes necessários, executar as migrações de banco de dados e iniciar o aplicativo.

```bash
dotnet restore
dotnet ef database update
dotnet run
```

Navegue até `http://localhost:5000` em um navegador. Selecione o link **Criar novo** e crie alguns itens _de tarefas_.

![conecta-se com êxito ao Banco de Dados SQL](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Para parar o .NET Core a qualquer momento, pressione `Ctrl+C` no terminal.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Criar um Banco de Dados SQL de produção

Nesta etapa, você cria um Banco de Dados SQL no Azure. Quando seu aplicativo é implantado no Azure, ele usa esse banco de dados na nuvem.

Para o Banco de Dados SQL, este tutorial usa o [Banco de Dados SQL do Azure](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Criar um servidor lógico do Banco de Dados SQL

No Cloud Shell, crie um servidor lógico do Banco de Dados SQL com o comando [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create).

Substitua o espaço reservado *\<server_name>* por um nome exclusivo do Banco de Dados SQL. Esse nome é usado como parte do ponto de extremidade do Banco de Dados SQL, `<server_name>.database.windows.net` e, portanto, o nome precisa ser exclusivo em todos os servidores lógicos no Azure. O nome deve conter somente letras minúsculas, números e o caractere hífen (-), e deve ter entre 3 e 50 caracteres. Além disso, substitua *\<db_username>* e *\<db_password>* por um nome de usuário e uma senha de sua escolha. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Quando o servidor lógico do Banco de Dados SQL é criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

```json
{
  "administratorLogin": "sqladmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/<server_name>",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "<server_name>",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
```

### <a name="configure-a-server-firewall-rule"></a>Configurar uma regra de firewall de servidor

Crie uma [regra de firewall no nível de servidor do Banco de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) usando o comando [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Quando o IP inicial e o IP final estiverem definidos como 0.0.0.0, o firewall estará aberto somente para outros recursos do Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Você pode ser ainda mais restritivo na regra de firewall ao [usar somente os endereços de IP de saída que seu aplicativo usa](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Criar um banco de dados

Crie um banco de dados com um [nível de desempenho S0](../sql-database/sql-database-service-tiers-dtu.md) no servidor usando o comando [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Criar uma cadeia de conexão

Substitua a cadeia de caracteres a seguir pelos *\<server_name>* , *\<db_username>* e *\<db_password>* usados anteriormente.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

Esta é a cadeia de conexão do aplicativo .NET Core. Copie-a para uso posterior.

## <a name="deploy-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta etapa, você implantará seu aplicativo .NET Core conectado ao Banco de Dados SQL no Serviço de Aplicativo.

### <a name="configure-local-git-deployment"></a>Configurar a implantação do git local

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Configurar a cadeia de conexão

Para definir as cadeias de conexão para o aplicativo do Azure, use o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. No comando a seguir, substitua *\<app name>* , bem como o parâmetro *\<connection_string>* , pela cadeia de conexão criada anteriormente.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

No ASP.NET Core, você poderá usar essa cadeia de conexão nomeada (`MyDbConnection`) empregando o modelo padrão, como qualquer cadeia de conexão especificada em *appsettings.json*. Nesse caso, `MyDbConnection` também é definido em seu *appsettings.json*. Durante a execução no Serviço de Aplicativo, a cadeia de conexão definida no Serviço de Aplicativo tem precedência sobre a cadeia de conexão definida no seu *appsettings.json*. O código usa o valor *appsettings.json* durante o desenvolvimento local e o mesmo código usa o valor do Serviço de Aplicativo quando implantado.

Para ver como a cadeia de conexão é referenciada em seu código, consulte [Conectar-se ao Banco de Dados SQL em produção](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Configurar variável de ambiente

Em seguida, defina a configuração de aplicativo `ASPNETCORE_ENVIRONMENT` como _Produção_. Essa configuração permite saber se o aplicativo está em execução no Azure, porque você usa o SQLite para o ambiente de desenvolvimento local e o Banco de Dados SQL para o ambiente do Azure.

O exemplo a seguir define uma configuração de aplicativo `ASPNETCORE_ENVIRONMENT` no aplicativo do Azure. Substitua o espaço reservado *\<app_name>* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Para ver como a variável de ambiente é referenciada em seu código, consulte [Conectar-se ao Banco de Dados SQL em produção](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Conectar-se ao Banco de Dados SQL em produção

No repositório local, abra Startup.cs e encontre o seguinte código:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Substitua-o pelo código a seguir, que usa as variáveis de ambiente configuradas anteriormente.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Se esse código detectar que ele está sendo executado em produção (que indica o ambiente do Azure), ele usará a cadeia de conexão configurada para se conectar ao Banco de Dados SQL.

A chamada `Database.Migrate()` ajuda você quando ele está em execução no Azure, porque ela cria automaticamente os bancos de dados necessários para o aplicativo .NET Core, de acordo com sua configuração de migração. 

> [!IMPORTANT]
> Para aplicativos de produção que precisam ser expandidos, siga as práticas recomendadas em [Aplicar migrações em produção](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Salve suas alterações e depois confirme-as no repositório do Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Enviar do Git para o Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-azure-app"></a>Navegar até o aplicativo do Azure

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

Adicione alguns itens de tarefas.

![aplicativo em execução no Serviço de Aplicativo](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Parabéns!** Você está executando um aplicativo .NET Core controlado por dados no Serviço de Aplicativo.

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplantar

Nesta etapa, você faz uma alteração no esquema de banco de dados e publica-o no Azure.

### <a name="update-your-data-model"></a>Atualizar seu modelo de dados

Abra _Models\Todo.cs_ no editor de códigos. Adicione a seguinte propriedade à classe `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Executar Migrações do Code First localmente

Execute alguns comandos para fazer as atualizações para seu banco de dados local.

```bash
dotnet ef migrations add AddProperty
```

Atualize o banco de dados local:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Usar a nova propriedade

Faça algumas alterações em seu código para usar a propriedade `Done`. Para simplificar este tutorial, somente as exibições `Index` e `Create` serão alteradas para observar a propriedade em ação.

Abra _Controllers\TodosController.cs_.

Localize o `Create([Bind("ID,Description,CreatedDate")] Todo todo)` método e adicione `Done` à lista de propriedades no atributo `Bind`. Quando terminar, a assinatura do método `Create()` deverá ter o seguinte código:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Abra _Views\Todos\Create.cshtml_.

No código do Razor, você deve ver um elemento `<div class="form-group">` para `Description` e, em seguida, outro elemento `<div class="form-group">` para `CreatedDate`. Imediatamente após esses dois elementos, adicione outro elemento `<div class="form-group">` para `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Abra _Views\Todos\Index.cshtml_.

Procure o elemento vazio `<th></th>`. Logo acima desse elemento, adicione o seguinte código do Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Localize o elemento `<td>` que contém os auxiliares de marcação `asp-action`. Logo acima desse elemento, adicione o seguinte código do Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

Isso é tudo o que você precisa para ver as alterações nas exibições `Index` e `Create`.

### <a name="test-your-changes-locally"></a>Testar suas alterações localmente

Execute o aplicativo localmente.

```bash
dotnet run
```

No navegador, navegue até `http://localhost:5000/`. Agora você pode adicionar um item de tarefas e marcar **Concluído**. Em seguida, ele deverá aparecer na sua página inicial como um item concluído. Lembre-se de que a exibição `Edit` não mostra o campo `Done`, porque você não alterou a exibição `Edit`.

### <a name="publish-changes-to-azure"></a>Publicar alterações no Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Quando `git push` estiver concluído, navegue até seu aplicativo do Serviço de Aplicativo e tente adicionar um item de tarefa pendente e marcar **Concluído**.

![Aplicativo do Azure após Migração do Code First](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Observe que todos os itens de tarefas existentes ainda são exibidos. Quando você republicar o aplicativo .NET Core, os dados existentes no Banco de Dados SQL não serão perdidos. Além disso, as Migrações do Entity Framework Core apenas alteram o esquema de dados e deixam os dados existentes intactos.

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

Enquanto o aplicativo ASP.NET Core é executado no serviço de aplicativo do Azure, você pode transferir os logs do console para o Cloud Shell. Dessa forma, é possível obter as mesmas mensagens de diagnóstico para ajudá-lo a depurar erros de aplicativo.

O projeto de exemplo já segue as diretrizes em [Registro do ASP.NET Core no Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) com duas alterações de configuração:

- Inclui uma referência a `Microsoft.Extensions.Logging.AzureAppServices` em *DotNetCoreSqlDb.csproj*.
- Chama `loggerFactory.AddAzureWebAppDiagnostics()` em *Program.cs*.

Para definir o [nível de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) do ASP.NET Core no Serviço de Aplicativo para `Information` do nível padrão `Error`, use o comando [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) no Cloud Shell.

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> O nível de log do projeto já está definido como `Information` em *appsettings.json*.
> 

Para iniciar o streaming de log, use o comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) no Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Depois que o streaming de log for iniciado, atualize o aplicativo do Azure no navegador para obter parte do tráfego da Web. Agora é possível ver os logs do console redirecionados para o terminal. Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.

Para obter mais informações sobre como personalizar os logs do ASP.NET Core, veja [Efetuar logon no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="manage-your-azure-app"></a>Gerenciar o aplicativo do Azure

Para ver o aplicativo que você criou, no [portal do Azure](https://portal.azure.com), procure **Serviços de Aplicativos** e selecione essa opção.

![Selecionar Serviços de Aplicativos no portal do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Por padrão, o portal mostra a página **Visão Geral** do aplicativo. Esta página fornece uma visão de como está seu aplicativo. Aqui, você também pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir. As guias no lado esquerdo da página mostram as diversas páginas de configuração que você pode abrir.

![Página Serviço de Aplicativo no portal do Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Criar um Banco de Dados SQL no Azure
> * Conectar um aplicativo .NET Core ao Banco de Dados SQL
> * Implantar o aplicativo no Azure
> * Atualizar o modelo de dados e reimplantar o aplicativo
> * Transmitir logs do Azure para seu terminal
> * Gerenciar o aplicativo no portal do Azure

Prossiga para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)
