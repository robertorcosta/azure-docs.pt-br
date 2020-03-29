---
title: Como adicionar recursos personalizados à API REST do Azure
description: Aprenda a adicionar recursos personalizados à API Azure REST. Este artigo passará pelos requisitos e práticas recomendadas para pontos finais que desejam implementar recursos personalizados.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650520"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Adicionando recursos personalizados à API Azure REST

Este artigo passará pelos requisitos e práticas recomendadas para criar os pontos finais do Azure Custom Resource Provider que implementa recursos personalizados. Se você não estiver familiarizado com os provedores de recursos personalizados do Azure, consulte [a visão geral dos provedores de recursos personalizados.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Como definir um ponto final de recurso

Um **ponto final** é uma URL que aponta para um serviço, que implementa o contrato subjacente entre ele e o Azure. O ponto final é definido no provedor de recursos personalizado e pode ser qualquer URL acessível ao público. A amostra abaixo tem `myCustomResource` um `endpointURL` **resourceType** chamado implementado por .

Provedor **de recursos de amostra:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
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

## <a name="building-a-resource-endpoint"></a>Construindo um ponto final de recurso

Um **ponto final** que implementa um **resourceType** deve lidar com a solicitação e a resposta para a nova API no Azure. Quando um provedor de recursos personalizado com um **resourceType** é criado, ele gerará um novo conjunto de APIs no Azure. Neste caso, o **resourceType** gerará uma nova API `GET`de `DELETE` recursos do Azure para `PUT` `GET` , e para executar crud em um único recurso, bem como para recuperar todos os recursos existentes:

Manipular recurso`PUT`único `GET`( `DELETE`, e ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Recuperar todos`GET`os recursos ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Para recursos personalizados, os provedores de recursos`Proxy`personalizados`Proxy, Cache`oferecem dois tipos de **roteamentoTypes**: " e " " " " "

### <a name="proxy-routing-type"></a>tipo de roteamento proxy

O`Proxy`" **roteamentoType** proxies todos os métodos de solicitação para o **ponto final** especificado no provedor de recursos personalizado. Quando usar`Proxy`" ":

- É necessário controle total sobre a resposta.
- Integrando sistemas com recursos existentes.

Para saber mais`Proxy`sobre os recursos " " , consulte [a referência proxy de recursos personalizado](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>tipo de roteamento de cache proxy

O`Proxy, Cache`" **roteamento "** `PUT` Somente `DELETE` proxies e métodos de solicitação para o **ponto final** especificado no provedor de recursos personalizado. O provedor de recursos `GET` personalizado retornará automaticamente as solicitações com base no que ele tem armazenado em seu cache. Se um recurso personalizado for marcado com cache, o provedor de recursos personalizado também adicionará / substituirá campos na resposta para tornar o Azure compatível com APIs. Quando usar`Proxy, Cache`" ":

- Criando um novo sistema que não tem recursos existentes.
- Trabalhe com o ecossistema Azure existente.

Para saber mais`Proxy, Cache`sobre os recursos " " , consulte [a referência de cache de recursos personalizado](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Criando um recurso personalizado

Existem duas maneiras principais de criar um recurso personalizado de um provedor de recursos personalizado:

- CLI do Azure
- Modelos do Azure Resource Manager

### <a name="azure-cli"></a>CLI do Azure

Criar um recurso personalizado:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parâmetro | Obrigatório | Descrição
---|---|---
is-full-object | *Sim* | Indica que o objeto de propriedades inclui outras opções, como localização, tags, SKU e/ou plano.
id | *Sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **ResourceProvider**
properties | *Sim* | O órgão de solicitação que será enviado para o **ponto final.**

Excluir um Recurso Personalizado do Azure:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
id | *Sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **ResourceProvider**.

Recuperar um Recurso Personalizado do Azure:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parâmetro | Obrigatório | Descrição
---|---|---
id | *Sim* | A ID de recurso referente ao recurso personalizado. Isso deve existir fora do **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager

> [!NOTE]
> Os recursos exigem que `id`a `name`resposta `type` contenha um ponto final e **apropriado.**

Os modelos do Azure `name`Resource `type` Manager exigem isso `id`e são retornados corretamente do ponto final a jusante. Uma resposta de recurso retornada deve estar no formulário:

Resposta **de ponto final da** amostra:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Modelo de exemplo do Azure Resource Manager:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parâmetro | Obrigatório | Descrição
---|---|---
resourceTypeName | *Sim* | O **nome** do **resourceType** definido no provedor personalizado.
resourceProviderName | *Sim* | O nome de instância do provedor de recursos personalizado.
customResourceName | *Sim* | O nome do recurso personalizado.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral dos provedores de recursos personalizados do Azure](overview.md)
- [Quickstart: crie o Provedor de Recursos Personalizados do Azure e implante recursos personalizados](./create-custom-provider.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Referência: Referência de proxy de recursos personalizado](proxy-resource-endpoint-reference.md)
- [Referência: Referência de cache de recursos personalizado](proxy-cache-resource-endpoint-reference.md)
