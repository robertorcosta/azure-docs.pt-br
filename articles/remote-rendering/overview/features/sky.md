---
title: Reflexos do céu
description: Descreve como configurar mapas de ambiente para reflexos de céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 7316df7bcf78e3a154510e69116c288b2b293d4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680603"
---
# <a name="sky-reflections"></a>Reflexos do céu

Na renderização remota do Azure, uma textura céu é usada para leve os objetos de forma realista. Para aplicativos de realidade aumentada, essa textura deve se parecer com os arredores do mundo real, para que os objetos pareçam convincentes. Este artigo descreve como alterar a textura do céu.

> [!NOTE]
> A textura do céu também é conhecida como um *mapa de ambiente*. Esses termos são usados de maneira intercambiável.

## <a name="object-lighting"></a>Iluminação do objeto

A renderização remota do Azure emprega o PBR ( *processamento com base fisicamente* ) para cálculos de iluminação realísticas. Embora você possa adicionar [fontes leves](lights.md) à sua cena, usar uma boa textura do céu tem o maior impacto.

As imagens abaixo mostram os resultados da iluminação de superfícies diferentes apenas com uma textura céu:

| Áspero  | 0                                        | 0,25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Não metal  | ![Dielectric0](media/dielectric-0.png)   | ![GreenPointPark](media/dielectric-0.25.png)  | ![GreenPointPark](media/dielectric-0.5.png)  | ![GreenPointPark](media/dielectric-0.75.png)  | ![GreenPointPark](media/dielectric-1.png)  |
| Metal      | ![GreenPointPark](media/metallic-0.png)  | ![GreenPointPark](media/metallic-0.25.png)    | ![GreenPointPark](media/metallic-0.5.png)    | ![GreenPointPark](media/metallic-0.75.png)    | ![GreenPointPark](media/metallic-1.png)    |

Para obter mais informações sobre o modelo de iluminação, consulte o capítulo [materiais](../../concepts/materials.md) .

> [!IMPORTANT]
> A renderização remota do Azure usa a textura do céu somente para modelos de iluminação. Ele não renderiza o céu como plano de fundo, já que os aplicativos de realidade aumentada já têm um plano de fundo adequado – o mundo real.

## <a name="changing-the-sky-texture"></a>Alterando a textura do céu

Para alterar o mapa do ambiente, tudo o que você precisa fazer é [carregar uma textura](../../concepts/textures.md) e alterar a `SkyReflectionSettings`sessão:

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

Observe que a `LoadTextureFromSASAsync` variante é usada acima porque uma textura interna é carregada. No caso de carregamento de [armazenamentos de blob vinculados](../../how-tos/create-an-account.md#link-storage-accounts), use a variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Tipos de texturas do céu

Você pode usar *texturas* *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* e 2D como mapas de ambiente.

Todas as texturas precisam estar em um [formato de textura com suporte](../../concepts/textures.md#supported-texture-formats). Você não precisa fornecer mipmaps para texturas de céu.

### <a name="cube-environment-maps"></a>Mapas de ambiente de cubo

Para referência, aqui está um cubemap não encapsulado:

![Um cubemap não encapsulado](media/Cubemap-example.png)

`AzureSession.Actions.LoadTextureAsync` /  Use `LoadTextureFromSASAsync` with `TextureType.CubeMap` para carregar texturas cubemap.

### <a name="sphere-environment-maps"></a>Mapas de ambiente de esfera

Ao usar uma textura 2D como um mapa de ambiente, a imagem deve estar no [espaço de coordenadas esférico](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Uma imagem do céu em coordenadas esféricas](media/spheremap-example.png)

Use `AzureSession.Actions.LoadTextureAsync` com `TextureType.Texture2D` para carregar mapas de ambiente esféricos.

## <a name="built-in-environment-maps"></a>Mapas de ambiente internos

A renderização remota do Azure fornece alguns mapas de ambiente internos que estão sempre disponíveis. Todos os mapas de ambiente internos são cubemaps.

|Identificador                         | Descrição                                              | Ilustração                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Variedade de luzes de faixa, iluminação de base brilhante de raio    | ![Autoshop](media/autoshop.png)
|builtin://BoilerRoom               | Configuração de luz de interno brilhante, luzes de janela múltiplas      | ![BoilerRoom](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes coloridas variadas de acordo com a configuração de mídia óptica média  | ![ColorfulStudio](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante de Hall                     | ![SmallHangar](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Configuração de interno Dim com contraste escuro              | ![IndustrialPipeAndValve](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Quarto de dia da sala de temperatura, luz de área de janela brilhante     | ![Lebombo](media/lebombo.png)
|builtin://SataraNight              | Céu da noite escura e terra com muitas luzes ao redor   | ![SataraNight](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz brilhante e contraste da sombra                      | ![SunnyVondelpark](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luz céu clara com iluminação de aterramento moderada            | ![Syferfontein](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e tonalidade moderadamente variados                         | ![TearsOfSteelBridge](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | A luz do sol da noite está se aproximando do sol                    | ![VeniceSunset](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tom claro, Lush-verde e branco, terra esmaecida | ![WhippleCreekRegionalPark](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dia com luz de aterramento ambiente brilhante                 | ![WinterRiver](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![DefaultSky](media/tears-of-steel-bridge.png)

## <a name="next-steps"></a>Próximas etapas

* [Luzes](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta de linha de comando TexConv](../../resources/tools/tex-conv.md)
