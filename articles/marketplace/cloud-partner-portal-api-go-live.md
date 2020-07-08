---
title: API do Go Live – Azure Marketplace
description: A API do Go Live inicia o processo de listagem de ofertas em tempo real.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515840"
---
# <a name="go-live"></a>Go Live

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue a funcionar após a migração para o Partner Center.

Essa API inicia o processo de envio de um aplicativo para produção. Essa operação geralmente é demorada. Essa chamada usa a lista de email de notificação da operação da API [Publicar](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Data type** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificador do publicador da oferta a ser recuperada, por exemplo `contoso`       |  String       |
| offerId        | O identificador da oferta a ser recuperada                                   |  String       |
| api-version    | Última versão da API                                                   |  Data         |
|  |  |  |

## <a name="header"></a>parâmetro
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Tipo de conteúdo    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Exemplo de corpo

### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Location    |  O caminho relativo para recuperar o status da operação            |
|  |  |

### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local para rastrear o status da operação. |
|  400     | `Bad/Malformed request` – as informações de erro adicionais são encontradas dentro do corpo da resposta. |
|  404     |  `Not found` – a entidade especificada não existe.                                       |
|  |  |
