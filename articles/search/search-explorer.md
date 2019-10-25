---
title: Use a ferramenta Search Explorer para consultar dados no portal do Azure
titleSuffix: Azure Cognitive Search
description: O Search Explorer é integrado ao portal do Azure, útil para explorar conteúdo e validar consultas no Pesquisa Cognitiva do Azure. Insira cadeias de caracteres para pesquisa de termo ou frase ou expressões de pesquisa totalmente qualificadas com sintaxe avançada.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0bb50bd50c58b85f1d5de100c34d00d114a9ed77
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792960"
---
# <a name="use-search-explorer-in-the-azure-portal-for-querying-documents-in-azure-cognitive-search"></a>Usar o Gerenciador de pesquisa no portal do Azure para consultar documentos no Azure Pesquisa Cognitiva 

Este artigo mostra como consultar um índice de Pesquisa Cognitiva do Azure existente usando o **Search Explorer** no portal do Azure. Você pode iniciar o Search Explorer na barra de comandos para enviar expressões de consulta de Lucene simples ou completas para qualquer índice existente em seu serviço. 

   ![Comando Search Explorer no portal](./media/search-explorer/search-explorer-cmd2.png "Comando Search Explorer no portal")

## <a name="basic-search-strings"></a>Cadeias de caracteres de pesquisa básica

Os exemplos a seguir pressupõem o índice de exemplo interno de imóveis. Você pode criar esse índice usando o assistente para importar dados no portal, escolhendo **exemplos** como a fonte de dados.

### <a name="example-1---empty-search"></a>Exemplo 1 – pesquisa vazia

Para a primeira observação do seu código, execute uma pesquisa vazia clicando em **Pesquisar** sem nenhum termo fornecido. Uma pesquisa vazia é útil como uma primeira consulta porque ela retorna documentos inteiros para que você possa examinar a composição do documento. Em uma pesquisa vazia, não há nenhuma classificação de pesquisa e os documentos são retornados em ordem arbitrária (`"@search.score": 1` para todos os documentos). Por padrão, são retornados 50 documentos em uma solicitação de pesquisa.

Uma sintaxe equivalente para uma pesquisa vazia é `*` ou `search=*`.

   ```Input
   search=*
   ```

   **Resultados**
   
   ![Exemplo de consulta vazia](./media/search-explorer/search-explorer-example-empty.png "Exemplo de consulta não qualificada ou vazia")

### <a name="example-2---free-text-search"></a>Exemplo 2 – pesquisa de texto livre

Consultas de forma livre, com ou sem operadores, são úteis para simular consultas definidas pelo usuário enviadas de um aplicativo personalizado para o Azure Pesquisa Cognitiva. Observe que, quando você fornece termos ou expressões de consulta, a classificação de pesquisa entra em cena. O exemplo a seguir ilustra uma pesquisa de texto livre.

   ```Input
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **Resultados**

   Você pode usar Ctrl-F para pesquisar termos específicos de interesse nos resultados.

   ![Exemplo de consulta de texto livre](./media/search-explorer/search-explorer-example-freetext.png "Exemplo de consulta de texto livre")

### <a name="example-3---count-of-matching-documents"></a>Exemplo 3 – contagem de documentos correspondentes 

Adicione **$count** para obter o número de correspondências encontradas em um índice. Em uma pesquisa vazia, a contagem é o número total de documentos no índice. Em uma pesquisa qualificada, é o número de documentos que correspondem à entrada da consulta.

   ```Input1
   $count=true
   ```
   **Resultados**

   ![Exemplo de contagem de documentos](./media/search-explorer/search-explorer-example-count.png "Contagem de documentos correspondentes no índice")

### <a name="example-4---restrict-fields-in-search-results"></a>Exemplo 4 – restringir os campos nos resultados da pesquisa

Adicione **$select** para limitar os resultados aos campos nomeados explicitamente para a saída mais legível no **Gerenciador de pesquisa**. Para manter a cadeia de caracteres de pesquisa e **$count=true**, coloque **&** como prefixo dos argumentos. 

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **Resultados**

   ![Exemplo de limitar campos](./media/search-explorer/search-explorer-example-selectfield.png "Restringir campos nos resultados da pesquisa")

### <a name="example-5---return-next-batch-of-results"></a>Exemplo 5 – retornar o próximo lote de resultados

O Azure Pesquisa Cognitiva retorna as principais correspondências 50 com base na classificação de pesquisa. Para obter o próximo conjunto de documentos correspondentes, acrescente **$top=100,&$skip=50** para aumentar o conjunto de resultados para 100 documentos (o padrão é 50, o máximo é 1000), ignorando os primeiros 50 documentos. Lembre-se de que você precisa fornecer critérios de pesquisa, como um termo ou expressão de consulta, para obter os resultados classificados. Observe que as pontuações de pesquisa diminuem quanto mais você avança nos resultados da pesquisa.

   ```Input
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **Resultados**

   ![Resultados da pesquisa em lotes](./media/search-explorer/search-explorer-example-topskip.png "Retornar o próximo lote dos resultados da pesquisa")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>Expressões de filtro (maior que, menor que, igual a)

