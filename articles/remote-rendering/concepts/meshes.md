---
title: Malhas
description: Definição de malhas no escopo da renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681760"
---
# <a name="meshes"></a>Malhas

## <a name="mesh-resource"></a>Recurso de malha

As malhas são um [recurso compartilhado](../concepts/lifetime.md)imutável, que só pode ser criado por meio da [conversão de modelo](../how-tos/conversion/model-conversion.md). As malhas contêm uma ou várias *submalhas*. Cada submalha faz referência a um [material](materials.md) com o qual ele deve ser renderizado por padrão. Para posicionar uma malha no espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [entidade](entities.md).

### <a name="mesh-resource-properties"></a>Propriedades do recurso de malha

As `Mesh` Propriedades de classe são:

* **Materiais:** Uma matriz de materiais. Cada material é usado por uma submalha diferente. Várias entradas na matriz podem referenciar o mesmo [material](materials.md). Esses dados não podem ser modificados em tempo de execução.

* **Limites:** Uma AABB (caixa delimitadora alinhada ao eixo de espaço local) dos vértices de malha.

## <a name="meshcomponent"></a>MeshComponent

A `MeshComponent` classe é usada para posicionar uma instância de um recurso de malha. Cada MeshComponent faz referência a uma única malha. Ele pode substituir quais materiais são usados para renderizar cada submalha.

### <a name="meshcomponent-properties"></a>Propriedades de MeshComponent

* **Malha:** O recurso de malha usado por esse componente.

* **Materiais:** A matriz de materiais especificada no próprio componente de malha. A matriz terá sempre o mesmo comprimento que a matriz de *materiais* no recurso de malha. Os materiais que não devem ser substituídos do padrão de malha são definidos como *NULL* nessa matriz.

* **UsedMaterials:** A matriz de materiais realmente usados para cada submalha. Será idêntico aos dados na matriz de *materiais* , para valores não nulos. Caso contrário, ele contém o valor da matriz de *materiais* na instância de malha.

## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)
