---
title: Integração de recursos
description: Aprenda a realizar o onboarding de recursos usando os Provedores Personalizados do Azure para aplicar o gerenciamento ou a configuração a outros tipos de recursos do Azure.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650403"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Visão geral do recurso do Azure Custom Providers

O onboarding de recursos do Azure Custom Providers é um modelo de extensibilidade para tipos de recursos do Azure. Ele permite que você aplique operações ou gerenciamento em todos os recursos existentes do Azure em escala. Para obter mais informações, consulte [como os provedores personalizados do Azure podem estender o Azure](overview.md). Este artigo descreve:

- Que recurso o onboarding pode fazer.
- Recursos de enboarding básico e como usá-lo.
- Onde encontrar guias e amostras de código para começar.

> [!IMPORTANT]
> Os Provedores Personalizados estão atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço, e não recomendamos para cargas de trabalho de produção. Certos recursos podem não ter suporte ou recursos restritos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>O que o recurso de bordo pode fazer?

Semelhante aos [recursos personalizados do Azure Custom Providers,](./custom-providers-resources-endpoint-how-to.md)o onboarding de recursos define um contrato que proxy solicita ções de "onboarding" para um ponto final. Ao contrário dos recursos personalizados, o onboarding de recursos não cria um novo tipo de recurso. Em vez disso, permite a extensão dos tipos de recursos existentes. E o onboarding de recursos funciona com a Política do Azure, para que a gestão e a configuração dos recursos possam ser feitas em escala. Alguns exemplos de fluxos de trabalho de onboarding de recursos:

- Instale e gerencie em extensões de máquinas virtuais.
- Faça upload e configure padrões em contas de armazenamento do Azure.
- Habilite as configurações de diagnóstico da linha de base em escala.

## <a name="resource-onboarding-basics"></a>Noções básicas de onboarding de recursos

Você configura o onboarding de recursos através do Azure Custom Providers usando o Microsoft.CustomProviders/resourceProviders e os tipos de recursos Microsoft.CustomProviders/associations. Para habilitar o onboarding de recursos para um provedor personalizado, durante o processo de configuração, crie um **resourceType** chamado "associações" com um **roteamentoType** que inclua "Extensão". Os Microsoft.CustomProviders/associações e o Microsoft.CustomProviders/resourceProviders não precisam pertencer ao mesmo grupo de recursos.

Aqui está uma amostra do provedor personalizado do Azure:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Propriedade | Obrigatório? | Descrição
---|---|---
name | Sim | O nome da definição do ponto de extremidade. Para o embarque de recursos, o nome deve ser "associações".
routingType | Sim | Determina o tipo de contrato com o ponto final. Para onboarding de recursos, os **roteamentos válidosTipos** são "Proxy,Cache,Extensão" e "Webhook,Cache,Extensão".
endpoint | Sim | O ponto de extremidade para o qual rotear as solicitações. Isso lidará com a resposta e quaisquer efeitos colaterais da solicitação.

Depois de criar o provedor personalizado com o tipo de recurso de associações, você pode segmentar usando microsoft.CustomProviders/associações. Microsoft.CustomProviders/associações é um recurso de extensão que pode estender qualquer outro recurso do Azure. Quando uma instância do Microsoft.CustomProviders/associações for criada, ela terá um recurso de destino de propriedade **ResourceId**, que deve ser um Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications resource ID. Nesses casos, a solicitação será encaminhada para o tipo de recurso de associações na instância Microsoft.CustomProviders/resourceProviders que você criou.

> [!NOTE]
> Se um ID de recurso microsoft.solutions/aplicativos for fornecido como **o targetResourceId,** deve haver um Microsoft.CustomProviders/resourceProviders implantado no grupo de recursos gerenciados com o nome "público".

Associação de provedores personalizados do Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Propriedade | Obrigatório? | Descrição
---|---|---
targetResourceId | Sim | O ID de recursos do Microsoft.CustomProviders/resourceProviders ou Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Como usar o onboarding de recursos

O onboarding de recursos funciona ampliando outros recursos com o recurso de extensão Microsoft.CustomProviders/associations. Na amostra a seguir, a solicitação é feita para uma máquina virtual, mas qualquer recurso pode ser estendido.

Primeiro, você precisa criar um recurso de provedor personalizado com um tipo de recurso de associações. Isso declarará a URL de retorno de chamada que será usada quando um recurso correspondente do Microsoft.CustomProviders/associações for criado, que tem como alvo o provedor personalizado.

Exemplo Microsoft.CustomProviders/resourceProviders criam solicitação:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Depois de criar o provedor personalizado, você pode direcionar outros recursos e aplicar os efeitos colaterais do provedor personalizado a eles.

Exemplo Microsoft.CustomProviders/associações criam solicitação:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Essa solicitação será então encaminhada para o ponto final especificado no provedor personalizado que você criou, que é referenciado pelo **targetResourceId** neste formulário:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

O ponto final deve responder com `Content-Type` um aplicativo/json e um órgão de resposta JSON válido. Os campos que são devolvidos sob o objeto de **propriedades** do JSON serão adicionados à resposta de retorno da associação.

## <a name="getting-help"></a>Obtendo ajuda

Se você tiver dúvidas sobre o desenvolvimento do Azure Custom Resource Providers, tente perguntar-lhes no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Veja estes artigos para saber mais:

- [Tutorial: Onboarding de recursos com provedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Crie um provedor de recursos personalizado e implante recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
