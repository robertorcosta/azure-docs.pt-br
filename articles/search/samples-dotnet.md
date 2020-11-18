---
title: Amostras do .NET
titleSuffix: Azure Cognitive Search
description: Encontre exemplos de código C# de demonstração do Azure Pesquisa Cognitiva que usam as bibliotecas de cliente .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686653"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Exemplos de código .NET (C#) para o Azure Pesquisa Cognitiva

Saiba mais sobre os exemplos de código do C# que demonstram os recursos e a funcionalidade do Azure Pesquisa Cognitiva. Os repositórios primários são os seguintes:

| Repositório | Descrição |
|------------|-------------|
| [Azure-SDK-for-NET/SDK/Search/Azure.Search.Documents/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Exemplos produzidos pela equipe do SDK do Azure que acompanha a biblioteca de cliente do Azure.Search.Documents no SDK. Você também pode examinar os [testes de unidade](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) da biblioteca de cliente para ver como várias APIs são chamadas. |
| [Azure-Samples/Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Exemplos que acompanham artigos de instruções na documentação, incluindo [como usar a biblioteca de cliente .net](search-howto-dotnet-sdk.md).|
| [Azure-Samples/Search-dotnet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) | Exemplos que acompanham os guias de início rápido e tutoriais na documentação.|

> [!Tip]
> Experimente o [navegador de exemplos](/samples/browse/?languages=csharp&products=azure-cognitive-search) para pesquisar exemplos de código da Microsoft no GitHub, filtrados por produto, serviço e idioma.

## <a name="net-sdk-samples"></a>Exemplos de SDK do .NET

O SDK do Azure para .NET inclui vários exemplos e um [Leiame de exemplos](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) que descreve cada um deles. Essa lista é fornecida abaixo para sua conveniência.

| Exemplos | Descrição |
|---------|-------------|
| ["Olá, mundo", de forma síncrona](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstra como criar um cliente, autenticar e tratar erros usando métodos síncronos.|
| ["Olá, mundo", de forma assíncrona](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstra como criar um cliente, autenticar e tratar erros usando métodos assíncronos.  |
| [Operações de nível de serviço](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstra como criar índices, indexadores, fontes de dados, habilidades e mapas de sinônimos. Este exemplo também mostra como obter estatísticas de serviço e como consultar um índice.  |
| [Operações de índice](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstra como executar uma ação no índice existente, nesse caso, obter uma contagem de documentos armazenados no índice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Demonstra uma técnica para trabalhar com tipos de dados sem suporte.  |
| [Indexando documentos (modelo de push)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indexação de modelo "Push", em que você envia uma carga JSON para um índice em um serviço.   |
| [Exemplo de chave de criptografia](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Demonstra o uso de uma chave de criptografia gerenciada pelo cliente para adicionar uma camada extra de proteção por conteúdo confidencial.  |

## <a name="documentation-samples"></a>Amostras de documentação

Os exemplos a seguir têm um artigo associado na [documentação do pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/).

| Exemplos | Descrição |
|---------|-------------|
| [TUTORIAIS](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Código-fonte para [início rápido: criar um índice de pesquisa ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Código-fonte de [como usar a biblioteca de cliente .net](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | As listas de sinônimos são usadas para expansão de consulta, fornecendo termos de correspondência externos a um índice. Este exemplo é incluído no [exemplo: Adicionar sinônimos em C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Código-fonte por trás de trechos relacionados ao indexador em vários artigos. Este exemplo mostra como configurar um indexador que tem um agendamento, mapeamentos de campo e parâmetros.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Código-fonte de [como configurar chaves gerenciadas pelo cliente para criptografia de dados](search-security-manage-encryption-keys.md) |
| [Criar seu primeiro aplicativo em C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Código-fonte para [tutorial: Crie seu primeiro aplicativo de pesquisa](tutorial-csharp-create-first-app.md). Embora a maioria dos exemplos sejam aplicativos de console, este exemplo MVC usa uma página da Web para antecipar o índice de hotéis de exemplo, demonstrando pesquisa básica, paginação, preenchimento automático e consultas sugeridas, facetas e filtros. |
| [fontes de dados múltiplos](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Código-fonte para [tutorial: índice de várias fontes de dados](tutorial-multiple-data-sources.md). |
|  [otimização de dados-indexação](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Código-fonte para [tutorial: otimizar a indexação com a API de envio por push](tutorial-optimize-indexing-push-api.md).  |
| [tutorial – reenriquecimento de ia](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Código-fonte para [tutorial: conteúdo pesquisável gerado por ia de BLOBs do Azure usando o SDK do .net](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Amostras e soluções autônomas

| Exemplos | Descrição |
|---------|-------------|
| [Azure-Search-Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Código-fonte para habilidades personalizadas consumíveis que você pode incorporar em suas soluções ganhas.  |
| [Acelerador de solução de mineração de conhecimento](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Inclui modelos, arquivos de suporte e relatórios analíticos para ajudá-lo a desenvolver um protótipo de uma solução de mineração de conhecimento de ponta a ponta.  |
| [Repositório de aplicativos de pesquisa do Covid-19](https://github.com/liamca/covid19search) | Repositório de código-fonte para o [aplicativo de pesquisa Covid-19](https://covid19search.azurewebsites.net/) baseado em pesquisa cognitiva |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Saiba mais sobre a [solução JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |