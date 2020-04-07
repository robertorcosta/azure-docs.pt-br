---
title: Cortar aviões
description: Explica o que são os planos cortados e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681019"
---
# <a name="cut-planes"></a>Cortar aviões

Um *plano de corte* é um recurso visual que corta pixels em um lado de um plano virtual, revelando o interior das [meshes](../../concepts/meshes.md).
A imagem abaixo demonstra o efeito. A esquerda mostra a malha original, à direita pode-se olhar dentro da malha:

![Avião cortado](./media/cutplane-1.png)

## <a name="limitations"></a>Limitações

* Por enquanto, a Renderização Remota Azure suporta um **máximo de oito aviões de corte ativos**. Você pode criar mais componentes de plano de corte, mas se você tentar habilitar mais simultaneamente, ele ignorará a ativação. Desabilite outros planos primeiro se quiser mudar qual componente deve afetar a cena.
* Cada plano de corte afeta todos os objetos renderizados remotamente. Atualmente, não há como excluir objetos específicos ou peças de malha.
* Os planos cortados são puramente uma característica visual, eles não afetam o resultado de [consultas espaciais.](spatial-queries.md) Se você quiser lançar raios em uma malha cortada, você pode ajustar o ponto de partida do raio para estar no plano de corte. Desta forma, o raio só pode atingir partes visíveis.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Cada plano de corte ativo incorre em um pequeno custo durante a renderização. Desabilite ou exclua os planos cortados quando eles não forem necessários.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Você adiciona um plano de corte à cena criando um *CutPlaneComponent*. A localização e orientação do avião é determinada pela [entidade](../../concepts/entities.md)proprietária do componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Propriedades cutplaneComponent

As seguintes propriedades são expostas em um componente plano de corte:

* **Ativado:** Você pode desligar temporariamente os planos de corte desabilitando o componente. Aviões de corte desativados não incorrem em renderização e também não contam com o limite global de avião cortado.

* **Normal:** Especifica qual direção (+X,-X,+Y,-Y,+Z,-Z) é usada como o plano normal. Essa direção é relativa à orientação da entidade proprietária. Mova e gire a entidade proprietária para a colocação exata.

* **FadeColor** e **FadeLength:**

  Se o valor alfa do *FadeColor* não for zero, os pixels próximos ao plano de corte desaparecerão em direção à parte RGB do FadeColor. A força do canal alfa determina se ele irá desaparecer totalmente em direção à cor desbotada ou apenas parcialmente. *FadeLength* define sobre qual distância esse desbotamento ocorrerá.

## <a name="next-steps"></a>Próximas etapas

* [Renderização unilateral](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)
