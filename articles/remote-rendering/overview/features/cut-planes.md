---
title: Recortar planos
description: Explica o que são planos de recorte e como usá-los
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 8075d9cd4530bafb12a338830baf0fe22eb03bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681019"
---
# <a name="cut-planes"></a>Recortar planos

Um *plano recortado* é um recurso visual que corta pixels em um lado de um plano virtual, revelando o interior das [malhas](../../concepts/meshes.md).
A imagem abaixo demonstra o efeito. A esquerda mostra a malha original, à direita, que pode ser examinada dentro da malha:

![Recortar plano](./media/cutplane-1.png)

## <a name="limitations"></a>Limitações

* Por enquanto, a renderização remota do Azure dá suporte a um **máximo de oito planos de recorte ativos**. Você pode criar mais componentes de plano recortados, mas se tentar habilitar mais simultaneamente, ele ignorará a ativação. Desabilite os outros planos primeiro se desejar alternar qual componente deve afetar a cena.
* Cada plano de recorte afeta todos os objetos renderizados remotamente. Atualmente, não há nenhuma maneira de excluir objetos específicos ou partes de malha.
* Os planos de recorte são puramente um recurso Visual, eles não afetam o resultado de [consultas espaciais](spatial-queries.md). Se você quiser Ray Cast em uma malha aberta, você pode ajustar o ponto inicial do raio para estar no plano de recorte. Dessa forma, o raio só pode atingir as partes visíveis.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Cada plano de recorte ativo gera um pequeno custo durante a renderização. Desabilite ou exclua planos de recorte quando eles não forem necessários.

## <a name="cutplanecomponent"></a>CutPlaneComponent

Você adiciona um plano de recorte à cena criando um *CutPlaneComponent*. O local e a orientação do plano são determinados pela [entidade](../../concepts/entities.md)do proprietário do componente.

```cs
void CreateCutPlane(AzureSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Actions.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>Propriedades de CutPlaneComponent

As propriedades a seguir são expostas em um componente de plano de recorte:

* **Habilitado:** Você pode desativar temporariamente os planos de corte desabilitando o componente. Os planos de corte desabilitados não incorrem em sobrecarga de renderização e também não contam com o limite do plano de recorte global.

* **Normal:** Especifica qual direção (+ X,-X, + Y,-Y, + Z,-Z) é usada como o plano normal. Essa direção é relativa à orientação da entidade do proprietário. Mova e gire a entidade do proprietário para posicionamento exato.

* **FadeColor** e **FadeLength:**

  Se o valor alfa de *FadeColor* for diferente de zero, os pixels próximos ao plano de recorte ficarão em direção à parte RGB de FadeColor. A força do canal alfa determina se ele ficará totalmente esmaecido em direção à cor de esmaecimento ou apenas parcialmente. *FadeLength* define em qual distância esse fade será realizado.

## <a name="next-steps"></a>Próximas etapas

* [Renderização de lado único](single-sided-rendering.md)
* [Consultas espaciais](spatial-queries.md)
