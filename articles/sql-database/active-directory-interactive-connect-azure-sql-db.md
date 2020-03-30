---
title: ActiveDirectoryInteractive se conecta ao SQL
description: Exemplo de código em C#, com explicações, para se conectar ao Banco de Dados SQL do Azure usando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692308"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Conecte-se ao banco de dados SQL do Azure com a autenticação multifatorial do Azure

Este artigo fornece um programa C# que se conecta ao Banco de Dados SQL do Azure. O programa usa autenticação de modo interativo, que suporta [autenticação multifatorial do Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

Para obter mais informações sobre o suporte à autenticação multifatorial para ferramentas SQL, consulte [o suporte ao Azure Active Directory no SQL Server Data Tools (SSDT).](https://docs.microsoft.com/sql/ssdt/azure-active-directory)

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multifatorial para banco de dados SQL do Azure

A partir da versão 4.7.2 do [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) Framework .NET, o enum tem um novo valor: `ActiveDirectoryInteractive`. Em um programa C# do cliente, o valor enum direciona o sistema para usar o modo interativo Azure Active Directory (Azure AD) que suporta autenticação multifatorial para se conectar a um banco de dados SQL do Azure. O usuário que executa o programa verá as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário.

   Se o domínio do usuário for federado pelo Azure AD, esta caixa de diálogo não aparecerá, porque nenhuma senha é necessária.

   Se a diretiva Azure AD impor autenticação multifatorial ao usuário, as duas próximas caixas de diálogo serão exibidas.

* A primeira vez que um usuário passa pela Autenticação Multifatorial, o sistema exibe uma caixa de diálogo que pede um número de telefone celular para enviar mensagens de texto. Cada mensagem fornece o *código de verificação* que o usuário deverá digitar na caixa de diálogo seguinte.

* Uma caixa de diálogo que pede um código de verificação de autenticação multifatorial, que o sistema enviou para um telefone celular.

Para obter informações sobre como configurar o Azure AD para exigir autenticação multifatorial, consulte [Como começar com a autenticação multifatorial do Azure na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para obter capturas de tela dessas caixas de diálogo, consulte [Configurar autenticação multifatorial para o SQL Server Management Studio e o Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Você pode pesquisar APIs do Quadro .NET com a página da [ferramenta .NET API Browser](https://docs.microsoft.com/dotnet/api/).
>
> Você também pode pesquisar diretamente com o [parâmetro opcional&lt;?term= valor de&gt; pesquisa](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar seu aplicativo C# no portal do Azure

Antes de começar, você deve ter um [servidor do Banco de Dados SQL do Azure](sql-database-get-started-portal.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registrar seu aplicativo e definir permissões

Para usar a autenticação do Azure AD, o programa C# deverá se registrar como um aplicativo do Azure AD. Para registrar um aplicativo, você precisa ser administrador do Azure AD ou ter recebido uma função de *Desenvolvedor de Aplicativos* do Azure AD. Para obter mais informações sobre como atribuir funções, consulte [Atribuir funções de administrador e não-administrador aos usuários com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Completar um registro de aplicativo gera e exibe um **ID de aplicativo.** O programa deve incluir essa identificação para se conectar.

Para registrar e definir as permissões necessárias para seu aplicativo:

1. No portal Azure, selecione > **inscrições** > do Aplicativo de **Diretório Ativo do Azure****Novo registro**.

    ![Registro do aplicativo](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Após a criação do registro do aplicativo, o valor **do ID** do aplicativo é gerado e exibido.

    ![ID do aplicativo exibida](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **permissões** > de API**Adicione uma permissão**.

    ![Configurações de permissões do aplicativo registrado](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **APIs que minha organização usa** > tipo **Azure SQL Database** no > de pesquisa e selecione O Banco de Dados **SQL do Azure**.

    ![Adicionar acesso à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **permissões delegadas** > **user_impersonation** > **adicionar permissões**.

    ![Delegar permissões à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Definir um administrador do Azure AD para seu servidor do Banco de Dados SQL

Para que seu programa C# seja executado, um administrador de servidor Azure SQL precisa atribuir um administrador Azure AD para o servidor SQL Database. 

Na página **SQL Server,** selecione > **Admin Set de configuração de**diretório **ativo**.

Para obter mais informações sobre administradores do Azure AD e usuários para o Azure SQL Database, consulte as capturas de tela no [Configure e gerencie a autenticação do Azure Active Directory com o SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um usuário não administrador a um banco de dados (opcional)

Um administrador do Azure AD para um servidor do Banco de Dados SQL pode executar o programa C# de exemplo. Um usuário do Azure AD poderá executar o programa se eles estiverem no banco de dados. Um administrador do SQL do Azure AD ou um usuário do Azure AD que já exista no banco de dados e tenha a permissão `ALTER ANY USER` no banco de dados poderá adicionar um usuário.

Você pode adicionar um usuário ao banco [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) de dados com o comando SQL. Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, confira [Usar a Autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O exemplo C# [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) se baseia no namespace. De interesse especial para autenticação multifatorial está o enum `SqlAuthenticationMethod`, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Use este valor com um nome de usuário Azure AD para implementar a autenticação multifatorial. Esse valor é o foco deste artigo. Ele produz uma experiência interativa exibindo caixas de diálogo para a senha do usuário e, em seguida, para validação de autenticação multifatorial se a autenticação multifatorial for imposta a este usuário. Esse valor está disponível a partir do .NET Framework versão 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use esse valor para uma conta *federada*. Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Este método de autenticação não suporta autenticação multifatorial.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Use esse valor para a autenticação que requer um nome de usuário e uma senha do Azure AD. O Banco de Dados SQL do Azure faz a autenticação. Este método não suporta autenticação multifatorial.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Defina os valores de parâmetro de C# do portal do Azure

Para o programa C# ser executado com êxito, você precisará atribuir os valores adequados aos campos estáticos. Campos com valores de exemplo são mostradas aqui. Também são mostrados os locais do portal Azure onde você pode obter os valores necessários.

| Nome do campo estático | Valor de exemplo | Onde encontrar no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores SQL Filtram** > **por nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Usuário do Diretório** > **User** > Ativo do Azure**Novo usuário convidado** |
| Initial_DatabaseName | "myDatabase" | **SQL servidores bancos** > **de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Registros do** > **aplicativo** > do Diretório Ativo do Azure**Pesquisa por nome** > **ID do aplicativo** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Registros do** > **aplicativo** > do diretório ativo do Azure**Pesquisa por nome** > *[Seu Aplicativo-registro]* > **Configurações** > **Redirecionam URIs**<br /><br />Para este artigo, qualquer valor válido é bom para RedirectUri, porque não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifique com o SQL Server Management Studio

Antes de executar o programa C#, é uma boa ideia verificar se sua configuração e configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer falha do programa C# então pode ser limitada ao código-fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificar os endereços IP do firewall do Banco de Dados SQL

Execute o SSMS no mesmo computador e no mesmo local em que você planeja executar o programa C#. Para este teste, qualquer modo **de autenticação** é OK. Se não houver nenhuma indicação de que o firewall do servidor do banco de dados não está aceitando seu endereço IP, veja [regras de firewall no nível de servidor do Banco de Dados SQL do Azure e no nível do firewall do banco de dados](sql-database-firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifique a autenticação multifatorial do diretório ativo do Azure

Execute o SSMS novamente, dessa vez com a **Autenticação** definida como **Active Directory – Universal com suporte de MFA**. Essa opção exige o SSMS versão 17.5 ou posterior.

Para obter mais informações, consulte [Configurar autenticação multifatorial para SSMS e Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se você é um usuário convidado no banco de dados, você também precisa fornecer o nome de domínio Azure AD para o banco de dados: Selecione **opções** > **de nome de domínio AD ou ID de inquilino**. Para encontrar o nome de domínio no portal Azure, selecione nomes de domínio **personalizados do Azure Active** > **Directory**. No programa C# de exemplo, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>Exemplo de código C#

O programa C# de exemplo depende do assembly DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Para instalar este pacote, no Visual Studio, selecione **Projeto** > **Gerenciar Pacotes NuGet**. Pesquise e instale **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Este é um exemplo de código fonte C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Este é um exemplo da saída de teste C#.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- [Administrador de diretórioativo do Get-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