Use o parâmetro **$filter** quando desejar especificar critérios precisos em vez de pesquisa de texto livre. Este exemplo pesquisa quartos maior que 3:

   ```Input
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **Resultados**

   ![Expressão de filtro](./media/search-explorer/search-explorer-example-filter.png "Filtrar por critérios")

## <a name="order-by-expressions"></a>Expressões orderby

Adicione **$orderby** para classificar os resultados por outro campo além da pontuação de pesquisa. Uma expressão de exemplo que você pode usar para testar isso é:

   ```Input
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **Resultados**

   ![Expressão OrderBy](./media/search-explorer/search-explorer-example-ordery.png "Alterar a ordem de classificação")

As expressões **$filter** e **$orderby** são construções do OData. Para saber mais, confira [Sintaxe de filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search).

<a name="start-search-explorer"></a>

## <a name="how-to-start-search-explorer"></a>Como iniciar o gerenciador de pesquisa

1. No [portal do Azure](https://portal.azure.com), abra a página de serviço de pesquisa no painel ou [localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de visão geral do serviço, clique em **Gerenciador de pesquisa**.

   ![Comando Search Explorer no portal](./media/search-explorer/search-explorer-cmd2.png "Comando Search Explorer no portal")

3. Selecione o índice a ser consultado.

   ![Selecionar o índice a ser consultado](./media/search-explorer/search-explorer-changeindex-se2.png "Selecionar o índice")

4. Opcionalmente, defina a versão da API. Por padrão, a versão atual disponível de forma geral da API é selecionada, mas você poderá escolher uma versão prévia ou API mais antiga, se a sintaxe que deseja usar for específica da versão.

5. Após o índice e a versão da API serem selecionados, insira os termos de pesquisa ou expressões de consulta totalmente qualificadas na barra de pesquisa e clique em **Pesquisar** para executar.

   ![Insira os termos de pesquisa e clique em Pesquisar](./media/search-explorer/search-explorer-query-string-example.png "Insira os termos de pesquisa e clique em Pesquisar")

Dicas para pesquisar no **Gerenciador de pesquisa**:

+ Os resultados são retornados como documentos JSON detalhados para que você possa exibir o conteúdo e a construção do documento na totalidade. Você pode usar expressões de consulta, mostradas nos exemplos, para limitar quais campos são retornados.

+ Os documentos contêm todos os campos marcados como **Recuperáveis** no índice. Para exibir atributos de índice no portal, clique em *realestate-us-sample* na lista **Índices** na página de visão geral da pesquisa.

+ As consultas de forma livre, semelhantes ao que você pode digitar em um navegador da Web comercial, são úteis para testar uma experiência do usuário final. Por exemplo, supondo o índice de exemplo realestate interno, você poderia digitar "Apartamentos Seattle lago washington" e, em seguida, usar Ctrl-F para localizar termos nos resultados da pesquisa. 

+ Expressões de filtro e consulta devem ser articuladas em uma sintaxe com suporte pelo Pesquisa Cognitiva do Azure. O padrão é uma [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search), mas você pode opcionalmente usar uma [Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) para consultas mais poderosas. [Expressões de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) são uma sintaxe de OData.


## <a name="next-steps"></a>Próximos passos

Os recursos a seguir fornecem exemplos e informações de sintaxe de consulta adicionais.

 + [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Exemplos de consulta do Lucene](search-query-lucene-examples.md) 
 + [Sintaxe de expressão do filtro OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
