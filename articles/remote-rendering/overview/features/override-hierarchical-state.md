---
title: Substituição de estado hierárquico
description: Explica o conceito de componentes de substituição de estado hierárquico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 6a8f956a692fd857fc864ca0132acc85d9942bbd
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891803"
---
# <a name="hierarchical-state-override"></a>Substituição de estado hierárquico

Em muitos casos, é necessário alterar dinamicamente a aparência de partes de um [modelo](../../concepts/models.md), por exemplo, ocultar subgráficos ou alternar partes para renderização transparente. A alteração dos materiais de cada parte envolvida não é prática, pois requer iteração em todo o grafo de cena e o gerenciamento da clonagem e da atribuição de material em cada nó.

Para realizar esse caso de uso com a menor sobrecarga possível, use o `HierarchicalStateOverrideComponent`. Esse componente implementa atualizações de estado hierárquicas em ramificações arbitrárias do grafo de cena. Isso significa que um estado pode ser definido em qualquer nível no grafo de cena e perpassa a hierarquia até que seja substituído por um novo estado ou aplicado a um objeto folha.

Como exemplo, considere o modelo de um carro, o qual você deseja alternar completamente para ser transparente, exceto a parte interna do motor. Esse caso de uso envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó raiz do modelo e ativa a renderização transparente para o carro inteiro.
* O segundo componente é atribuído ao nó raiz do motor e substitui o estado novamente, desativando de forma explícita o modo de transparência.

## <a name="features"></a>Recursos

O conjunto fixo de estados que podem ser substituídos é:

* **`Hidden`**: As respectivas malhas no grafo de cena são ocultas ou mostradas.
* **`Tint color`**: Um objeto renderizado pode ser colorido por cores com sua cor de tonalidade individual e peso de tonalidade. A imagem abaixo mostra a coloração do aro de uma roda.
  
  ![Color Tint](./media/color-tint.png)

* **`See-through`**: A geometria é renderizada semitransparentemente, por exemplo, para revelar as partes internas de um objeto. A seguinte imagem mostra todo o carro sendo renderizado no modo de transparência, exceto a pinça de freio vermelha:

  ![See-Through](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito de transparência só funciona quando é usado o [modo de renderização](../../concepts/rendering-modes.md) *TileBasedComposition*.

* **`Selected`**: A geometria é renderizada com uma [estrutura de tópicos de seleção](outlines.md).

  ![Contorno de seleção](./media/selection-outline.png)

* **`DisableCollision`**: A geometria é isenta de [consultas espaciais](spatial-queries.md). O **`Hidden`** sinalizador não afeta o sinalizador de estado de colisão, portanto, esses dois sinalizadores geralmente são definidos juntos.

## <a name="hierarchical-overrides"></a>Substituições hierárquicas

O `HierarchicalStateOverrideComponent` pode ser anexado em vários níveis de uma hierarquia de objetos. Como só pode haver um componente de cada tipo em uma entidade, cada `HierarchicalStateOverrideComponent` gerencia os estados de hidden, see-through, selected, color tint e collision.

Dessa forma, cada estado pode ser definido como:

* `ForceOn` – o estado está habilitado para toda a malha nesse nó e abaixo dele
* `ForceOff` – o estado está desabilitado para todas as malhas nesse nó e abaixo dele
* `InheritFromParent` – o estado não é afetado por esse componente de substituição

Os estados podem ser alterados diretamente ou por meio da função `SetState`:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Cor de tonalidade

A `tint color` substituição é um pouco especial, pois há um estado ligado/desligado/herdado e uma propriedade de cor de tonalidade. A parte alfa da cor da tonalidade define o peso do efeito de tonalidade: se definido como 0,0, nenhuma cor de tonalidade será visível e, se for definido como 1,0, o objeto será renderizado com a cor de tonalidade pura. Para valores intermediários, a cor final será misturada com a cor de tonalidade. A cor da tonalidade pode ser alterada quadro a quadro para obter uma animação colorida.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Uma instância do `HierarchicalStateOverrideComponent` em si não adiciona muita sobrecarga de runtime. No entanto, é sempre uma boa prática manter um número baixo de componentes ativos. Por exemplo, ao implementar um sistema de seleção que realça o objeto selecionado, é recomendável excluir o componente quando o realce é removido. Manter os componentes com recursos neutros pode gerar acúmulo rapidamente.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que a renderização padrão. Se grandes partes do grafo de cena forem alternadas para *see-through*, com muitas camadas de geometria ficando visíveis, ele poderá se tornar um gargalo de desempenho. O mesmo se dá para objetos com [contornos de seleção](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Próximas etapas

* [Contornos](../../overview/features/outlines.md)
* [Modos de renderização](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)
