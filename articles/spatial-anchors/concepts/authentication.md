---
title: Autenticação e autorização
description: Conheça as várias maneiras que um aplicativo ou serviço pode autenticar para âncoras espaciais do Azure e os níveis de controle que você tem para acessar o Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656956"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autenticação e autorização para âncoras espaciais do Azure

Nesta seção, abordaremos as várias maneiras que você pode autenticar para âncoras espaciais do Azure a partir de seu aplicativo ou serviço web, e as maneiras pelas quais você pode usar o Controle de Acesso Baseado em Função no Azure Directory (Azure AD) para controlar o acesso às suas contas de Âncoras Espaciais.

## <a name="overview"></a>Visão geral

![Uma visão geral da autenticação para âncoras espaciais do Azure](./media/spatial-anchors-authentication-overview.png)

Para acessar uma determinada conta do Azure Spatial Anchors, os clientes precisam primeiro obter um token de acesso do Azure Mixed Reality Security Token Service (STS). Os tokens obtidos no STS vivem por 24 horas e contêm informações para que os serviços de Âncoras Espaciais tomem decisões de autorização na conta e garantam que apenas os diretores autorizados possam acessar essa conta.

Os tokens de acesso podem ser obtidos em troca de chaves de conta ou de tokens emitidos pelo Azure AD.

As chaves da conta permitem que você comece rapidamente a usar o serviço Ancorantes Espaciais do Azure; no entanto, antes de implantar seu aplicativo para produção, é aconselhável que você atualize seu aplicativo para usar a autenticação baseada no Azure AD.

Os tokens de autenticação Azure AD podem ser obtidos de duas maneiras:

- Se você estiver construindo um aplicativo corporativo e sua empresa estiver usando o Azure AD como seu sistema de identidade, você poderá usar a autenticação Azure AD baseada no usuário em seu aplicativo e conceder acesso às suas contas de âncoras espaciais usando seus grupos de segurança Azure AD existentes, ou diretamente aos usuários da sua organização.
- Caso contrário, é recomendável que você obtenha tokens Azure AD de um serviço web que suporta seu aplicativo. O uso de um serviço web de suporte é o método recomendado de autenticação para aplicativos de produção, pois evita incorporar as credenciais para acessar âncoras espaciais do Azure no aplicativo do cliente.

## <a name="account-keys"></a>Chaves da conta

Usar as chaves da conta para ter acesso à sua conta azure Spatial Anchors é a maneira mais simples de começar. Você encontrará as chaves da sua conta no portal Azure. Navegue até sua conta e selecione a guia "Chaves".

