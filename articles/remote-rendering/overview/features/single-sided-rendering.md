---
title: Renderização unilateral
description: Descreve configurações de renderização de um lado e casos de uso
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 34ee5d4978c6476da407cde33598a5713177078e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682007"
---
# <a name="single-sided-rendering"></a>Renderização unilateral

A maioria dos renderizadores usa [abate back-face](https://en.wikipedia.org/wiki/Back-face_culling) para melhorar o desempenho. No entanto, quando as meshes são cortadas com [planos de corte,](cut-planes.md)os usuários muitas vezes olharão para o lado de trás dos triângulos. Se esses triângulos forem eliminados, o resultado não parece convincente.

A maneira de prevenir esse problema de forma confiável é tornar triângulos *de dupla lateral*. Como não usar o abate back-face tem implicações de desempenho, por padrão, a renderização remota do Azure só muda para renderização dupla face para as fundações que estão se cruzando com um plano de corte.

A *configuração de renderização unilateral* permite personalizar esse comportamento.

> [!CAUTION]
> A configuração de renderização unilateral é um recurso experimental. Pode ser removido novamente no futuro. Por favor, não altere a configuração padrão, a menos que realmente resolva um problema crítico em seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

A configuração de renderização unilateral só tem um efeito para `opaqueMaterialDefaultSidedness` as meshes que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a opção definida como `SingleSided`. Por padrão, esta `DoubleSided`opção é definida como .

## <a name="single-sided-rendering-setting"></a>Configuração de renderização unilateral

Existem três modos diferentes:

**Normal:** Neste modo, as meshes são sempre renderizadas à medida que são convertidas. Isso significa que as `opaqueMaterialDefaultSidedness` meshes convertidas com conjunto para `SingleSided` sempre serão renderizadas com abate back-face ativado, mesmo quando eles cruzam um plano de corte.

**DynamicDoubleSiding:** Neste modo, quando um plano de corte cruza uma malha, ele é automaticamente comutado para renderização de dupla lateral. Este modo é o modo padrão.

**Sempre duplamente:** Força toda a geometria unilateral a ser feita de lado duplo o tempo todo. Este modo é exposto principalmente para que você possa facilmente comparar o impacto de desempenho entre renderização unilateral e dupla.

A alteração das configurações de renderização de um lado pode ser feita da seguinte forma:

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

* [Cortar aviões](cut-planes.md)
* [Configuração da conversão do modelo](../../how-tos/conversion/configure-model-conversion.md)
