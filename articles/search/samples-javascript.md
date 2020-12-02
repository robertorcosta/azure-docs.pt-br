---
title: Exemplos de JavaScript
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código JavaScript de demonstração do Azure Pesquisa Cognitiva que usam o SDK do .NET do Azure para JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 234c70fffb6f353c670d23624cc446fdaf6bd886
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498958"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Exemplos de código JavaScript para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do JavaScript que demonstram os recursos e a funcionalidade do Azure Pesquisa Cognitiva. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [Azure-SDK-for-js/árvore/mestre/SDK/pesquisa/pesquisa-documentos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Exemplos produzidos pela equipe do SDK do Azure que acompanha a biblioteca de cliente do Azure.Search.Documents no SDK. Você também pode examinar os [testes de unidade](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) da biblioteca de cliente para ver como várias APIs são chamadas. |
| [Azure-Samples/Azure-Search-JavaScript-Samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Exemplos de código que acompanham artigos de instruções, incluindo o [início rápido: criar um índice de pesquisa em JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="javascript-sdk-samples"></a>Exemplos de SDK do JavaScript

O SDK do Azure para Java inclui vários exemplos e uma [página de introdução](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) que aborda a instalação do pacote, a configuração do cliente e a solução de problemas. A página também descreve as seguintes categorias de exemplo, listadas aqui para sua conveniência.

| Exemplos | Descrição |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). Essa categoria de exemplo também inclui um exemplo de estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md).|
| [Qualificações](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e excluir [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Demonstra a execução da consulta em um índice público somente leitura hospedado pela Microsoft.  |

## <a name="typescript-samples"></a>Exemplos de TypeScript

O SDK também fornece exemplos do TypeScript, listados aqui para sua conveniência.

| Exemplos | Descrição |
|---------|-------------|
| [índices](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Demonstra como criar, atualizar, obter, listar e excluir [índices de pesquisa](search-what-is-an-index.md). Essa categoria de exemplo também inclui um exemplo de estatística de serviço. |
| [dataSourceConnections (para indexadores)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Demonstra como criar, atualizar, obter, listar e excluir fontes de dados do indexador, necessárias para a indexação baseada em indexador de [fontes de dados do Azure com suporte](search-indexer-overview.md#supported-data-sources). |
| [indexadores](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Demonstra como criar, atualizar, obter, listar, redefinir e excluir [indexadores](search-indexer-overview.md).|
| [Qualificações](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Demonstra como criar, atualizar, obter, listar e excluir [habilidades](cognitive-search-working-with-skillsets.md) que são indexadores anexados e que executam o enriquecimento baseado em ia durante a indexação. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Demonstra como criar, atualizar, obter, listar e excluir [mapas de sinônimos](search-synonyms.md).  |
| [Consultas](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Demonstra a execução da consulta em um índice público somente leitura hospedado pela Microsoft.  |

## <a name="documentation-samples"></a>Amostras de documentação

Os exemplos a seguir têm um artigo associado na [documentação do pesquisa cognitiva do Azure](./index.yml).

| Exemplos | Descrição | 
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Código-fonte para [início rápido: criar um índice de pesquisa em JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Amostras autônomas

| Exemplos | Descrição |
|---------|-------------|
| [Azure-Search-reagir-modelo](https://github.com/dereklegenzoff/azure-search-react-template) | Modelo de reagem para o Azure Pesquisa Cognitiva (github.com) |