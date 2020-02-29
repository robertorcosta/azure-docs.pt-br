---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197582"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As informações a seguir são comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault:

- Substitua `{api-version}` pela versão da api no URI.
- Substitua `{subscription-id}` pelo identificador de assinatura no URI
- Substitua `{resource-group-name}` pelo grupo de recursos. Para saber mais, consulte Usar os grupos de recursos para gerenciar seus recursos do Azure.
- Substitua `{vault-name}` pelo nome de seu cofre de chaves no URI.
- Defina o cabeçalho Content-Type como application/json.
- Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory (AAD). Para saber mais, confira [Autenticação de solicitações do Azure Resource Manager](authentication-requests-and-responses.md).

## <a name="common-error-response"></a>Resposta de erro comum
O serviço usará códigos de status HTTP para indicar êxito ou falha. Além disso, as falhas contêm uma resposta no seguinte formato:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome do elemento | Type | DESCRIÇÃO |
|---|---|---|
| código | string | O tipo de erro que ocorreu.|
| message | string | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Consulte Também
 [Referência da API REST do Azure Key Vault](/rest/api/keyvault/)
