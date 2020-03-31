---
title: Mudar a alimentação na API AZURE Cosmos DB para Cassandra
description: Aprenda a usar o feed de alterações na API Azure Cosmos DB para cassandra para obter as alterações feitas em seus dados.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694617"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Mudar a alimentação na API AZURE Cosmos DB para Cassandra

O suporte de feed de [alterações](change-feed.md) na API Azure Cosmos DB para Cassandra está disponível através dos predicados de consulta na Linguagem de Consulta de Cassandra (CQL). Usando essas condições de predicado, você pode consultar a API de feed de alteração. Os aplicativos podem obter as alterações feitas em uma tabela usando a chave primária (também conhecida como chave de partição) como é necessário no CQL. Você pode, então, tomar outras ações com base nos resultados. As alterações nas linhas na tabela são capturadas na ordem do tempo de modificação e a ordem de classificação é garantida por chave de partição.

O exemplo a seguir mostra como obter uma alimentação de alteração em todas as linhas em uma tabela de keyspace da API Cassandra usando .NET. O predicado COSMOS_CHANGEFEED_START_TIME() é usado diretamente dentro do CQL para consultar itens na alimentação de alteração a partir de um horário de início especificado (neste caso, data atual). Você pode baixar a amostra completa [aqui](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

Em cada iteração, a consulta é retomada no último ponto de alterações, utilizando-se o estado de paginação. Podemos ver um fluxo contínuo de novas alterações na tabela no Keyspace. Veremos alterações nas linhas inseridas ou atualizadas. A observação das operações de exclusão usando o feed de alterações na API Cassandra não é suportada no momento. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Para obter as alterações em uma única linha por chave primária, você pode adicionar a chave principal na consulta. O exemplo a seguir mostra como rastrear alterações para a linha onde "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar o feed de alterações com a API Cassandra:

* Inserções e atualizações são suportadas no momento. A operação Excluir ainda não está suportada. Como solução de solução, você pode adicionar um marcador suave em linhas que estão sendo excluídas. Por exemplo, adicione um campo na linha chamado "excluído" e defina-o como "verdadeiro".
* A última atualização é persistida, pois na API SQL principal e atualizações intermediárias para a entidade não estão disponíveis.


## <a name="error-handling"></a>Tratamento de erros

Os seguintes códigos de erro e mensagens são suportados ao usar o feed de alterações na API de Cassandra:

* **Código de erro HTTP 429** - Quando o feed de alteração é limitado, ele retorna uma página vazia.

## <a name="next-steps"></a>Próximas etapas

* [Gerencie os recursos da API do Azure Cosmos DB Cassandra usando modelos do Azure Resource Manager](manage-cassandra-with-resource-manager.md)
