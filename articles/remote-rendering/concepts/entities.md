---
title: Entidades
description: Definição de entidades no escopo da API de renderização remota do Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: d7b9ecd048b080ae0ec9fd3fb7a4fb35009551b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681942"
---
# <a name="entities"></a>Entidades

Uma *entidade* representa um objeto móvel no espaço e é o bloco de construção fundamental do conteúdo renderizado remotamente.

## <a name="entity-properties"></a>Propriedades da entidade

As entidades têm uma transformação definida por uma posição, rotação e escala. Por si só, as entidades não têm nenhuma funcionalidade observável. Em vez disso, o comportamento é adicionado por meio de componentes, que são anexados a entidades. Por exemplo, anexar um [CutPlaneComponent](../overview/features/cut-planes.md) criará um plano de recorte na posição da entidade.

O aspecto mais importante da própria entidade é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades são anexadas como filhos a uma entidade pai compartilhada, todas essas entidades podem ser movidas, giradas e dimensionadas de forma não dinâmica alterando a transformação da entidade pai.

Uma entidade é exclusivamente de propriedade de seu pai, o que significa que, quando o pai `Entity.Destroy()`é destruído com, são seus filhos e todos os [componentes](components.md)conectados. Assim, a remoção de um modelo da cena é realizada chamando `Destroy` o nó raiz de um modelo, retornado por `AzureSession.Actions.LoadModelAsync()` ou sua variante `AzureSession.Actions.LoadModelFromSASAsync()`SAS.

As entidades são criadas quando o servidor carrega o conteúdo ou quando o usuário deseja adicionar um objeto à cena. Por exemplo, se um usuário quiser adicionar um plano de recorte para visualizar o interior de uma malha, o usuário poderá criar uma entidade em que o plano deve existir e, em seguida, adicionar o componente plano de recorte a ela.

## <a name="query-functions"></a>Funções de consulta

Há dois tipos de funções de consulta em entidades: chamadas síncronas e assíncronas. As consultas síncronas só podem ser usadas para dados que estão presentes no cliente e não envolvem muita computação. Os exemplos são consultas para componentes, transformações de objeto relativo ou relações pai/filho. As consultas assíncronas são usadas para dados que residem apenas no servidor ou envolvem uma computação extra que seria muito cara para ser executada no cliente. Exemplos são consultas de dados de limites espaciais ou consultas de metadados.

### <a name="querying-components"></a>Consultando componentes

Para localizar um componente de um tipo específico, use `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Consultando transformações

As consultas de transformação são chamadas síncronas no objeto. É importante observar que as transformações consultadas por meio da API são transformações de espaço local, em relação ao pai do objeto. As exceções são objetos raiz, para os quais o espaço local e o espaço do mundo são idênticos.

> [!NOTE]
> Não há API dedicada para consultar a transformação de espaço mundial de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

### <a name="querying-spatial-bounds"></a>Consultando limites espaciais

As consultas de limites são chamadas assíncronas que operam em uma hierarquia de objetos completa, usando uma entidade como raiz. Consulte o capítulo dedicado sobre [limites de objeto](object-bounds.md).

### <a name="querying-metadata"></a>Consultando metadados

Os metadados são dados adicionais armazenados em objetos, que são ignorados pelo servidor. Os metadados de objeto são essencialmente um conjunto de pares (nome, valor), em que o _valor_ pode ser numérico, booliano ou tipo de cadeia de caracteres. Os metadados podem ser exportados com o modelo.

As consultas de metadados são chamadas assíncronas em uma entidade específica. A consulta retorna apenas os metadados de uma única entidade, não as informações mescladas de um subgrafo.

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

A consulta terá sucesso mesmo se o objeto não mantiver nenhum metadado.

## <a name="next-steps"></a>Próximas etapas

* [Componentes](components.md)
* [Limites do objeto](object-bounds.md)
