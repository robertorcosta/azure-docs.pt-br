---
title: Como adicionar ações personalizadas à API REST do Azure
description: Aprenda a adicionar ações personalizadas à API Azure REST. Este artigo passará pelos requisitos e melhores práticas para pontos finais que desejam implementar ações personalizadas.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650390"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Adicionando ações personalizadas à API Azure REST

Este artigo passará pelos requisitos e práticas recomendadas para criar pontos finais do Provedor de Recursos Personalizados do Azure que implementam ações personalizadas. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral dos provedores de recursos personalizados.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>Como definir um ponto final de ação

Um **ponto final** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto final é definido no provedor de recursos personalizado e pode ser qualquer URL acessível ao público. A amostra abaixo tem `myCustomAction` uma `endpointURL` **ação** chamada implementada por .

Provedor **de recursos de amostra:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
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

## <a name="building-an-action-endpoint"></a>Construindo um ponto final de ação

Um **ponto final** que implemente uma **ação** deve lidar com a solicitação e resposta para a nova API no Azure. Quando um provedor de recursos personalizado com uma **ação** é criado, ele gerará um novo conjunto de APIs no Azure. Neste caso, a ação gerará uma nova API de ação do Azure para `POST` chamadas:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Solicitação de entrada da API do Azure:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Esta solicitação será então encaminhada para o **ponto final** no formulário:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Da mesma forma, a resposta do **ponto final** é então encaminhada de volta ao cliente. A resposta do ponto final deve retornar:

- Um documento de objeto JSON válido. Todas as matrizes e strings devem ser aninhados um objeto superior.
- O `Content-Type` cabeçalho deve ser definido como "application/json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Resposta do provedor de recursos personalizado do Azure:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Chamando uma ação personalizada

Existem duas maneiras principais de cancelar uma ação personalizada de um provedor de recursos personalizado:

- CLI do Azure
- Modelos do Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
ação | *Sim* | O nome da ação definida no **ResourceProvider**.
ids | *Sim* | O ID de recurso do **ResourceProvider**.
corpo da solicitação | *Não* | O órgão de solicitação que será enviado para o **ponto final.**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> As ações têm suporte limitado nos modelos de gerenciador de recursos do Azure. Para que a ação seja chamada dentro de [`list`](../templates/template-functions-resource.md#list) um modelo, ele deve conter o prefixo em seu nome.

Provedor **de recursos de exemplo** com ação de lista:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Modelo de exemplo do Azure Resource Manager:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parâmetro | Obrigatório | Descrição
---|---|---
identificador de recursos | *Sim* | O ID de recurso do **ResourceProvider**.
apiVersion | *Sim* | A versão aPI do tempo de execução dos recursos. Isso deve ser sempre "2018-09-01-preview".
functionValues | *Não* | O órgão de solicitação que será enviado para o **ponto final.**

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Quickstart: crie o Provedor de Recursos Personalizados do Azure e implante recursos personalizados](./create-custom-provider.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar recursos personalizados à API Azure REST](./custom-providers-resources-endpoint-how-to.md)
