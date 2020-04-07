---
title: Componentes
description: Definição de componentes no escopo da renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: cb8b38addef736914a8627971e57ea2b173293d6
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681890"
---
# <a name="components"></a>Componentes

A renderização remota do Azure usa o padrão [do sistema de componentes da entidade.](https://en.wikipedia.org/wiki/Entity_component_system) Enquanto [as entidades](entities.md) representam a posição e a composição hierárquica dos objetos, os componentes são responsáveis pela implementação do comportamento.

Os tipos de componentes mais utilizados são [os componentes](meshes.md)de malha, que adicionam malhas no pipeline de renderização. Da mesma forma, [componentes de luz](../overview/features/lights.md) são usados para adicionar iluminação e [cortar componentes do plano](../overview/features/cut-planes.md) são usados para cortar as vaqueiras abertas.

Todos esses componentes utilizam a transformação (posição, rotação, escala) da entidade a que estão ligados, como seu ponto de referência.

## <a name="working-with-components"></a>Trabalhando com componentes

Você pode facilmente adicionar, remover e manipular componentes de forma programática:

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

Um componente é anexado a uma entidade no momento da criação. Não pode ser transferido para outra entidade depois. Os componentes são explicitamente `Component.Destroy()` excluídos com ou automaticamente quando a entidade proprietária do componente é destruída.

Apenas uma instância de cada tipo de componente pode ser adicionada a uma entidade por vez.

## <a name="unity-specific"></a>Unidade específica

A integração Unity tem funções de extensão adicionais para interagir com componentes. Consulte [objetos e componentes do jogo Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](object-bounds.md)
* [Malhas](meshes.md)
