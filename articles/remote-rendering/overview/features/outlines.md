---
title: Renderização de estrutura de tópicos
description: Explica como fazer renderização de contorno de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680824"
---
# <a name="outline-rendering"></a>Renderização de estrutura de tópicos

Os objetos selecionados podem ser realçados visualmente adicionando renderização de estrutura de tópicos por meio do [componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md). Este capítulo explica como os parâmetros globais para renderização de estrutura de tópicos são alterados por meio da API do cliente.

As propriedades da estrutura de tópicos são uma configuração global. Todos os objetos que usam a renderização de estrutura de tópicos usarão a mesma configuração-não é possível usar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para`OutlineSettings`

A `OutlineSettings` classe contém as configurações relacionadas às propriedades globais da estrutura de tópicos. Ele expõe os seguintes membros:

| Parâmetro      | Type    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor usada para desenhar o contorno. A parte alfa é ignorada.         |
| `PulseRateHz`    | FLOAT   | A taxa na qual a estrutura de tópicos oscila por segundo|
| `PulseIntensity` | FLOAT   | A intensidade do efeito de pulso de contorno. Deve estar entre 0,0 para nenhum Pulsing e 1,0 para Pulsing completo. A intensidade define implicitamente a opacidade mínima do contorno `MinOpacity = 1.0 - PulseIntensity`como. |

![Descreve](./media/outlines.png) o efeito de alterar o `color` parâmetro de amarelo (esquerdo) para Magenta (centro) e `pulseIntensity` de 0 a 0,8 (direita).

## <a name="example"></a>Exemplo

O código a seguir mostra um exemplo de configuração de parâmetros de estrutura de tópicos por meio da API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Desempenho

A renderização da estrutura de tópicos pode ter um impacto significativo no desempenho de renderização. Esse impacto varia com base na relação espacial de espaço na tela entre objetos selecionados e não selecionados para um determinado quadro.

## <a name="next-steps"></a>Próximas etapas

* [Componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md)
