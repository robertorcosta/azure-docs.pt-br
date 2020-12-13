---
title: Criar uma consulta básica
titleSuffix: Azure Cognitive Search
description: Saiba como construir uma solicitação de consulta no Pesquisa Cognitiva, quais ferramentas e APIs usar para teste e código e como decisões de consulta começam com design de índice.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371081"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Criar uma consulta básica no Azure Pesquisa Cognitiva

Este artigo explica a construção da consulta passo a passo. Os exemplos estão em REST para que você possa copiar cadeias de caracteres no **Gerenciador de pesquisa** no portal ou criar consultas interativamente, usando o postmaster ou o Visual Studio Code. Você pode usar qualquer camada ou versão do Pesquisa Cognitiva para os exemplos neste artigo.

## <a name="choose-a-tool-or-api"></a>Escolher uma ferramenta ou API

Escolha entre as ferramentas e APIs a seguir para criar consultas para cargas de trabalho de teste ou de produção.

| Metodologia | Descrição |
|-------------|-------------|
| Portal| O [Search Explorer (Portal)](search-explorer.md) fornece uma barra de pesquisa e opções para as seleções de índice e de versão de API. Os resultados são retornados como documentos JSON. Recomendado para investigação, teste e validação iniciais. <br/>[Saiba mais.](search-explorer.md) |
| Ferramentas de teste na Web| O [postmaster ou o Visual Studio Code](search-get-started-rest.md) são opções fortes para as chamadas REST de [documentos de pesquisa](/rest/api/searchservice/search-documents) do formular. A API REST dá suporte a todas as operações programáticas no Azure Pesquisa Cognitiva, para que você possa emitir solicitações interativamente para entender como ela funciona antes de investir no código.  |
| SDK do Azure | [SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em C#.  [Saiba mais.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (Python)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em Python. [Saiba mais.](search-get-started-python.md) <br/><br/> [SearchClient (JavaScript)](/dotnet/api/azure.search.documents.searchclient) pode ser usado para consultar um índice de pesquisa em JavaScript. [Saiba mais.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Configurar um cliente de pesquisa

Um cliente de pesquisa é autenticado no serviço de pesquisa, envia solicitações e processa respostas. As consultas são sempre direcionadas à coleção de documentos de um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta.

### <a name="in-the-portal"></a>No portal

O Gerenciador de pesquisa e outras ferramentas de portal têm uma conexão de cliente interna com o serviço, com índices de acesso direto e outros objetos de páginas do Portal. O acesso a ferramentas, assistentes e objetos pressupõe que você tenha direitos administrativos no serviço. Com o Gerenciador de pesquisa, você pode se concentrar para especificar a cadeia de caracteres de pesquisa e outros parâmetros. 

### <a name="using-rest"></a>Usar o REST

Para chamadas REST, você pode usar o [postmaster ou ferramentas semelhantes](search-get-started-rest.md) como o cliente para especificar uma solicitação de [pesquisa de documentos](/rest/api/searchservice/search-documents) . Cada solicitação é autônoma, portanto, você deve fornecer o ponto de extremidade (URL para o serviço) e uma chave de API de administrador ou de consulta para acesso. Dependendo da solicitação, a URL também pode incluir o nome do índice, a coleção de documentos e outras propriedades. Algumas propriedades, como tipo de conteúdo e chave de API, são passadas no cabeçalho da solicitação. Outros parâmetros podem ser passados na URL ou no corpo da solicitação. Todas as chamadas REST exigem uma chave de API para autenticação e uma versão de API.

### <a name="using-azure-sdks"></a>Usando SDKs do Azure

Os SDKs do Azure fornecem clientes de pesquisa que podem persistir o estado, permitindo a reutilização da conexão. Para operações de consulta, você criará uma instância de um SearchClient e fornecerá valores para as seguintes propriedades: EndPoint, Key, index. Em seguida, você pode chamar o método Search para fornecer a cadeia de caracteres de consulta. 

| Idioma | Cliente | Exemplo |
|----------|--------|---------|
| C# e .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa em C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa no Python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Envie sua primeira consulta de pesquisa em Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Envie sua primeira consulta de pesquisa em JavaScript](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

O Azure Pesquisa Cognitiva oferece a você uma opção entre dois analisadores de consulta para lidar com consultas típicas e especializadas. As solicitações que usam o analisador simples normalmente são consultas de pesquisa de texto completo, formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como o padrão para sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta completa do Lucene](query-Lucene-syntax.md#bkmk_syntax), ativada quando você adiciona `queryType=full` à solicitação, expõe a amplamente adotada e expressiva linguagem de consulta desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simple. Qualquer consulta que você escrever para a sintaxe simples será executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com **`queryType`** configurações diferentes, que produzem resultados diferentes. Na primeira consulta, o `^3` After `historic` é tratado como parte do termo de pesquisa. O resultado mais classificado para essa consulta é "Marquis pedágio & Suites", que tem o *oceano* em sua descrição.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

A mesma consulta que usa o analisador Lucene completo interpreta `^3` como um preforçador de termo em campo. A alternância de analisadores altera a classificação, com os resultados que contêm o termo *histórico* movendo para a parte superior.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Habilitar comportamentos de consulta em um índice

Design de índice e design de consulta são rigidamente acoplados ao Pesquisa Cognitiva do Azure. O *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que você pode compilar.

Atributos de índice em um campo para definir as operações permitidas - se um campo está *pesquisável* no índice, *recuperáveis* nos resultados *classificável*, *filtrável* e assim por diante. Nas consultas de exemplo, `"$orderby": "Rating desc"` só funciona porque o campo de classificação está marcado como *classificável* no esquema de índice.

![Definição de índice para o exemplo de Hotel](./media/search-query-overview/hotel-sample-index-definition.png "Definição de índice para o exemplo de Hotel")

A captura de tela acima é uma lista parcial de atributos de índice para o [índice de exemplo de hotéis](search-get-started-portal.md). Você pode criar e exibir o esquema de índice inteiro no Portal. Para obter mais informações sobre atributos de índice, consulte [criar índice (API REST)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Próximas etapas

Agora que você entende como a solicitação é construída, experimente exemplos usando a sintaxe simples e completa.

+ [Exemplos de consulta simples](search-query-simple-examples.md)
+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)