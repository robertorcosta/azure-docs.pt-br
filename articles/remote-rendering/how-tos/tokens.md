---
title: Obter tokens de acesso de serviço
description: Descreve como criar tokens para acessar as APIs ARR REST
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687075"
---
# <a name="get-service-access-tokens"></a>Obter tokens de acesso de serviço

O acesso às APIs ARR REST só é concedido para usuários autorizados. Para provar sua autorização, você deve enviar um *token de acesso* junto com solicitações REST. Esses tokens são emitidos pelo *Secure Token Service* (STS) em troca de uma chave de conta. Os tokens têm uma **vida útil de 24 horas** e, portanto, podem ser emitidos aos usuários sem dar-lhes acesso total ao serviço.

Este artigo descreve como criar esse token de acesso.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta ARR,](create-an-account.md)se você ainda não tiver uma.

## <a name="token-service-rest-api"></a>API rest do serviço de token

Para criar tokens de acesso, o *Secure Token Service* fornece uma única API REST. A URL do serviço ARR STS é https:\//sts.mixedreality.azure.com.

### <a name="get-token-request"></a>Pedido de 'Obter token'

| URI | Método |
|-----------|:-----------|
| **/accountsId**/token | GET |

| Cabeçalho | Valor |
|--------|:------|
| Autorização | **"AccountId**bearer :**accountKey"** |

Substitua *o AccountId* e *a accountKey* por seus respectivos dados.

### <a name="get-token-response"></a>Resposta 'Obter token'

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | AccessToken: string | Sucesso |

| Cabeçalho | Finalidade |
|--------|:------|
| MS-CV | Este valor pode ser usado para rastrear a chamada dentro do serviço |

## <a name="getting-a-token-using-powershell"></a>Obtendo um token usando powershell

O código PowerShell abaixo demonstra como enviar a solicitação REST necessária para o STS. Em seguida, imprime o token para o prompt PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

O script apenas imprime o token para a saída, de onde você pode copiar & coá-lo. Para um projeto real, você deve automatizar esse processo.

## <a name="next-steps"></a>Próximas etapas

* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
* [Azure Frontend APIs](../how-tos/frontend-apis.md)
* [API DE RESTO de gerenciamento de sessões](../how-tos/session-rest-api.md)
