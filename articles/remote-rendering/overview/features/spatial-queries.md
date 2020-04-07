---
title: Consultas espaciais
description: Como fazer consultas espaciais em uma cena
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680525"
---
# <a name="spatial-queries"></a>Consultas espaciais

Consultas espaciais são operações com as quais você pode perguntar ao serviço de renderização remota quais objetos estão localizados em uma área. Consultas espaciais são frequentemente usadas para implementar interações, como descobrir para qual objeto um usuário está apontando.

Todas as consultas espaciais são avaliadas no servidor. Consequentemente, são operações assíncronas e os resultados chegarão com um atraso que depende da latência da sua rede. Como toda consulta espacial gera tráfego de rede, tome cuidado para não fazer muitos de uma vez.

## <a name="collision-meshes"></a>As minuas de colisão

As consultas espaciais são alimentadas pelo motor [da Física de Havok](https://www.havok.com/products/havok-physics) e requerem uma malha de colisão dedicada para estar presente. Por padrão, [a conversão do modelo](../../how-tos/conversion/model-conversion.md) gera colidições de colisão. Se você não precisar de consultas espaciais em um modelo complexo, considere desativar a geração de malha de colisão nas [opções de conversão,](../../how-tos/conversion/configure-model-conversion.md)pois ela tem um impacto em várias maneiras:

* [A conversão do modelo](../../how-tos/conversion/model-conversion.md) levará consideravelmente mais tempo.
* Os tamanhos de arquivo do modelo convertido são visivelmente maiores, impactando a velocidade de download.
* Os tempos de carregamento em tempo de execução são maiores.
* O consumo de memória da CPU em tempo de execução é maior.
* Há uma pequena sobrecarga de desempenho de tempo de execução para cada instância do modelo.

## <a name="ray-casts"></a>Elencos de raios

Um *molde de raios* é uma consulta espacial onde o tempo de execução verifica quais objetos são interceptados por um raio, começando em uma determinada posição e apontando para uma determinada direção. Como otimização, uma distância máxima de raios também é dada, para não procurar objetos que estão muito longe.

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

Existem três modos de coleta de hits:

* **Mais próximo:** Neste modo, apenas o acerto mais próximo será relatado.
* **Qualquer um:** Prefira este modo quando tudo o que você quer saber é *se* um raio atingiria alguma coisa, mas não se importa com o que foi atingido exatamente. Essa consulta pode ser consideravelmente mais barata de avaliar, mas também tem apenas poucos aplicativos.
* **Todos:** Neste modo, todos os acertos ao longo do raio são relatados, classificados pela distância. Não use este modo a menos que você realmente precise de mais do que o primeiro golpe. Limitar o número de `MaxHits` acessos relatados com a opção.

Para excluir objetos seletivamente de serem considerados para lanças de raios, o componente [HierarchicalStateOverrideComponent](override-hierarchical-state.md) pode ser usado.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>Resultado do hit

O resultado de uma consulta de elenco de raios é uma série de hits. A matriz está vazia, se nenhum objeto foi atingido.

Um Hit tem as seguintes propriedades:

* **HitEntity:** Qual [entidade](../../concepts/entities.md) foi atingida.
* **SubPartId:** Qual *submalha* foi atingida em um [MeshComponent](../../concepts/meshes.md). Pode ser usado `MeshComponent.UsedMaterials` para indexar e olhar o [material](../../concepts/materials.md) nesse ponto.
* **Posição de acerto:** A posição espacial mundial onde o raio cruzou o objeto.
* **Hitnormal:** A superfície espacial mundial normal da malha na posição da intersecção.
* **DistânciaToHit:** A distância da posição inicial do raio até o golpe.

## <a name="next-steps"></a>Próximas etapas

* [Limites do objeto](../../concepts/object-bounds.md)
* [Sobrepondo estados hierárquicos](override-hierarchical-state.md)
