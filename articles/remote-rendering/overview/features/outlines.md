---
title: Renderização de contorno
description: Explica como fazer renderização de contorno de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680824"
---
# <a name="outline-rendering"></a>Renderização de contorno

Os objetos selecionados podem ser destacados visualmente adicionando renderização de contorno através do [componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md). Este capítulo explica como os parâmetros globais para renderização de contorno são alterados através da API do cliente.

As propriedades do contorno são um cenário global. Todos os objetos que usam renderização de contorno usarão a mesma configuração - não é possível usar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para`OutlineSettings`

A `OutlineSettings` classe mantém as configurações relacionadas às propriedades de contorno global. Expõe os seguintes membros:

| Parâmetro      | Type    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor que é usada para desenhar o contorno. A porção alfa é ignorada.         |
| `PulseRateHz`    | FLOAT   | A taxa em que o contorno oscila por segundo|
| `PulseIntensity` | FLOAT   | A intensidade do efeito de pulso de contorno. Deve estar entre 0,0 para nenhum pulsar e 1,0 para pulsação total. A intensidade define implicitamente a opacidade mínima do contorno como `MinOpacity = 1.0 - PulseIntensity`. |

![Contornos](./media/outlines.png) O efeito `color` da mudança do parâmetro de amarelo (esquerda) `pulseIntensity` para magenta (centro) e de 0 a 0,8 (direita).

## <a name="example"></a>Exemplo

O código a seguir mostra um exemplo para definir parâmetros de contorno através da API:

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

A renderização de contorno pode ter um impacto significativo no desempenho da renderização. Esse impacto varia de acordo com a relação espacial espaço tela-espaço entre objetos selecionados e não selecionados para um determinado quadro.

## <a name="next-steps"></a>Próximas etapas

* [Componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md)
