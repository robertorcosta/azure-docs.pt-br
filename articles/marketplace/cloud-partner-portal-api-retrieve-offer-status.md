---
title: Recuperar o status da oferta-Azure Marketplace
description: API para recuperar o status atual da oferta.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.openlocfilehash: 897c2517c3836e1c3940db02efae0e5d94667a65
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114067"
---
# <a name="retrieve-offer-status"></a>Recuperar status da oferta

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue a funcionar após a migração para o Partner Center.

Recupera o status atual da oferta.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros do URI

|  **Nome**       |   **Descrição**                            |  **Data type** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Identificador do editor, por exemplo `Contoso`  |     String     |
|  offerId        | GUID que identifica exclusivamente a oferta      |     String     |
|  api-version    | Última versão da API                        |     Data       |
|  |  |


## <a name="header"></a>parâmetro


|  Nome           |  Valor               |
|  -------------  | -------------------  |
|  Tipo de conteúdo   |  `application/json`  |
|  Autorização  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Exemplo de corpo


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
  }
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**             |    **Descrição**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | O status da oferta. Para a lista de valores possíveis, consulte [Status da oferta](#offer-status) abaixo. |
|  da nuvem para o dispositivo             | Matriz de mensagens associadas à oferta                                                    |
|  etapas                | Matriz das etapas que a oferta passa durante a publicação uma oferta                      |
|  estimatedTimeFrame   | Estimativa do tempo que levaria para concluir essa etapa, em formato amigável                       |
|  id                   | Identificador da etapa                                                                         |
|  stepName             | Nome da etapa                                                                               |
|  descrição          | Descrição da etapa                                                                        |
|  status               | Status da etapa. Para a lista de valores possíveis, consulte [Status da etapa](#step-status) abaixo.    |
|  da nuvem para o dispositivo             | Matriz de mensagens relacionadas à etapa                                                          |
|  processPercentage    | Porcentagem de conclusão da etapa                                                              |
|  previewLinks         | *Não implementado atualmente*                                                                    |
|  liveLinks            | *Não implementado atualmente*                                                                    |
|  notificationEmails   | Preteridas para as ofertas migradas para o Partner Center. Os emails de notificação para ofertas migradas serão enviados para o email especificado nas informações de contato do vendedor nas configurações da conta.<br><br>Para ofertas não migradas, lista separada por vírgulas de endereços de email a serem notificados do progresso da operação        |
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
|  Êxito                   | O envio da oferta concluiu o processamento.               |
|  Canceled                    | O envio da oferta foi cancelado.                           |
|  Falhou                      | O envio da oferta falhou.                                 |
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
|  Concluir                    | A etapa está concluída.                            |
|  Canceled                    | A etapa foi cancelada.                           |
|  |  |
