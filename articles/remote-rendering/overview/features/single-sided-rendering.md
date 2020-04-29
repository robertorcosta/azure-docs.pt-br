---
title: Renderização de um lado
description: Descreve configurações de renderização única e casos de uso
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682007"
---
# <a name="single-sided-rendering"></a>Renderização de um lado

A maioria dos renderizadores usa a [remoção de frente e volta](https://en.wikipedia.org/wiki/Back-face_culling) para melhorar o desempenho. No entanto, quando as malhas são recortadas abertas com [planos de corte](cut-planes.md), os usuários costumam examinar o verso dos triângulos. Se esses triângulos forem descartados, o resultado não parecerá convincente.

A maneira de evitar esse problema de forma confiável é renderizar os triângulos *duas vezes*. Como não usar a remoção de face para frente tem implicações de desempenho, por padrão, a renderização remota do Azure alterna apenas para renderização dupla para malhas que fazem interseção com um plano de recorte.

A configuração de *renderização única* permite que você personalize esse comportamento.

> [!CAUTION]
> A configuração de renderização de lado único é um recurso experimental. Ele pode ser removido novamente no futuro. Não altere a configuração padrão, a menos que ela realmente resolva um problema crítico em seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

A configuração de renderização única tem um efeito apenas para as malhas que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a `opaqueMaterialDefaultSidedness` opção definida como `SingleSided`. Por padrão, essa opção é definida `DoubleSided`como.

## <a name="single-sided-rendering-setting"></a>Configuração de renderização de lado único

Há três modos diferentes:

**Normal:** Nesse modo, as malhas são sempre renderizadas à medida que são convertidas. Isso significa que as malhas `opaqueMaterialDefaultSidedness` convertidas com Set como `SingleSided` serão sempre renderizadas com a remoção de face de fundo habilitada, mesmo quando elas interseccionam um plano de recorte.

**DynamicDoubleSiding:** Nesse modo, quando um plano de recorte cruza uma malha, ele é alternado automaticamente para renderização de lado duplo. Esse modo é o modo padrão.

**AlwaysDoubleSided:** Força que toda a geometria de lado único seja renderizada duas vezes. Esse modo é exposto principalmente para que você possa comparar facilmente o impacto de desempenho entre renderização única e dupla.

A alteração das configurações de renderização de lado único pode ser feita da seguinte maneira:

``` cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

## <a name="next-steps"></a>Próximas etapas

* [Recortar planos](cut-planes.md)
* [Configurando a conversão de modelo](../../how-tos/conversion/configure-model-conversion.md)