![Uma visão geral da autenticação para âncoras espaciais do Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Duas teclas são disponibilizadas, ambas válidas simultaneamente para acesso à conta Âncoras Espaciais. Recomenda-se atualizar regularmente a chave que usa para acessar a conta; ter duas chaves válidas separadas habilitar tais atualizações sem tempo de inatividade; você só tem que atualizar alternativamente a chave primária e a chave secundária.

O SDK tem suporte integrado para autenticação com chaves de conta; você simplesmente precisa definir a propriedade AccountKey no objeto cloudSession.

# <a name="c"></a>[C #](#tab/csharp)

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

Uma vez feito isso, o SDK lidará com a troca da chave da conta por um token de acesso e o cache necessário de tokens para o seu aplicativo.

> [!WARNING]
> O uso de chaves de conta é recomendado para um embarque rápido, mas durante o desenvolvimento/prototipagem apenas. É fortemente recomendável não enviar seu aplicativo para a produção usando uma chave de conta incorporada nele e, em vez disso, usar as abordagens de autenticação Azure AD baseadas no usuário ou no serviço listadas a seguir.

## <a name="azure-ad-user-authentication"></a>Autenticação do usuário Azure AD

Para aplicativos direcionados aos usuários do Azure Active Directory, a abordagem recomendada é usar um token Azure AD para o usuário, que você pode obter usando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Você deve seguir as etapas listadas no [registro de um aplicativo quickstart](../../active-directory/develop/quickstart-register-app.md), que incluem:

1. Configuração no portal Azure
    1.  Registre seu aplicativo no Azure AD como **aplicativo nativo**. Como parte do registro, você precisará determinar se seu aplicativo deve ser multi-inquilino ou não, e fornecer os URLs redirecionados permitidos para sua aplicação.
        1.  Mude para a **guia de permissões** da API
        2.  Selecione **Adicionar uma permissão**
            1.  Selecione **provedor de recursos de realidade mista** em **APIs que minha organização usa** guia
            2.  Selecione **permissões delegadas**
            3.  Verifique a caixa para **mixedreality.signin** em **mixedreality**
            4.  Selecione **Adicionar permissões**
        3.  Selecione **o consentimento do grant admin**
    2.  Conceda ao seu aplicativo ou aos usuários acesso ao seu recurso:
        1.  Navegue até o recurso Âncoras Espaciais no portal Azure
        2.  Mude para a guia **Controle de acesso (IAM)**
        3.  **Acessa a atribuição de função de adicionar**
            1.  [Selecionar uma função](#role-based-access-control)
            2.  No campo **Selecionar,** digite o nome do usuário(s), grupo(s) e/ou aplicativo(s) ao qual deseja atribuir acesso.
            3.  Pressione **Salvar**.
2. Em seu código:
    1.  Certifique-se de usar o **ID** do aplicativo e **redirecionar uri** do seu próprio aplicativo Azure AD como os parâmetros **de ID** e **RedirectUri** do cliente no ADAL
    2.  Defina as informações do inquilino:
        1.  Se o seu aplicativo suportar **apenas a minha organização,** substitua este valor pelo seu **ID** de inquilino ou **nome do inquilino** (por exemplo, contoso.microsoft.com)
        2.  Se seu aplicativo suportar **contas em qualquer diretório organizacional,** substitua esse valor por **Organizações**
        3.  Se o seu aplicativo suportar **todos os usuários da conta Microsoft,** substitua esse valor pelo **Common**
    3.  Em sua solicitação de **resource** token,https://sts.mixedreality.azure.comdefina o recurso como " ". Esse "recurso" indicará ao Azure AD que seu aplicativo está solicitando um token para o serviço Azure Spatial Anchors.

Com isso, sua aplicação deve ser capaz de obter do MSAL um token AD do Azure; você pode definir esse token Azure AD como a **autenticaçãoToken** no objeto de configuração da sessão na nuvem.

# <a name="c"></a>[C #](#tab/csharp)

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

## <a name="azure-ad-service-authentication"></a>Autenticação do serviço Azure AD

A opção recomendada para implantar aplicativos aproveitando âncoras espaciais do Azure para a produção é aproveitar um serviço de back-end que irá intermediar solicitações de autenticação. O esquema geral deve ser descrito neste diagrama:

![Uma visão geral da autenticação para âncoras espaciais do Azure](./media/spatial-anchors-aad-authentication.png)

Aqui, supõe-se que seu aplicativo usa seu próprio mecanismo (por exemplo: conta Microsoft, PlayFab, Facebook, Google ID, nome de usuário/senha personalizado, etc.) para autenticar ao seu serviço backend. Uma vez que seus usuários sejam autenticados ao seu serviço de backend, esse serviço pode recuperar um token Azure AD, trocá-lo por um token de acesso por Âncoras Espaciais do Azure e devolvê-lo ao seu aplicativo cliente.

O token de acesso Azure AD é recuperado usando a [biblioteca MSAL](../../active-directory/develop/msal-overview.md). Você deve seguir as etapas listadas no [registro de um aplicativo quickstart](../../active-directory/develop/quickstart-register-app.md), que incluem:

1.  Configuração no portal Azure:
    1.  Registre seu aplicativo no Azure AD:
        1.  No portal Azure, navegue até **o Azure Active Directory**e selecione **registros de aplicativos**
        2.  Selecione **o novo registro de inscrição**
        3.  Digite o nome do seu aplicativo, selecione **o aplicativo da Web / API** como o tipo de aplicativo e digite a URL auth para o seu serviço. Em seguida, aperte **Create**.
        4.  Nesse aplicativo, clique **em Configurações**e selecione a guia **Teclas.** Digite o nome da sua chave, selecione uma duração e clique **em Salvar**. Certifique-se de salvar o valor-chave exibido nesse momento, pois você precisará incluí-lo no código do seu serviço web.
    2.  Conceda ao seu aplicativo e/ou aos usuários acesso ao seu recurso:
        1.  Navegue até o recurso Âncoras Espaciais no portal Azure
        2.  Mude para a guia **Controle de acesso (IAM)**
        3.  **Acessa a atribuição de função de adicionar**
        1.  [Selecionar uma função](#role-based-access-control)
        2.  No campo **seleto,** digite o nome dos aplicativos criados e ao qual deseja atribuir acesso. Se você quiser que os usuários do seu aplicativo tenham funções diferentes contra a conta Âncoras Espaciais, você deve registrar vários aplicativos no Azure AD e atribuir a cada uma das funções separadas. Em seguida, implemente sua lógica de autorização para usar a função certa para seus usuários.
    3.  Pressione **Salvar**.
2.  Em seu código (nota: você pode usar a amostra de serviço incluída no GitHub):
    1.  Certifique-se de usar o ID do aplicativo, o segredo do aplicativo e redirecionar o Uri do seu próprio aplicativo Azure AD como os parâmetros de ID, secreto e RedirectUri do cliente no ADAL
    2.  Defina o ID do inquilino como seu próprio ID de inquilino AAAzure ADD no parâmetro de autoridade em ADAL
    3.  Em sua solicitação de **resource** token,https://sts.mixedreality.azure.comdefina o recurso para "

Com isso, seu serviço de back-end pode recuperar um token Azure AD. Em seguida, ele pode trocá-lo por um token MR que ele vai retornar ao cliente. O uso de um token Azure AD para recuperar um token MR é feito através de uma chamada REST. Aqui está uma chamada de amostra:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Quando o cabeçalho autorização for formatado da seguinte forma:`Bearer <accoundId>:<accountKey>`

E a resposta contém o token MR em texto simples.

Esse token MR é então devolvido ao cliente. Seu aplicativo cliente pode, então, defini-lo como seu token de acesso na configuração de sessão na nuvem.

# <a name="c"></a>[C #](#tab/csharp)

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

Para ajudar a controlar o nível de acesso concedido aos aplicativos, serviços ou usuários ad do seu serviço, foram criadas as seguintes funções para que você atribua conforme necessário às suas contas do Azure Spatial Anchors:

- **Proprietário da conta de âncoras espaciais**: os aplicativos ou usuários que têm essa função são capazes de criar âncoras espaciais, consultar para eles e excluí-los. Quando você autentica em sua conta usando chaves de conta, a função **'Proprietário da conta âncoras espaciais'** é atribuída ao principal autenticado.
- **Colaboradore de conta âncoras espaciais**: os aplicativos ou usuários que têm essa função são capazes de criar âncoras espaciais, consultar para eles, mas não podem excluí-los.
- **Leitore de contas âncoras espaciais**: aplicativos ou usuários que têm essa função só podem consultar âncoras espaciais, mas não podem criar novos, excluir os existentes ou atualizar metadados em âncoras espaciais. Isso é normalmente usado para aplicativos onde alguns usuários fazem a curadoria do ambiente, enquanto outros só podem recordar âncoras anteriormente colocadas nesse ambiente.

## <a name="next-steps"></a>Próximas etapas

Crie seu primeiro aplicativo com âncoras espaciais azure.

> [!div class="nextstepaction"]
> [Unidade (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unidade (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unidade (Android)](../quickstarts/get-started-unity-android.md)

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
