---
title: Entidades
description: Definição de entidades no escopo da API de renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681942"
---
# <a name="entities"></a>Entidades

Uma *Entidade* representa um objeto móvel no espaço e é o bloco fundamental de construção de conteúdo renderizado remotamente.

## <a name="entity-properties"></a>Propriedades da entidade

As entidades têm uma transformação definida por uma posição, rotação e escala. Por si só, as entidades não têm nenhuma funcionalidade observável. Em vez disso, o comportamento é adicionado através de componentes, que são anexados a entidades. Por exemplo, anexar um [CutPlaneComponent](../overview/features/cut-planes.md) criará um plano de corte na posição da entidade.

O aspecto mais importante da própria entidade é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades são anexadas como crianças a uma entidade-mãe compartilhada, todas essas entidades podem ser movidas, giradas e dimensionadas em uníssão alterando a transformação da entidade-mãe.

Uma entidade é de propriedade exclusiva de seu pai, `Entity.Destroy()`o que significa que quando o pai é destruído com , assim como seus filhos e todos os [componentes](components.md)conectados . Assim, a remoção de um modelo `Destroy` da cena é realizada chamando `AzureSession.Actions.LoadModelAsync()` o nó raiz `AzureSession.Actions.LoadModelFromSASAsync()`de um modelo, devolvido por ou sua variante SAS .

As entidades são criadas quando o servidor carrega conteúdo ou quando o usuário deseja adicionar um objeto à cena. Por exemplo, se um usuário quiser adicionar um plano de corte para visualizar o interior de uma malha, o usuário pode criar uma entidade onde o avião deve existir e, em seguida, adicionar o componente do plano de corte a ele.

## <a name="query-functions"></a>Funções de consulta

Existem dois tipos de funções de consulta em entidades: chamadas síncronas e assíncronas. Consultas síncronas só podem ser usadas para dados que estão presentes no cliente e não envolvem muita computação. Exemplos são consultas para componentes, transformações relativas de objetos ou relacionamentos entre pais e filhos. Consultas assíncronas são usadas para dados que só residem no servidor ou envolvem computação extra que seria muito caro para ser executado no cliente. Exemplos são consultas de limites espaciais ou consultas de meta dados.

### <a name="querying-components"></a>Consultando componentes

Para encontrar um componente de `FindComponentOfType`um tipo específico, use:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Consultando transformações

Transformar consultas são chamadas síncronas no objeto. É importante notar que as transformações consultadas através da API são transformações espaciais locais, em relação ao pai do objeto. Exceções são objetos radiculares, para os quais o espaço local e o espaço mundial são idênticos.

> [!NOTE]
> Não há Uma API dedicada para consultar a transformação do espaço mundial de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Consultando limites espaciais

Consultas de limites são chamadas assíncronas que operam em uma hierarquia completa de objetos, usando uma entidade como raiz. Veja o capítulo dedicado sobre [os limites do objeto](object-bounds.md).

### <a name="querying-metadata"></a>Consultando metadados

Metadados são dados adicionais armazenados em objetos, que são ignorados pelo servidor. Metadados de objeto são essencialmente um conjunto de pares (nome, valor), onde _o valor_ pode ser do tipo numérico, booleano ou de seqüência. Metadados podem ser exportados com o modelo.

Consultas de metadados são chamadas assíncronas em uma entidade específica. A consulta só retorna os metadados de uma única entidade, não as informações mescladas de um subgráfico.

```cs
MetadataQueryAsync metaDataQuery = entity.QueryMetaDataAsync();
metaDataQuery.Completed += (MetadataQueryAsync query) =>
{
    if (query.IsRanToCompletion)
    {
        ObjectMetaData metaData = query.Result;
        ObjectMetaDataEntry entry = metaData.GetMetadataByName("MyInt64Value");
        System.Int64 intValue = entry.AsInt64;

        // ...
    }
};
```

A consulta será bem sucedida mesmo que o objeto não tenha metadados.

## <a name="next-steps"></a>Próximas etapas

* [Componentes](components.md)
* [Limites do objeto](object-bounds.md)
