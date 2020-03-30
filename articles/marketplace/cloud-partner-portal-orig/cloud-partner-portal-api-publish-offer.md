---
title: Publicar uma oferta | Mercado Azure
description: API para publicar a oferta especificada.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 4163bf5727c327d559b81db42f99684aa0cc8d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280517"
---
<a name="publish-an-offer"></a>Publicar uma oferta
================

Inicia o processo de publicação da oferta especificada. Essa ligação é uma operação demorada.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |    **Descrição**                               |  **Tipo de dados** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador do editor, por exemplo `contoso`      |   String       |
|  offerId       | Identificador da oferta                                 |   String       |
|  api-version   | Última versão da API                        |   Data         |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Tipo de conteúdo    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Exemplo de corpo
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

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | A URL que pode ser consultada para determinar o status atual da operação.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local que pode ser usado para rastrear a operação iniciada. |
| 400   | `Bad/Malformed request` – O corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` – Indica que a entidade a ser publicada apresentou falha na validação.                                                        |
| 404   | `Not found` – A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
