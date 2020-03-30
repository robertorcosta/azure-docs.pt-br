---
title: SaaS Fulfillment API v2 | Mercado Azure
description: Este artigo explica como criar e gerenciar uma oferta SaaS no AppSource e no Azure Marketplace usando as APIs de preenchimento v2 associadas.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275757"
---
# <a name="saas-fulfillment-apis-version-2"></a>APIs de cumprimento de SaaS, versão 2 

Este artigo detalha as APIs que permitem aos parceiros vender seus aplicativos SaaS no mercado AppSource e no Azure Marketplace. Essas APIs são um requisito para ofertas SaaS transacíveis no AppSource e no Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gerenciamento do ciclo de vida da assinatura SaaS

O Azure SaaS gerencia todo o ciclo de vida de uma compra de assinatura SaaS. Ele usa as APIs de preenchimento como um mecanismo para impulsionar o cumprimento real, mudanças nos planos e exclusão da assinatura com o parceiro. A conta do cliente é baseada no estado da assinatura SaaS que a Microsoft mantém. O diagrama a seguir mostra os estados e as operações que impulsionam as mudanças entre estados.

![Estados do ciclo de vida da assinatura SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Estados de uma assinatura SaaS

A tabela a seguir lista os estados de provisionamento para uma assinatura SaaS, incluindo uma descrição e diagrama de seqüência para cada um (se aplicável). 

#### <a name="provisioning"></a>Provisionamento

Quando um cliente inicia uma compra, o parceiro recebe essas informações em um código de autorização em uma página web interativa do cliente que usa um parâmetro url. Um exemplo `https://contoso.com/signup?token=..`é, enquanto a URL `https://contoso.com/signup`da página de aterrissagem no Partner Center é . O código de autorização pode ser validado e trocado pelos detalhes do serviço de provisionamento ligando para a API Resolver.  Quando um serviço SaaS termina o provisionamento, ele envia uma chamada ativa para sinalizar que o cumprimento está concluído e que o cliente pode ser cobrado. 

O diagrama a seguir mostra a seqüência de chamadas de API para um cenário de provisionamento.  

![API pede o fornecimento de um serviço SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Provisionado

Este estado é o estado estável de um serviço prestado.

##### <a name="provisioning-for-update"></a>Provisionamento para atualização 

Este estado significa que uma atualização para um serviço existente está pendente. Tal atualização pode ser iniciada pelo cliente, seja no marketplace ou no serviço SaaS (apenas para transações diretas ao cliente).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Provisionamento para atualização (quando é iniciado a partir do marketplace)

O diagrama a seguir mostra a seqüência de ações quando uma atualização é iniciada a partir do mercado.

![A API chama quando a atualização é iniciada a partir do mercado](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Provisionamento para atualização (quando é iniciado a partir do serviço SaaS)

O diagrama a seguir mostra as ações quando uma atualização é iniciada a partir do serviço SaaS. (A chamada webhook é substituída por uma atualização da assinatura iniciada pelo serviço SaaS.) 

![A API chama quando a atualização é iniciada a partir do serviço SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspenso

Este estado indica que o pagamento de um cliente não foi recebido. Por política, forneceremos ao cliente um período de carência antes de cancelar a assinatura. Quando uma assinatura está neste estado: 

- Como parceiro, você pode optar por degradar ou bloquear o acesso do usuário ao serviço.
- A assinatura deve ser mantida em um estado recuperável que pode restaurar a funcionalidade completa sem qualquer perda de dados ou configurações. 
- Espere obter uma solicitação de reintegração para esta assinatura através das APIs de preenchimento ou de uma solicitação de desprovisionamento no final do período de carência. 

#### <a name="unsubscribed"></a>Não inscrito 

As assinaturas chegam a este estado em resposta a uma solicitação explícita do cliente ou ao não pagamento de dívidas. A expectativa do parceiro é que os dados do cliente sejam retidos para recuperação a pedido por um determinado número de dias e, em seguida, excluídos. 


## <a name="api-reference"></a>Reference API

Esta seção documenta a *API de assinatura* saas e a *API de operações*.  O valor `api-version` do parâmetro para APIs `2018-08-31`versão 2 é .  


### <a name="parameter-and-entity-definitions"></a>Definições de parâmetros e entidades

A tabela a seguir lista as definições para parâmetros comuns e entidades usadas pelas APIs de cumprimento.

|     Entidade/Parâmetro     |     Definição                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | O identificador GUID para um recurso SaaS.  |
| `name`                   | Um nome amigável fornecido para este recurso pelo cliente. |
| `publisherId`            | Um identificador de seqüência único para cada editor (por exemplo: "contoso"). |
| `offerId`                | Um identificador de string único para cada oferta (por exemplo: "offer1").  |
| `planId`                 | Um identificador de seqüência exclusivo para cada plano/SKU (por exemplo: "prata"). |
| `operationId`            | O identificador GUID para uma operação específica.  |
|  `action`                | A ação que está sendo `Unsubscribe` `Suspend`realizada `Reinstate`em `ChangePlan` `ChangeQuantity`um `Transfer`recurso, ou , ou , ou , . . |
|   |   |

Os identificadores globalmente únicos[(GUIDs)](https://en.wikipedia.org/wiki/Universally_unique_identifier)são números de 128 bits (32 hexadecimal) que são normalmente gerados automaticamente. 

#### <a name="resolve-a-subscription"></a>Resolva uma assinatura 

O ponto final de resolução permite que o editor resolva um token de marketplace para um ID de recurso persistente. O ID de recurso é o identificador exclusivo para uma assinatura SaaS. Quando um usuário é redirecionado para o site de um parceiro, a URL contém um token nos parâmetros de consulta. Espera-se que o parceiro use esse token e faça uma solicitação para resolvê-lo. A resposta contém o ID de assinatura saas exclusivo, nome, id de oferta e plano para o recurso. Este token é válido por apenas uma hora. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Postar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |

*Solicitar cabeçalhos:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Por exemplo:`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  O parâmetro de consulta de token na URL quando o usuário é redirecionado para o site `https://contoso.com/signup?token=..`do parceiro SaaS do Azure (por exemplo: ). *Nota:* A URL decodifica o valor do token do navegador antes de usá-lo.  |

*Códigos de resposta:*

Código: 200<br>
Resolve o token opaco para uma assinatura SaaS. Corpo de resposta:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Código: 400<br>
Solicitação inválida. x-ms-marketplace-token está faltando, malformado ou expirado.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API de assinatura

A API de assinatura suporta as seguintes operações HTTPS: **Get**, **Post**, **Patch**e **Delete**.


#### <a name="list-subscriptions"></a>Listar assinaturas

Lista todas as assinaturas do SaaS para um editor.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Obter<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  A versão da operação a ser usada para esta solicitação.  |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
| Tipo de conteúdo       |  `application/json`  |
| x-ms-requestid     |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| autorização      |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Por exemplo:`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200 <br/>
Obtém o editor e assinaturas correspondentes para todas as ofertas do editor, com base no token de autenticação.

>[!Note]
>[ApIs falsas](#mock-apis) são usadas quando você desenvolve a oferta pela primeira vez, enquanto APIs reais precisam ser usadas ao publicar a oferta.  APIs reais e APIs mock diferem pela primeira linha do código.  Na API real há `subscription` a seção, enquanto esta seção não existe para a API simulada.

Carga de resposta para API simulada:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
E para uma API de verdade: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
O token de continuação só estará presente se houver "páginas" adicionais de planos para recuperar. 

Código: 403 <br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual. 

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Obtenha assinatura

Obtém a assinatura SaaS especificada. Use esta chamada para obter informações sobre licenças e informações do plano.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token via API Resolve.   |
|  ApiVersion        |   A versão da operação a ser usada para esta solicitação.   |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      |  `application/json`  |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Por exemplo:`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br>
Obtém a assinatura SaaS do identificador. Carga de resposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.<br> 

Código: 500<br>
Erro interno do servidor.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Listar planos disponíveis

Use esta chamada para descobrir se há alguma oferta privada ou pública para o editor atual.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   A versão da operação a ser usada para esta solicitação.  |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid  | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`". |

*Códigos de resposta:*

Código: 200<br>
Recebe uma lista de planos disponíveis para um cliente. Corpo de resposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Código: 404<br>
Não encontrado.<br> 

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual. <br> 

Código: 500<br>
Erro interno do servidor.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Ativar uma assinatura

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Postar<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |

*Solicitar cabeçalhos:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json`  |
|  x-ms-requestid    | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esta seqüência correlaciona todos os eventos da operação do cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`". |

*Solicitar carga útil:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Códigos de resposta:*

Código: 200<br>
Ativa a assinatura.<br>

Código: 400<br>
Má solicitação: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Mude o plano da assinatura

Atualize o plano da assinatura.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| autorização      |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | O link para um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
O pedido de mudança de plano foi aceito. Espera-se que o parceiro faça uma pesquisa no Local da Operação para determinar um sucesso ou falha. <br>

Código: 400<br>
Má solicitação: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Apenas um plano ou quantidade pode ser corrigido de uma só vez, não ambos. Edições em uma **Update** assinatura com Update `allowedCustomerOperations`não está em .

#### <a name="change-the-quantity-on-the-subscription"></a>Alterar a quantidade na assinatura

Atualize a quantidade na assinatura.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      | `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.    |
| autorização      |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
Request Body:
{
    "quantity": 5
}
```

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Vincule a um recurso para obter o status da operação.   |

*Códigos de resposta:*

Código: 202<br>
O pedido de alteração da quantidade foi aceito. Espera-se que o parceiro faça uma pesquisa no Local da Operação para determinar um sucesso ou falha. <br>

Código: 400<br>
Má solicitação: falhas de validação.


Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>Apenas um plano ou quantidade pode ser corrigido de uma só vez, não ambos. Edições em uma **Update** assinatura com Update `allowedCustomerOperations`não está em .

#### <a name="delete-a-subscription"></a>Excluir uma assinatura

Cancelar a assinatura e excluir a assinatura especificada.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Excluir<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |

*Solicitar cabeçalhos:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  x-ms-correlationid  |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 202<br>
O parceiro iniciou uma chamada para cancelar a assinatura do SaaS.<br>

Código: 400<br>
Excluir em uma **Delete** assinatura `allowedCustomerOperations`com Excluir não em .

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API de operações

A API de operações suporta as seguintes operações de Patch e Get.

#### <a name="list-outstanding-operations"></a>Liste operações pendentes 

Lista as operações pendentes para o editor atual. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   A versão da operação a ser usada para esta solicitação.                |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |

*Solicitar cabeçalhos:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     |  `application/json` |
|  x-ms-requestid    |  Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`".  |

*Códigos de resposta:*

Código: 200<br> Recebe a lista de operações pendentes em uma assinatura. Carga de resposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Código: 400<br>
Má solicitação: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 500<br>
Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Obter status de operação

Permite que o editor rastreie o status da operação `Subscribe` `Unsubscribe`assincronia deflagrada especificada (como, ou `ChangePlan` `ChangeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Obter<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  A versão da operação a ser usada para esta solicitação.  |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Tipo de conteúdo      |  `application/json`   |
|  x-ms-requestid    |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app) Por exemplo:`Bearer <access_token>`".  |

*Códigos de resposta:*<br>

Código: 200<br> Obtém a operação SaaS pendente especificada. Carga de resposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Código: 400<br>
Má solicitação: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.
 
Código: 404<br>
Não encontrado.

Código: 500<br> Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Atualize o status de uma operação

Atualize o status de uma operação para indicar sucesso ou falha com os valores fornecidos.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parâmetros de consulta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  A versão da operação a ser usada para esta solicitação.  |
| subscriptionId     | Um identificador exclusivo da assinatura SaaS que é obtido após a resolução do token usando a API Resolver.  |
|  operationId       | A operação que está sendo concluída. |

*Solicitar cabeçalhos:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Tipo de conteúdo     | `application/json`   |
|   x-ms-requestid   |   Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  x-ms-correlationid |  Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este parâmetro correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
|  autorização     |  [Obtenha o token do portador do token web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)  Por exemplo:`Bearer <access_token>`".  |

*Solicitar carga útil:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Códigos de resposta:*

Código: 200<br> Uma chamada para informar a conclusão de uma operação do lado do parceiro. Por exemplo, essa resposta poderia sinalizar a mudança de assentos ou planos.

Código: 400<br>
Má solicitação: falhas de validação.

Código: 403<br>
Não autorizado. O token de autenticação não foi fornecido ou é inválido, ou a solicitação está tentando acessar uma aquisição que não pertence ao editor atual.

Código: 404<br>
Não encontrado.

Código: 409<br>
Conflito. Por exemplo, uma transação mais nova já está cumprida.

Código: 500<br> Erro interno do servidor.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementando um webhook no serviço SaaS

O editor deve implementar um webhook neste serviço SaaS para notificar proativamente os usuários de alterações em seu serviço. Espera-se que o serviço SaaS ligue para a API de operações para validar e autorizar antes de tomar uma ação sobre a notificação do webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Onde a ação pode ser uma das seguintes: 
- `Unsubscribe`(quando o recurso foi excluído)
- `ChangePlan`(quando a operação do plano de mudança estiver concluída)
- `ChangeQuantity`(quando a operação de quantidade de alteração estiver concluída)
- `Suspend`(quando o recurso foi suspenso)
- `Reinstate`(quando o recurso foi restabelecido após a suspensão)

Onde o status pode ser um dos seguintes: 
- **NotStarted** <br>
 - **Inprogress** <br>
- **Conseguiu** <br>
- **Falha** <br>
- **Conflito** <br>

Em uma notificação de webhook, os status acionáveis são **bem sucedidos** e **falhos**. O ciclo de vida de uma operação é de **Não Iniciado** para um estado terminal como **Sucesso,** **Falha**ou **Conflito**. Se você receber **NotStarted** ou **InProgress,** continue a solicitar o status via API GET até que a operação atinja um estado terminal antes de agir. 

## <a name="mock-apis"></a>APIs simuladas

Você pode usar nossas APIs falsas para ajudá-lo a começar com o desenvolvimento, particularmente prototipagem, bem como projetos de teste. 

Ponto final `https://marketplaceapi.microsoft.com/api` do host: (sem necessidade de autenticação)<br/>
Versão aPI:`2018-09-15`<br/>
Uri de amostra:`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

Os caminhos de ponto final da API são os mesmos em APIs falsas e reais, mas as versões da API são diferentes. A versão `2018-09-15` é para `2018-08-31` a versão simulada e para a versão de produção. 

Qualquer uma das chamadas da API neste artigo pode ser feita para o ponto final do host simulado. Em geral, espere obter dados falsos de volta como uma resposta. Chamadas para os métodos de assinatura de atualização na API simulada sempre retornam 500. 

## <a name="next-steps"></a>Próximas etapas

Os desenvolvedores também podem recuperar e manipular programaticamente cargas de trabalho, ofertas e perfis de editores usando as [APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)do Cloud Partner Portal REST .
