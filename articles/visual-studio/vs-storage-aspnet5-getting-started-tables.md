---
title: Introdução ao armazenamento de tabelas usando o Visual Studio (ASP.NET Core)
description: Como começar a usar o armazenamento de Tabela do Azure em um projeto do ASP.NET Core no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298821"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Introdução ao armazenamento de Tabela do Azure e serviços conectados do Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Este artigo descreve como começar a usar o Armazenamento de Tabelas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de Armazenamento do Azure em um projeto ASP.NET Core usando o recurso **Serviços Conectados** do Visual Studio. A operação **Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto. (Veja a [documentação do Armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento do Azure).

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Para obter mais informações sobre como usar o Armazenamento de Tabelas do Azure, consulte [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../storage/storage-dotnet-how-to-use-tables.md).

Para começar, primeiramente crie uma tabela em sua conta de armazenamento. Este artigo mostrará como criar uma tabela no C# e como realizar operações básicas de tabela, como adicionar, modificar, ler e remover entradas da tabela.  O código usa a Biblioteca de Cliente de Armazenamento do Azure para .NET. Para saber mais sobre ASP.NET, confira [ASP.NET](https://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que os métodos assíncronos estejam sendo usados. Confira [Programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para saber mais.

## <a name="access-tables-in-code"></a>Acessar tabelas em código

Para acessar tabelas em projetos do ASP.NET Core, você precisa incluir os itens a seguir para quaisquer arquivos de origem de C# que acessam o armazenamento de tabelas do Azure.

1. Adicione as instruções `using` necessárias:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Obtenha um objeto `CloudStorageAccount` que represente as informações da conta de armazenamento. Use o código a seguir, usando o nome da conta de armazenamento e a chave de conta, que você pode encontrar na cadeia de conexão de armazenamento em appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Obtenha um objeto `CloudTableClient` para referenciar objetos de tabela em sua conta de armazenamento:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um objeto de referência `CloudTable` para fazer referência a entidades e a uma tabela específica:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela em código

Para criar a tabela do Azure, crie um método assíncrono e, dentro dela, chame `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade. O código a seguir define uma classe de entidade chamada `CustomerEntity` que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

As operações de tabela que envolvem entidades usam o objeto `CloudTable` criado anteriormente em [Acessar tabelas no código](#access-tables-in-code). O objeto `TableOperation` representa a operação a ser realizada. O exemplo de código a seguir mostra como criar um objeto `CloudTable` e um objeto `CustomerEntity`. Para preparar a operação, um `TableOperation` é criado para inserir a entidade de cliente na tabela. Finalmente, a operação é executada chamando `CloudTable.ExecuteAsync`.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Mateus Rodrigues" e "Luis Rodrigues") e os adiciona a um objeto `TableBatchOperation` usando o método `Insert` e depois inicia a operação chamando `CloudTable.ExecuteBatchAsync`.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Para consultar uma tabela de todas as entidades em uma partição, use um objeto `TableQuery`. O exemplo de código a seguir especifica um filtro para as entidades em que 'Smith’ é a chave de partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Obter uma única entidade

Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto `TableOperation` para especificar o cliente chamado 'Luis Rodrigues'. O método retorna uma única entidade, em vez de uma coleção, e o valor retornado está em `TableResult.Result` é um objeto `CustomerEntity`. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço `Table`.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Excluir uma entidade

Você poderá excluir uma entidade facilmente depois de encontrá-la. O código a seguir busca e exclui uma entidade de cliente chamada "Ben Smith":

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
