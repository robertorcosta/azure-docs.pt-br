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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454346"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Como delegar o registro de usuário e a assinatura do produto

A delegação permite que você use seu site existente para lidar com o login/login do desenvolvedor e a assinatura de produtos, em vez de usar a funcionalidade incorporada no portal do desenvolvedor. Ele permite que seu site possua os dados do usuário e realize a validação dessas etapas de forma personalizada.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Delegando a entrada e inscrição de desenvolvedores

Para delegar desenvolvedor, fazer login e se inscrever no seu site existente, você precisará criar um ponto final de delegação especial em seu site. Ele precisa funcionar como o ponto de entrada para qualquer solicitação iniciada no portal de desenvolvedores da API Management.

O fluxo de trabalho final será o seguinte:

1. O desenvolvedor clica no login ou no link de inscrição no portal de desenvolvedores da API Management
2. O navegador é redirecionado ao ponto de extremidade de delegação
3. Ponto final da delegação em retorno redireciona ou apresenta ui pedindo ao usuário para entrar ou se inscrever
4. Em caso de êxito, o usuário é redirecionado de volta para o portal do desenvolvedor do Gerenciamento de API onde começou

Para começar, vamos configurar o Gerenciamento de API para encaminhar as solicitações por meio do seu ponto de extremidade de delegação. No portal Azure, procure **por Segurança** em seu recurso de Gerenciamento de API e clique no item **Delegação.** Clique na caixa de seleção para ativar 'Delegar login & se inscrever'.

![Página de delegação][api-management-delegation-signin-up]

* Decida qual será o URL do seu ponto de extremidade de delegação especial e insira-o no campo **URL do ponto de extremidade de delegação** . 
* No campo de chave de autenticação de Delegação, insira um segredo que será usado para calcular uma assinatura fornecida a você para verificação, para garantir que a solicitação realmente venha do Gerenciamento de API do Azure. Você pode clicar no botão **gerar** para o Gerenciamento de API gerar aleatoriamente uma chave para você.

Agora, você precisa criar o **ponto de extremidade de delegação**. Ele precisa realizar uma série de ações:

1. Receba uma solicitação com a seguinte forma:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL da página de origem}&salt={string}&sig={string}*
   > 
   > 
   
    Consultar parâmetros para o caso de login/inscrição:
   
   * **operation**: identifica o tipo de solicitação de delegação – neste caso, pode ser somente **SignIn**
   * **returnUrl**: a URL da página onde o usuário clicou em um link de login ou de cadascção
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado
2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Calcule um hash HMAC-SHA512 de uma cadeia de caracteres baseada nos parâmetros de consulta **returnUrl** e **salt** ([código de exemplo fornecido abaixo]):
     
     > HMAC(**salt**+ '\n' +**returnUrl**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Verifique se você está recebendo uma solicitação de login/inscrição: o parâmetro de consulta de **operação** será definido como "**SignIn**".
4. Presenteie o usuário com ui para fazer login ou se inscrever
5. Se o usuário estiver se inscrevendo, você precisará criar uma conta correspondente para ele no Gerenciamento de API. [Crie um usuário] com a API REST do Gerenciamento de API. Ao fazer isso, certifique-se de definir o ID do usuário para o mesmo valor que em sua loja de usuário ou para um ID que você pode acompanhar.
6. Quando o usuário for autenticado com sucesso:
   
   * [solicite um token de logon único (SSO)] por meio da API REST do Gerenciamento de API
   * anexe um parâmetro de consulta returnUrl ao URL SSO que você recebeu da chamada à API acima:
     
     > por exemplo, https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * redirecione o usuário à URL produzida acima

Além da operação **SignIn,** você também pode executar o gerenciamento de contas seguindo as etapas anteriores e usando uma das seguintes operações:

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

Você deve passar os seguintes parâmetros de consulta para operações de gerenciamento de conta.

* **operation**: identifica o tipo de solicitação de delegação (ChangePassword, ChangeProfile ou CloseAccount)
* **userId**: o ID do usuário da conta para gerenciar
* **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
* **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Delegando a assinatura de produtos
Delegar a assinatura do produto funciona de forma semelhante ao delegar o login/up do usuário. O fluxo de trabalho final seria o seguinte:

1. O desenvolvedor seleciona um produto no portal de desenvolvedores da API Management e clica no botão Assinar.
2. O navegador é redirecionado para o ponto final da delegação.
3. O ponto final da delegação executa as etapas de assinatura de produtos necessárias. Cabe a você projetar os passos. Eles podem incluir redirecionar para outra página para solicitar informações de cobrança, fazer perguntas adicionais ou simplesmente armazenar as informações e não exigir qualquer ação do usuário.

Para habilitar a funcionalidade, na página **Delegação**, clique em **Delegar assinatura do produto**.

Em seguida, certifique-se de que o ponto final da delegação faça as seguintes ações:

1. Receba uma solicitação com a seguinte forma:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product to subscribeto}&userId={user making request}&salt={string}&sig={string}*
   >
   
    Parâmetros de consulta para a assinatura de produto:
   
   * **operation**: identifica o tipo de solicitação de delegação. Para solicitações de assinatura do produto, as opções válidas são:
     * “Subscribe”: uma solicitação para que o usuário assine determinado produto com uma ID fornecida (veja abaixo)
     * “Unsubscribe”: uma solicitação para cancelar a assinatura do usuário de um produto
     * “Renew”: uma solicitação para renovar uma assinatura (por exemplo, que pode estar expirando)
   * **productId**: a ID do produto para o qual o usuário solicitou uma assinatura
   * **subscriptionId**: on *Unsubscribe* and *Renew* - o ID da assinatura do produto
   * **userId**: o ID do usuário a solicitação é feita para
   * **salt**: uma cadeia de caracteres de salt especial usada para calcular um hash de segurança
   * **sig**: um hash de segurança calculado para ser usado para comparação com seu próprio hash calculado

2. Confirme que a solicitação está vindo do Gerenciamento de API do Azure (opcional, mas altamente recomendado por segurança)
   
   * Calcule um HMAC-SHA512 de uma string com base nos parâmetros **de ida,** **userId**e **sal:**
     
     > HMAC(**salt**+ '\n' +**productId**+ '\n' +**userId**)
     > 
     > 
   * Compare o hash calculado acima ao valor do parâmetro de consulta **sig**. Se os dois hashes forem correspondentes, prossiga para a próxima etapa. Caso contrário, recuse as solicitações.
3. Processar a assinatura do produto com base no tipo de operação solicitada em **operação** - por exemplo, faturamento, mais perguntas, etc.
4. Ao inscrever o usuário com sucesso no produto do seu lado, inscreva o usuário no produto API [Management, chamando a API REST para assinaturas].

## <a name="example-code"></a><a name="delegate-example-code"> </a> Código de exemplo

Estas amostras de código mostram como:

* Pegue a chave de validação da *delegação,* que está definida na tela delegação do portal do editor
* Crie um HMAC, que é então usado para validar a assinatura, comprovando a validade da Url retornada aprovada.

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
> Você precisa [republicar o portal do desenvolvedor](api-management-howto-developer-portal-customize.md#publish) para que as mudanças de delegação entrem em vigor.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre delegação, consulte o vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[solicite um token de logon único (SSO)]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[Crie um usuário]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[chamando a API REST para assinaturas]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[código de exemplo fornecido abaixo]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
