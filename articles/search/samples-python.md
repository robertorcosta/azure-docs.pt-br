---
title: Exemplos em Python
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código do Python de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para Python ou REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686594"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Exemplos de código do Python para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do Python que demonstram os recursos e a funcionalidade do Azure Pesquisa Cognitiva. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [Azure-SDK-for-Python/árvore/mestre/SDK/pesquisa/Azure-Search-Documents/Samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Exemplos produzidos pela equipe do SDK do Azure que acompanha a biblioteca de cliente do Azure.Search.Documents no SDK. Você também pode examinar os [testes de unidade](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) da biblioteca de cliente para ver como várias APIs são chamadas. |
| [Azure-Samples/Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples) | Exemplos de código que acompanham artigos de instruções, incluindo o [início rápido: criar um índice de pesquisa em Python](search-get-started-python.md).|

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="python-sdk-samples"></a>Exemplos de SDK do Python

O SDK do Azure para Python inclui vários exemplos e uma [página de introdução](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) que inclui pré-requisitos e instalação de pacote. A página também contém links para os exemplos a seguir, listados aqui para sua conveniência.

| Exemplos | Descrição |
|---------|-------------|
| [Autenticar](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Demonstra como configurar um cliente do e autenticar o serviço do. | 
| [Operações de criação de índice-leitura-atualização-exclusão](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). |
| [Operações de criação-leitura-atualização-exclusão do indexador](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md). |
| [Pesquisar fontes de dados do indexador](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [Sinônimos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Carregue os documentos](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Demonstra como carregar ou mesclar documentos em um índice em uma operação de [importação de dados](search-what-is-data-import.md) . |
| [Consulta simples](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Demonstra como configurar uma [consulta básica](search-query-overview.md). |
| [Consulta de filtro](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Demonstra a configuração de uma [expressão de filtro](search-filters.md). |
| [Consulta de faceta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Demonstra como trabalhar com [facetas](search-filters-facets.md). |

## <a name="documentation-samples"></a>Amostras de documentação

Os exemplos a seguir têm um artigo associado na [documentação do pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/).

| Exemplos | Descrição | 
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa em Python](search-get-started-python.md).  |
| [tutorial – reenriquecimento de ia](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Código-fonte para [tutorial: use python e ia para gerar conteúdo pesquisável de BLOBs do Azure](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-habilidades personalizadas](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Código-fonte por [exemplo: criar uma habilidade personalizada usando Python](cognitive-search-custom-skill-python.md).  |