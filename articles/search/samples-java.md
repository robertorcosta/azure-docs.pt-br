---
title: Exemplos do Java
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código Java de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: c1d21d88ed49cb14aa9f964791154f001a40fbe2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499570"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Exemplos de código Java para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código Java que demonstram os recursos e a funcionalidade do Azure Pesquisa Cognitiva. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [Azure-SDK-para-Java/árvore/mestre/SDK/pesquisa/Azure-Search-Documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Exemplos produzidos pela equipe do SDK do Azure que acompanha a biblioteca de cliente do Azure.Search.Documents no SDK. Você também pode examinar os [testes de unidade](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) da biblioteca de cliente para ver como várias APIs são chamadas. |
| [Azure-Samples/Azure-Search-java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Exemplos de código que acompanham artigos de instruções. Os **exemplos neste repositório ainda não foram atualizados para usar o SDK do Azure para Java**. Atualmente, esses exemplos chamam APIs REST no código Java.|

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="java-sdk-samples"></a>Exemplos de SDK do Java

O SDK do Azure para Java inclui vários exemplos e uma [página de introdução](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) que aborda a instalação do pacote. A página também lista uma grande variedade de exemplos. Várias das operações mais comuns são listadas abaixo para sua conveniência.

| Exemplos | Descrição |
|---------|-------------|
| [Pesquisar criação de índice](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Demonstra como criar [índices de pesquisa](search-what-is-an-index.md). |
| [Criação de sinônimos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Demonstra como criar [mapas de sinônimos](search-synonyms.md).  |
| [Criação do indexador de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Demonstra como criar [indexadores](search-indexer-overview.md). |
| [Criação da fonte de dados do indexador de pesquisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstra como criar fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [Criação de conconhecimento](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstra como criar [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [Carregue os documentos](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Demonstra como carregar ou mesclar documentos em um índice em uma operação de [importação de dados](search-what-is-data-import.md) . |
| [Sintaxe de consulta](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Demonstra como configurar uma [consulta básica](search-query-overview.md). |

## <a name="documentation-samples"></a>Amostras de documentação

Os exemplos a seguir têm um artigo associado na [documentação do pesquisa cognitiva do Azure](./index.yml).

| Exemplos | Descrição | 
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa em Java](search-get-started-java.md). Este exemplo chama as APIs REST. |
| [pesquisa-Java-indexer-demonstração](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Demonstra um Azure Cosmos DB indexador em Java. Este exemplo chama as APIs REST. |