---
title: Malhas
description: Definição de meshes no escopo da renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: b800272a538243d39a89e4eed64bc5cbc2d53ad8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681760"
---
# <a name="meshes"></a>Malhas

## <a name="mesh-resource"></a>Recurso de malha

As meshes são um [recurso compartilhado](../concepts/lifetime.md)imutável, que só pode ser criado através da conversão de [modelos.](../how-tos/conversion/model-conversion.md) As barragens contêm uma ou várias *submeshes*. Cada submalha faz referência a um [material](materials.md) com o qual deve ser renderizado por padrão. Para colocar uma malha em espaço 3D, adicione um [MeshComponent](#meshcomponent) a uma [Entidade](entities.md).

### <a name="mesh-resource-properties"></a>Propriedades de recursos de malha

As `Mesh` propriedades da classe são:

* **Materiais:** Uma variedade de materiais. Cada material é usado por uma submalha diferente. Várias entradas na matriz podem fazer referência ao mesmo [material](materials.md). Esses dados não podem ser modificados em tempo de execução.

* **Limites:** Uma caixa de limitador alinhada ao eixo local (AABB) dos vértices de malha.

## <a name="meshcomponent"></a>MeshComponent

A `MeshComponent` classe é usada para colocar uma instância de um recurso de malha. Cada MeshComponent faz referência a uma única malha. Pode substituir quais materiais são usados para renderizar cada submalha.

### <a name="meshcomponent-properties"></a>Propriedades MeshComponent

* **Malha:** O recurso de malha que é usado por este componente.

* **Materiais:** A matriz de materiais especificada no próprio componente de malha. A matriz sempre terá o mesmo comprimento que a matriz *Materiais* no recurso de malha. Os materiais que não devem ser substituídos da malha padrão, são definidos *como nulos* nesta matriz.

* **Materiais usados:** A matriz de materiais realmente usados para cada submalha. Serão idênticos aos dados da matriz *Materiais,* para valores não nulos. Caso contrário, ele contém o valor da matriz *Materiais* na instância de malha.

## <a name="next-steps"></a>Próximas etapas

* [Materiais](materials.md)
