---
title: Códigos de erro da API REST - Azure Key Vault
description: Esses códigos de erro podem ser retornados por uma operação em um serviço web do Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 01fb5393217834bc0196da25c4a56314ca7eae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294547"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Códigos de erro da API do Cofre da Chave Azure
 
Os seguintes códigos de erro podem ser retornados por uma operação em um serviço web do Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: Solicitação não autenticada

401 significa que a solicitação não é autenticada para Key Vault. 

Uma solicitação é autenticada se:

- O cofre da chave sabe a identidade do chamador; E
- O chamador pode tentar acessar os recursos do Key Vault. 

Existem várias razões diferentes para que um pedido possa retornar 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nenhum token de autenticação anexado à solicitação. 

Aqui está um exemplo de solicitação PUT, definindo o valor de um segredo:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

O cabeçalho "Autorização" é o token de acesso que é necessário a cada chamada ao Key Vault para operações de data-plane. Se o cabeçalho estiver faltando, então a resposta deve ser 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>O token não tem o recurso correto associado a ele. 

Ao solicitar um token de acesso do ponto final do Azure OAUTH, um parâmetro chamado "recurso" é obrigatório. O valor é importante para o provedor de tokens porque ele escopo do token para o seu uso pretendido. O recurso para **todos os** tokens acessarem um Key Vault é *https:\//vault.keyvault.net* (sem barra de arrasto).

### <a name="the-token-is-expired"></a>O token está expirado

Os tokens são codificados base64 e os valores podem [http://jwt.calebb.net](http://jwt.calebb.net)ser decodificados em sites como . Aqui está o token decodificado acima:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Podemos ver muitas partes importantes neste token:

- aud (audiência): O recurso do token. Note que <https://vault.azure.net>isso é. Este token NÃO funcionará para qualquer recurso que não corresponda explicitamente a esse valor, como gráfico.
- iat (emitido em): O número de carrapatos desde o início da época quando o token foi emitido.
- nbf (não antes): O número de carrapatos desde o início da época quando este token se torna válido.
- exp (expiração): O número de carrapatos desde o início da época quando este token expira.
- appid (ID de aplicativo): O GUID para o ID do aplicativo que faz essa solicitação.
- tid (ID do inquilino): O GUID para o ID do inquilino do principal fazendo esta solicitação

É importante que todos os valores sejam devidamente identificados no token para que a solicitação funcione. Se tudo estiver correto, então a solicitação não resultará em 401.

### <a name="troubleshooting-401"></a>Solução de problemas 401

401s devem ser investigados a partir do ponto de geração de token, antes que a solicitação seja feita para o cofre-chave. Geralmente o código está sendo usado para solicitar o token. Uma vez que o token é recebido, ele é passado para a solicitação do Cofre de Chaves. Se o código estiver sendo executado localmente, você pode `https://login.microsoftonline.com`usar o Fiddler para capturar a solicitação/resposta a . Um pedido é assim:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

As seguintes informações fornecidas pelo usuário estão corretas:

- O iD do inquilino do cofre chave
- Valor de recurso definido como https%3A%2F%2Fvault.azure.net (URL codificado)
- ID do Cliente
- Segredo do cliente

Certifique-se de que o resto da solicitação é quase idêntica.

Se você só conseguir obter o token de acesso de resposta, você pode decodificá-lo (como mostrado acima) para garantir o ID do inquilino, o ID do cliente (ID do aplicativo) e o recurso.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: Permissões insuficientes

HTTP 403 significa que a solicitação foi autenticada (ela conhece a identidade solicitante), mas a identidade não tem permissão para acessar o recurso solicitado. Há duas causas:

- Não há política de acesso para a identidade.
- O endereço IP do recurso solicitante não está listado em branco nas configurações de firewall do cofre de chaves.

O HTTP 403 geralmente ocorre quando o aplicativo do cliente não está usando o ID do cliente que o cliente acha que é. Isso geralmente significa que as políticas de acesso não estão configuradas corretamente para a identidade real de chamada.

### <a name="troubleshooting-403"></a>Solução de problemas 403

Primeiro, ligue o registro. Para obter instruções sobre como fazê-lo, consulte [o registro do Azure Key Vault](key-vault-logging.md).

Uma vez que o registro é ligado, você pode determinar se o 403 é devido à política de acesso ou política de firewall.

#### <a name="error-due-to-firewall-policy"></a>Erro devido à política de firewall

"O endereço do cliente (00.00.00.00) não está autorizado e o chamador não é um serviço confiável"

Há uma lista limitada de "Azure Trusted Services". Os sites do Azure **não** são um serviço azure confiável. Para obter mais informações, consulte o blog post [Key Vault Firewall access by Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Você deve adicionar o endereço IP do Site do Azure ao Cofre de Chaves para que ele funcione.

Se devido à política de acesso: encontre o ID do objeto para a solicitação e certifique-se de que o ID do objeto corresponda ao objeto ao qual o usuário está tentando atribuir a política de acesso. Muitas vezes haverá vários objetos no AAD que têm o mesmo nome, por isso escolher o correto é muito importante. Excluindo e readicionando a política de acesso, é possível ver se existem vários objetos com o mesmo nome.

Além disso, a maioria das políticas de acesso não requer o uso do "aplicativo autorizado", como mostrado no portal. O aplicativo autorizado é usado para cenários de autenticação "em nome", que são raros. 


## <a name="http-429-too-many-requests"></a>HTTP 429: Muitos pedidos

O estrangulamento ocorre quando o número de solicitações excede o máximo indicado para o período de tempo. Se ocorrer estrangulamento, a resposta do Cofre de Chaves será HTTP 429. Existem máximas indicadas para tipos de solicitações feitas. Por exemplo: a criação de uma chave HSM 2048 bits é de 5 solicitações por 10 segundos, mas todas as outras transações HSM têm um limite de 1000 solicitações/10 segundos. Por isso, é importante entender quais tipos de chamadas estão sendo feitas ao determinar a causa do estrangulamento.
Em geral, as solicitações para o Key Vault são limitadas a 2000 solicitações/10 segundos. Exceções são operações-chave, como documentado nos [limites de serviço do Key Vault](key-vault-service-limits.md)

### <a name="troubleshooting-429"></a>Solução de problemas 429
O estrangulamento é trabalhado em torno de usar essas técnicas:

- Reduza o número de solicitações feitas ao Key Vault, determinando se há padrões para um recurso solicitado e tentando calá-las no aplicativo de chamada. 

- Quando ocorrer o estrangulamento do Key Vault, adapte o código solicitante para usar um backoff exponencial para tentar novamente. O algoritmo é explicado aqui: [Como estrangular seu aplicativo](key-vault-ovw-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se o número de solicitações não puder ser reduzido por cache e o backoff cronometrado não funcionar, então considere dividir as chaves em vários cofres de chaves. O limite de serviço para uma única assinatura é de 5x o limite individual do Key Vault. Se usar mais de 5 Cofres-chave, deve-se considerar o uso de várias assinaturas. 

Orientação detalhada, incluindo solicitação para aumentar limites, pode ser encontrado aqui: [Orientação de estrangulamento do Key Vault](key-vault-ovw-throttling.md)


