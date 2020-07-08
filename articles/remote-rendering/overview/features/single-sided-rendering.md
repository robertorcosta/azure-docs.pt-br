---
title: Renderização de um lado
description: Descreve configurações de renderização de um lado e casos de uso
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.openlocfilehash: 1a9f80166e47b17644b37d4bc9b93e1abefe3432
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022753"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided":::tratamento

A maioria dos renderizadores usa [rejeição trivial](https://en.wikipedia.org/wiki/Back-face_culling) para melhorar o desempenho. No entanto, quando as malhas estão cortadas abertas com [planos de corte](cut-planes.md), os usuários costumam examinar o verso dos triângulos. Se esses triângulos forem descartados, o resultado não parecerá convincente.

A maneira de evitar esse problema de modo confiável é renderizar triângulos *em dois lados*. Não usar a rejeição trivial tem implicações de desempenho, por isso, o Azure Remote Rendering muda para renderização em dois lados, por padrão, apenas no caso de malhas que fazem interseção com um plano de recorte.

A configuração de * :::no-loc text="single-sided"::: renderização* permite que você personalize esse comportamento.

> [!CAUTION]
> A :::no-loc text="single-sided"::: configuração de renderização é um recurso experimental. Ele pode ser removido novamente no futuro. Não mude a configuração padrão, a menos que ela realmente resolva um problema crítico em seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

A :::no-loc text="single-sided"::: configuração de renderização só tem um efeito para malhas que foram [convertidas](../../how-tos/conversion/configure-model-conversion.md) com a `opaqueMaterialDefaultSidedness` opção definida como `SingleSided` . Por padrão, essa opção fica definida como `DoubleSided`.

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided":::configuração de renderização

Há três modos diferentes:

**Normal:** nesse modo, as malhas são sempre renderizadas à medida que são convertidas. Isso significa que as malhas convertidas com `opaqueMaterialDefaultSidedness` definido como `SingleSided` sempre serão renderizadas com a rejeição trivial habilitada, mesmo quando elas fazem intersecção com um plano de corte.

**DynamicDoubleSiding:** nesse modo, quando um plano de corte cruza uma malha, ele muda automaticamente para renderização em dois lados. Esse modo é o padrão.

**AlwaysDoubleSided:** força para que toda a geometria de um lado seja renderizada duas vezes. Esse modo é exposto principalmente para que você possa comparar facilmente o impacto no desempenho entre :::no-loc text="single-sided"::: e :::no-loc text="double-sided"::: renderizar.

A alteração das :::no-loc text="single-sided"::: configurações de renderização pode ser feita da seguinte maneira:

```cs
void ChangeSingleSidedRendering(AzureSession session)
{
    SingleSidedSettings settings = session.Actions.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<AzureSession> session)
{
    ApiHandle<SingleSidedSettings> settings = *session->Actions()->SingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->Mode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->Mode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="next-steps"></a>Próximas etapas

* [Recortar planos](cut-planes.md)
* [Configurar a conversão de modelo](../../how-tos/conversion/configure-model-conversion.md)
