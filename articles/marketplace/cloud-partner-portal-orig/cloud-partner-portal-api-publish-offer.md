---
title: Publicar uma oferta | Azure Marketplace
description: API para publicar a oferta especificada.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255933"
---
# <a name="publish-an-offer"></a>Publicar uma oferta

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao Partner Center e continuarão funcionando depois que suas ofertas forem migradas para o Partner Center. A integração apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) para garantir que seu código continue a funcionar após a migração para o Partner Center.

Inicia o processo de publicação da oferta especificada. Essa ligação é uma operação demorada.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador do editor, por exemplo `contoso`      |   Cadeia de caracteres       |
|  offerId       | Identificador da oferta                                 |   Cadeia de caracteres       |
|  api-version   | Última versão da API                        |   Data         |
|  |  |

## <a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Tipo de conteúdo    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exemplo de corpo
------------

### <a name="request"></a>Solicitação

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  emails de notificação    | Lista separada por vírgula de endereços de email para ser notificado sobre o andamento da operação de publicação. |
|  |  |


### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Local    | O caminho relativo para recuperar o status da operação     |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Auto-completar** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local que pode ser usado para rastrear a operação iniciada. |
| 400   | `Bad/Malformed request` – O corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` – Indica que a entidade a ser publicada apresentou falha na validação.                                                        |
| 404   | `Not found` – A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
