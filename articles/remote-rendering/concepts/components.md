---
title: Componentes
description: Definição de componentes no escopo do Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 42efc1395fee28a3854420f15c2ad08b301cc1f7
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758734"
---
# <a name="components"></a>Componentes

O Azure Remote Rendering usa o padrão [Sistema de componente de entidade](https://en.wikipedia.org/wiki/Entity_component_system). Embora as [entidades](entities.md) representem a posição e a composição hierárquica dos objetos, os componentes são responsáveis por implementar o comportamento.

Os tipos de componentes usados com mais frequência são [componentes de malha](meshes.md), que adicionam malhas ao pipeline de renderização. Da mesma forma, [componentes de luz](../overview/features/lights.md) são usados para adicionar iluminação e [componentes de plano de corte](../overview/features/cut-planes.md) são usados para cortar malhas abertas.

Todos esses componentes usam a transformação (posição, rotação, escala) da entidade à qual eles estão anexados como ponto de referência.

## <a name="working-with-components"></a>Trabalho com componentes

É fácil adicionar, remover e manipular componentes programaticamente:

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

```cpp
// create a point light component
ApiHandle<AzureSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Actions()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```


Um componente é anexado a uma entidade no momento da criação. Ele não pode ser movido depois para outra entidade. Os componentes são excluídos explicitamente com `Component.Destroy()` ou automaticamente quando a entidade do proprietário é destruída.

Apenas uma instância de cada tipo de componente pode ser adicionada por vez a uma entidade.

## <a name="unity-specific"></a>Específico ao Unity

A integração do Unity tem funções de extensão adicionais para interação com componentes. Veja [Componentes e objetos de jogos do Unity](../how-tos/unity/objects-components.md).

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](object-bounds.md)
* [Malhas](meshes.md)
