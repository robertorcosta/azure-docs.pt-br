---
title: Adquira um token do Azure AD para autorizar solicitações de um aplicativo cliente
titleSuffix: Azure Storage
description: Use o Azure Active Directory para autenticar dentro de um aplicativo cliente, adquirir um token OAuth 2.0 e autorizar solicitações ao armazenamento Azure Blob e armazenamento na fila.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d3ee211298598d78f423d88fd4df1c58ed4bfa29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268477"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Adquira um token do Azure AD para autorizar solicitações de um aplicativo cliente

Uma vantagem fundamental de usar o Azure Active Directory (Azure AD) com armazenamento Azure Blob ou armazenamento na fila é que suas credenciais não precisam mais ser armazenadas em seu código. Em vez disso, você pode solicitar um token de acesso OAuth 2.0 da plataforma de identidade Microsoft (anteriormente Azure AD). O Azure AD autentica o principal de segurança (um usuário, grupo ou diretor de serviço) executando o aplicativo. Se a autenticação for bem-sucedida, o Azure AD retorna o token de acesso ao aplicativo e, em seguida, o aplicativo poderá usar o token de acesso para autorizar solicitações ao armazenamento Do Zure Blob ou armazenamento na fila.

Este artigo mostra como configurar seu aplicativo nativo ou aplicativo web para autenticação com a plataforma de identidade Microsoft 2.0. O exemplo de código apresenta o .NET, mas outras linguagens usam uma abordagem semelhante. Para obter mais informações sobre a plataforma de identidade Microsoft 2.0, consulte [a visão geral da plataforma de identidade da Microsoft (v2.0).](../../active-directory/develop/v2-overview.md)

