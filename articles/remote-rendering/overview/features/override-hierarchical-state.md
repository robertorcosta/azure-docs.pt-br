---
title: Substituição de estado hierárquico
description: Explica o conceito de componentes de substituição de estado hierárquico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680785"
---
# <a name="hierarchical-state-override"></a>Substituição de estado hierárquico

Em muitos casos, é necessário alterar dinamicamente a aparência de partes de um [modelo](../../concepts/models.md), por exemplo, ocultar Subgráficos ou alternar partes para renderização transparente. Alterar os materiais de cada parte envolvida não é prático, pois ele requer iterar em todo o grafo de cena e gerenciar a clonagem e a atribuição de material em cada nó.

Para realizar esse caso de uso com a menor sobrecarga possível, use `HierarchicalStateOverrideComponent`o. Esse componente implementa atualizações de estado hierárquicas em ramificações arbitrárias do grafo de cena. Isso significa que um estado pode ser definido em qualquer nível no grafo de cena e vaza a hierarquia até que seja substituído por um novo estado ou aplicado a um objeto folha.

Como exemplo, considere o modelo de um carro e você deseja alternar todo o carro para ser transparente, exceto para a parte do mecanismo interno. Esse caso de uso envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó raiz do modelo e ativa a renderização transparente para o carro inteiro.
* O segundo componente é atribuído ao nó raiz do mecanismo e substitui o estado novamente, desativando explicitamente o modo de visualização.

## <a name="features"></a>Recursos

O conjunto fixo de Estados que podem ser substituídos é:

* **Hidden**: as respectivas malhas no grafo de cena são ocultas ou mostradas.
* **Cor da tonalidade**: um objeto renderizado pode ser colorido por cores com sua cor de tonalidade individual e peso de tonalidade. A imagem abaixo mostra a tonalidade de cores do rim de uma roda.
  
  ![Tonalidade de cores](./media/color-tint.png)

* **Consulte**: a geometria é renderizada semitransparentemente, por exemplo, para revelar as partes internas de um objeto. A imagem a seguir mostra todo o carro sendo renderizado no modo de visualização, exceto para o Caliper de freio vermelho:

  ![Consulte](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito ver só funciona quando o modo de *TileBasedComposition* [renderização](../../concepts/rendering-modes.md) TileBasedComposition é usado.

* **Selecionado**: a geometria é renderizada com uma [estrutura de tópicos de seleção](outlines.md).

  ![Contorno da seleção](./media/selection-outline.png)

* **DisableCollision**: a geometria é isenta de [consultas espaciais](spatial-queries.md). O sinalizador **oculto** não desativa as colisões, portanto, esses dois sinalizadores geralmente são definidos juntos.

## <a name="hierarchical-overrides"></a>Substituições hierárquicas

O `HierarchicalStateOverrideComponent` pode ser anexado em vários níveis de uma hierarquia de objetos. Como só pode haver um componente de cada tipo em uma entidade, cada `HierarchicalStateOverrideComponent` um gerencia os Estados de ocultos, confira, selecionado, tonalidade de cores e colisão.

Portanto, cada Estado pode ser definido como um de:

* `ForceOn`-o estado está habilitado para toda a malha no e abaixo deste nó
* `ForceOff`-o estado está desabilitado para todas as malhas no e abaixo deste nó
* `InheritFromParent`-o estado não é afetado por este componente de substituição

Você pode alterar os Estados diretamente ou por `SetState` meio da função:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Cor da tonalidade

A substituição da cor da tonalidade é um pouco especial, pois há um estado ligado/desligado/herdado e uma propriedade de cor de tonalidade. A parte alfa da cor da tonalidade define o peso do efeito de tonalidade: se estiver definida como 0,0, nenhuma cor de tonalidade ficará visível e, se for definido como 1,0, o objeto será renderizado com a cor de tonalidade pura. Para valores intermediários, a cor final será misturada com a cor de tonalidade. A cor da tonalidade pode ser alterada em uma base por quadro para obter uma animação colorida.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Uma instância de `HierarchicalStateOverrideComponent` si só não adiciona muita sobrecarga de tempo de execução. No entanto, é sempre uma boa prática manter o número de componentes ativos baixos. Por exemplo, ao implementar um sistema de seleção que realça o objeto selecionado, é recomendável excluir o componente quando o realce for removido. Manter os componentes com recursos neutros pode se somar rapidamente.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que a renderização padrão. Se grandes partes do grafo de cena forem alternadas para o *Ver*, com muitas camadas de geometria sendo visíveis, ela poderá se tornar um afunilamento de desempenho. O mesmo é válido para objetos com [contornos de seleção](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Próximas etapas

* [Descreve](../../overview/features/outlines.md)
* [Renderização de modos](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)
