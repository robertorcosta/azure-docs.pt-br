---
title: Reflexos do céu
description: Descreve como configurar mapas de ambiente para reflexos de céu
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: a193fdf82ed5e9a785c6c01db4931fb05e9aaac7
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205737"
---
# <a name="sky-reflections"></a>Reflexos do céu

No Azure Remote Rendering, uma textura de céu é usada para iluminar objetos de modo realista. Para aplicativos de realidade aumentada, essa textura deve se parecer com o ambiente real, para que os objetos pareçam convincentes. Este artigo descreve como mudar a textura do céu.

> [!NOTE]
> A textura do céu também é chamada de *mapa de ambiente*. Esses termos são usados de maneira intercambiável.

## <a name="object-lighting"></a>Iluminação do objeto

O Azure Remote Rendering emprega *renderização baseada em física* (PBR) para cálculos de iluminação realística. Embora você possa adicionar [fontes de luz](lights.md) à sua cena, usar uma boa textura do céu gera um impacto maior.

As imagens abaixo mostram os resultados da iluminação de superfícies diferentes apenas com uma textura de céu:

| Irregularidade  | 0                                        | 0,25                                          | 0.5                                          | 0,75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| Não metal  | ![Interelétrica, áspero = 0](media/dielectric-0.png)   | ![Interelétrica, áspero = 0,25](media/dielectric-0.25.png)  | ![Interelétrica, irregularidade = 0,5](media/dielectric-0.5.png)  | ![Interelétrica, áspero = 0,75](media/dielectric-0.75.png)  | ![Interelétrica, áspero = 1](media/dielectric-1.png)  |
| Metal      | ![Metal, áspero = 0](media/metallic-0.png)  | ![Metal, áspero = 0,25](media/metallic-0.25.png)    | ![Metal, áspero = 0,5](media/metallic-0.5.png)    | ![Metal, áspero = 0,75](media/metallic-0.75.png)    | ![Metal, áspero = 1](media/metallic-1.png)    |

Para mais informações sobre o modelo de iluminação, veja o capítulo de [materiais](../../concepts/materials.md).

> [!IMPORTANT]
> O Azure Remote Rendering usa a textura de céu somente para modelos de iluminação. Ele não renderiza o céu como plano de fundo, já que os aplicativos de realidade aumentada já têm um plano de fundo adequado: o ambiente físico.

## <a name="changing-the-sky-texture"></a>Mudar a textura do céu

Para mudar o mapa do ambiente, tudo o que você precisa fazer é [carregar uma textura](../../concepts/textures.md) e mudar `SkyReflectionSettings` da sessão:

```cs
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

```cpp
void ChangeEnvironmentMap(ApiHandle<AzureSession> session)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUrl = "builtin://VeniceSunset";
    ApiHandle<LoadTextureAsync> skyTextureLoad = *session->Actions()->LoadTextureFromSASAsync(params);

    skyTextureLoad->Completed([&](ApiHandle<LoadTextureAsync> res)
        {
            if (res->GetIsRanToCompletion())
            {
                ApiHandle<SkyReflectionSettings> settings = session->Actions()->GetSkyReflectionSettings();
                settings->SetSkyReflectionTexture(res->GetResult());
            }
            else
            {
                printf("Texture loading failed!\n");
            }
        });
}

