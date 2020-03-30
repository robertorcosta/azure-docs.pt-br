---
title: Migrar contêineres Azure Cosmos não particionados para contêineres particionados
description: Saiba como migrar todos os contêineres não particionados existentes em contêineres particionados.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623360"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>Migrar contêineres não particionados para contêineres particionados

O Azure Cosmos DB suporta a criação de contêineres sem uma chave de partição. Atualmente você pode criar contêineres não particionados usando Azure CLI e Azure Cosmos DB SDKs (.Net, Java, NodeJs) que têm uma versão menor ou igual a 2.x. Não é possível criar recipientes não particionados usando o portal Azure. No entanto, tais recipientes não particionados não são elásticos e têm capacidade de armazenamento fixo de 20 GB e limite de throughput de 10K RU/s.

Os contêineres não particionados são legados e você deve migrar seus contêineres não particionados existentes para contêineres particionados para dimensionar o armazenamento e o throughput. O Azure Cosmos DB fornece um mecanismo definido pelo sistema para migrar seus contêineres não particionados para contêineres particionados. Este documento explica como todos os contêineres não particionados existentes são migrados automaticamente para contêineres particionados. Você só pode aproveitar o recurso de migração automática se estiver usando a versão V3 dos SDKs em todos os idiomas.

> [!NOTE]
> Atualmente, você não pode migrar contas acs

## <a name="migrate-container-using-the-system-defined-partition-key"></a>Migrar contêiner usando a chave de partição definida pelo sistema

Para suportar a migração, o Azure Cosmos `/_partitionkey` DB fornece uma chave de partição definida pelo sistema nomeada em todos os contêineres que não possuem uma chave de partição. Não é possível alterar a definição da chave de partição depois que os contêineres forem migrados. Por exemplo, a definição de um contêiner que é migrado para um contêiner particionado será a seguinte:

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

Depois que o contêiner é migrado, você `_partitionKey` pode criar documentos povoando a propriedade junto com as outras propriedades do documento. A `_partitionKey` propriedade representa a chave de partição de seus documentos.

Escolher a chave de partição certa é importante para utilizar o throughput provisionado de forma ideal. Para obter mais informações, veja [como escolher um artigo-chave de partição.](partitioning-overview.md)

> [!NOTE]
> Você pode aproveitar a chave de partição definida pelo sistema somente se estiver usando a versão mais recente/V3 dos SDKs em todos os idiomas.

O exemplo a seguir mostra um código de exemplo para criar um documento com a chave de partição definida pelo sistema e ler esse documento:

**Representação json do documento**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

Para obter a amostra completa, consulte o repositório [.Net amostras][1] GitHub.
                      
## <a name="migrate-the-documents"></a>Migrar os documentos

Embora a definição do contêiner seja aprimorada com uma propriedade de chave de partição, os documentos dentro do contêiner não são migrados automaticamente. O que significa que `/_partitionKey` o caminho de propriedade da chave de partição do sistema não é automaticamente adicionado aos documentos existentes. Você precisa reparticionar os documentos existentes lendo os documentos que foram criados sem uma chave de partição e reescrevê-los de volta com `_partitionKey` a propriedade nos documentos.

## <a name="access-documents-that-dont-have-a-partition-key"></a>Acesse documentos que não têm uma chave de partição

Os aplicativos podem acessar os documentos existentes que não possuem uma chave de partição usando a propriedade especial do sistema chamada "PartitionKey.None", este é o valor dos documentos não migrados. Você pode usar esta propriedade em todas as operações crud e consulta. O exemplo a seguir mostra uma amostra para ler um único documento da NonePartitionKey. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

Para obter a amostra completa sobre como reparticionar os documentos, consulte o repositório [.Net amostras][1] GitHub. 

## <a name="compatibility-with-sdks"></a>Compatibilidade com SDKs

A versão mais antiga dos SDKs Azure Cosmos DB como V2.x.x e V1.x.x não suportam a propriedade de chave de partição definida pelo sistema. Então, quando você lê a definição de contêiner de um SDK mais antigo, ele não contém nenhuma definição de chave de partição e esses recipientes se comportarão exatamente como antes. Os aplicativos que são construídos com a versão mais antiga dos SDKs continuam a funcionar com não partições como é sem alterações. 

Se um contêiner migrado for consumido pela versão mais recente/V3 do SDK e você começar a preencher a chave de partição definida pelo sistema dentro dos novos documentos, você não poderá mais acessar (ler, atualizar, excluir, consultar) esses documentos dos SDKs mais antigos.

## <a name="known-issues"></a>Problemas conhecidos

**A consulta para a contagem de itens que foram inseridos sem uma chave de partição usando o V3 SDK pode envolver maior consumo de throughput**

Se você consultar o V3 SDK para os itens inseridos usando o V2 SDK ou os itens inseridos `PartitionKey.None` usando o V3 SDK com parâmetro, `PartitionKey.None` a consulta de contagem pode consumir mais RU/s se o parâmetro for fornecido nas Opções de Alimentação. Recomendamos que você não `PartitionKey.None` forneça o parâmetro se nenhum outro item for inserido com uma chave de partição.

Se novos itens forem inseridos com valores diferentes para a chave de partição, a consulta para tais contagens de itens, passando a chave apropriada, não `FeedOptions` terá problemas. Depois de inserir novos documentos com a chave de partição, se você precisar consultar apenas a contagem de documentos sem o valor da chave de partição, essa consulta pode novamente incorrer em RU/s mais altos semelhante às coleções partições regulares.

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no BD Cosmos do Azure](partitioning-overview.md)
* [Unidades de solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration