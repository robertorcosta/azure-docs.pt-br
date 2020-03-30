---
title: Referência de cache de recurso personalizado
description: Referência personalizada de cache de recursos para provedores de recursos personalizados do Azure. Este artigo passará pelos requisitos para pontos finais que implementam recursos personalizados de cache.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650377"
---
# <a name="custom-resource-cache-reference"></a>Referência de cache de recursos personalizada

Este artigo passará pelos requisitos para pontos finais que implementam recursos personalizados de cache. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral dos provedores de recursos personalizados.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>Como definir um ponto final de recurso de cache

Um recurso proxy pode ser criado especificando o **roteamentoType** para "Proxy, Cache".

Provedor de recursos personalizado de exemplo:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Construindo o ponto final do recurso proxy

Um **ponto final** que implementa um **ponto final** de recurso "Proxy, Cache" deve lidar com a solicitação e a resposta para a nova API no Azure. Neste caso, o **resourceType** gerará uma nova API `GET`de `DELETE` recursos do Azure para `PUT`, `GET` e para executar crud em um único recurso, bem como para recuperar todos os recursos existentes:

> [!NOTE]
> A API do Azure gerará `GET`os `DELETE`métodos de `PUT`solicitação, e `PUT` `DELETE`, mas o **ponto final** do cache só precisa lidar e .
> Recomendamos que o **ponto** final `GET`também implemente .

### <a name="create-a-custom-resource"></a>Criar um recurso personalizado

Solicitação de entrada da API do Azure:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Esta solicitação será então encaminhada para o **ponto final** no formulário:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Da mesma forma, a resposta do **ponto final** é então encaminhada de volta ao cliente. A resposta do ponto final deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e strings devem ser aninhados um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset=utf-8".
- O provedor de recursos `name`personalizado `type`substituirá os campos e `id` os campos para a solicitação.
- O provedor de recursos personalizado `properties` só retornará os campos o objeto para um ponto final de cache.

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Os `name` `id`campos `type` e campos serão gerados automaticamente para o recurso personalizado pelo provedor de recursos personalizado.

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Remover um recurso personalizado

Solicitação de entrada da API do Azure:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Esta solicitação será então encaminhada para o **ponto final** no formulário:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Da mesma forma, a resposta do **ponto final** é então encaminhada de volta ao cliente. A resposta do ponto final deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e strings devem ser aninhados sob um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset=utf-8".
- O Provedor de Recursos Personalizados do Azure só removerá o item de seu cache se uma resposta de 200 níveis for devolvida. Mesmo que o recurso não exista, o **ponto final** deve retornar 204.

**Ponto final** Resposta:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Recuperar um recurso personalizado

Solicitação de entrada da API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

A solicitação **não** será encaminhada para o **ponto final.**

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Enumerar todos os recursos personalizados

Solicitação de entrada da API do Azure:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Esta solicitação **não** será encaminhada para o **ponto final**.

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Quickstart: crie o Provedor de Recursos Personalizados do Azure e implante recursos personalizados](./create-custom-provider.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de proxy de recursos personalizado](proxy-resource-endpoint-reference.md)
