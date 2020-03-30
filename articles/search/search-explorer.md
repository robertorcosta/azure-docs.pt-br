---
title: Ferramenta de consulta do explorador de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Neste portal Dozure, use o Search Explorer para aprender a sintaxe de consulta, testar expressões de consulta ou inspecionar um documento de pesquisa. Pesquisar índices de consultas de exploradores no Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/27/2020
ms.openlocfilehash: 9fb34141d19866a2f49ac164e0d89802cf7818c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369701"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>Quickstart: Use o search explorer para executar consultas no portal

**O Search explorer** é uma ferramenta de consulta incorporada usada para executar consultas contra um índice de pesquisa no Azure Cognitive Search. Esta ferramenta facilita a sintaxe de consulta, testa uma consulta ou a expressão do filtro ou confirma os resultados de uma atualização de índice verificando a existência de conteúdo mais novo.

Este quickstart usa **o índice de amostra de imóveis-us-us** para demonstrar o explorador de pesquisa. As solicitações são formuladas usando a [API Search REST](https://docs.microsoft.com/rest/api/searchservice/), com respostas retornadas como documentos JSON.

## <a name="prerequisites"></a>Pré-requisitos

+ [Crie um serviço da Pesquisa Cognitiva do Azure](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido.

+ **realestate-us-sample-index** é usado para este quickstart. Passe o assistente [**de dados Importação**](search-import-data-portal.md) para gerar o índice a partir da fonte de dados de amostras incorporadas.

## <a name="start-search-explorer"></a>Iniciar o Gerenciador de pesquisa

1. No [portal Azure,](https://portal.azure.com)abra a página do serviço de busca no painel ou [encontre seu serviço.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. Abrir o explorador de pesquisa da barra de comando:

   ![Comando de explorador de pesquisa no portal](./media/search-explorer/search-explorer-cmd2.png "Comando de explorador de pesquisa no portal")

    Ou use a guia **do explorador de pesquisa** incorporado em um índice aberto:

   ![Guia do explorador de pesquisa](./media/search-explorer/search-explorer-tab.png "Guia do explorador de pesquisa")

## <a name="unspecified-query"></a>Consulta não especificada

Para uma primeira olhada no conteúdo, execute uma pesquisa vazia clicando **em Pesquisar** sem termos fornecidos. Uma pesquisa vazia é útil como uma primeira consulta porque ela retorna documentos inteiros para que você possa examinar a composição do documento. Em uma pesquisa vazia, não há nenhuma classificação de pesquisa e os documentos são retornados em ordem arbitrária (`"@search.score": 1` para todos os documentos). Por padrão, são retornados 50 documentos em uma solicitação de pesquisa.

Uma sintaxe equivalente para uma pesquisa vazia é `*` ou `search=*`.
   
   ```http
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Exemplo de consulta não qualificado ou vazio")

## <a name="free-text-search"></a>Pesquisa de texto gratuita

Consultas de formulário livre, com ou sem operadores, são úteis para simular consultas definidas pelo usuário enviadas de um aplicativo personalizado para o Azure Cognitive Search. Apenas os campos atribuídos como **Pesquisáveis** na definição de índice são digitalizados para correspondências. 

Observe que quando você fornece critérios de pesquisa, como termos de consulta ou expressões, a classificação de pesquisa entra em jogo. O exemplo a seguir ilustra uma pesquisa de texto livre.

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar termos específicos de interesse nos resultados.

   ![Exemplo de consulta de texto gratuito](./media/search-explorer/search-explorer-example-freetext.png "Exemplo de consulta de texto gratuito")

## <a name="count-of-matching-documents"></a>Contagem de documentos correspondentes 

Adicione **$count=true** para obter o número de correspondências encontradas em um índice. Em uma pesquisa vazia, a contagem é o número total de documentos no índice. Em uma pesquisa qualificada, é o número de documentos que correspondem à entrada da consulta.

   ```http
   $count=true
   ```

   **Resultados**

   ![Exemplo de contagem de documentos](./media/search-explorer/search-explorer-example-count.png "Contagem de documentos correspondentes no índice")

## <a name="limit-fields-in-search-results"></a>Limitar campos nos resultados de pesquisa

Adicione [**$select**](search-query-odata-select.md) para limitar os resultados aos campos explicitamente nomeados para uma saída mais legível no **explorador de pesquisa**. Para manter a cadeia de caracteres de pesquisa e **$count=true**, coloque **&** como prefixo dos argumentos. 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Exemplo de campos de limite](./media/search-explorer/search-explorer-example-selectfield.png "Restringir campos nos resultados de pesquisa")

## <a name="return-next-batch-of-results"></a>Devolva o próximo lote de resultados

A Busca Cognitiva do Azure retorna as 50 melhores partidas com base no ranking de pesquisa. Para obter o próximo conjunto de documentos correspondentes, acrescente **$top=100,&$skip=50** para aumentar o conjunto de resultados para 100 documentos (o padrão é 50, o máximo é 1000), ignorando os primeiros 50 documentos. Lembre-se de que você precisa fornecer critérios de pesquisa, como um termo ou expressão de consulta, para obter os resultados classificados. Observe que as pontuações de pesquisa diminuem quanto mais você avança nos resultados da pesquisa.

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   ![Resultados da pesquisa em lote](./media/search-explorer/search-explorer-example-topskip.png "Devolva o próximo lote de resultados de pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maior que, menor que, igual a)

Use o parâmetro [**$filter**](search-query-odata-filter.md) quando quiser especificar critérios precisos em vez de pesquisa de texto gratuita. O campo deve ser atribuído como **Filtravel** no índice. Este exemplo pesquisa quartos maiores que 3: 

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   ![Expressão do filtro](./media/search-explorer/search-explorer-example-filter.png "Filtrar por critérios")

## <a name="order-by-expressions"></a>Expressões orderby

Adicione [**$orderby**](search-query-odata-orderby.md) para classificar resultados por outro campo além da pontuação de pesquisa. O campo deve ser atribuído como **Sortable** no índice. Uma expressão de exemplo que você pode usar para testar isso é:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   ![Ordenando por expressão](./media/search-explorer/search-explorer-example-ordery.png "Alterar ordem de classificação")

As expressões **$filter** e **$orderby** são construções do OData. Para saber mais, confira [Sintaxe de filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>Observações

Neste quickstart, você usou **o Search explorer** para consultar um índice usando a API REST.

+ Os resultados são retornados como documentos JSON detalhados para que você possa exibir o conteúdo e a construção do documento na totalidade. Você pode usar expressões de consulta, mostradas nos exemplos, para limitar quais campos são retornados.

+ Os documentos contêm todos os campos marcados como **Recuperáveis** no índice. Para exibir atributos de índice no portal, clique em *realestate-us-sample* na lista **Índices** na página de visão geral da pesquisa.

+ As consultas de forma livre, semelhantes ao que você pode digitar em um navegador da Web comercial, são úteis para testar uma experiência do usuário final. Por exemplo, supondo o índice de exemplo realestate interno, você poderia digitar "Apartamentos Seattle lago washington" e, em seguida, usar Ctrl-F para localizar termos nos resultados da pesquisa. 

+ As expressões de consulta e filtro são articuladas em uma sintaxe apoiada pela Azure Cognitive Search. O padrão é uma [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), mas você pode opcionalmente usar uma [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [Expressões de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe de OData.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você está trabalhando em sua própria assinatura, é uma boa ideia identificar, no final de um projeto, se você ainda precisa dos recursos criados. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode localizar e gerenciar recursos no portal usando o link **Todos os recursos** ou **Grupos de recursos** no painel de navegação à esquerda.

Se você estiver usando um serviço gratuito, estará limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre estruturas de consulta e sintaxe, use o Carteiro ou uma ferramenta equivalente para criar expressões de consulta que aproveitam mais partes da API. A [API Search REST](https://docs.microsoft.com/rest/api/searchservice/) é especialmente útil para aprendizado e exploração.

> [!div class="nextstepaction"]
> [Crie uma consulta básica no Carteiro](search-query-simple-examples.md)