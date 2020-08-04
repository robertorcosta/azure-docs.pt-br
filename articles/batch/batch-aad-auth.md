---
title: Autenticar serviços do Lote do Azure com Azure Active Directory
description: O Lote dá suporte ao Azure AD para autenticação por meio do serviço do Lote. Saiba como autenticar de uma das duas maneiras.
ms.topic: how-to
ms.date: 01/28/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 19042b4bb0998d104792d7511ab2972299b4f58d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533505"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticar soluções do serviço do Lote no Active Directory

O Lote do Azure permite a autenticação com o [Azure Active Directory][aad_about]. O Azure AD é o serviço de gerenciamento de identidade e diretório multilocatário com base em nuvem da Microsoft. O Azure em si usa o Azure AD para autenticar seus clientes, administradores de serviços e usuários organizacionais.

Ao usar a autenticação do Azure AD com o Lote do Azure, você pode fazer a autenticação usando uma destas duas maneiras:

- Usando a **autenticação integrada** para autenticar um usuário que está interagindo com o aplicativo. Um aplicativo que usa a autenticação integrada coleta as credenciais do usuário e usa essas credenciais para autenticar o acesso aos recursos do Lote.
- Usando uma **entidade de serviço** para autenticar um aplicativo autônomo. Uma entidade de serviço define a política e as permissões de um aplicativo, a fim de representar o aplicativo ao acessar recursos em runtime.

