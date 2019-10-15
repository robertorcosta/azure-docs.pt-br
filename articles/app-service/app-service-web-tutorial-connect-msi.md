---
title: Proteger a conexão do Banco de Dados SQL com identidade gerenciada – Serviço de Aplicativo do Azure | Microsoft Docs
description: Saiba como proteger a conectividade do banco de dados usando uma identidade gerenciada e também como aplicar isso a outros serviços do Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177067"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: proteger a conexão do Banco de Dados SQL do Azure no Serviço de Aplicativo usando uma identidade gerenciada

O [Serviço de Aplicativo](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches no Azure. Ele também fornece uma [identidade gerenciada](overview-managed-identity.md) para seu aplicativo, que é uma solução perfeita para proteger o acesso ao [Banco de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades gerenciadas no Serviço de Aplicativo tornam seu aplicativo mais seguro, eliminando os segredos do aplicativo, como as credenciais nas cadeias de conexão. Neste tutorial, você adicionará a identidade gerenciada ao aplicativo Web de exemplo criado em um dos tutoriais a seguir: 

- [Tutorial: criar um aplicativo ASP.NET no Azure com Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

Quando você terminar, seu aplicativo de exemplo se conectará ao Banco de Dados SQL com segurança sem a necessidade de nomes de usuário e senhas.

> [!NOTE]
> As etapas abordadas neste tutorial são compatíveis com as versões a seguir:
> 
> - .NET Framework 4.7.2 e versões posteriores.
> - .NET Core 2.2 e versões posteriores.
>

O que você aprenderá:

> [!div class="checklist"]
> * Habilitar identidades gerenciadas
> * Conceder ao Banco de Dados SQL acesso à identidade gerenciada
> * Configure o Entity Framework para usar a autenticação do Azure AD com o Banco de Dados SQL
> * Conecte-se ao Banco de Dados SQL do Visual Studio usando a autenticação do Azure AD

> [!NOTE]
>A autenticação do Azure AD é _diferente_ da [autenticação Integrada do Windows ](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) no Active Directory (AD DS) local. O AD DS e o Azure AD usam protocolos de autenticação completamente diferentes. Para saber mais, confira a [Documentação sobre o Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua do ponto em que você parou no [Tutorial: Criar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md) ou no [Tutorial: Criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](app-service-web-tutorial-dotnetcore-sqldb.md). Caso ainda não o tenha feito, primeiro siga um dos dois tutoriais. Como alternativa, você pode adaptar as etapas ao seu próprio aplicativo .NET com o Banco de Dados SQL.

Para depurar seu aplicativo usando o banco de dados SQL como back-end, verifique se você permitiu a conexão do cliente no seu computador. Caso contrário, adicione o IP do cliente seguindo as etapas descritas em [Gerenciar regras de firewall de IP no nível do servidor usando o portal do Azure](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Conceder acesso de banco de dados ao usuário do Azure AD

Primeiro, habilite a autenticação do Azure AD para o Banco de Dados SQL atribuindo um usuário do Azure AD como o administrador do Active Directory do servidor do Banco de Dados SQL. Esse usuário é diferente da conta Microsoft usada para inscrição na assinatura do Azure. Esse usuário precisa ser criado, importado, sincronizado ou convidado por você no Azure AD. Para obter mais informações sobre usuários com permissão no Azure AD, confira [Recursos e limitações do Azure AD no banco de dados SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Se o locatário do Azure AD ainda não tiver um usuário, crie um seguindo as etapas em [Adicionar ou excluir usuários usando o Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Localize a ID do usuário do Azure AD usando a [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) e substitua *\<user-principal-name>* . O resultado é salvo em uma variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes de entidade de usuário no Azure AD, execute `az ad user list --query [].userPrincipalName`.
>

Adicione o usuário do Azure AD como um administrador do Active Directory usando o comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) no Cloud Shell. No comando a seguir, substitua *\<nome do servidor>* pelo nome do servidor do Banco de Dados SQL (sem o sufixo `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre como adicionar um administrador no Active Directory, confira [Provisionar um administrador do Azure Active Directory para o servidor de Banco de Dados SQL do Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

### <a name="windows"></a>Windows
O Visual Studio para Windows é integrado à autenticação do Azure AD. Para habilitar o desenvolvimento e a depuração no Visual Studio, adicione o usuário do Azure AD no Visual Studio selecionando **Arquivo** > **Configurações de Conta** no menu e clicando em **Adicionar uma conta**.

Para definir o usuário do Azure AD para autenticação de serviço do Azure, selecione **Ferramentas** > **Opções** no menu, em seguida, selecione **Autenticação de Serviço do Azure** > **Seleção de conta**. Selecione o usuário do Azure AD que você adicionou e clique em **OK**.

Agora você está pronto para desenvolver e depurar seu aplicativo com o banco de dados SQL como back-end, usando a autenticação do Azure AD.

### <a name="macos"></a>MacOS

O Visual Studio para Mac não é integrado à autenticação do Azure AD. No entanto, a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), que será usada mais adiante, pode usar tokens da CLI do Azure. Para permitir o desenvolvimento e a depuração no Visual Studio, primeiro você precisa [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no computador local.

Quando a CLI do Azure estiver instalada no computador local, entre nela com o seguinte comando usando o usuário do Azure AD:

```bash
az login --allow-no-subscriptions
```
Agora você está pronto para desenvolver e depurar seu aplicativo com o banco de dados SQL como back-end, usando a autenticação do Azure AD.

## <a name="modify-your-project"></a>Modifique seu projeto

As etapas que você seguirá em seu projeto dependerão de ele ser um projeto ASP.NET ou ASP.NET Core.

- [Modificar ASP.NET](#modify-aspnet)
- [Modificar ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificar ASP.NET

No Visual Studio, abra o Console do Gerenciador de Pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

Em *Web.config*, comece pela parte superior do arquivo e faça as seguintes alterações:

- Em `<configSections>`, adicione a seguinte declaração de seção:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo da marca `</configSections>`, adicione o seguinte código XML para `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Localizar a cadeia de caracteres de conexão chamada `MyDbConnection` e substitua seu valor `connectionString` por `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Substitua _\<server-name >_ e _\<db-name>_ pelo nome do servidor e do banco de dados.

Isso é tudo de que você precisa para se conectar ao Banco de Dados SQL. Ao depurar no Visual Studio, seu código utiliza o usuário do Azure AD que você configurou na seção [Configurar Visual Studio](#set-up-visual-studio). Você configurará o servidor do Banco de dados SQL posteriormente para permitir a conexão da identidade gerenciada do seu aplicativo do Serviço de Aplicativo.

Digite `Ctrl+F5` para executar o aplicativo novamente. Agora, o mesmo aplicativo CRUD em seu navegador está se conectando diretamente ao Banco de Dados SQL do Azure, usando a autenticação do Azure AD. Essa configuração permite que você execute migrações de banco de dados do Visual Studio.

### <a name="modify-aspnet-core"></a>Modificar ASP.NET Core

No Visual Studio, abra o Console do Gerenciador de Pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

No [tutorial do ASP.NET Core e do Banco de Dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md), a cadeia de conexão `MyDbConnection` não é usada porque o ambiente de desenvolvimento local usa um arquivo de banco de dados do SQLite e o ambiente de produção do Azure usa uma cadeia de conexão do Serviço de Aplicativo. Com a autenticação do Active Directory, o ideal é que ambos os ambientes usem a mesma cadeia de conexão. Em *appsettings.json*, substitua o valor da cadeia de conexão `MyDbConnection` por:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Em *Startup.cs*, remova a seção de código que você adicionou anteriormente:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

E substitua pelo código a seguir:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Em seguida, você fornece o contexto de banco de dados do Entity Framework com o token de acesso para o Banco de Dados SQL. Em *Data\MyDatabaseContext.cs*, adicione o código a seguir dentro das chaves do construtor `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` vazio:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Este código de demonstração é síncrono para fins de clareza. Para obter mais informações, confira [Guia assíncrono para construtores](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Isso é tudo de que você precisa para se conectar ao Banco de Dados SQL. Ao depurar no Visual Studio, seu código utiliza o usuário do Azure AD que você configurou na seção [Configurar Visual Studio](#set-up-visual-studio). Você configurará o servidor do Banco de dados SQL posteriormente para permitir a conexão da identidade gerenciada do seu aplicativo do Serviço de Aplicativo. A classe `AzureServiceTokenProvider` armazena em cache o token na memória e recupera-o do Azure AD logo antes da expiração. Você não precisa de nenhum código personalizado para atualizar o token.

> [!TIP]
> Se o usuário do Azure AD que você configurou tiver acesso a vários locatários, chame `GetAccessTokenAsync("https://database.windows.net/", tenantid)` com a ID de locatário desejada para recuperar o token de acesso adequado.

Digite `Ctrl+F5` para executar o aplicativo novamente. Agora, o mesmo aplicativo CRUD em seu navegador está se conectando diretamente ao Banco de Dados SQL do Azure, usando a autenticação do Azure AD. Essa configuração permite que você execute migrações de banco de dados do Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Usar conectividade de identidade gerenciada

Em seguida, você pode configurar o aplicativo de seu Serviço de Aplicativo para conexão com o banco de dados SQL com uma identidade gerenciada atribuída ao sistema.

### <a name="enable-managed-identity-on-app"></a>Habilitar a identidade gerenciada no aplicativo

Para habilitar uma identidade gerenciada para o aplicativo do Azure, use o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando a seguir, substitua *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Veja um exemplo da saída:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Adicionar identidade gerenciada a um grupo do Azure AD

Para conceder esse acesso de identidade ao seu Banco de Dados SQL, você precisará adicioná-lo a um [grupo do Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). No Cloud Shell, adicione-o a um novo grupo chamado _myAzureSQLDBAccessGroup_, conforme mostrado no seguinte script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se você quiser ver a saída JSON completa para cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Conceder permissões ao grupo do Azure AD

No Cloud Shell, entre no Banco de Dados SQL usando o comando SQLCMD. Substitua _\<server-name>_ pelo nome do servidor do banco de dados SQL, _\<db-name>_ pelo nome do banco de dados usado por seu aplicativo e _\<aad-user-name>_ e _\<aad-password>_ pelas credenciais do usuário do Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

No prompt do SQL do banco de dados desejado, execute os comandos a seguir para adicionar o grupo do Azure AD e conceda as permissões necessárias ao seu aplicativo. Por exemplo, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Digite `EXIT` para retornar ao prompt do Cloud Shell.

### <a name="modify-connection-string"></a>Modificar cadeia de conexão

Lembre-se de que as mesmas alterações feitas em *Web.config* ou *appsettings.json* funcionam com a identidade gerenciada, portanto, o que resta fazer é remover a cadeia de conexão existente no Serviço de Aplicativo, criada pela Visual Studio ao implantar seu aplicativo pela primeira vez. Use o comando a seguir, mas substitua *\<app-name>* pelo nome de seu aplicativo.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar suas alterações

Agora, só falta publicar suas alterações no Azure.

**Se você veio do [Tutorial: Criar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)** , publique suas alterações no Visual Studio. No **Gerenciador de Soluções**, clique com botão direito no projeto **DotNetAppSqlD** e selecione **Publicar**.

![Publicar no Gerenciador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. 

**Se você veio do [Tutorial: Criar um aplicativo ASP.NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](app-service-web-tutorial-dotnetcore-sqldb.md)** , publique suas alterações usando o Git, com os comandos a seguir:

```bash
git commit -am "configure managed identity"
git push azure master
```

Quando a nova página da Web mostra a lista de tarefas pendentes, seu aplicativo se conecta ao banco de dados usando a identidade gerenciada.

![Aplicativo do Azure após Migração do Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, você pode editar a lista de tarefas pendentes como fazia antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> * Habilitar identidades gerenciadas
> * Conceder ao Banco de Dados SQL acesso à identidade gerenciada
> * Configure o Entity Framework para usar a autenticação do Azure AD com o Banco de Dados SQL
> * Conecte-se ao Banco de Dados SQL do Visual Studio usando a autenticação do Azure AD

Vá para o próximo tutorial para saber como mapear um nome DNS personalizado para o seu aplicativo Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md)
