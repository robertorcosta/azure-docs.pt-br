---
title: Recortar planos
description: Explica o que são planos de recorte e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 5d641b573a1cad5cac6db6199f5bad5c06151c62
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759074"
---
# <a name="cut-planes"></a>Recortar planos

Um *plano de recorte* é um recurso visual que corta pixels em um lado de um plano virtual, revelando o interior das [malhas](../../concepts/meshes.md).
A imagem abaixo demonstra o efeito. À esquerda, há a malha original, à direita, é possível ver dentro da malha:

![Recortar planos](./media/cutplane-1.png)

## <a name="limitations"></a>Limitações

* Por enquanto, o Azure Remote Rendering permite um **máximo de oito planos de recorte ativos**. É possível criar mais componentes de plano de recorte, mas, se você tentar habilitar mais planos simultaneamente, ele ignorará a ativação. Desabilite os outros planos primeiro se quiser alternar qual componente deve afetar a cena.
* Cada plano de recorte afeta todos os objetos renderizados remotamente. Atualmente, não há nenhuma maneira de excluir objetos específicos ou partes de malha.
* Os planos de recorte são puramente um recurso visual, que não afetam o resultado de [consultas espaciais](spatial-queries.md). Se você quiser projetar um raio em uma malha de corte aberto, é possível ajustar o ponto inicial do raio para estar no plano de recorte. Dessa forma, o raio só pode atingir as partes visíveis.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Cada plano de recorte ativo gera um pequeno custo durante a renderização. Desabilite ou exclua planos de recorte quando eles não forem necessários.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Adicione um plano de recorte à cena criando um *CutPlaneComponent*. O local e a orientação do plano são determinados pela [entidade](../../concepts/entities.md) do proprietário do componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<AzureSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Actions()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->Normal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->FadeColor(fadeColor);
    cutPlane->FadeLength(0.05f); // gradient width: 5cm
}
```


### <a name="cutplanecomponent-properties"></a>Propriedades de CutPlaneComponent

As propriedades a seguir são expostas em um componente de plano de recorte:

* **Habilitado:** É possível desativar temporariamente os planos de recorte desabilitando o componente. Os planos de recorte desabilitados não geram sobrecarga de renderização e também não contam para o limite do plano de recorte global.

* **Normal:** Especifica qual direção (+X,-X,+Y,-Y,+Z,-Z) é usada como o plano normal. Essa direção é relativa à orientação da entidade do proprietário. Mova e gire a entidade do proprietário para o posicionamento exato.

* **FadeColor** e **FadeLength:**

  Se o valor alfa de *FadeColor* for diferente de zero, os pixels próximos ao plano de recorte ficarão em direção à parte RGB de FadeColor. A força do canal alfa determina se ele ficará totalmente esmaecido em direção à cor de esmaecimento ou apenas parcialmente. *FadeLength* define em qual distância esse esmaecimento será realizado.

## <a name="next-steps"></a>Próximas etapas

* [Renderização de lado único](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)
