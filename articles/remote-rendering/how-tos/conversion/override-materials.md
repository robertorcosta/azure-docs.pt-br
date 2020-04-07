---
title: Materiais de substituição durante a conversão do modelo
description: Explica o material que sobrepor o fluxo de trabalho no tempo de conversão
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 90653db4c572877a728964851a99beebf2e823a4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681474"
---
# <a name="override-materials-during-model-conversion"></a>Materiais de substituição durante a conversão do modelo

Durante a conversão, as configurações do material no modelo de origem são utilizadas para definir os [materiais PBR utilizados](../../overview/features/pbr-materials.md) pelo renderizador.
Às [vezes,](../../reference/material-mapping.md) a conversão padrão não dá os resultados desejados e você precisa fazer alterações.
Quando um modelo é convertido para uso na renderização remota do Azure, você pode fornecer um arquivo de substituição de material para personalizar como a conversão de material é feita por material.
A seção sobre [a configuração da conversão do modelo](configure-model-conversion.md) tem instruções para declarar o nome do arquivo de substituição de material.

## <a name="the-override-file-used-during-conversion"></a>O arquivo de substituição usado durante a conversão

Como um exemplo simples, digamos que um modelo de caixa tenha um único material, chamado "Default". A cor albedo precisa ser ajustada para uso em ARR.
Neste caso, `box_materials_override.json` um arquivo pode ser criado da seguinte forma:

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

O `box_materials_override.json` arquivo é colocado no recipiente `ConversionSettings.json` de `box.fbx`entrada, e um é adicionado ao lado , que informa a conversão onde encontrar o arquivo de substituição (ver [Configuração da conversão do modelo](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

Quando o modelo for convertido, as novas configurações serão aplicadas.

### <a name="color-materials"></a>Materiais de cor

O modelo [de material colorido](../../overview/features/color-materials.md) descreve uma superfície constantemente sombreada que é independente da iluminação.
Isso é útil para ativos feitos por algoritmos de fotogrametria, por exemplo.
Em arquivos de substituição de material, um material pode `unlit` ser `true`declarado como um material de cor por definição de .

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

Às vezes, você pode querer que o processo de conversão ignore mapas de textura específicos. Esse pode ser o caso quando seu modelo foi gerado por uma ferramenta que gera mapas especiais não compreendidos corretamente pelo renderizador. Por exemplo, um "OpacityMap" que é usado para definir algo diferente da opacidade, ou um modelo onde o "NormalMap" é armazenado como "BumpMap". (Neste último caso, você deseja ignorar "NormalMap", o que fará com que o conversor use "BumpMap" como "NormalMap".)

O princípio é simples. Basta adicionar uma `ignoreTextureMaps` propriedade chamada e adicionar qualquer mapa de textura que você queira ignorar:

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

O esquema JSON completo para arquivos de materiais é dado aqui. Com exceção `unlit` `ignoreTextureMaps`e , as propriedades disponíveis são um subconjunto das propriedades descritas nas seções sobre o [material de cor](../../overview/features/color-materials.md) e modelos de material [PBR.](../../overview/features/pbr-materials.md)

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

* [Materiais de cor](../../overview/features/color-materials.md)
* [Materiais PBR](../../overview/features/pbr-materials.md)