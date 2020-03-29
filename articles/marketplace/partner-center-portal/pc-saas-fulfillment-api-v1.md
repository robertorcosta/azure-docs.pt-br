---
title: SaaS Fulfillment APIs v1 | Mercado Azure
description: Explica como criar e gerenciar uma oferta SaaS no Azure Marketplace usando as APIs de Fulfillment v1 associadas.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 05/23/2019
ms.author: dsindona
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec8373288a2ea5809ee5d349c52c57051586035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288335"
---
# <a name="saas-fulfillment-apis-version-1-deprecated"></a>SaaS Fulfillment APIs versão 1 (preterida)

Este artigo explica como criar uma oferta de SaaS com APIs. As APIs, compostas por métodos REST e endpoints, são necessárias para permitir assinaturas à sua oferta SaaS se você tiver o Sell através do Azure selecionado.  

> [!WARNING]
> Esta versão inicial da API SaaS Fulfillment é depreciada; em vez disso, use [a API V2 de cumprimento do SaaS](./pc-saas-fulfillment-api-v2.md).  Esta versão inicial da API está sendo mantida atualmente apenas para atender os editores existentes. 

As APIs a seguir são fornecidas para ajudá-lo a integrar o seu serviço SaaS ao Azure:

-   Resolver
-   Assinar
-   Converter
-   Cancelar assinatura


## <a name="api-methods-and-endpoints"></a>Métodos e pontos finais da API

As seções a seguir descrevem os métodos de API e os pontos de extremidade disponíveis para habilitar assinaturas para uma oferta de SaaS.


### <a name="marketplace-api-endpoint-and-api-version"></a>Ponto de extremidade de API e a versão de API do Marketplace

O ponto de extremidade para a API do Azure Marketplace é `https://marketplaceapi.microsoft.com`.

A versão atual da API é `api-version=2017-04-15`.


### <a name="resolve-subscription"></a>Resolver assinatura

A ação POST ao resolver o ponto de extremidade permite que os usuários resolvam um token do marketplace para uma ID de recurso persistente.  A ID de recurso é o identificador exclusivo para a assinatura de SAAS. 

Quando um usuário é redirecionado para o site de um ISV, a URL contém um token nos parâmetros de consulta. O ISV deve usar esse token e fazer uma solicitação para resolvê-lo. A resposta contém a ID de assinatura exclusiva de SAAS, nome, ID de oferta e o plano para o recurso. Esse token é válido por apenas uma hora.

*Solicitação*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **Nome do parâmetro** |     **Descrição**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  A versão da operação a ser usada para esta solicitação.   |
|  |  |


*Cabeçalhos*

| **Tecla de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Este campo correlaciona todos os eventos da operação do cliente com eventos do lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| Content-type       | Sim          | `application/json`                                        |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
| x-ms-marketplace-token| Sim| O parâmetro de consulta de token na URL quando o usuário é redirecionado para o site do SaaS ISV do Azure. **Nota:** Este token só é válido por 1 hora. Realize também a decodificação URL do valor do token do navegador antes de usá-lo.|
|  |  |  |
  

*Corpo de Resposta*

``` json
{
    "id": "",
    "subscriptionName": "",
    "offerId": "",
    "planId": "",
}
```

| **Nome do parâmetro** | **Tipo de dados** | **Descrição**                       |
|--------------------|---------------|---------------------------------------|
| id                 | String        | A ID da assinatura de SaaS.          |
| subscriptionName| String| Nome da assinatura de SaaS definida pelo usuário no Azure ao assinar o serviço SaaS.|
| OfferId            | String        | ID da oferta que o usuário assinou. |
| planId             | String        | ID do plano que o usuário assinou.  |
|  |  |  |


*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | Token resolvido com êxito.                                                            |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. Falha ao resolver o token porque ou ele está malformado ou expirou (o token só é válido por 1 hora depois de gerado). |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                                 |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                                |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                                        |
|  |  |  |


*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Este ID é usado para quaisquer reconciliações. |
| Retry-After        | Não           | Esse valor é definido somente para uma resposta 429.                                                                   |
|  |  |  |


### <a name="subscribe"></a>Assinar

O ponto de extremidade do assinante permite aos usuários iniciar uma assinatura de um serviço SaaS para um plano específico e habilitar a cobrança no sistema de comércio.

**Colocar**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | ID exclusivo da assinatura SaaS que é obtido após a resolução do token via Resolve API.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

