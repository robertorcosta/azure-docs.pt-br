---
title: Reflexos do céu
description: Descreve como configurar mapas de ambiente para reflexão do céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680603"
---
# <a name="sky-reflections"></a>Reflexos do céu

Na Renderização Remota do Azure, uma textura de céu é usada para iluminar objetos de forma realista. Para aplicações de realidade aumentada, essa textura deve se assemelhar ao seu ambiente real, para fazer os objetos parecerem convincentes. Este artigo descreve como mudar a textura do céu.

> [!NOTE]
> A textura do céu também é referida como um *mapa do ambiente.* Estes termos são usados de forma intercambiável.

## <a name="object-lighting"></a>Iluminação de objeto

A renderização remota do Azure emprega *renderização fisicamente baseada* (PBR) para cálculos de iluminação realistas. Embora você possa adicionar [fontes de luz](lights.md) à sua cena, usar uma boa textura de céu tem o maior impacto.

As imagens abaixo mostram resultados de iluminação de diferentes superfícies apenas com uma textura de céu:

| Rugosidade  | 0                                        | 0,25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Não metálico  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Para obter mais informações sobre o modelo de iluminação, consulte o capítulo [de materiais.](../../concepts/materials.md)

> [!IMPORTANT]
> A renderização remota do Azure usa a textura do céu apenas para modelos de iluminação. Isso não torna o céu como um plano de fundo, uma vez que as aplicações de Realidade Aumentada já têm um fundo adequado - o mundo real.

## <a name="changing-the-sky-texture"></a>Mudando a textura do céu

Para alterar o mapa do ambiente, tudo o que você `SkyReflectionSettings`precisa fazer é carregar uma [textura](../../concepts/textures.md) e alterar a da sessão:

``` cs
LoadTextureAsync _skyTextureLoad = null;
void ChangeEnvironmentMap(AzureSession session)
{
    _skyTextureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap));

    _skyTextureLoad.Completed += (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                try
                {
                    session.Actions.SkyReflectionSettings.SkyReflectionTexture = res.Result;
                }
                catch (RRException exception)
                {
                    System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
                }
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
        };
}
```

Observe que `LoadTextureFromSASAsync` a variante é usada acima porque uma textura embutida está carregada. Em caso de carregamento de [armazenamentos blob ligados,](../../how-tos/create-an-account.md#link-storage-accounts)use a `LoadTextureAsync` variante.

## <a name="sky-texture-types"></a>Tipos de textura do céu

Você pode usar *[cubos](https://en.wikipedia.org/wiki/Cube_mapping)* e *texturas 2D* como mapas de ambiente.

Todas as texturas devem estar em um [formato de textura suportada.](../../concepts/textures.md#supported-texture-formats) Você não precisa fornecer mipmaps para texturas do céu.

### <a name="cube-environment-maps"></a>Mapas do ambiente cubo

Para referência, aqui está um mapa de cubo desembrulhado:

![Um cubo desembrulhado](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Use `LoadTextureFromSASAsync` `TextureType.CubeMap` com para carregar texturas cubemap.

### <a name="sphere-environment-maps"></a>Mapas do ambiente sphere

Ao usar uma textura 2D como um mapa de ambiente, a imagem tem que estar em [espaço de coordenadas esféricas](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Uma imagem do céu em coordenadas esféricas](media/spheremap-example.png)

Use `AzureSession.Actions.LoadTextureAsync` `TextureType.Texture2D` com para carregar mapas de ambiente esférico.

## <a name="built-in-environment-maps"></a>Mapas de ambiente incorporados

A renderização remota do Azure fornece alguns mapas de ambiente incorporados que estão sempre disponíveis. Todos os mapas de ambiente incorporados são cubos.

|Identificador                         | Descrição                                              | Ilustração                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Variedade de luzes listradas, iluminação de base interior brilhante    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Configuração de luz interior brilhante, múltiplas luzes de janela      | ![Sala da Caldeira](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes variadamente coloridas em ambiente interno de luz média  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante                     | ![Pequeno Hangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Ajuste interno escuro com contraste claro-escuro              | ![Tubulação industrialandvalve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luz ambiente diurna, luz brilhante da área da janela     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Céu escuro e terra com muitas luzes ao redor   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz solar brilhante e contraste de sombras                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luz clara do céu com iluminação terrestre moderada            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e sombra moderadamente variados                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luz do pôr do sol à noite se aproximando do crepúsculo                    | ![VenezaPôr-do-sol](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tons brilhantes, verde-exuberante e branco, solo escurecido | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Diurno com luz ambiente brilhante                 | ![Rio Winter](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Próximas etapas

* [Luzes](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta texconv de linha de comando](../../resources/tools/tex-conv.md)
