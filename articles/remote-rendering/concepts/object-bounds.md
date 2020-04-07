---
title: Limites do objeto
description: Explica como os limites dos objetos espaciais podem ser consultados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681708"
---
# <a name="object-bounds"></a>Limites do objeto

Os limites dos objetos representam o volume que uma [entidade](entities.md) e seus filhos ocupam. Na renderização remota do Azure, os limites do objeto são sempre dados como *caixas de limitador alinhadas ao eixo* (AABB). Os limites dos objetos podem ser no *espaço local* ou no *espaço mundial.* De qualquer forma, eles estão sempre alinhados ao eixo, o que significa que a extensão e o volume podem diferir entre a representação espacial local e mundial.

## <a name="querying-object-bounds"></a>Consultando limites de objetos

A AABB local de uma [malha](meshes.md) pode ser consultada diretamente a partir do recurso de malha. Esses limites podem ser transformados no espaço local ou no espaço mundial de uma entidade usando a transformação da entidade.

É possível calcular os limites de toda uma hierarquia de objetos desta forma, mas isso requer atravessar a hierarquia, consultar os limites de cada malha e combiná-los manualmente. Esta operação é tediosa e ineficiente.

Uma maneira melhor `QueryLocalBoundsAsync` é `QueryWorldBoundsAsync` ligar ou ligar para uma entidade. O cálculo é então descarregado para o servidor e retornado com o mínimo de atraso.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>Próximas etapas

* [Consultas espaciais](../overview/features/spatial-queries.md)
