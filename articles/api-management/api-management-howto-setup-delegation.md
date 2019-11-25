---
title: Como delegar o registro de usuário e a assinatura do produto
description: Saiba como delegar a assinatura de produto e registro de usuário a um terceiro no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454346"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registro de usuário e a assinatura do produto

Delegation allows you to use your existing website for handling developer sign in/sign up and subscription to products, as opposed to using the built-in functionality in the developer portal. It enables your website to own the user data and perform the validation of these steps in a custom way.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>Delegando a entrada e inscrição de desenvolvedores

To delegate developer, sign in and sign up to your existing website, you'll need to create a special delegation endpoint on your site. It needs to act as the entry-point for any such request initiated from the API Management developer portal.

O fluxo de trabalho final será o seguinte:

1. Developer clicks on the sign in or sign up link at the API Management developer portal
2. O navegador é redirecionado ao ponto de extremidade de delegação
3. Delegation endpoint in return redirects to or presents UI asking user to sign in or sign up
4. Em caso de êxito, o usuário é redirecionado de volta para o portal do desenvolvedor do Gerenciamento de API onde começou

Para começar, vamos configurar o Gerenciamento de API para encaminhar as solicitações por meio do seu ponto de extremidade de delegação. In the Azure portal, search for **Security** in your API Management resource and then click the **Delegation** item. Click the checkbox to enable 'Delegate sign in & sign up'.

![Página de delegação][api-management-delegation-signin-up]

* Decida qual será o URL do seu ponto de extremidade de delegação especial e insira-o no campo **URL do ponto de extremidade de delegação** . 
* No campo de chave de autenticação de Delegação, insira um segredo que será usado para calcular uma assinatura fornecida a você para verificação, para garantir que a solicitação realmente venha do Gerenciamento de API do Azure. Você pode clicar no botão **gerar** para o Gerenciamento de API gerar aleatoriamente uma chave para você.

Agora, você precisa criar o **ponto de extremidade de delegação**. Ele precisa realizar uma série de ações:

1. Receba uma solicitação com a seguinte forma:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Query parameters for the sign in / sign up case:
   
   * **operation**: identifica o tipo de solicitação de delegação – neste caso, pode ser somente **SignIn**
   * **returnUrl**: the URL of the page where the user clicked on a sign in or sign up link
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado
2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Calcule um hash HMAC-SHA512 de uma cadeia de caracteres baseada nos parâmetros de consulta **returnUrl** e **salt** ([código de exemplo fornecido abaixo]):
     
     > HMAC(**salt**+ '\n' +**returnUrl**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Verify that you are receiving a request for sign in/sign up: the **operation** query parameter will be set to "**SignIn**".
4. Present the user with UI to sign in or sign up
5. Se o usuário estiver se inscrevendo, você precisará criar uma conta correspondente para ele no Gerenciamento de API. [Crie um usuário] com a API REST do Gerenciamento de API. When doing so, ensure that you set the user ID to the same value as in your user store or to an ID that you can keep track of.
6. Quando o usuário for autenticado com sucesso:
   
   * [solicite um token de logon único (SSO)] por meio da API REST do Gerenciamento de API
   * anexe um parâmetro de consulta returnUrl ao URL SSO que você recebeu da chamada à API acima:
     
     > por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecione o usuário à URL produzida acima

Além da operação **SignIn**, você também pode executar o gerenciamento de conta seguindo as etapas anteriores e usando uma das seguintes operações:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Você deve passar os seguintes parâmetros de consulta para operações de gerenciamento de conta.

* **operation**: identifica o tipo de solicitação de delegação (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: the user ID of the account to manage
* **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
* **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

## <a name="delegate-product-subscription"> </a>Delegando a assinatura de produtos
Delegating product subscription works similarly to delegating user sign in/-up. O fluxo de trabalho final seria o seguinte:

1. Developer selects a product in the API Management developer portal and clicks on the Subscribe button.
2. Browser is redirected to the delegation endpoint.
3. Delegation endpoint performs required product subscription steps. It's up to you to design the steps. They may include redirecting to another page to request billing information, asking additional questions, or simply storing the information and not requiring any user action.

Para habilitar a funcionalidade, na página **Delegação**, clique em **Delegar assinatura do produto**.

Next, ensure the delegation endpoint does the following actions:

1. Receba uma solicitação com a seguinte forma:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribe to}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parâmetros de consulta para a assinatura de produto:
   
   * **operation**: identifica o tipo de solicitação de delegação. Para solicitações de assinatura do produto, as opções válidas são:
     * “Subscribe”: uma solicitação para que o usuário assine determinado produto com uma ID fornecida (veja abaixo)
     * “Unsubscribe”: uma solicitação para cancelar a assinatura do usuário de um produto
     * “Renew”: uma solicitação para renovar uma assinatura (por exemplo, que pode estar expirando)
   * **productId**: a ID do produto para o qual o usuário solicitou uma assinatura
   * **subscriptionId**: on *Unsubscribe* and *Renew* - the ID of the product subscription
   * **userId**: the ID of the user the request is made for
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Compute an HMAC-SHA512 of a string based on the **productId**, **userId**, and **salt** query parameters:
     
     > HMAC(**salt**+ '\n' +**productId**+ '\n' +**userId**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Process product subscription based on the type of operation requested in **operation** - for example, billing, further questions, etc.
4. On successfully subscribing the user to the product on your side, subscribe the user to the API Management product by [calling the REST API for subscriptions].

## <a name="delegate-example-code"> </a> Código de exemplo

These code samples show how to:

* Take the *delegation validation key*, which is set in the Delegation screen of the publisher portal
* Create an HMAC, which is then used to validate the signature, proving the validity of the passed returnUrl.

O mesmo código funciona para productId e userId com pequenas modificações.

**Código C# para gerar hash de returnUrl**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**Código NodeJS para gerar hash de returnUrl**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> You need to [republish the developer portal](api-management-howto-developer-portal-customize.md#publish) for the delegation changes to take effect.

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre delegação, consulte o vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicite um token de logon único (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Crie um usuário]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[calling the REST API for subscriptions]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
