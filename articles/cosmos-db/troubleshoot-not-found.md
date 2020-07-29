---
title: Solucionar problemas Azure Cosmos DB exceção não encontrada
description: Como diagnosticar e corrigir a exceção não encontrada
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293975"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>Diagnosticar e solucionar problemas Azure Cosmos DB não encontrados
O código de status HTTP 404 representa que o recurso não existe mais.

## <a name="expected-behavior"></a>Comportamento esperado
Há muitos cenários válidos em que um aplicativo está esperando um 404 e manipula corretamente o cenário.

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>Não encontrado foi retornado para um item que deve existir ou existe
Veja abaixo o motivo possível para um código de status 404 ser retornado se o item deve ou não ser encerrado.

### <a name="1-race-condition"></a>1. condição de corrida
Há várias instâncias de cliente SDK e a leitura aconteceu antes da gravação.

#### <a name="solution"></a>Solução:
1. A consistência de conta padrão para Cosmos DB é a consistência da sessão. Quando um item é criado ou atualizado, a resposta retorna um token de sessão que pode ser passado entre as instâncias do SDK para garantir que a solicitação de leitura esteja lendo de uma réplica com essa alteração.
2. Alterar o [nível de consistência](consistency-levels-choosing.md) para um [nível mais forte](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. combinação de chave de partição e ID inválida
A chave de partição e a combinação de ID não são válidas.

#### <a name="solution"></a>Solução:
Corrija a lógica do aplicativo que está causando a combinação incorreta. 

### <a name="3-invalid-character-in-item-id"></a>3. caractere inválido na ID do item
Um item é inserido em Cosmos DB com um [caractere inválido](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) na ID do item.

#### <a name="solution"></a>Solução:
É recomendável que os usuários alterem a ID para um valor diferente que não contenha os caracteres especiais. Se a alteração da ID não for uma opção, você poderá codificar a ID para escapar os caracteres especiais.

Itens já inseridos no contêiner a ID pode ser substituída usando valores de RID em vez de referências baseadas em nome.
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. limpeza de TTL (vida útil)
O item tinha a propriedade [TTL (vida útil)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) definida. O item foi limpo porque a vida útil expirou.

#### <a name="solution"></a>Solução:
Altere a vida útil para impedir que o item seja limpo.

### <a name="5-lazy-indexing"></a>5. indexação lenta
A [indexação lenta](index-policy.md#indexing-mode) não foi detectada.

#### <a name="solution"></a>Solução:
Aguarde até que a indexação acompanhe ou altere a política de indexação

### <a name="6-parent-resource-deleted"></a>6. recurso pai excluído
O banco de dados e/ou o contêiner no qual o item existe foi excluído.

#### <a name="solution"></a>Solução:
1. [Restaure](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) o recurso pai ou recrie os recursos.
2. Criar um novo recurso para substituir o recurso excluído

## <a name="next-steps"></a>Próximas etapas
* [Diagnosticar e solucionar](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md)