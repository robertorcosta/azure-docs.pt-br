---
title: Consultas espaciais
description: Como fazer consultas espaciais em uma cena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680525"
---
# <a name="spatial-queries"></a>Consultas espaciais

As consultas espaciais são operações com as quais você pode solicitar ao serviço de renderização remoto quais objetos estão localizados em uma área. Consultas espaciais são usadas frequentemente para implementar interações, como descobrir em qual objeto um usuário está apontando.

Todas as consultas espaciais são avaliadas no servidor. Consequentemente, eles são operações assíncronas e os resultados chegarão com um atraso que depende da sua latência de rede. Como cada consulta espacial gera o tráfego de rede, tenha cuidado para não fazer muitas de uma vez.

## <a name="collision-meshes"></a>Malhas de colisão

As consultas espaciais são alimentadas pelo mecanismo de [física Havok](https://www.havok.com/products/havok-physics) e exigem a presença de uma malha de colisão dedicada. Por padrão, a [conversão de modelo](../../how-tos/conversion/model-conversion.md) gera malhas de colisão. Se você não precisar de consultas espaciais em um modelo complexo, considere desabilitar a geração de malha de colisão nas [Opções de conversão](../../how-tos/conversion/configure-model-conversion.md), pois ela tem um impacto de várias maneiras:

* A [conversão de modelo](../../how-tos/conversion/model-conversion.md) levará consideravelmente mais tempo.
* Os tamanhos de arquivo de modelo convertidos são visivelmente maiores, impactando a velocidade de download.
* Tempos de carregamento em tempo de execução são mais longos.
* O consumo de memória de CPU em tempo de execução é maior.
* Há uma pequena sobrecarga de desempenho de tempo de execução para cada instância de modelo.

## <a name="ray-casts"></a>Ray casts

Um *Ray Cast* é uma consulta espacial em que o tempo de execução verifica quais objetos são interseccionados por um raio, começando em uma determinada posição e apontando para uma determinada direção. Como uma otimização, uma distância máxima de Ray também é fornecida, para não Pesquisar objetos que estejam muito distantes.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

Há três modos de coleta de visita:

* **Mais próximo:** Nesse modo, somente a visita mais próxima será relatada.
* **Qualquer:** Prefira esse modo quando tudo o que você deseja saber é *se* um raio atingiria algo, mas não se importa com o que foi exatamente pressionado. Essa consulta pode ser consideravelmente mais barata para ser avaliada, mas também tem apenas alguns aplicativos.
* **Todos:** Nesse modo, todas as ocorrências ao longo do raio são relatadas, classificadas por distância. Não use esse modo, a menos que você realmente precise de mais do que o primeiro acesso. Limite o número de ocorrências relatadas com a `MaxHits` opção.

Para excluir objetos de forma seletiva de serem considerados para Ray casts, o componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) pode ser usado.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultado da visita

O resultado de uma consulta Ray Cast é uma matriz de ocorrências. A matriz estará vazia se nenhum objeto tiver sido atingido.

Um clique tem as seguintes propriedades:

* **HitEntity:** Qual [entidade](../../concepts/entities.md) foi atingida.
* **Subpartid:** Qual *submalha* foi atingida em um [MeshComponent](../../concepts/meshes.md). Pode ser usado para indexar `MeshComponent.UsedMaterials` e pesquisar o [material](../../concepts/materials.md) nesse ponto.
* **HitPosition:** A posição de espaço mundial em que Ray interseccionau o objeto.
* **HitNormal:** A superfície de espaço do mundo normal da malha na posição da interseção.
* **DistanceToHit:** A distância da posição de início de Ray até o pressionamento.

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](../../concepts/object-bounds.md)
* [Substituindo Estados hierárquicos](override-hierarchical-state.md)
