---
title: Componentes
description: Definição de componentes no escopo do Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: a488e2499b92b290ad2b55120c3c70a18d45d426
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613934"
---
# <a name="components"></a>Componentes

O Azure Remote Rendering usa o padrão [Sistema de componente de entidade](https://en.wikipedia.org/wiki/Entity_component_system). Embora as [entidades](entities.md) representem a posição e a composição hierárquica dos objetos, os componentes são responsáveis por implementar o comportamento.

Os tipos de componentes usados com mais frequência são [:::no-loc text="mesh components":::](meshes.md) , que adicionam malhas ao pipeline de renderização. Da mesma forma, [componentes de luz](../overview/features/lights.md) são usados para adicionar iluminação e [componentes de plano de corte](../overview/features/cut-planes.md) são usados para cortar malhas abertas.

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

## <a name="api-documentation"></a>Documentação da API

* [ComponentBase C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# remotamente. CreateComponent ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.remotemanager.createcomponent)
* [Entidade C#. FindComponentOfType ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](https://docs.microsoft.com/cpp/api/remote-rendering/componentbase)
* [C++ Remotemanager:: CreateComponent ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#createcomponent)
* [Entidade C++:: FindComponentOfType ()](https://docs.microsoft.com/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](object-bounds.md)
* [Malhas](meshes.md)