Para saber mais sobre o Azure AD, veja a [documentação do Azure Active Directory](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Pontos de extremidade para autenticação

Para autenticar aplicativos do Lote no Azure AD, você precisa incluir alguns pontos de extremidade conhecidos no código.

### <a name="azure-ad-endpoint"></a>Ponto de extremidade do Azure AD

O ponto de extremidade de autoridade base do Azure AD é:

`https://login.microsoftonline.com/`

Para autenticar no Azure AD, use esse ponto de extremidade junto com a ID do locatário (ID do diretório). A ID do locatário identifica o locatário do Azure AD a ser usado para autenticação. Para recuperar a ID do locatário, siga as etapas descritas em [Obter a ID do locatário para o Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> O ponto de extremidade específico ao locatário é necessário quando você realiza uma autenticação usando uma entidade de serviço.
>
> O ponto de extremidade específico ao locatário é opcional, mas recomendado, quando você realiza uma autenticação usando a autenticação integrada. No entanto, você também pode usar o ponto de extremidade comum do Azure AD. O ponto de extremidade comum fornece uma interface genérica de coleta de credenciais quando um locatário específico não é fornecido. O ponto de extremidade comum é `https://login.microsoftonline.com/common`.
>
>

Para obter mais informações sobre pontos de extremidade do Azure Active Directory, veja [Cenários de autenticação do Azure Active Directory][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Ponto de extremidade de recursos do Lote

Use o **ponto de extremidade de recursos do Lote do Azure** para adquirir um token para autenticar solicitações no serviço do Lote:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrar seu aplicativo em um locatário

A primeira etapa do uso do Azure AD para realizar a autenticação é registrar seu aplicativo em um locatário do Azure AD. Registrar o aplicativo permite chamar a [ADAL (Biblioteca de Autenticação do Active Directory)][aad_adal] (ADAL) do seu código. A ADAL fornece uma API para realizar a autenticação no Azure AD por meio do aplicativo. O registro do aplicativo é necessário se você pretende usar a autenticação integrada ou uma entidade de serviço.

Ao registrar o aplicativo, você fornece informações sobre ele ao Azure AD. O Microsoft Azure AD, em seguida, fornece um ID do aplicativo (também chamado de *ID do Cliente*) que você usa para associar o aplicativo ao Microsoft Azure AD no runtime. Para saber mais sobre a ID do aplicativo, veja [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Para registrar o aplicativo Lote, siga as etapas da seção [Adicionar um Aplicativo](../active-directory/develop/quickstart-register-app.md) em [Integração de aplicativos com o Azure Active Directory][aad_integrate]. Se você registrar o aplicativo como um Aplicativo Nativo, poderá especificar qualquer URI válido para o **URI de Redirecionamento**. Ele não precisa ser um ponto de extremidade real.

Depois de registrar o aplicativo, você verá a ID do aplicativo:

![Registrar seu aplicativo Lote no Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Cenários de autenticação do Azure AD](../active-directory/develop/authentication-vs-authorization.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obter a ID do locatário para o Active Directory

A ID do locatário identifica o locatário do Azure AD que fornece serviços de autenticação para o aplicativo. Para obter a ID de locatário, siga estas etapas:

1. No portal do Azure, selecione seu Active Directory.
1. Selecione **Propriedades**.
1. Copie o valor do GUID fornecido para o **ID de Diretório**. Esse valor também é chamado de ID do locatário.

![Copiar a ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Usar a autenticação integrada

Para autenticar com a autenticação integrada, você precisa conceder as permissões do aplicativo para se conectar à API do serviço do Lote. Esta etapa permite que o aplicativo autentique chamadas à API do serviço do Lote no Azure AD.

Depois de registrar o aplicativo, siga estas etapas no portal do Azure para conceder a ele o acesso ao serviço do Lote:

1. No painel de navegação esquerdo do Portal do Azure, escolha **Todos os serviços**. Escolha **Registros de Aplicativo**.
1. Pesquise pelo nome do seu aplicativo na lista de registros do aplicativo:

    ![Procure o nome do aplicativo](./media/batch-aad-auth/search-app-registration.png)

1. Escolha o aplicativo e **Permissões de API**.
1. Na janela **Permissões de API**, escolha **Adicionar uma Permissão**.
1. Em **Selecionar uma API**, pesquise a API do Lote. Procure cada uma dessas cadeias de caracteres até encontrar a API:
    1. **Lote do Microsoft Azure**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é a ID para a API do Lote.
1. Depois de encontrar a API do Lote, escolha a API e **Selecionar**.
1. Em **Selecionar permissões**, marque a caixa de seleção ao lado de **Acessar Serviço do Lote do Azure** e escolha **Adicionar Permissões**.

A seção **Permissões de API** agora mostra que o aplicativo do Azure Active Directory tem acesso ao Microsoft Graph e à API do Serviço de lote. As permissões são concedidas ao Microsoft Graph automaticamente quando você registra o aplicativo no Azure Active Directory pela primeira vez.

![Conceder permissões de API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Usar uma entidade de serviço

Para autenticar um aplicativo que é executado de forma autônoma, use uma entidade de serviço. Depois de registrar o aplicativo, siga estas etapas no portal do Azure para configurar uma entidade de serviço:

1. Solicite um segredo para o aplicativo.
1. Atribua o controle de acesso baseado em função (RBAC) ao seu aplicativo.

### <a name="request-a-secret-for-your-application"></a>Solicitar um segredo para o aplicativo

Quando o aplicativo é autenticado com uma entidade de serviço, ele envia a ID do aplicativo e um segredo para o Azure Active Directory. Você precisará criar e copiar a chave secreta a ser usada do código.

Siga estas etapas no portal do Azure:

1. No painel de navegação esquerdo do Portal do Azure, escolha **Todos os serviços**. Escolha **Registros de Aplicativo**.
1. Escolha seu aplicativo na lista de registros de aplicativo.
1. Escolha o aplicativo e **Certificados e Segredos**. Na seção **Segredos do Cliente**, escolha **Novo Segredo do Cliente**.
1. Para criar um segredo, insira uma descrição para o segredo. Depois, escolha uma validade para o segredo de um ano, dois anos ou sem validade.
1. Escolha **Adicionar** para criar e exibir o segredo. Copie o valor do segredo para um local seguro, pois você não poderá acessá-lo novamente depois de sair da página.

    ![Criar uma chave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-rbac-to-your-application"></a>Atribuir RBAC ao seu aplicativo

Para autenticar com uma entidade de serviço, é preciso atribuir uma função RBAC ao aplicativo. Siga estas etapas:

1. No portal do Azure, navegue para a conta do Lote usada pelo aplicativo.
1. Na seção **Configurações** da conta do Lote, escolha **Controle de Acesso (IAM)** .
1. Selecione a guia **Atribuições de função**.
1. Selecione **Adicionar atribuição de função**.
1. Na lista suspensa **Função**, escolha a função *Colaborador* ou *Leitor* para o aplicativo. Para obter mais informações sobre essas funções, consulte [Introdução ao Controle de Acesso Baseado em Função no portal do Azure](../role-based-access-control/overview.md).
1. No campo **Selecionar**, insira o nome de seu aplicativo. Escolha o aplicativo na lista e escolha **Salvar**.

Seu aplicativo agora deve aparecer em suas configurações de controle de acesso com uma função do Azure atribuída.

![Atribuir uma função do Azure ao seu aplicativo](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Atribuir uma função personalizada

Uma função personalizada concede permissão granular a um usuário para enviar trabalhos, tarefas e muito mais. Com ela, é possível impedir que os usuários executem operações que afetam o custo, como a criação de pools ou a mudança de nós.

É possível usar uma função personalizada para conceder permissões a um usuário, grupo ou entidade de serviço do Azure Active Directory para as seguintes operações de RBAC:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (para qualquer operação de leitura)
- Microsoft.Batch/batchAccounts/listKeys/action (para qualquer operação)

As funções personalizadas são para usuários autenticados pelo Azure Active Directory, não pelas credenciais de conta do Lote (chave compartilhada). Observe que as credenciais de conta do Lote dão permissão total para a conta do Lote. Observe também que os trabalhos que usam o pool automático exigem permissões no nível do pool.

Aqui está um exemplo de uma definição de função personalizada:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Para obter mais informações gerais sobre como criar uma função personalizada, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obter a ID do locatário para o Azure Active Directory

A ID do locatário identifica o locatário do Azure AD que fornece serviços de autenticação para o aplicativo. Para obter a ID de locatário, siga estas etapas:

1. No portal do Azure, selecione seu Active Directory.
1. Selecione **Propriedades**.
1. Copie o valor do GUID fornecido para o **ID de Diretório**. Esse valor também é chamado de ID do locatário.

![Copiar a ID do diretório](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Exemplos de código

Os exemplos de código desta seção mostram como realizar a autenticação com o Azure AD usando a autenticação integrada e com uma entidade de serviço. A maioria desses exemplos de código usa o .NET, mas os conceitos são semelhantes para outras linguagens.

> [!NOTE]
> Um token de autenticação do AD do Azure expirará após uma hora. Ao usar uma vida útil longa **BatchClient** de objeto, é recomendável que você recuperar um token da ADAL em cada solicitação para garantir que você sempre tenha um token válido.
>
>
> Para fazer isso no .NET, escrever um método que recupera o token do AD do Azure e passar esse método para um **BatchTokenCredentials** objeto como um delegado. O método de representante é chamado em cada solicitação para o serviço de lote para garantir que um token válido seja fornecido. Por padrão ADAL armazena em cache os tokens, para que um novo token é recuperado do AD do Azure somente quando necessário. Para saber mais sobre tokens no Azure Active Directory, veja [Cenários de autenticação do Azure Active Directory][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Exemplo de código: Usar autenticação integrada do Azure AD com .NET do Lote

Para autenticar com a autenticação integrada por meio do .NET do Lote, referencie o pacote [.NET do Lote do Azure](https://www.nuget.org/packages/Microsoft.Azure.Batch/) e o pacote [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Inclua as seguintes intruções `using` no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencie o ponto de extremidade do Azure AD no código, incluindo a ID do locatário. Para recuperar a ID do locatário, siga as etapas descritas em [Obter a ID do locatário para o Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referencie o ponto de extremidade de recursos do serviço do Lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referencie a conta do Lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro de aplicativo no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Copie também o URI de redirecionamento que você especificou, se você registrou o aplicativo como um aplicativo nativo. O URI de redirecionamento especificado no código deve corresponder ao URI de redirecionamento que você forneceu quando registrou o aplicativo:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do AD do Azure. O método de retorno de chamada **GetAuthenticationTokenAsync** mostrado aqui chama a ADAL para autenticar um usuário que está interagindo com o aplicativo. O método **AcquireTokenAsync** fornecido pela ADAL solicita ao usuário suas credenciais e o aplicativo continua depois que o usuário fornece essas credenciais (a menos que ele já tenha as credenciais armazenadas em cache):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que use o delegado como um parâmetro. Use essas credenciais para abrir um objeto **BatchClient**. Você pode usar esse objeto **BatchClient** para as próximas operações no serviço do Lote:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Exemplo de código: Usar uma entidade de serviço do Azure AD com .NET do Lote

Para autenticar com uma entidade de serviço por meio do .NET do Lote, referencie o pacote [.NET do Lote do Azure](https://www.nuget.org/packages/Azure.Batch/) e o pacote [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Inclua as seguintes intruções `using` no seu código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Referencie o ponto de extremidade do Azure AD no código, incluindo a ID do locatário. Ao usar uma entidade de serviço, você deve fornecer um ponto de extremidade específico ao locatário. Para recuperar a ID do locatário, siga as etapas descritas em [Obter a ID do locatário para o Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Referencie o ponto de extremidade de recursos do serviço do Lote:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Referencie a conta do Lote:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro de aplicativo no portal do Azure:

```csharp
private const string ClientId = "<application-id>";
```

Especifique a chave secreta que você copiou do portal do Azure:

```csharp
private const string ClientKey = "<secret-key>";
```

Escreva um método de retorno de chamada para adquirir o token de autenticação do AD do Azure. O método de retorno de chamada **GetAuthenticationTokenAsync** mostrado aqui chama a ADAL para a autenticação autônoma:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construa um objeto **BatchTokenCredentials** que use o delegado como um parâmetro. Use essas credenciais para abrir um objeto **BatchClient**. Em seguida, use esse objeto **BatchClient** para operações subsequentes no serviço do Lote:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Exemplo de código: Usar uma entidade de serviço do Azure AD com Python do Lote

Para autenticar com uma entidade de serviço do Python do Lote, instale e referencie os módulos [azure-batch](https://pypi.org/project/azure-batch/) e [azure-common](https://pypi.org/project/azure-common/).

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Ao usar uma entidade de serviço, você deverá fornecer a ID do locatário. Para recuperar a ID do locatário, siga as etapas descritas em [Obter a ID do locatário para o Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>"
```

Referencie o ponto de extremidade de recursos do serviço do Lote:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Referencie a conta do Lote:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Especifique a ID do aplicativo (ID do cliente) para seu aplicativo. A ID do aplicativo está disponível no registro de aplicativo no portal do Azure:

```python
CLIENT_ID = "<application-id>"
```

Especifique a chave secreta que você copiou do portal do Azure:

```python
SECRET = "<secret-key>"
```

Crie um objeto **ServicePrincipalCredentials**:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Use as credenciais da entidade de serviço para abrir um objeto **BatchServiceClient**. Em seguida, use esse objeto **BatchServiceClient** para operações subsequentes no serviço do Lote.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o Azure AD, veja a [documentação do Azure Active Directory](../active-directory/index.yml). Exemplos mais detalhados que mostram como usar o ADAL estão disponíveis na biblioteca [Amostras de código do Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

- Para saber mais sobre entidades de serviço, consulte [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Para criar uma entidade de serviço usando o portal do Azure, consulte [Usar o portal para criar um aplicativo e uma entidade de serviço do Active Directory que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md). Você também pode criar uma entidade de serviço com o PowerShell ou a CLI do Azure.

- Para autenticar aplicativos do Gerenciamento de Lotes usando o Azure AD, consulte [Autenticar soluções do Gerenciamento de Lotes de com o Active Directory](batch-aad-auth-management.md).

- Para obter um exemplo com Python de como criar um cliente do Lote autenticado usando um token do Microsoft Azure AD, consulte o exemplo [Implantar imagem personalizada do Lote do Azure com um script Python](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md).

[aad_about]: ../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/azuread-dev/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/develop/authentication-vs-authorization.md "Cenários de autenticação do Azure AD"
[aad_integrate]: ../active-directory/develop/quickstart-register-app.md "Integrando aplicativos com o Azure Active Directory"
[azure_portal]: https://portal.azure.com