|  **Tecla de cabeçalho**        | **Obrigatório** |  **Descrição**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   Não         | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| x-ms-correlationid     |   Não         | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match |   Não         |   Valor de ETag de validador forte.                                                          |
| content-type           |   Sim        |    `application/json`                                                                   |
|  autorização         |   Sim        |    O token de portador JWT (Token Web JSON).                                               |
| x-ms-marketplace-session-mode| Não | Sinalizador para habilitar o modo de simulação ao assinar uma oferta de SaaS. Se definido, a assinatura não será cobrada. Isso é útil para cenários de teste de ISV. Por favor, coloque-o **como 'dryrun'**|
|  |  |  |

*Corpo*

``` json
{
    "lanId": "",
}
```

| **Nome do elemento** | **Tipo de dados** | **Descrição**                      |
|------------------|---------------|--------------------------------------|
| planId           | Cadeia de caracteres (obrigatória)        | O ID do plano do usuário do serviço SaaS está se subscrevendo.  |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta 202, acompanhe o status da operação de solicitação no cabeçalho 'Operação-localização'. A autenticação é a mesma que a de outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Esse valor é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>Ponto de extremidade de alteração de plano

O ponto de extremidade de alteração permite que o usuário converta seu plano inscrito atualmente em um novo plano.

**Patch**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Tecla de cabeçalho**          | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid      | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| If-Match/If-None-Match | Não           | Valor de ETag de validador forte.                              |
| content-type            | Sim          | `application/json`                                        |
| autorização           | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Corpo*

```json
{
    "planId": ""
}
```

|  **Nome do elemento** |  **Tipo de dados**  | **Descrição**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  Cadeia de caracteres (obrigatória)         | O ID do plano do usuário do serviço SaaS está se subscrevendo.          |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 409                  | `Conflict`           | Outra operação está em andamento na assinatura.                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                          |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, esse valor é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Esse valor é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>Excluir assinatura

A ação de exclusão no ponto de extremidade de assinatura permite que um usuário exclua uma assinatura com uma ID especificada.

*Solicitação*

**Excluir**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Tecla de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | Ativação da assinatura de SaaS recebida para um plano específico.                   |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão faltando ou então o corpo do JSON está malformado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                   |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                  |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                  |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente. Tente novamente mais tarde.                          |
|  |  |  |

Para uma resposta 202, acompanhe o status da operação de solicitação no cabeçalho 'Operação-localização'. A autenticação é a mesma que a de outras APIs do Marketplace.

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
| Operation-Location | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>Obter status da operação

Esse ponto de extremidade permite que o usuário acompanhe o status de uma operação assíncrona disparada (assinar/cancelar assinatura/alterar plano).

*Solicitação*

**Obter**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | ID exclusiva para a operação disparada.                |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Tecla de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.   |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.  |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  | 

*Corpo de Resposta*

```json
{
    "id": "",
    "status": "",
    "resourceLocation": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro** | **Tipo de dados** | **Descrição**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | String        | Identificador da operação.                                                                      |
| status             | Enum          | Status da operação, um dos seguintes: `In Progress`, `Succeeded` ou `Failed`.          |
| resourceLocation   | String        | Link para a assinatura que foi criada ou modificada. Isso ajuda o cliente a obter o estado atualizado após a operação. Esse valor não é definido para operações `Unsubscribe`. |
| criado            | Datetime      | Hora de criação de operação em UTC.                                                           |
| lastModified       | Datetime      | Última atualização da operação em UTC.                                                      |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | Assinatura não encontrada com o ID dado.                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Sim          | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="get-subscription"></a>Obter assinatura

A ação Get no ponto de extremidade da assinatura permite que um usuário recupere uma assinatura com um identificador de recurso especificado.

*Solicitação*

**Obter**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | A ID da assinatura de SaaS.                              |
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Tecla de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente, preferencialmente um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.                                                           |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                                                                    |
|  |  |  |

*Corpo de Resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | ID do recurso de assinatura de SaaS no Azure.    |
| offerId                | String        | ID da oferta que o usuário assinou.         |
| planId                 | String        | ID do plano que o usuário assinou.          |
| saasSubscriptionName   | String        | O nome da assinatura de SaaS.                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas ele não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`: a assinatura do Azure está suspensa.  |
| criado                | Datetime      | Valor do carimbo de data/hora de criação da assinatura em UTC. |
| lastModified           | Datetime      | Valor do carimbo de data/hora de modificação da assinatura em UTC. |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente, tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Não           | Intervalo com o qual o cliente pode verificar o status.                                                       |
| eTag               | Sim          | Vincule a um recurso para obter o status da operação.                                                        |
|  |  |  |

### <a name="get-subscriptions"></a>Obter assinaturas

A ação Get no ponto de extremidade de assinaturas permite que um usuário recupere todas as assinaturas para todas as ofertas do ISV.

*Solicitação*

**Obter**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **Nome do parâmetro**  | **Descrição**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | A versão da operação a ser usada para esta solicitação. |
|  |  |

*Cabeçalhos*

