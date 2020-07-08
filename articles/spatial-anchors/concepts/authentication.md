---
title: Autenticação e autorização
description: Saiba mais sobre as várias maneiras pelas quais um aplicativo ou serviço pode se autenticar para Âncoras Espaciais do Azure e os níveis de controle que você precisa para portar acesso a Âncoras Espaciais do Azure.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: baf5252a6b158855739546c2a03e63dceee6701e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84456497"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para Âncoras Espaciais do Azure

Nesta seção, abordaremos as várias maneiras pelas quais você pode autenticar as Âncoras Espaciais do Azure de seu aplicativo ou serviço Web e as maneiras como você pode usar o controle de acesso baseado em função no Azure Directory (Azure AD) para controlar o acesso às suas contas de Âncoras Espaciais.

## <a name="overview"></a>Visão geral

![Uma visão geral de autenticação para Âncoras Espaciais do Azure](./media/spatial-anchors-authentication-overview.png)

Para acessar uma determinada conta de Âncoras Espaciais do Azure, os clientes precisam primeiro obter um token de acesso do STS (serviço de token de segurança) da Realidade Misturada do Azure. Os tokens obtidos do STS Live por 24 horas e contêm informações para os serviços de Âncoras Espaciais para tomar decisões de autorização na conta e garantir que apenas entidades de segurança autorizadas possam acessar essa conta.

Os tokens de acesso podem ser obtidos pela troca de chaves de conta ou tokens emitidos pelo Azure AD.

As chaves de conta permitem que você comece rapidamente a usar o serviço Âncoras Espaciais do Azure; no entanto, antes de implantar seu aplicativo para produção, é recomendável que você atualize seu aplicativo para usar a autenticação baseada no Azure AD.

Os tokens de autenticação do Azure AD podem ser obtidos de duas maneiras:

- Se você estiver criando um aplicativo empresarial e sua empresa estiver usando o Azure AD como seu sistema de identidade, você poderá usar a autenticação do Azure AD baseada no usuário em seu aplicativo e conceder acesso às suas contas de âncoras espaciais usando seus grupos de segurança existentes do Azure AD ou diretamente aos usuários em sua organização.
- Caso contrário, é recomendável que você obtenha tokens do Azure AD de um serviço Web que dê suporte ao seu aplicativo. Usar um serviço Web de suporte é o método de autenticação recomendado para aplicativos de produção, pois evita a inserção de credenciais para acessar Âncoras Espaciais do Azure em seu aplicativo cliente.

## <a name="account-keys"></a>Chaves de conta

O uso de chaves de conta para acesso à sua conta de Âncoras Espaciais do Azure é a maneira mais simples de começar. Você encontrará suas chaves de conta no portal do Azure. Navegue até sua conta e selecione a guia "Chaves".

