---
title: Tutorial – O aplicativo Web acessa o Microsoft Graph como o aplicativo | Azure
description: Neste tutorial, você aprenderá a acessar dados no Microsoft Graph usando identidades gerenciadas.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: 70b180efa35d6310735f045a85103719b17c8555
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428121"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Tutorial: acessar o Microsoft Graph de um aplicativo protegido como o aplicativo

Saiba como acessar o Microsoft Graph de um aplicativo Web em execução no Serviço de Aplicativo do Azure.

:::image type="content" alt-text="Acessar o Microsoft Graph" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Você deseja chamar o Microsoft Graph em nome do aplicativo Web.  Um modo seguro de permitir o acesso do aplicativo Web aos dados é pelo uso de uma [identidade gerenciada atribuída ao sistema](/azure/active-directory/managed-identities-azure-resources/overview). Uma identidade gerenciada do Azure AD permite que os Serviços de Aplicativos acessem recursos por meio do RBAC (controle de acesso baseado em função), sem a necessidade de credenciais do aplicativo. Depois de atribuir uma identidade gerenciada ao seu aplicativo Web, o Azure cuida da criação e da distribuição de um certificado.  Você não precisa se preocupar em gerenciar segredos nem credenciais de aplicativo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar uma identidade gerenciada atribuída ao sistema em um aplicativo Web
> * Adicionar permissões de API do Microsoft Graph a uma identidade gerenciada
> * Chamar o Microsoft Graph por meio de um aplicativo Web usando identidades gerenciadas

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo Web em execução no Serviço de Aplicativo do Azure que tem o [módulo de autenticação/autorização do Serviço de Aplicativo habilitado](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Habilitar a identidade gerenciada no aplicativo

Se você criar e publicar o aplicativo Web por meio do Visual Studio, a identidade gerenciada foi habilitada no aplicativo para você. No serviço de aplicativo, selecione **Identidade** no painel de navegação à esquerda e depois selecione **Atribuído ao sistema**.  Verifique se **Status** está definido como **Ativado**.  Se não estiver, clique primeiro em **Salvar** e depois em **Sim** para habilitar a identidade gerenciada atribuída ao sistema.  Quando a identidade gerenciada é habilitada, o status é definido como *Ativado* e a ID do objeto fica disponível.

Anote a **ID do objeto**, que será necessária na próxima etapa.

:::image type="content" alt-text="Identidade atribuída pelo sistema" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Permitir acesso ao Microsoft Graph

Ao acessar o Microsoft Graph, a identidade gerenciada precisa ter as permissões adequadas para a operação que ele deseja executar. Atualmente, não há nenhuma opção para atribuir essas permissões por meio do portal do Azure. O seguinte script adicionará as permissões de API do Microsoft Graph solicitadas ao objeto de entidade de serviço de identidade gerenciada:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module (You need admin on the machine)
#Install-Module AzureAD 

# Your tenant id (in Azure Portal, under Azure Active Directory -> Overview )
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get ID of the managed identity for the web app
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph"

# Assign permissions to managed identity service principal
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spID/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Depois de executar o script, é possível verificar no [portal do Azure](https://portal.azure.com) que as permissões de API solicitadas são atribuídas à identidade gerenciada.  Navegue para o **Azure Active Directory** e selecione **Aplicativos empresariais**.  Essa folha exibe todas as entidades de serviço em seu locatário.  Em **Todos os Aplicativos**, selecione a entidade de serviço para a identidade gerenciada.  Se você estiver seguindo este tutorial, há duas entidades de serviço com o mesmo nome de exibição ("SecureWebApp2020094113531", por exemplo).  A entidade de serviço que tem uma *URL de Home Page* representa o aplicativo Web no locatário.  A entidade de serviço sem a *URL da Home Page* representa a identidade gerenciada atribuída ao sistema para o aplicativo Web. A ID de objeto para a identidade gerenciada corresponde à ID do objeto da identidade gerenciada que você criou anteriormente.  

Selecione a entidade de serviço para a identidade gerenciada.

:::image type="content" alt-text="Todos os aplicativos" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

Em **Visão geral**, selecione **Permissões** e você verá as permissões adicionadas para o Microsoft Graph.

:::image type="content" alt-text="Permissões" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Chamar o Microsoft Graph (.NET)

A classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) é usada para obtenção de uma credencial de token para o código destinada à autorização de solicitações ao Armazenamento do Azure.  Crie uma instância da classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential), que usa a identidade gerenciada para buscar tokens e anexá-los ao cliente do serviço. O exemplo de código a seguir obtém a credencial de token autenticada e a usa para criar um objeto de cliente de serviço, que obtém os usuários no grupo.  

### <a name="install-microsoftgraph-client-library-package"></a>Instalar o pacote da biblioteca de clientes do Microsoft.Graph

Instale o [pacote do NuGet Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph) em seu projeto usando a interface de linha de comando do .NET Core ou o Console do Gerenciador de Pacotes no Visual Studio.

# <a name="command-line"></a>[Linha de comando](#tab/command-line)

Abra uma linha de comando e alterne para o diretório que contém o arquivo de projeto.

Execute os comandos de instalação:

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[Gerenciador de Pacotes](#tab/package-manager)

Abra o projeto/solução no Visual Studio e abra o console do usando o comando **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

Execute os comandos de instalação:
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>Exemplo

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Graph service client with a DefaultAzureCredential which gets an access token using the available Managed Identity
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você concluiu este tutorial e não precisa mais do aplicativo Web nem dos recursos associados, [limpe os recursos que você criou](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Criar uma identidade gerenciada atribuída ao sistema em um aplicativo Web
> * Adicionar permissões de API do Microsoft Graph a uma identidade gerenciada
> * Chamar o Microsoft Graph por meio de um aplicativo Web usando identidades gerenciadas

Saiba como conectar um [aplicativo .NET Core](tutorial-dotnetcore-sqldb-app.md), [aplicativo Python](tutorial-python-postgresql-app.md), [aplicativo Java](tutorial-java-spring-cosmosdb.md) ou [aplicativo Node.js](tutorial-nodejs-mongodb-app.md) a um banco de dados.