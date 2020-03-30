---
title: Criar contêineres Azure Cosmos com grande chave de partição
description: Aprenda a criar um contêiner no Azure Cosmos DB com grande chave de partição usando o portal Azure e diferentes SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 7184a6b85e93c41dfe914813301a4b1a0c88f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887675"
---
# <a name="create-containers-with-large-partition-key"></a>Criar contêineres com grande chave de partição

O Azure Cosmos DB usa o esquema de particionamento baseado em hash para obter dimensionamento horizontal de dados. Todos os contêineres Azure Cosmos criados antes de 3 de maio de 2019 usam uma função hash que computa hash com base nos primeiros 100 bytes da chave de partição. Se houver várias teclas de partição que tenham os mesmos 100 primeiros bytes, então essas partições lógicas são consideradas como a mesma partição lógica pelo serviço. Isso pode levar a problemas como a cota de tamanho de partição estar incorreta e índices únicos sendo aplicados nas teclas de partição. Grandes teclas de partição são introduzidas para resolver esse problema. O Azure Cosmos DB agora suporta grandes chaves de partição com valores de até 2 KB.

Grandes teclas de partição são suportadas usando a funcionalidade de uma versão aprimorada da função hash, que pode gerar um hash único de grandes teclas de partição até 2 KB. Esta versão hash também é recomendada para cenários com alta chave de partição cardinalidade, independentemente do tamanho da chave de partição. Uma cardinalidade chave de partição é definida como o número de partições lógicas únicas, por exemplo, na ordem de ~30000 partições lógicas em um contêiner. Este artigo descreve como criar um contêiner com uma grande chave de partição usando o portal Azure e diferentes SDKs.

## <a name="create-a-large-partition-key-azure-portal"></a>Crie uma grande chave de partição (portal Azure)

Para criar uma grande chave de partição, ao criar um novo contêiner usando o portal Azure, verifique se a chave De partição É maior que a opção **de 100 bytes.** Desmarque a caixa de seleção se você não precisar de grandes chaves de partição ou se você tiver aplicativos em execução na versão SDKs com mais de 1,18.

![Crie grandes chaves de partição usando o portal Azure](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>Crie uma grande chave de partição (PowerShell)

Para criar um contêiner com grande suporte à chave de partição, veja,

* [Crie um contêiner Azure Cosmos com um grande tamanho de chave de partição](manage-with-powershell.md#create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>Crie uma grande chave de partição (.Net SDK)

Para criar um contêiner com uma grande chave de partição usando o .NET SDK, especifique a `PartitionKeyDefinitionVersion.V2` propriedade. O exemplo a seguir mostra como especificar a propriedade Version no objeto PartitionKeyDefinition e defini-la como PartitionKeyDefinitionVersion.V2.

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>Versões com suporte SDK

As chaves de partição Large são suportadas com as seguintes versões mínimas de SDKs:

|Tipo de SDK  | Versão mínima   |
|---------|---------|
|.Net     |    1.18     |
|Sincronização Java     |   2.4.0      |
|Java Assíncrono   |  2.5.0        |
| API REST | versão maior `2017-05-03` do `x-ms-version` que usando o cabeçalho de solicitação.|
| Modelo do Resource Manager | versão 2 usando `"version":2` a `partitionKey` propriedade dentro do objeto. |

Atualmente, não é possível usar contêineres com grande chave de partição dentro de Power BI e Azure Logic Apps. Você pode usar contêineres sem uma grande chave de partição desses aplicativos.

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no BD Cosmos do Azure](partitioning-overview.md)
* [Unidades de solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](account-overview.md)