Para obter uma visão geral do fluxo de concessão do código do OAuth 2.0, consulte [Autorizar acesso a aplicativos Web do Azure Active Directory usando o fluxo de concessão do código do OAuth 2.0](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Atribuir uma função a um diretor de segurança do Azure AD

Para autenticar uma entidade de segurança do seu aplicativo de armazenamento do Azure, primeiro configure as configurações de RBAC (controle) de acesso baseado em função para essa entidade de segurança. O Azure Storage define funções RBAC incorporadas que englobam permissões para contêineres e filas. Quando a função RBAC é atribuída a uma entidade de segurança, essa entidade de segurança recebe acesso para esse recurso. Para obter mais informações, consulte [Gerenciar os direitos de acesso aos dados do Azure Blob e da Fila com o RBAC](storage-auth-aad-rbac.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registre o aplicativo com um locatário do Azure AD

O primeiro passo para usar o Azure AD para autorizar o acesso aos recursos de armazenamento é registrar seu aplicativo cliente com um inquilino Azure AD do [portal Azure](https://portal.azure.com). Quando você registra seu aplicativo cliente, você fornece informações sobre o aplicativo para o Azure AD. O Azure AD, em seguida, fornece uma ID do cliente (também chamado de *ID do aplicativo*) que você usa para associar o aplicativo ao Azure AD no runtime. Para saber mais sobre a ID do cliente, consulte [Objetos de entidade de serviço e aplicativo no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Para registrar seu aplicativo de armazenamento Azure, siga as etapas mostradas no [Quickstart: Registre um aplicativo com a plataforma de identidade Microsoft](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). A imagem a seguir mostra configurações comuns para registrar um aplicativo web:

![Captura de tela mostrando como registrar seu aplicativo de armazenamento com o Azure AD](./media/storage-auth-aad-app/app-registration.png)

> [!NOTE]
> Se você registrar seu aplicativo como um aplicativo nativo, você poderá especificar qualquer URI válido para o **URI redirecionado**. Para aplicações nativas, esse valor não precisa ser uma URL real. Para aplicações web, o URI de redirecionamento deve ser um URI válido, porque especifica a URL para a qual os tokens são fornecidos.

Depois de registrar o aplicativo, você verá a ID do aplicativo (ou ID do cliente) em **Configurações **:

![Captura de tela mostrando o ID do cliente](./media/storage-auth-aad-app/app-registration-client-id.png)

Para obter mais informações sobre como registrar um aplicativo no Azure AD, consulte [Integrando aplicativos com o Azure Active Directory](../../active-directory/develop/quickstart-v2-register-an-app.md).

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Conceder as permissões de aplicativo registradas ao Armazenamento do Microsoft Azure

Em seguida, conceda suas permissões de aplicativo para chamar APIs de armazenamento do Azure. Esta etapa permite que seu aplicativo autorize solicitações ao Azure Storage com o Azure AD.

1. Na **página Visão geral** do aplicativo registrado, selecione **Exibir permissões de API**.
1. Na **seção permissões da API,** **selecione Adicionar uma permissão** e escolha **APIs da Microsoft**.
1. Selecione **O armazenamento do Azure** na lista de resultados para exibir o painel **de permissões de API** de solicitação.
1. Em **que tipo de permissões seu aplicativo exige?** **Delegated permissions** Esta opção é selecionada para você por padrão.
1. Na **seção Seleto permissões** do painel **Depermissões apedido,** selecione a caixa de seleção ao lado **de user_impersonation**e clique em **Adicionar permissões**.

    ![Captura de tela mostrando permissões para armazenamento](media/storage-auth-aad-app/registered-app-permissions-1.png)

O **painel de permissões** da API agora mostra que seu aplicativo Azure AD registrado tem acesso tanto ao Microsoft Graph quanto ao Azure Storage. As permissões são concedidas ao Microsoft Graph automaticamente quando você registra seu aplicativo pela primeira vez com o Azure AD.

![Captura de tela mostrando permissões de aplicativos de registro](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="create-a-client-secret"></a>Criar um segredo do cliente

O aplicativo precisa de um segredo do cliente para provar sua identidade ao solicitar um token. Para adicionar o segredo do cliente, siga estas etapas:

1. Navegue até o seu registro de aplicativo no portal Azure.
1. Selecione a configuração **de segredos & Certificados.**
1. Em **segredos do Cliente,** clique em **Novo segredo do cliente** para criar um novo segredo.
1. Forneça uma descrição do segredo e escolha o intervalo de validade desejado.
1. Copie imediatamente o valor do novo segredo para um local seguro. O valor total é exibido para você apenas uma vez.

    ![Captura de tela mostrando o segredo do cliente](media/storage-auth-aad-app/client-secret.png)

## <a name="client-libraries-for-token-acquisition"></a>Bibliotecas de clientes para aquisição de tokens

Depois de registrar seu aplicativo e lhe conceder permissões para acessar dados no armazenamento Azure Blob ou no armazenamento de filas, você pode adicionar código ao seu aplicativo para autenticar um principal de segurança e adquirir um token OAuth 2.0. Para autenticar e adquirir o token, você pode usar uma das bibliotecas de [autenticação](../../active-directory/develop/reference-v2-libraries.md) da plataforma de identidade da Microsoft ou outra biblioteca de código aberto que suporta o OpenID Connect 1.0. Seu aplicativo pode então usar o token de acesso para autorizar uma solicitação contra o armazenamento do Azure Blob ou o armazenamento na fila.

Para obter uma lista de cenários para os quais a aquisição de tokens é suportada, consulte a seção [fluxos](/en-us/azure/active-directory/develop/msal-authentication-flows) de autenticação do conteúdo da Biblioteca de [Autenticação da Microsoft](/azure/active-directory/develop/msal-overview).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Valores conhecidos para autenticação com Azure AD

Para autenticar uma entidade de segurança com Azure AD, você precisa incluir alguns valores conhecidos no código.

### <a name="azure-ad-authority"></a>Autoridade do Azure AD

Para a nuvem pública da Microsoft, a autoridade do Azure AD básica é a seguinte, em que *tenant-id* é a ID do locatário do Active Directory (ou ID de diretório):

`https://login.microsoftonline.com/<tenant-id>/`

A ID do locatário identifica o locatário do Azure AD a ser usado para autenticação. Também é referido como o ID do diretório. Para recuperar o ID do inquilino, navegue até a página **Visão Geral** para o registro do aplicativo no portal Azure e copie o valor a partir daí.

### <a name="azure-storage-resource-id"></a>ID de recurso de armazenamento do Azure

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: crie um blob de blocos

O exemplo de código mostra como obter um token de acesso do Azure AD. O token de acesso é usado para autenticar o usuário especificado e, em seguida, autoriza uma solicitação para criar um blob de blocos. Para que esse exemplo funcione, primeiro siga as etapas descritas nas seções anteriores.

Para solicitar o token, você precisará dos seguintes valores do registro do seu aplicativo:

- O nome do seu domínio Azure AD. Recupere esse valor da página **Visão Geral** do diretório ativo do Azure.
- O ID do inquilino (ou diretório). Recupere esse valor da página **Visão Geral** do seu registro de aplicativo.
- O ID do cliente (ou aplicativo). Recupere esse valor da página **Visão Geral** do seu registro de aplicativo.
- O cliente redireciona o URI. Recupere este valor das configurações de **autenticação** para o registro do aplicativo.
- O valor do segredo do cliente. Recupere este valor do local para o qual você o copiou anteriormente.

### <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento

Para executar a amostra de código, crie uma conta de armazenamento na mesma assinatura do Diretório Ativo do Azure. Em seguida, crie um contêiner dentro dessa conta de armazenamento. O código de amostra criará uma bolha de bloco neste recipiente.

Em seguida, atribua explicitamente a função **Decontribuinte de dados blob de armazenamento** à conta de usuário a qual você executará o código de amostra. Para obter instruções sobre como atribuir essa função no portal Azure, consulte [o acesso de Grant ao Blob do Azure e os dados de fila com o RBAC no portal Azure](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Quando você cria uma conta do Azure Storage, você não é automaticamente atribuído permissões para acessar dados via Azure AD. Você deve atribuir explicitamente a si mesmo uma função RBAC para o Armazenamento do Microsoft Azure. Você pode atribuí-la no nível de assinatura, grupo de recursos, conta de armazenamento ou contêiner ou fila.

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Crie um aplicativo web que autoriza o acesso ao armazenamento Blob com o Azure AD

Quando seu aplicativo acessa o Azure Storage, ele o faz em nome do usuário, o que significa que os recursos de blob ou fila são acessados usando as permissões do usuário que está logado. Para tentar este exemplo de código, você precisa de um aplicativo web que solicita ao usuário que faça login usando uma identidade AD do Azure. Você pode criar o seu próprio, ou usar o aplicativo de exemplo fornecido pela Microsoft.

Um aplicativo web de amostra completa que adquire um token e o usa para criar uma bolha no Azure Storage está disponível no [GitHub](https://aka.ms/aadstorage). Revisar e executar a amostra concluída pode ser útil para entender os exemplos de código. Para obter instruções sobre como executar a amostra concluída, consulte a seção intitulada [Exibir e execute a amostra concluída](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Adicionar referências e usar instruções  

No Visual Studio, instale a biblioteca cliente do Azure Storage. No menu **Ferramentas,** selecione **NuGet Package Manager**e console **do Gerenciador de pacotes.** Digite os seguintes comandos na janela do console para instalar os pacotes necessários na biblioteca cliente do Azure Storage para .NET:

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Azure.Storage.Common
```

Em seguida, adicione as seguintes instruções usando o arquivo HomeController.cs:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

#### <a name="create-a-block-blob"></a>criar um blob de blocos

Adicione o seguinte trecho de código para criar uma bolha de bloco:

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with your storage account URL
    CloudBlockBlob blob =
        new CloudBlockBlob(
            new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
            storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

> [!NOTE]
> Para autorizar operações de blob e fila com um token OAuth 2.0, você deve usar HTTPS.

No exemplo acima, a biblioteca de cliente .NET lida com a autorização da solicitação para criar o blob de blocos. Bibliotecas de clientes do Azure Storage para outros idiomas também lidam com a autorização da solicitação para você. No entanto, se você estiver chamando uma operação de armazenamento do Azure com um token de OAuth, usando a API REST, você precisará autorizar a solicitação usando o token OAuth.

Para chamar as operações de serviço Blob e Queue usando tokens de acesso OAuth, passe o token de acesso no cabeçalho **Authorization** usando o esquema **Bearer** e especifique uma versão de serviço de 2017-11-09 ou superior, conforme mostrado no exemplo a seguir:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-oauth-token-from-azure-ad"></a>Obter um token OAuth do Azure AD

Em seguida, adicione um método que solicita um token do Azure AD em nome do usuário. Este método define o escopo para o qual as permissões devem ser concedidas. Para obter mais informações sobre permissões e escopos, consulte [Permissões e consentimento no ponto final da plataforma de identidade Microsoft](../../active-directory/develop/v2-permissions-and-consent.md).

Use o ID de recurso para construir o escopo para o qual adquirir o token. O exemplo constrói o escopo usando o ID de `user_impersonation` recurso juntamente com o escopo incorporado, o que indica que o token está sendo solicitado em nome do usuário.

Tenha em mente que você pode precisar apresentar ao usuário uma interface que permita ao usuário consentir em solicitar o token em seu nome. Quando o consentimento é necessário, o exemplo pega o **MsalUiRequiredException** e chama outro método para facilitar a solicitação de consentimento:

```csharp
public async Task<IActionResult> Blob()
{
    var scopes = new string[] { "https://storage.azure.com/user_impersonation" };
    try
    {
        var accessToken =
            await _tokenAcquisition.GetAccessTokenOnBehalfOfUser(HttpContext, scopes);
        ViewData["Message"] = await CreateBlob(accessToken);
        return View();
    }
    catch (MsalUiRequiredException ex)
    {
        AuthenticationProperties properties =
            BuildAuthenticationPropertiesForIncrementalConsent(scopes, ex);
        return Challenge(properties);
    }
}
```

Consentimento é o processo de um usuário conceder autorização a um aplicativo para acessar recursos protegidos em seu nome. A plataforma de identidade 2.0 da Microsoft suporta consentimento incremental, o que significa que um diretor de segurança pode solicitar um conjunto mínimo de permissões inicialmente e adicionar permissões ao longo do tempo, conforme necessário. Quando seu código solicitar um token de acesso, especifique o escopo `scope` das permissões que seu aplicativo precisa a qualquer momento no parâmetro. Para obter mais informações sobre consentimento incremental, consulte a seção intitulada **Consentimento Incremental e Dinâmico** em Por que atualizar para a plataforma de identidade da Microsoft [(v2.0)?](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent)

O método a seguir constrói as propriedades de autenticação para solicitar o consentimento incremental:

```csharp
private AuthenticationProperties BuildAuthenticationPropertiesForIncrementalConsent(string[] scopes,
                                                                                    MsalUiRequiredException ex)
{
    AuthenticationProperties properties = new AuthenticationProperties();

    // Set the scopes, including the scopes that ADAL.NET or MSAL.NET need for the Token cache.
    string[] additionalBuildInScopes = new string[] { "openid", "offline_access", "profile" };
    properties.SetParameter<ICollection<string>>(OpenIdConnectParameterNames.Scope,
                                                 scopes.Union(additionalBuildInScopes).ToList());

    // Attempt to set the login_hint so that the logged-in user is not presented
    // with an account selection dialog.
    string loginHint = HttpContext.User.GetLoginHint();
    if (!string.IsNullOrWhiteSpace(loginHint))
    {
        properties.SetParameter<string>(OpenIdConnectParameterNames.LoginHint, loginHint);

        string domainHint = HttpContext.User.GetDomainHint();
        properties.SetParameter<string>(OpenIdConnectParameterNames.DomainHint, domainHint);
    }

    // Specify any additional claims that are required (for instance, MFA).
    if (!string.IsNullOrEmpty(ex.Claims))
    {
        properties.Items.Add("claims", ex.Claims);
    }

    return properties;
}
```

## <a name="view-and-run-the-completed-sample"></a>Exibir e executar a amostra completa

Para executar o aplicativo de exemplo, primeiro clone ou baixe-o no [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal). Em seguida, atualize o aplicativo conforme descrito nas seções a seguir.

### <a name="provide-values-in-the-settings-file"></a>Fornecer valores no arquivo de configurações

Em seguida, atualize o arquivo *appsettings.json* com seus próprios valores, da seguinte forma:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    // To call an API
    "ClientSecret": "<client-secret>"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Atualize a conta de armazenamento e o nome do contêiner

No arquivo *HomeController.cs,* atualize o URI que faz referência ao blob de bloco para usar o nome da sua conta de armazenamento e contêiner:

```csharp
CloudBlockBlob blob = new CloudBlockBlob(
                      new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt"),
                      storageCredentials);
```

### <a name="enable-implicit-grant-flow"></a>Permitir fluxo implícito de subvenção

Para executar a amostra, talvez seja necessário configurar o fluxo de subvenção implícito para o registro do aplicativo. Siga estas etapas:

1. Navegue até o seu registro de aplicativo no portal Azure.
1. Na seção Gerenciar, selecione a **configuração Autenticação.**
1. Em **Configurações Avançadas,** na seção **Subvenção implícita,** selecione as caixas de seleção para habilitar tokens de acesso e tokens de ID, conforme mostrado na imagem a seguir:

    ![Captura de tela mostrando como ativar configurações para fluxo de subvenção implícito](media/storage-auth-aad-app/enable-implicit-grant-flow.png)

### <a name="update-the-port-used-by-localhost"></a>Atualize a porta usada pelo host local

Ao executar a amostra, você pode descobrir que você precisa atualizar o URI de redirecionamento especificado no registro do aplicativo para usar a porta *localhost* atribuída em tempo de execução. Para atualizar o URI de redirecionamento para usar a porta atribuída, siga estas etapas:

1. Navegue até o seu registro de aplicativo no portal Azure.
1. Na seção Gerenciar, selecione a **configuração Autenticação.**
1. Em **Redirecionar URIs,** edite a porta para corresponder à usada pelo aplicativo de amostra, conforme mostrado na imagem a seguir:

    ![Captura de tela mostrando URIs redirecionamento para registro de aplicativos](media/storage-auth-aad-app/redirect-uri.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre a plataforma de identidade da Microsoft, consulte a [plataforma de identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).
- Para saber mais sobre as funções RBAC para armazenamento Azure, consulte [Gerenciar direitos de acesso a dados de armazenamento com RBAC](storage-auth-aad-rbac.md).
- Para saber como usar identidades gerenciadas para recursos do Azure com o Azure Storage, consulte [Authenticate acesso a blobs e filas com o Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).
