---
title: Modelos de amostra
description: Lista fontes para modelos de amostra.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80678487"
---
# <a name="sample-models"></a>Modelos de amostra

Este artigo lista alguns recursos para dados de amostra que podem ser usados para testar o serviço Azure Remote Rendering.

## <a name="built-in-sample-model"></a>Modelo de amostra interno

Fornecemos um modelo de amostra interno que sempre pode ser carregado usando a URL **builtin://Engine**

![Modelo de exemplo](./media/sample-model.png "Modelo de exemplo")

Estatísticas do modelo:

| Nome | Valor |
|-----------|:-----------|
| [Tamanho da VM necessário](../how-tos/session-rest-api.md#create-a-session) | padrão |
| Número de triângulos | 18,7 milhões |
| Número de blocos móveis | 2073 |
| Número de materiais | 94 |

## <a name="third-party-data"></a>Dados de terceiros

O Grupo Khronos mantém um conjunto de modelos de amostra glTF para teste. O ARR dá suporte ao formato glTF em texto ( *.gltf*) e na forma binária ( *.glb*). Sugerimos usar os modelos de PBR para obter os melhores resultados visuais:

* [Modelos de amostra glTF](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Renderizar um modelo com o Unity](../quickstarts/render-model.md)
* [Início Rápido: Converter um modelo para renderização](../quickstarts/convert-model.md)
