---
title: Substituição hierárquica do estado
description: Explica o conceito de componentes de sobreposição de estado hierárquico.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680785"
---
# <a name="hierarchical-state-override"></a>Substituição hierárquica do estado

Em muitos casos, é necessário alterar dinamicamente a aparência de partes de um [modelo,](../../concepts/models.md)por exemplo, ocultar subgrafos ou mudar peças para renderização transparente. Mudar os materiais de cada parte envolvida não é prático, pois requer iterar sobre todo o gráfico da cena, e gerenciar a clonagem e a atribuição de materiais em cada nó.

Para realizar este caso de uso com `HierarchicalStateOverrideComponent`a menor sobrecarga possível, use o . Este componente implementa atualizações de estado hierárquico em ramos arbitrários do gráfico de cena. Isso significa que um estado pode ser definido em qualquer nível no gráfico de cena e ele escorre pela hierarquia até que seja substituído por um novo estado, ou aplicado a um objeto de folha.

Como exemplo, considere o modelo de um carro e você quer trocar todo o carro para ser transparente, exceto para a parte interna do motor. Este caso de uso envolve apenas duas instâncias do componente:

* O primeiro componente é atribuído ao nó raiz do modelo e liga a renderização transparente para todo o carro.
* O segundo componente é atribuído ao nó raiz do motor e substitui o estado novamente desligando explicitamente o modo de passagem.

## <a name="features"></a>Recursos

O conjunto fixo de estados que podem ser substituídos são:

* **Oculta**: As respectivas barras no gráfico de cena estão escondidas ou mostradas.
* **Cor de lata**: Um objeto renderizado pode ser colorido com sua cor de estacção individual e peso de estaca. A imagem abaixo mostra coloração da borda de uma roda.
  
  ![Color Tint](./media/color-tint.png)

* **Transparente**: A geometria é renderizada semi-transparente, por exemplo, para revelar as partes internas de um objeto. A imagem a seguir mostra todo o carro sendo renderizado no modo de passagem, exceto pela pinça de freio vermelha:

  ![See-Through](./media/see-through.png)

  > [!IMPORTANT]
  > O efeito see-through só funciona quando o modo de [renderização](../../concepts/rendering-modes.md) *TileBasedComposition* é usado.

* **Selecionado:** A geometria é renderizada com um [esboço de seleção](outlines.md).

  ![Contorno de Seleção](./media/selection-outline.png)

* **DisableCollision**: A geometria está isenta de [consultas espaciais](spatial-queries.md). A bandeira **Oculta** não desliga colisões, então essas duas bandeiras são frequentemente montadas.

## <a name="hierarchical-overrides"></a>Substituições hierárquicas

O `HierarchicalStateOverrideComponent` pode ser anexado em vários níveis de uma hierarquia de objetos. Como só pode haver um componente de cada `HierarchicalStateOverrideComponent` tipo em uma entidade, cada um gerencia os estados para ocultação, ver-through, selecionado, color tint e colisão.

Portanto, cada estado pode ser definido como um de:

* `ForceOn`- o estado está habilitado para toda a malha sobre e abaixo deste nó
* `ForceOff`- o Estado está desativado para todas as meshes sobre e abaixo deste nó
* `InheritFromParent`- o estado não é afetado por este componente de substituição

Você pode alterar estados `SetState` diretamente ou através da função:

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>Cor de lata

A substituição de cor de estante é ligeiramente especial, na qual há um estado on/off/herdar e uma propriedade de cor de estaca. A porção alfa da cor de estante define o peso do efeito de coloração: Se definido como 0,0, nenhuma cor de estaca é visível e se definido para 1.0 o objeto será renderizado com cor de coloração pura. Para valores intermediários, a cor final será misturada com a cor de estat. A cor de estacção pode ser alterada por quadro para obter uma animação de cores.

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Uma instância `HierarchicalStateOverrideComponent` de si mesmo não adiciona muita sobrecarga de tempo de execução. No entanto, é sempre uma boa prática manter o número de componentes ativos baixo. Por exemplo, ao implementar um sistema de seleção que destaca o objeto escolhido, recomenda-se excluir o componente quando o destaque for removido. Manter os componentes ao redor com características neutras pode rapidamente somar.

A renderização transparente coloca mais carga de trabalho nas GPUs do servidor do que a renderização padrão. Se grandes partes do gráfico de cena forem comutadas para *visíveis,* com muitas camadas de geometria sendo visíveis, pode se tornar um gargalo de desempenho. O mesmo é válido para objetos com [contornos de seleção](../../overview/features/outlines.md#performance).

## <a name="next-steps"></a>Próximas etapas

* [Descreve](../../overview/features/outlines.md)
* [Modos de renderização](../../concepts/rendering-modes.md)
* [Consultas espaciais](../../overview/features/spatial-queries.md)
