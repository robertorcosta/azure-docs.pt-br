---
title: Obtenha informações sobre um modelo convertido
description: Descrição de todos os parâmetros de conversão do modelo
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681513"
---
# <a name="get-information-about-a-converted-model"></a>Obtenha informações sobre um modelo convertido

O arquivo arrAsset produzido pelo serviço de conversão destina-se exclusivamente ao consumo pelo serviço de prestação. Pode haver momentos, no entanto, em que você deseja acessar informações sobre um modelo sem iniciar uma sessão de renderização. Portanto, o serviço de conversão coloca um arquivo JSON ao lado do arquivo arrAsset no recipiente de saída. Por exemplo, se `buggy.gltf` um arquivo for convertido, o `buggy.info.json` contêiner de saída `buggy.arrAsset`conterá um arquivo chamado ao lado do ativo convertido . Ele contém informações sobre o modelo de origem, o modelo convertido e sobre a conversão em si.

## <a name="example-info-file"></a>Arquivo *de informações* de exemplo

Aqui está um arquivo *de informações* de `buggy.gltf`exemplo produzido convertendo um arquivo chamado :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informações no arquivo de informações

### <a name="the-files-section"></a>A seção *de arquivos*

Esta seção contém os nomes de arquivos fornecidos.

* `input`: O nome do arquivo de origem.
* `output`: O nome do arquivo de saída, quando o usuário tiver especificado um nome não padrão.

### <a name="the-conversionsettings-section"></a>A seção *conversõesConfigurações*

Esta seção contém uma cópia das [Configurações](configure-model-conversion.md#settings-file) de conversão especificadas quando o modelo foi convertido.

### <a name="the-inputinfo-section"></a>A seção *inputInfo*

Esta seção registra informações sobre o formato do arquivo de origem.

* `sourceAssetExtension`: A extensão do arquivo do arquivo de origem.
* `sourceAssetFormat`: Uma descrição do formato do arquivo de origem.
* `sourceAssetFormatVersion`: A versão do formato de arquivo de origem.
* `sourceAssetGenerator`: O nome da ferramenta que gerou o arquivo de origem, se disponível.

### <a name="the-inputstatistics-section"></a>A seção *inputStatistics*

Esta seção fornece informações sobre a cena de origem. Muitas vezes haverá discrepâncias entre os valores desta seção e os valores equivalentes na ferramenta que criou o modelo de origem. Tais diferenças são esperadas, pois o modelo é modificado durante as etapas de exportação e conversão.

* `numMeshes`: O número de peças de malha, onde cada peça pode referenciar um único material.
* `numFaces`: O número total de _triângulos_ em todo o modelo. Observe que a malha é triangulada durante a conversão.
* `numVertices`: O número total de vértices em todo o modelo.
* `numMaterial`: O número total de materiais em todo o modelo.
* `numFacesSmallestMesh`: O número de triângulos na menor malha do modelo.
* `numFacesBiggestMesh`: O número de triângulos na maior malha do modelo.
* `numNodes`: O número de nódulos no gráfico de cena do modelo.
* `numMeshUsagesInScene`: O número de vezes que os nódulos fazem referência às traves. Mais de um nó pode referenciar a mesma malha.
* `maxNodeDepth`: A profundidade máxima dos nódulos dentro do gráfico de cena.

### <a name="the-outputinfo-section"></a>A seção *outputInfo*

Esta seção registra informações gerais sobre a saída gerada.

* `conversionToolVersion`: Versão do conversor do modelo.
* `conversionHash`: Um hash dos dados dentro do arrAsset que pode contribuir para a renderização. Pode ser usado para entender se o serviço de conversão produziu um resultado diferente ao ser executado no mesmo arquivo.

### <a name="the-outputstatistics-section"></a>A seção *outputStatistics*

Esta seção registra informações calculadas a partir do ativo convertido.

* `numMeshPartsCreated`: O número de meshes no arrAsset. Pode diferir `numMeshes` da `inputStatistics` seção, porque a insacing é afetada pelo processo de conversão.
* `numMeshPartsInstanced`: O número de meshes que são reutilizadas no arrAsset.
* `recenteringOffset`: Quando `recenterToOrigin` a opção nas [Configurações de conversão](configure-model-conversion.md) estiver ativada, esse valor é a tradução que moveria o modelo convertido de volta à sua posição original.
* `boundingBox`: Os limites do modelo.

## <a name="next-steps"></a>Próximas etapas

* [Conversão de modelo](model-conversion.md)
* [Configure a conversão do modelo](configure-model-conversion.md)
