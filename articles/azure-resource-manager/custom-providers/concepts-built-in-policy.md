---
title: Implantar associações usando políticas
description: Saiba mais sobre a implantação de associações para um provedor personalizado usando o serviço Azure Policy.
author: msHich
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: hich
ms.openlocfilehash: 536b95eb07619d0ce2d02ec01e1f51ed52c1b5e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650468"
---
# <a name="deploy-associations-for-a-custom-provider-using-azure-policy"></a>Implantar associações para um provedor personalizado usando a política do Azure

As políticas do Azure podem ser usadas para implantar associações para associar recursos a um provedor personalizado. Neste artigo, descrevemos uma política incorporada que implanta associações e como você pode usar essa política.

## <a name="built-in-policy-to-deploy-associations"></a>Política incorporada para implantar associações

Implantar associações para um provedor personalizado é uma política incorporada que pode ser usada para implantar associação para associar um recurso a um provedor personalizado. A política aceita três parâmetros:

- ID do provedor personalizado - Este ID é o ID de recursos do provedor personalizado ao qual os recursos precisam ser associados.
- Tipos de recursos para associar - Esses tipos de recursos são a lista de tipos de recursos a serem associados ao provedor personalizado. Você pode associar vários tipos de recursos a um provedor personalizado usando a mesma diretiva.
- Prefixo nome de associação - Esta string é o prefixo a ser adicionado ao nome do recurso de associação que está sendo criado. O valor padrão é "DeployedByPolicy".

A diretiva usa a avaliação DeployIfNotExist. Ele é executado depois que um provedor de recursos lidou com uma solicitação de criação ou atualização de recursos e a avaliação retornou um código de status de sucesso. Depois disso, o recurso de associação é implantado usando uma implantação de modelo.
Para obter mais informações sobre associações, consulte [o onboarding de recursos do Azure Custom Providers](./concepts-resource-onboarding.md)

## <a name="how-to-use-the-deploy-associations-built-in-policy"></a>Como usar a política de implantação de associações incorporadas 

### <a name="prerequisites"></a>Pré-requisitos
Se o provedor personalizado precisar de permissões para o escopo da política para executar uma ação, a implantação de política de recurso de associação não funcionaria sem a concessão das permissões.

### <a name="policy-assignment"></a>Atribuição de política
Para usar a diretiva incorporada, crie uma atribuição de diretiva e designe as associações Deploy para uma política de provedor personalizado. A política identificará recursos não compatíveis e implantará associação para esses recursos.

![Atribuir política incorporada](media/concepts-built-in-policy/assign-builtin-policy-customprovider.png)

## <a name="getting-help"></a>Obtendo ajuda

Se você tiver dúvidas sobre o desenvolvimento do Azure Custom Resource Providers, tente perguntar-lhes no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca ```azure-custom-providers``` para obter uma resposta rápida.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o uso de política incorporada para implantar associações. Veja estes artigos para saber mais:

- [Conceitos: Azure Custom Providers onboarding de recursos](./concepts-resource-onboarding.md)
- [Tutorial: Onboarding de recursos com provedores personalizados](./tutorial-resource-onboarding.md)
- [Tutorial: Crie ações e recursos personalizados no Azure](./tutorial-get-started-with-custom-providers.md)
- [Quickstart: Crie um provedor de recursos personalizado e implante recursos personalizados](./create-custom-provider.md)
- [Como: Adicionar ações personalizadas a uma API Azure REST](./custom-providers-action-endpoint-how-to.md)
- [Como: Adicionar recursos personalizados a uma API Azure REST](./custom-providers-resources-endpoint-how-to.md)
