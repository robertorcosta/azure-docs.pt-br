---
title: Substituir materiais durante a conversão de modelo
description: Explica o fluxo de trabalho de substituição de material no momento da conversão
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681474"
---
# <a name="override-materials-during-model-conversion"></a>Substituir materiais durante a conversão de modelo

Durante a conversão, as configurações de material no modelo de origem são usadas para definir os [materiais de PBR](../../overview/features/pbr-materials.md) usados pelo renderizador.
Às vezes, a [conversão padrão](../../reference/material-mapping.md) não fornece os resultados desejados e você precisa fazer alterações.
Quando um modelo é convertido para uso na renderização remota do Azure, você pode fornecer um arquivo de substituição de material para personalizar como a conversão de material é feita em uma base por material.
A seção sobre como [Configurar a conversão de modelo](configure-model-conversion.md) tem instruções para declarar o nome de arquivo de substituição de material.

## <a name="the-override-file-used-during-conversion"></a>O arquivo de substituição usado durante a conversão

Como um exemplo simples, digamos que um modelo de caixa tenha um único material, chamado "default". A cor albedo precisa ser ajustada para uso em ARR.
Nesse caso, um `box_materials_override.json` arquivo pode ser criado da seguinte maneira:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

O `box_materials_override.json` arquivo é colocado no contêiner de entrada e um `ConversionSettings.json` é adicionado ao lado `box.fbx` , que informa à conversão onde encontrar o arquivo de substituição (Confira [Configurando a conversão do modelo](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Quando o modelo for convertido, as novas configurações serão aplicadas.

### <a name="color-materials"></a>Materiais de cores

O modelo de [material de cor](../../overview/features/color-materials.md) descreve uma superfície constantemente sombreada que é independente da iluminação.
Isso é útil para ativos feitos por algoritmos Photogrammetry, por exemplo.
Em arquivos de substituição de material, um material pode ser declarado como um material de cor, definindo `unlit` como `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Ignorar mapas de textura específicos

Às vezes, talvez você queira que o processo de conversão ignore mapas de textura específicos. Esse pode ser o caso quando o modelo foi gerado por uma ferramenta que gera mapas especiais não compreendidos corretamente pelo processador. Por exemplo, um "OpacityMap" que é usado para definir algo diferente de Opacity ou um modelo em que "NormalMap" é armazenado como "BumpMap". (No último caso, você deseja ignorar "NormalMap", o que fará com que o conversor use "BumpMap" como "NormalMap".)

O princípio é simples. Basta adicionar uma propriedade chamada `ignoreTextureMaps` e adicionar qualquer mapa de textura que você deseja ignorar:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Para obter a lista completa de mapas de textura que você pode ignorar, consulte o esquema JSON abaixo.

## <a name="json-schema"></a>JSON schema

O esquema JSON completo para arquivos de materiais é fornecido aqui. Com exceção de `unlit` e `ignoreTextureMaps` , as propriedades disponíveis são um subconjunto das propriedades descritas nas seções do material de [cores](../../overview/features/color-materials.md) e dos modelos de [material do PBR](../../overview/features/pbr-materials.md) .

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* [Materiais de cores](../../overview/features/color-materials.md)
* [Materiais de PBR](../../overview/features/pbr-materials.md)