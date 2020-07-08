---
title: Entidades
description: Definição de entidades no escopo da API de Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 5f6f7fc52a186117afcb92f6a2f80bf068e50ab9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509195"
---
# <a name="entities"></a>Entidades

Uma *Entidade* representa um objeto móvel no espaço e é o bloco de construção fundamental do conteúdo renderizado remotamente.

## <a name="entity-properties"></a>Propriedades da entidade

As entidades têm uma transformação definida por posição, rotação e escala. As entidades não têm nenhuma funcionalidade observável por si só. Em vez disso, o comportamento é adicionado por meio de componentes, que são anexados às entidades. Por exemplo, anexar um [CutPlaneComponent](../overview/features/cut-planes.md) criará um plano de corte na posição da entidade.

O aspecto mais importante da entidade em si é a hierarquia e a transformação hierárquica resultante. Por exemplo, quando várias entidades estão anexadas como filhos a uma entidade pai compartilhada, todas elas podem ser movidas, giradas e dimensionadas de forma não dinâmica alterando a transformação da entidade pai.

Uma entidade é de propriedade exclusiva de seu pai, o que significa que, quando o pai é destruído com `Entity.Destroy()`, também são seus filhos e todos os [componentes](components.md) conectados. Deste modo, a remoção de um modelo da cena é realizada chamando `Destroy` no nó raiz de um modelo, retornado por `AzureSession.Actions.LoadModelAsync()` ou sua variante SAS `AzureSession.Actions.LoadModelFromSASAsync()`.

As entidades são criadas quando o servidor carrega o conteúdo ou quando o usuário quer adicionar um objeto à cena. Por exemplo, se um usuário quiser adicionar um plano de corte para visualizar o interior de uma malha, o usuário poderá criar uma entidade em que o plano deve existir e, em seguida, adicionar o componente do plano de corte a ela.

## <a name="create-an-entity"></a>Criar uma entidade

Para adicionar uma nova entidade à cena, por exemplo, para passá-la como um objeto raiz para carregar modelos ou anexar componentes a ela, use o seguinte código:

```cs
Entity CreateNewEntity(AzureSession session)
{
    Entity entity = session.Actions.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<AzureSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Actions()->CreateEntity())
    {
        entity = entityRes.value();
        entity->Position(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>Funções de consulta

Há dois tipos de funções de consulta em entidades: chamadas síncronas e chamadas assíncronas. As consultas síncronas podem ser usadas apenas para dados que estão presentes no cliente e não envolvem muita computação. Por exemplo, consultas para componentes, transformações de objeto relativo ou relações pai/filho. As consultas assíncronas são usadas para dados que residem apenas no servidor ou envolvem computação extra que seria muito cara para ser executada no cliente. Por exemplo, consultas de limites espaciais ou consultas de metadados.

### <a name="querying-components"></a>Consultando componentes

Para encontrar um componente de um tipo específico, use `FindComponentOfType`:

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = *entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>Como consultar transformações

As chamadas síncronas no objeto são consultas de transformação. Observe que as transformações consultadas por meio da API são transformações de espaço local em relação ao pai do objeto. As exceções são objetos raiz para os quais o espaço local e o espaço do mundo são idênticos.

> [!NOTE]
> Não há API dedicada para consultar a transformação de espaço do mundo de objetos arbitrários.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = *entity->Position();
Quaternion rotation = *entity->Rotation();
```


### <a name="querying-spatial-bounds"></a>Consultando limites espaciais

Chamadas assíncronas são consultas de limites que operam em uma hierarquia de objetos completa, usando uma entidade como raiz. Consulte o capítulo dedicado sobre [limites de objetos](object-bounds.md).

### <a name="querying-metadata"></a>Consultando metadados

Os metadados são dados adicionais armazenados em objetos e ignorados pelo servidor. Os metadados de objeto são essencialmente um conjunto de pares (nome, valor), em que o _valor_ pode ser numérico, booliano ou tipo de cadeia de caracteres. É possível exportar os metadados com o modelo.

Consultas de metadados são as chamadas assíncronas em uma entidade específica. A consulta fornece apenas os metadados de uma única entidade, não as informações mescladas de um subgrafo.

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

```cpp
ApiHandle<MetadataQueryAsync> metaDataQuery = *entity->QueryMetaDataAsync();
metaDataQuery->Completed([](const ApiHandle<MetadataQueryAsync>& query)
    {
        if (query->IsRanToCompletion())
        {
            ApiHandle<ObjectMetaData> metaData = *query->Result();
            ApiHandle<ObjectMetaDataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
            int64_t intValue = *entry->AsInt64();

            // ...
        }
    });
```

A consulta será realizada mesmo se o objeto não tiver nenhum metadado.

## <a name="next-steps"></a>Próximas etapas

* [Componentes](components.md)
* [Limites de objetos](object-bounds.md)