![Uma visão geral de autenticação para Âncoras Espaciais do Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Duas chaves são disponibilizadas, que são simultaneamente válidas para acesso à conta de Âncoras Espaciais. É recomendável que você atualize regularmente a chave usada para acessar a conta; ter duas chaves válidas separadas habilita tais atualizações sem tempo de inatividade; você só precisa atualizar a chave primária e a chave secundária como alternativa.

O SDK tem suporte interno para autenticação com chaves de conta; basta definir a propriedade AccountKey em seu objeto cloudSession.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Quando isso for feito, o SDK manipulará a troca da chave de conta para um token de acesso e o cache de tokens necessário para seu aplicativo.

> [!WARNING]
> O uso de chaves de conta é recomendado para rápida integração, mas somente durante o desenvolvimento/protótipo. É altamente recomendável não enviar seu aplicativo para produção usando uma chave de conta incorporada e, em vez disso, usar as abordagens de autenticação do Azure AD baseadas em usuário ou em serviço listadas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação de usuário do Azure AD

Para aplicativos que visam usuários do Azure Active Directory, a abordagem recomendada é usar um token do Azure AD para o usuário, que você pode obter usando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Você deve seguir as etapas listadas em [Início rápido para registrar um aplicativo](../../active-directory/develop/quickstart-register-app.md), que incluem:

1. Configuração no portal do Azure
    1.  Registre seu aplicativo no Azure AD como **Aplicativo nativo**. Como parte do registro, você precisará determinar se seu aplicativo deve ser multilocatário ou não e fornecer as URLs de redirecionamento permitidas para seu aplicativo.
        1.  Alterne para a guia **Permissões de API**
        2.  Selecione **Adicionar uma permissão**
            1.  Selecione **Realidade Misturada da Microsoft** na guia **APIs que minha organização usa**
            2.  Selecionar **Permissões delegadas**
            3.  Marque a caixa de seleção **mixedreality.signin** em **mixedreality**
            4.  Selecione **Adicionar permissões**
        3.  Selecione **Conceder consentimento do administrador**
    2.  Conceda ao seu aplicativo ou usuários acesso ao seu recurso:
        1.  Navegue até o recurso Âncoras Espaciais no portal do Azure
        2.  Mude para a guia **Controle de acesso (IAM)**
        3.  Pressione **Adicionar atribuição de função**
            1.  [Selecionar uma função](#role-based-access-control)
            2.  No campo **Selecione**, insira o nome dos usuários, grupos e/ou aplicativos aos quais você deseja atribuir o acesso.
            3.  Pressione **Salvar**.
2. No seu código:
    1.  Use a **ID do aplicativo** e a **URI de redirecionamento** do seu aplicativo do Azure AD como os parâmetros **ID do cliente** e **RedirectUri** no MSAL
    2.  Defina as informações do locatário:
        1.  Se seu aplicativo dá suporte a **Somente minha organização**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
        2.  Se o aplicativo der suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por **organizações**
        3.  Se o aplicativo der suporte a **Todos os usuários de contas Microsoft**, substitua esse valor por **Comum**
    3.  Em sua solicitação de token, defina o **escopo** como "https://sts.mixedreality.azure.com//.default". Esse escopo indicará ao Azure AD que seu aplicativo está solicitando um token para o STS (serviço de token de segurança) da realidade misturada.

Com isso, seu aplicativo deve ser capaz de obter do MSAL um token do Azure AD; você pode definir esse token do Azure AD como o **authenticationToken** em seu objeto de configuração de sessão de nuvem.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autenticação de serviço do Azure AD

A opção recomendada para implantar aplicativos que aproveitam Âncoras Espaciais do Azure para produção é utilizar um serviço de back-end que atenderá às solicitações de autenticação. O esquema geral deve ser conforme descrito neste diagrama:

![Uma visão geral de autenticação para Âncoras Espaciais do Azure](./media/spatial-anchors-aad-authentication.png)

Aqui supõe-se que seu aplicativo use seu mecanismo (por exemplo: conta Microsoft, PlayFab, Facebook, Google ID, nome de usuário personalizado/senha etc.) para autenticar em seu serviço de back-end. Depois que os usuários são autenticados em seu serviço de back-end, esse serviço pode recuperar um token do Azure AD, trocá-lo por um token de acesso para Âncoras Espaciais do Azure e retorná-lo de volta para o aplicativo cliente.

O token de acesso do Azure AD é recuperado usando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Você deve seguir as etapas listadas em [Início rápido para registrar um aplicativo](../../active-directory/develop/quickstart-register-app.md), que incluem:

1.  Configuração no portal do Azure:
    1.  Registrar seu aplicativo no Azure AD:
        1.  No portal do Azure, navegue até **Microsoft Azure Active Directory** e selecione **registros de aplicativo**
        2.  Selecione **novo registro de aplicativo**
        3.  Insira o nome do seu aplicativo, selecione **aplicativo Web/API** como o tipo de aplicativo e insira a URL de autenticação para seu serviço. Em seguida, clique em **Criar**.
        4.  Nesse aplicativo, clique em **configurações**e, em seguida, selecione a guia **certificados e segredos** . Crie um novo segredo do cliente, selecione uma duração e clique em **Adicionar**. Certifique-se de salvar o valor secreto, pois será necessário incluí-lo no código do serviço Web.
    2.  Conceda ao seu aplicativo e/ou usuários acesso ao seu recurso:
        1.  Navegue até o recurso Âncoras Espaciais no portal do Azure
        2.  Mude para a guia **Controle de acesso (IAM)**
        3.  Pressione **Adicionar atribuição de função**
        1.  [Selecionar uma função](#role-based-access-control)
        2.  No campo **Selecione**, insira o nome dos aplicativos criados e aos quais você deseja atribuir o acesso. Se você quiser que os usuários do aplicativo tenham funções diferentes em relação à conta de Âncoras Espaciais, registre vários aplicativos no Azure AD e atribua a cada um uma função separada. Em seguida, implemente sua lógica de autorização para usar a função certa para seus usuários.
        3.  Observação-na seleção **Adicionar atribuição de função** , você deseja que o **acesso de atribuição** seja definido como "usuário, grupo ou entidade de serviço do Azure AD".
    3.  Pressione **Salvar**.
2.  Em seu código (observação: você pode usar o exemplo de serviço incluído no GitHub):
    1.  Use a ID do aplicativo, o segredo do aplicativo e a URI de redirecionamento do seu aplicativo do Azure AD como os parâmetros ID do cliente, segredo e RedirectUri no MSAL
    2.  Defina a ID do locatário como sua própria ID de locatário do Azure ADD no parâmetro de autoridade no MSAL.
    3.  Em sua solicitação de token, defina o **escopo** como "https://sts.mixedreality.azure.com//.default"

Com isso, o serviço de back-end pode recuperar um token do Azure AD. Em seguida, ele pode alterá-lo para um token MR que ele retornará ao cliente. Usar um token do Azure AD para recuperar um token MR é feito por meio de uma chamada REST. Aqui está uma chamada de exemplo:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Onde o cabeçalho de autorização é formatado da seguinte maneira: `Bearer <Azure_AD_token>`

E a resposta contém o token MR em texto sem formatação.

Esse token MR é retornado para o cliente. Seu aplicativo cliente pode defini-lo como seu token de acesso na configuração da sessão de nuvem.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para ajudar a controlar o nível de acesso concedido a aplicativos, serviços ou usuários do Azure AD do seu serviço, as seguintes funções foram criadas para você atribuir conforme o necessário em relação às suas contas de Âncoras Espaciais do Azure:

- **Proprietário da conta de Âncoras Espaciais**: os aplicativos ou usuários que têm essa função podem criar âncoras espaciais, consultá-las e excluí-las. Quando você se autentica em sua conta usando chaves de conta, a função **Proprietário da conta de Âncoras Espaciais** é atribuída à entidade de segurança autenticada.
- **Colaborador da conta de Âncoras Espaciais**: os aplicativos ou usuários que têm essa função podem criar âncoras espaciais, consultá-las, mas não pode excluí-las.
- **Leitor de conta de Âncoras Espaciais**: os aplicativos ou usuários que têm essa função só podem consultar âncoras espaciais, mas não podem criar novas, excluir existentes ou atualizar metadados em âncoras espaciais. Normalmente, isso é usado para aplicativos em que alguns usuários coletam o ambiente, enquanto outros só podem recuperar âncoras previamente colocadas nesse ambiente.

## <a name="next-steps"></a>Próximas etapas

Criar seu primeiro aplicativo com Âncoras Espaciais do Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
