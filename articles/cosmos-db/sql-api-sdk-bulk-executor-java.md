---
title: 'Azure Cosmos DB: API Java do executor em massa, recursos de & do SDK'
description: Saiba tudo sobre o SDK e a API Java de bulk executor, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK Java de bulk executor do Azure Cosmos DB.
author: milismsft
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/21/2018
ms.author: adrianmi
ms.openlocfilehash: 0030f974a36dc80dc8c4112000aa5934126a2482
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836437"
---
# <a name="java-bulk-executor-library-download-information"></a>Biblioteca bulk executor Java: informações sobre o download

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de Recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descrição**|A biblioteca bulk executor permite que aplicativos cliente executem operações em massa em contas do Azure Cosmos DB. a biblioteca bulk executor fornece os namespaces BulkImport e BulkUpdate. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar dados existentes em massa em contêineres Cosmos do Azure como patches.|
|**Download do SDK**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Biblioteca de executor em massa no GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **Documentação da API**| [Documentação de referência de API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Introdução**|[Introdução ao SDK Java da biblioteca bulk executor](bulk-executor-java.md)|
|**runtime mínimo com suporte**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Notas de versão

### <a name="2100"></a><a name="2.10.0"/>2.10.0

* Correção para DocumentAnalyzer. java para extrair corretamente valores de chave de partição aninhada do JSON.

### <a name="294"></a><a name="2.9.4"/>2.9.4

* Adicione funcionalidade em operações BulkDelete para tentar novamente em caso de falhas específicas e também retornar uma lista de falhas para o usuário que pode ser repetido.

### <a name="293"></a><a name="2.9.3"/>2.9.3

* Atualização para Cosmos SDK versão 2.4.7.

### <a name="292"></a><a name="2.9.2"/>2.9.2

* Corrija para ' mergeAll ' para continuar em ' ID ' e o valor de chave de partição para que qualquer propriedade de documento com patch que seja colocada após ' ID ' e o valor de chave de partição seja adicionada à lista de itens atualizados.

### <a name="291"></a><a name="2.9.1"/>2.9.1

* Atualize o grau inicial de simultaneidade para 1 e adicionei logs de depuração para minilote.


