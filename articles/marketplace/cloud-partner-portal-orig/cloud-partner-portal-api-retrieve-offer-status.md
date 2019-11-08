---
title: Recuperar status da oferta | Azure Marketplace
description: API recupera o status atual da oferta.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819657"
---
<a name="retrieve-offer-status"></a>Recuperar status da oferta 
=====================

Recupera o status atual da oferta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**       |   **Descrição**                            |  **Tipo de dados** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificador do publicador, por exemplo `Contoso`  |     Cadeia de caracteres     |
|  offerId        | GUID que identifica exclusivamente a oferta      |     Cadeia de caracteres     |
|  api-version    | Última versão da API                        |     Data       |
|  |  |


<a name="header"></a>Cabeçalho
------

|  Nome           |  Valor               |
|  -------------  | -------------------  |
|  Tipo de conteúdo   |  `application/json`  |
|  Autorização  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Resposta

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**             |    **Descrição**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | O status da oferta. Para a lista de valores possíveis, consulte [Status da oferta](#offer-status) abaixo. |
|  mensagens             | Matriz de mensagens associadas à oferta                                                    |
|  etapas                | Matriz das etapas que a oferta passa durante a publicação uma oferta                      |
|  estimatedTimeFrame   | Estimativa do tempo que levaria para concluir essa etapa, em formato amigável                       |
|  ID                   | Identificador da etapa                                                                         |
|  stepName             | Nome da etapa                                                                               |
|  Descrição          | Descrição da etapa                                                                        |
|  status               | Status da etapa. Para a lista de valores possíveis, consulte [Status da etapa](#step-status) abaixo.    |
|  mensagens             | Matriz de mensagens relacionadas à etapa                                                          |
|  processPercentage    | Porcentagem de conclusão da etapa                                                              |
|  previewLinks         | *Não implementado atualmente*                                                                    |
|  liveLinks            | *Não implementado atualmente*                                                                    |
|  notificationEmails   | Lista separada por vírgula de endereços de email para ser notificado sobre o andamento da operação        |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |   **Descrição**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` - A solicitação foi processada com êxito e o status atual da oferta foi retornado. |
|  400     | `Bad/Malformed request` - O corpo da resposta de erro pode conter mais informações.                 |
|  404     | `Not found` - A entidade especificada não existe.                                                |
|  |  |


### <a name="offer-status"></a>Status da oferta

|  **Nome**                    |    **Descrição**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | A oferta nunca foi publicada.                          |
|  NotStarted                  | A oferta é nova e não iniciada.                            |
|  WaitingForPublisherReview   | A oferta aguarda aprovação do editor.                 |
|  Executando                     | O envio da oferta está sendo processado.                     |
|  Bem-sucedido                   | O envio da oferta concluiu o processamento.               |
|  Cancelado                    | O envio da oferta foi cancelado.                           |
|  Falha                      | O envio da oferta falhou.                                 |
|  |  |


### <a name="step-status"></a>Status da etapa

|  **Nome**                    |    **Descrição**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | A etapa não iniciou.                        |
|  InProgress                  | Etapa em execução.                             |
|  WaitingForPublisherReview   | Etapa aguardando aprovação do publicador.      |
|  WaitingForApproval          | Etapa aguardando aprovação do processo.        |
|  Bloqueado                     | O etapa está bloqueada.                             |
|  Rejeitado                    | A etapa é rejeitada.                            |
|  Concluído                    | A etapa está concluída.                            |
|  Cancelado                    | A etapa foi cancelada.                           |
|  |  |
