---
title: Visão geral dos provedores personalizados
description: Saiba mais sobre os provedores de recursos personalizados do Azure e como estender o plano API do Azure para ajustar seus fluxos de trabalho.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650481"
---
# <a name="azure-custom-resource-providers-overview"></a>Visão geral dos provedores de recursos personalizados do Azure

O Azure Custom Resource Providers é uma plataforma de extensibilidade do Azure. Ele permite definir apis personalizadas que podem ser usadas para enriquecer a experiência padrão do Azure. Esta documentação descreve:

- Como construir e implantar um provedor de recursos personalizados do Azure.
- Como utilizar os provedores de recursos personalizados do Azure para ampliar os fluxos de trabalho existentes.
- Onde encontrar guias e amostras de código para começar.

![Visão geral do provedor personalizado](./media/overview/overview.png)

> [!IMPORTANT]
> Os Provedores Personalizados estão atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>O que os provedores de recursos personalizados podem fazer

Aqui estão alguns exemplos do que você pode alcançar com os provedores de recursos personalizados do Azure:

- Amplie a API REST do Azure Resource Manager para incluir serviços internos e externos.
- Habilite cenários personalizados em cima dos fluxos de trabalho existentes do Azure.
- Personalize o controle e o efeito do Azure Resource Manager.

## <a name="what-is-a-custom-resource-provider"></a>O que é um provedor de recursos personalizado

Os provedores de recursos personalizados do Azure são feitos criando um contrato entre o Azure e um ponto final. Este contrato define uma lista de novos recursos e ações por meio de um novo recurso, **Microsoft.CustomProviders/resourceProviders**. O provedor de recursos personalizado exporá essas novas APIs no Azure. Os provedores de recursos personalizados do Azure são compostos por três partes: provedor de recursos personalizado, **pontos finais**e recursos personalizados.

## <a name="how-to-build-custom-resource-providers"></a>Como construir provedores de recursos personalizados

Os provedores de recursos personalizados são uma lista de contratos entre o Azure e os endpoints. Este contrato descreve como o Azure deve interagir com um ponto final. O provedor de recursos age como um proxy e encaminhará solicitações e respostas para e a partir do **ponto final**especificado . Um provedor de recursos pode especificar dois tipos de contratos: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) e [**ações**](./custom-providers-action-endpoint-how-to.md). Estes são habilitados através de definições de ponto final. Uma definição de ponto final é composta por três campos: **nome,** **roteamentoType**e **endpoint**.

Ponto final da amostra:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Propriedade | Obrigatório | Descrição
---|---|---
name | *Sim* | O nome da definição do ponto de extremidade. O Azure exporá esse nome por meio de sua API em '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *Não* | Determina o tipo de contrato com o **ponto final**. Se não for especificado, o padrão será "Proxy".
endpoint | *Sim* | O ponto de extremidade para o qual rotear as solicitações. Isso processará a resposta, bem como os efeitos colaterais, da solicitação.

### <a name="building-custom-resources"></a>Construindo recursos personalizados

**ResourceTypes** descreve novos recursos personalizados que são adicionados ao Azure. Estes expõem métodos básicos DE CRUD RESTful. Veja [mais sobre a criação de recursos personalizados](./custom-providers-resources-endpoint-how-to.md)

Exemplo de provedor de recursos personalizados com **tipos de recursos**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

APIs adicionadas ao Azure para a amostra acima:

HttpMethod | URI de Exemplo | Descrição
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A API Azure REST chama para criar um novo recurso.
Delete (excluir) | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A API Azure REST chama para excluir um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | A API Azure REST chama para recuperar um recurso existente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | A aPI Azure REST chama para recuperar a lista de recursos existentes.

### <a name="building-custom-actions"></a>Construindo ações personalizadas

**As ações** descrevem novas ações que são adicionadas ao Azure. Estes podem ser expostos em cima do provedor de recursos ou aninhados um **resourceType**. Veja [mais sobre como criar ações personalizadas](./custom-providers-action-endpoint-how-to.md)

Exemplo de provedor de recursos personalizados com **ações:**

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
  "location": "eastus"
}
```

APIs adicionadas ao Azure para a amostra acima:

HttpMethod | URI de Exemplo | Descrição
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | A API Azure REST chama para ativar a ação.

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre o desenvolvimento do Provedor de Recursos Personalizados do Azure, tente perguntar sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante pode já ter sido feita e respondida. Portanto, verifique primeiro antes de postar. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre provedores personalizados. Vá para o próximo artigo para criar um provedor personalizado.

- [Quickstart: crie o Provedor de Recursos Personalizados do Azure e implante recursos personalizados](./create-custom-provider.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Como: Adicionar ações personalizadas à API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados à API Azure REST](./custom-providers-resources-endpoint-how-to.md)
