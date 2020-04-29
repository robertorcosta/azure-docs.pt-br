---
title: Componentes
description: Definição de componentes no escopo da renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681890"
---
# <a name="components"></a>Componentes

A renderização remota do Azure usa o padrão do [sistema de componentes de entidade](https://en.wikipedia.org/wiki/Entity_component_system) . Embora as [entidades](entities.md) representem a posição e a composição hierárquica de objetos, os componentes são responsáveis por implementar o comportamento.

Os tipos de componentes usados com mais frequência são [componentes de malha](meshes.md), que adicionam malhas ao pipeline de renderização. Da mesma forma, os [componentes leves](../overview/features/lights.md) são usados para adicionar componentes de iluminação e de [plano de recorte](../overview/features/cut-planes.md) são usados para cortar malhas abertas.

Todos esses componentes usam a transformação (posição, rotação, escala) da entidade à qual eles estão anexados, como seu ponto de referência.

## <a name="working-with-components"></a>Trabalhando com componentes

Você pode facilmente adicionar, remover e manipular componentes programaticamente:

```cs
// create a point light component
AzureSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Actions.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

Um componente é anexado a uma entidade no momento da criação. Ele não pode ser movido para outra entidade posteriormente. Os componentes são explicitamente excluídos `Component.Destroy()` com ou automaticamente quando a entidade do proprietário do componente é destruída.

Somente uma instância de cada tipo de componente pode ser adicionada a uma entidade por vez.

## <a name="unity-specific"></a>Específico do Unity

A integração do Unity tem funções de extensão adicionais para interagir com componentes. Consulte [componentes e objetos de jogos do Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](object-bounds.md)
* [Malhas](meshes.md)
