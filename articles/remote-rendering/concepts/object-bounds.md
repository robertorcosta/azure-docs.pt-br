---
title: Limites do objeto
description: Explica como os limites de objeto espacial podem ser consultados
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681708"
---
# <a name="object-bounds"></a>Limites do objeto

Os limites de objeto representam o volume que uma [entidade](entities.md) e seus filhos ocupam. Na renderização remota do Azure, os limites de objeto são sempre fornecidos como *caixas delimitadas alinhadas de eixo* (AABB). Os limites de objeto podem estar no *espaço local* ou no *espaço do mundo*. De qualquer forma, eles são sempre alinhados por eixo, o que significa que as extensões e o volume podem ser diferentes entre a representação de espaço local e internacional.

## <a name="querying-object-bounds"></a>Consultando limites de objeto

O AABB local de uma [malha](meshes.md) pode ser consultado diretamente do recurso de malha. Esses limites podem ser transformados no espaço local ou no espaço do mundo de uma entidade usando a transformação da entidade.

É possível calcular os limites de uma hierarquia de objetos inteira dessa forma, mas isso requer que o percorra a hierarquia, consultar os limites de cada malha e combiná-los manualmente. Essa operação é entediante e ineficiente.

Uma maneira melhor é chamar `QueryLocalBoundsAsync` ou `QueryWorldBoundsAsync` em uma entidade. O cálculo é então descarregado para o servidor e retornado com atraso mínimo.

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
