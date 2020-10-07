---
title: Paginação no Azure Cosmos DB
description: Saiba mais sobre conceitos de paginação e tokens de continuação
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 1cd0c3f48d4dc79294b3ebf9907ac18d23794830
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804190"
---
# <a name="pagination-in-azure-cosmos-db"></a>Paginação no Azure Cosmos DB

No Azure Cosmos DB, as consultas podem ter várias páginas de resultados. Este documento explica os critérios que o mecanismo de consulta do Azure Cosmos DB usa para decidir se os resultados da consulta devem ser divididos em várias páginas. Opcionalmente, você pode usar tokens de continuação para gerenciar os resultados da consulta que abrangem várias páginas.

## <a name="understanding-query-executions"></a>Noções básicas sobre execuções de consulta

Às vezes, os resultados da consulta serão divididos em várias páginas. Os resultados de cada página são gerados por uma execução de consulta separada. Quando os resultados da consulta não podem ser retornados em uma única execução, Azure Cosmos DB irá dividir os resultados automaticamente em várias páginas.

Você pode especificar o número máximo de itens retornados por uma consulta definindo o `MaxItemCount` . O `MaxItemCount` é especificado por solicitação e garante que o mecanismo de consulta retornará esse número de itens ou menos. Você pode definir `MaxItemCount` como `-1` se não quiser inserir um limite no número de resultados por execução de consulta.

Além disso, há outros motivos pelos quais o mecanismo de consulta pode precisar dividir os resultados da consulta em várias páginas. Elas incluem:

- O contêiner foi limitado e não havia RUs disponível para retornar mais resultados da consulta
- A resposta da execução da consulta era muito grande
- O tempo da execução da consulta era muito longo
- Era mais eficiente para o mecanismo de consulta retornar resultados em execuções adicionais

O número de itens retornados por execução de consulta sempre será menor ou igual a `MaxItemCount` . No entanto, é possível que outros critérios tenham limitado o número de resultados que a consulta pode retornar. Se você executar a mesma consulta várias vezes, o número de páginas poderá não ser constante. Por exemplo, se uma consulta for limitada, pode haver menos resultados disponíveis por página, o que significa que a consulta terá páginas adicionais. Em alguns casos, também é possível que sua consulta possa retornar uma página vazia de resultados.

## <a name="handling-multiple-pages-of-results"></a>Manipulando várias páginas de resultados

Para garantir os resultados de consulta precisos, você deve progredir em todas as páginas. Você deve continuar a executar consultas até que não haja nenhuma página adicional.

Aqui estão alguns exemplos para processar resultados de consultas com várias páginas:

- [SDK .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [SDK do Node.js](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [SDK do Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Tokens de continuação

No SDK do .NET e no SDK do Java, você pode opcionalmente usar tokens de continuação como um indicador para o progresso da consulta. Azure Cosmos DB execuções de consulta são sem monitoração de estado no lado do servidor e podem ser retomadas a qualquer momento usando o token de continuação. Não há suporte para tokens de continuação no SDK Node.js ou no SDK do Python.

Aqui estão alguns exemplos de uso de tokens de continuação:

- [SDK .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)

Se a consulta retornar um token de continuação, haverá resultados de consulta adicionais.

Na API REST do Azure Cosmos DB, você pode gerenciar tokens de continuação com o `x-ms-continuation` cabeçalho. Assim como acontece com a consulta com o SDK do .NET ou do Java, se o `x-ms-continuation` cabeçalho de resposta não estiver vazio, isso significará que a consulta tem resultados adicionais.

Contanto que você esteja usando a mesma versão do SDK, os tokens de continuação nunca expiram. Opcionalmente, você pode [restringir o tamanho de um token de continuação](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Independentemente da quantidade de dados ou do número de partições físicas em seu contêiner, as consultas retornam um único token de continuação.

Você não pode usar tokens de continuação para consultas com [Group by](sql-query-group-by.md) ou [DISTINCT](sql-query-keywords.md#distinct) porque essas consultas exigirão o armazenamento de um estado significativo. Para consultas com `DISTINCT` o, você pode usar tokens de continuação se adicionar `ORDER BY` à consulta.

Aqui está um exemplo de uma consulta com `DISTINCT` que poderia usar um token de continuação:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula ORDER BY](sql-query-order-by.md)