```

Observe que a variante `LoadTextureFromSASAsync` é usada acima porque uma textura incorporada está carregada. No caso de carregamento de [armazenamentos de blob vinculados](../../how-tos/create-an-account.md#link-storage-accounts), use a variante `LoadTextureAsync`.

## <a name="sky-texture-types"></a>Tipos de texturas do céu

É possível usar tanto *[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* quanto *texturas 2D* como mapas de ambiente.

Todas as texturas precisam estar em um [formato de textura compatível](../../concepts/textures.md#supported-texture-formats). Não é preciso inserir mipmaps para texturas de céu.

### <a name="cube-environment-maps"></a>Mapas de ambiente de cubo

Para referência, veja um cubemap não encapsulado:

![Um cubemap não encapsulado](media/Cubemap-example.png)

Use `AzureSession.Actions.LoadTextureAsync`/ `LoadTextureFromSASAsync` com `TextureType.CubeMap` para carregar texturas de cubemap.

### <a name="sphere-environment-maps"></a>Mapas de ambiente de esfera

Ao usar uma textura 2D como um mapa de ambiente, a imagem deve estar no [espaço esférico de coordenadas](https://en.wikipedia.org/wiki/Spherical_coordinate_system).

![Uma imagem do céu em coordenadas esféricas](media/spheremap-example.png)

Use `AzureSession.Actions.LoadTextureAsync` com `TextureType.Texture2D` para carregar mapas de ambiente esféricos.

## <a name="built-in-environment-maps"></a>Mapas de ambiente integrados

O Azure Remote Rendering fornece alguns mapas de ambiente integrados que estão sempre disponíveis. Todos os mapas de ambiente integrados são cubemaps.

|Identificador                         | Descrição                                              | Ilustração                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | Variedade de luzes de faixa, iluminação de base brilhante em ambientes internos    | ![Skybox do autoshop usado para leve um objeto](media/autoshop.png)
|builtin://BoilerRoom               | Configuração de luz brilhante em ambiente interno, luzes de várias janelas      | ![BoilerRoom Skybox costumava acender um objeto](media/boiler-room.png)
|builtin://ColorfulStudio           | Luzes coloridas variadas de acordo com a configuração de mídia óptica do meio  | ![ColorfulStudio Skybox costumava acender um objeto](media/colorful-studio.png)
|builtin://Hangar                   | Luz ambiente moderadamente brilhante de hall                     | ![SmallHangar Skybox costumava acender um objeto](media/hangar.png)
|builtin://IndustrialPipeAndValve   | Configuração de ambiente interno tremeluzente com contraste escuro              | ![IndustrialPipeAndValve Skybox costumava acender um objeto](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | Luz ambiente em cômodo durante o dia, luz brilhante em área de janela     | ![Lebombo Skybox costumava acender um objeto](media/lebombo.png)
|builtin://SataraNight              | Céu de noite escura e solo com muitas luzes ao redor   | ![SataraNight Skybox costumava acender um objeto](media/satara-night.png)
|builtin://SunnyVondelpark          | Luz do sol brilhante e contraste da sombra                      | ![SunnyVondelpark Skybox costumava acender um objeto](media/sunny-vondelpark.png)
|builtin://Syferfontein             | Luz no céu clara com iluminação moderada no solo            | ![Syferfontein Skybox costumava acender um objeto](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | Sol e sombra moderadamente variados                         | ![TearsOfSteelBridge Skybox costumava acender um objeto](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | Luz do pôr do sol se aproximando do anoitecer                    | ![VeniceSunset Skybox costumava acender um objeto](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | Tom claro, verde exuberante e tons de luz branca, solo escurecido | ![WhippleCreekRegionalPark Skybox costumava acender um objeto](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | Dia com luz ambiente brilhante no solo                 | ![WinterRiver Skybox costumava acender um objeto](media/winter-river.png)
|builtin://DefaultSky               | O mesmo que TearsOfSteelBridge                               | ![DefaultSky Skybox costumava acender um objeto](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>Documentação da API

* [Propriedade C# Remotemanager. SkyReflectionSettings](/dotnet/api/microsoft.azure.remoterendering.remotemanager.skyreflectionsettings)
* [C++ Remotomanager:: SkyReflectionSettings ()](/cpp/api/remote-rendering/remotemanager#skyreflectionsettings)

## <a name="next-steps"></a>Próximas etapas

* [Luzes](../../overview/features/lights.md)
* [Materiais](../../concepts/materials.md)
* [Texturas](../../concepts/textures.md)
* [A ferramenta de linha de comando TexConv](../../resources/tools/tex-conv.md)