| **Tecla de cabeçalho**     | **Obrigatório** | **Descrição**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | Não           | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do cliente. Recomende um GUID. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta.             |
| x-ms-correlationid | Não           | Um valor de cadeia de caracteres exclusiva para a operação no cliente. Esse valor é para correlacionar todos os eventos da operação de cliente com eventos no lado do servidor. Se esse valor não for fornecido, um será gerado e fornecido nos cabeçalhos de resposta. |
| autorização      | Sim          | O token de portador JWT (Token Web JSON).                    |
|  |  |  |

*Corpo de Resposta*

```json
{
    "id": "",
    "saasSubscriptionName": "",
    "offerId": "",
    "planId": "",
    "saasSubscriptionStatus": "",
    "created": "",
    "lastModified": ""
}
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | String        | ID do recurso de assinatura SaaS no Azure    |
| offerId                | String        | Ofereça ID que o usuário se inscreveu         |
| planId                 | String        | Plan ID que o usuário assinou          |
| saasSubscriptionName   | String        | Nome da assinatura saaS                |
| saasSubscriptionStatus | Enum          | Status da operação.  Um dos seguintes:  <br/> - `Subscribed`: a assinatura está ativa.  <br/> - `Pending`: o usuário cria o recurso, mas ele não é ativado pelo ISV.   <br/> - `Unsubscribed`: o usuário cancelou a assinatura.   <br/> - `Suspended`: o usuário suspendeu a assinatura.   <br/> - `Deactivated`: a assinatura do Azure está suspensa.  |
| criado                | Datetime      | Valor de carimbo de marca-tempo de criação de assinatura no UTC |
| lastModified           | Datetime      | Valor de carimbo de data hora modificado da assinatura no UTC |
|  |  |  |

*Códigos de resposta*

| **Código de status HTTP** | **Código de erro**     | **Descrição**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | A solicitação get foi resolvida com êxito e o corpo contém a resposta.    |
| 400                  | `BadRequest`         | Os cabeçalhos necessários estão ausentes ou um valor inválido de api-version foi especificado. |
| 403                  | `Forbidden`          | O chamador não está autorizado a realizar esta operação.                      |
| 404                  | `NotFound`           | A assinatura não foi encontrada com a ID especificada                                     |
| 429                  | `RequestThrottleId`  | O serviço está ocupado processando solicitações, tente novamente mais tarde.                     |
| 503                  | `ServiceUnavailable` | Serviço inativo temporariamente. Tente novamente mais tarde.                             |
|  |  |  |

*Cabeçalhos de resposta*

| **Chave de cabeçalho**     | **Obrigatório** | **Descrição**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | Sim          | O valor da ID de solicitação recebido do cliente.                                                                   |
| x-ms-correlationid | Sim          | A ID de correlação se passada pelo cliente. Caso contrário, é a ID de correlação do servidor.                   |
| x-ms-activityid    | Sim          | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações. |
| Retry-After        | Não           | Intervalo com o qual o cliente pode verificar o status.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>Webhook de SaaS

Um webhook de SaaS é usado para notificar sobre alterações de forma proativa para o serviço de SaaS. Essa API de POSTAGEM deve ser autenticado e será chamado pelo serviço da Microsoft. O serviço de SaaS deve chamar as operações API para validar e autorizar antes de fazer a notificação de webhook. 

*Corpo*

``` json
  {
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "action": "Subscribe", // Subscribe/Unsubscribe/ChangePlan
    "operationRequestSource":"Azure",
    "timeStamp":"2018-12-01T00:00:00"
  }
```

| **Nome do parâmetro**     | **Tipo de dados** | **Descrição**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | String       | ID exclusiva para a operação disparada.                |
| activityId   | String        | Um valor de cadeia de caracteres exclusivo para acompanhamento da solicitação do serviço. Isso é usado para quaisquer eventuais reconciliações.               |
| subscriptionId                     | String        | ID do recurso de assinatura de SaaS no Azure.    |
| offerId                | String        | ID da oferta que o usuário assinou. Fornecido somente com a ação "Atualizar".        |
| publisherId                | String        | ID do editor da oferta de SaaS         |
| planId                 | String        | ID do plano que o usuário assinou. Fornecido somente com a ação "Atualizar".          |
| ação                 | String        | A ação que está disparando esta notificação. Os valores possíveis: ativar, excluir, suspender, restabelecer, atualizar          |
| timeStamp                 | String        | Valor de carimbo de hora em UTC quando essa notificação foi disparada.          |
|  |  |  |


## <a name="next-steps"></a>Próximas etapas

Os desenvolvedores também podem recuperar e manipular programaticamente cargas de trabalho, ofertas e perfis de editores usando as [APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)do Cloud Partner Portal REST .
