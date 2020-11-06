---
title: API REST de configuração de Azure App-limitação
description: Páginas de referência para entender a limitação ao usar a API REST de configuração de Azure App
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423899"
---
# <a name="throttling"></a>Limitação

Os repositórios de configuração têm limites nas solicitações que eles podem atender. Todas as solicitações que excederem uma cota alocada para um repositório de configurações receberão uma resposta HTTP 429 (número excessivo de solicitações).

A limitação é dividida em diferentes políticas de cota:

- **Total de solicitações** -número total de solicitações
- **Largura de banda total** -dados de saída em bytes
- **Armazenamento** -tamanho total de armazenamento dos dados do usuário em bytes

## <a name="handling-throttled-responses"></a>Manipulando respostas limitadas

Quando o limite de taxa para uma determinada cota for atingido, o servidor responderá a solicitações adicionais desse tipo com um código de status _429_ . A resposta _429_ conterá um cabeçalho _Retry-After-MS_ fornecendo ao cliente um tempo de espera sugerido (em milissegundos) para permitir que a cota de solicitação seja reabastecido.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

No exemplo acima, o cliente excedeu sua cota permitida e é aconselhado a ficar lento e aguardar 10 milissegundos antes de tentar qualquer solicitação adicional. Os clientes também devem considerar a retirada progressiva.

## <a name="other-retry"></a>Outra repetição

O serviço pode identificar situações diferentes da limitação que precisam de uma repetição de cliente (por exemplo, serviço 503 indisponível). Em todos esses casos, o `retry-after-ms` cabeçalho de resposta será fornecido. Para aumentar a robustez, é aconselhável que o cliente siga o intervalo sugerido e execute uma nova tentativa.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
