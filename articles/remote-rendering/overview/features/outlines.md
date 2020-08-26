---
title: Renderização de estrutura de tópicos
description: Explica como fazer renderização de contorno de seleção
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 566bbca4c9b5c2f2a96ad231d69dda94374c7db2
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892993"
---
# <a name="outline-rendering"></a>Renderização de estrutura de tópicos

Os objetos selecionados podem ser realçados visualmente adicionando renderização de contorno através do [Componente de substituição de estado hierárquico](../../overview/features/override-hierarchical-state.md). Este capítulo explica como os parâmetros globais para renderização de contornos são alterados através da API do cliente.

As propriedades de contorno são uma configuração global. Todos os objetos que usam a renderização de contorno usarão a mesma configuração, não é possível usar uma cor de contorno por objeto.

## <a name="parameters-for-outlinesettings"></a>Parâmetros para `OutlineSettings`

A classe `OutlineSettings` contém as configurações relacionadas às propriedades globais de contorno. Ela apresenta os seguintes membros:

| Parâmetro      | Type    | Descrição                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | A cor usada para desenhar o contorno. A parte alfa é ignorada.         |
| `PulseRateHz`    | FLOAT   | A taxa de oscilação por segundo do contorno|
| `PulseIntensity` | FLOAT   | A intensidade do efeito de pulso do contorno. Deve estar entre 0,0 para nenhuma pulsação e 1,0 para pulsação total. A intensidade define implicitamente a opacidade mínima do contorno como `MinOpacity = 1.0 - PulseIntensity`. |

![Contornos](./media/outlines.png) O efeito de alterar o parâmetro `color` de amarelo (esquerda) para magenta (centro) e `pulseIntensity` de 0 a 0,8 (direita).

## <a name="example"></a>Exemplo

O código a seguir mostra um exemplo de configuração de parâmetros de contorno através da API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Desempenho

A renderização do contorno pode ter um impacto significativo no desempenho da renderização. Esse impacto varia conforme a relação espacial de espaço na tela entre objetos selecionados e não selecionados para um determinado quadro.

## <a name="next-steps"></a>Próximas etapas

* [Componente de substituição do estado hierárquico](../../overview/features/override-hierarchical-state.md)
