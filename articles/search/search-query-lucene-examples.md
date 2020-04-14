---
title: Use a sintaxe completa da consulta lucene
titleSuffix: Azure Cognitive Search
description: Sintaxe de consulta lucene para pesquisa difusa, pesquisa de proximidade, aumento de prazo, pesquisa de expressão regular e pesquisas curinga em um serviço de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bc691299f38d562aee5c08a89e10372331663f8e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262801"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Use a sintaxe de pesquisa "completa" de Lucene (consultas avançadas na Pesquisa Cognitiva do Azure)

Ao construir consultas para a Pesquisa Cognitiva do Azure, você pode substituir o [analisador de consulta simples](query-simple-syntax.md) padrão pelo analisador de consulta lucene mais expansivo [no Azure Cognitive Search](query-lucene-syntax.md) para formular definições de consulta especializadas e avançadas. 

O analisador Lucene suporta construções complexas de consulta, como consultas com escopo de campo, pesquisa difusa, pesquisa curinga infix e sufixo, pesquisa de proximidade, aumento de prazo e pesquisa de expressão regular. A energia adicional vem com requisitos adicionais de processamento, portanto, você deve esperar um tempo de execução um pouco mais longo. Neste artigo, execute exemplos em etapas, que demonstram as operações de consulta disponíveis ao usar a sintaxe completa.

> [!Note]
> Muitas construções de consulta especializadas habilitadas por meio da sintaxe de consulta Lucene completa não são [texto analisado](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente caso espera-se a lematização. A análise lexical é realizada somente em termos completos (uma consulta de termo ou de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação realizada em termos de consulta parcial é a redução. 
>

## <a name="formulate-requests-in-postman"></a>Formular solicitações em Postman

Os exemplos a seguir utilizam um índice de pesquisa de Trabalhos de Nova Iorque que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pelo [OpenData da cidade de Nova Iorque](https://opendata.cityofnewyork.us/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de sandbox fornecido pela Microsoft, o que significa que você não precisa de uma assinatura do Azure ou do Azure Cognitive Search para experimentar essas consultas.

O que é necessário é o Postman ou uma ferramenta equivalente para emitir solicitação HTTP em GET. Para obter mais informações, consulte [explorar com clientes REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho de solicitação

1. No cabeçalho de solicitação, defina **Content-Type** para `application/json`.

2. Adicione uma**api-key** e defina-a para essa cadeia de caracteres: `252044BE3886FE4A8E3BAA4F595114BB`. Essa é uma chave de consulta para o serviço de pesquisa de área restrita que hospeda o índice Trabalhos de Nova Iorque.

Após especificar o cabeçalho de solicitação, você poderá reutilizá-lo para todas as consultas neste artigo, trocando apenas a cadeia de caracteres **search=**. 

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir a URL da solicitação

A solicitação é um comando GET emparelhado com uma URL contendo o ponto final da Pesquisa Cognitiva do Azure e a seqüência de pesquisa.

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição de URL possui os elementos a seguir:

+ **`https://azs-playground.search.windows.net/`** é um serviço de busca de sandbox mantido pela equipe de desenvolvimento da Busca Cognitiva do Azure. 
+ **`indexes/nycjobs/`** é o índice NYC Jobs na coleção de índices desse serviço. O nome do serviço e índice são obrigatórios na solicitação.
+ **`docs`** é a coleção de documentos que contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho da solicitação somente funciona em operações de leitura direcionando a coleção de documentos.
+ **`api-version=2019-05-06`** define a versão api, que é um parâmetro necessário em cada solicitação.
+ **`search=*`** é a seqüência de consulta, que na consulta inicial é nula, retornando os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados. Documentos inteiros são devolvidos, o que permite que você veja todos os campos e todos os valores.

Cole essa URL em um cliente REST como uma etapa de validação e para visualizar a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

A seqüência **`search=*`** de consultas, é uma pesquisa não especificada equivalente à pesquisa nula ou vazia. É a busca mais simples que você pode fazer.

Opcionalmente, você **`$count=true`** pode adicionar à URL para retornar uma contagem dos documentos correspondentes aos critérios de pesquisa. Em uma cadeia de pesquisa vazia, esses são todos os documentos no índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa do Lucene

Adicione **queryType=full** para invocar a sintaxe de consulta completa, substituindo a sintaxe de consulta simples padrão. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Todos os exemplos deste artigo especificam o parâmetro de pesquisa **queryType=full**, indicando que a sintaxe completa é tratada pelo Analisador de Consulta Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: Consulta escopo para uma lista de campos

Este primeiro exemplo não é específico de Lucene, mas nós o lideramos para introduzir o primeiro conceito de consulta fundamental: escopo de campo. Este exemplo escopode toda a consulta e a resposta a apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é Postman ou Search Explorer. 

Por uma questão de brevidade, a consulta direciona apenas o campo *business_title* e especifica que somente os títulos da empresa sejam retornados. O parâmetro **searchFields** restringe a execução da consulta apenas ao campo business_title e **seleciona** especifica quais campos estão incluídos na resposta.

### <a name="search-expression"></a>Expressão de pesquisa

```http
&search=*&searchFields=business_title&$select=business_title
```

Aqui está a mesma consulta com vários campos em uma lista delimitada por vírgula.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Os espaços após as commas são opcionais.

> [!Tip]
> Ao usar a API REST do código do aplicativo, não `$select` se `searchFields`esqueça de parâmetros de codificação de URL como e .

### <a name="full-url"></a>URL completo

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/postman-sample-results.png)

Você deve ter notado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa de texto completo ou porque nenhum critério foi aplicado. Para pesquisa nula sem critérios, as linhas retornam em ordem arbitrária. Quando você inclui critérios de pesquisa reais, você verá os resultados de pesquisa evoluirem para valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: Pesquisa em campo

A sintaxe completa de Lucene suporta escopo de expressões individuais de pesquisa para um campo específico. Este exemplo busca títulos de negócios com o termo sênior neles, mas não júnior.

### <a name="search-expression"></a>Expressão de pesquisa

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Aqui está a mesma consulta com vários campos.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>URL completo

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/intrafieldfilter.png)

Você pode definir uma operação de pesquisa em campo com o **campoNome:pesquisaSsesexpresson,** onde a expressão de pesquisa pode ser uma única palavra ou uma frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores booleanos. Alguns exemplos incluem o seguinte:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias strings entre aspas se você quiser que ambas as strings sejam `state` avaliadas como uma única entidade, como neste caso procurando por dois locais distintos no campo. Além disso, verifique se o operador está em maiúsculas, como você pode ver com NÃO e E.

O campo especificado no **campoNome:pesquisaExpressão** deve ser um campo pesquisável. Consulte [Create Index (API de pesquisa cognitiva do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

> [!NOTE]
> No exemplo acima, não precisávamos `searchFields` usar o parâmetro porque cada parte da consulta tem um nome de campo explicitamente especificado. No entanto, você `searchFields` ainda pode usar o parâmetro se quiser executar uma consulta onde algumas partes são escopo para um campo específico, e o resto pode se aplicar a vários campos. Por exemplo, a `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` consulta `senior NOT junior` corresponderia `business_title` apenas ao campo, enquanto `posting_type` combinaria "externo" com o campo. O nome de campo fornecido no **campoName:searchExpression** sempre tem precedência sobre o `searchFields` parâmetro, `business_title` e `searchFields` é por isso que, neste exemplo, não precisamos incluir no parâmetro.

## <a name="example-3-fuzzy-search"></a>Exemplo 3: pesquisa difusa

A sintaxe Lucene completa também dá suporte à pesquisa difusa, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, acrescente o símbolo til `~` ao final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornariam mar, amar e maré.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

As frases não são suportadas diretamente, mas você pode especificar uma correspondência difusa em partes componentes de uma frase.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>URL completo

Essa consulta pesquisa trabalhos com o termo "associado" (deliberadamente com ortografia incorreta):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Resposta de pesquisa difusa](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> As consultas difusas não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: pesquisa por proximidade
As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, "hotel aeroporto"~5 encontrará os termos hotel e aeroporto em cinco palavras uma da outra em um documento.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>URL completo

Nessa consulta, para trabalhos com o termo "analista sênior", em que é separado por até uma palavra:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Consulta de proximidade](media/search-query-lucene-examples/proximity-before.png)

Tente novamente removendo as palavras entre o termo "analista sênior". Observe que 8 documentos são retornados para essa consulta, em oposição a 10 para a consulta anterior.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Exemplo 5: aumento de termos
O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. 

### <a name="full-urls"></a>URLs completos

Nessa consulta para "antes", pesquise trabalhos com o termo *analista de computador* e observe que não há resultados com as palavras *computador* e *analista*, mas os trabalhos de *computador* estão no topo dos resultados.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Aumento antes de termos](media/search-query-lucene-examples/termboostingbefore.png)

Na consulta para "depois" repita a pesquisa, dessa vez, aumentando os resultados com o termo *analista* em relação ao termo *computador*, caso ambas as palavras não existam. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Uma versão em formato mais legível por humanos da consulta acima é `search=business_title:computer analyst^2`. Para uma consulta que funcione, `^2` é codificado como `%5E2`, o que é mais difícil de ver.

  ![Aumento depois de termos](media/search-query-lucene-examples/termboostingafter.png)

O aumento de termos difere dos perfis de pontuação, em que os perfis de pontuação aumentam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como **gênero** no exemplo de índice de loja de música. O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, "rock^2 eletrônico" melhorará a posição dos documentos que contêm os termos de pesquisa no campo **gênero** , à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa "rock" serão mais bem classificados do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Ao definir o nível do fator, quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,2).


## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>URL completo

Nesta consulta, procure empregos com o termo Sênior `search=business_title:/(Sen|Jun)ior/`ou Júnior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Consulta de regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> Consultas Regex não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: pesquisa com curinga
Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (\*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

### <a name="search-expression"></a>Expressão de pesquisa

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>URL completo

Nessa consulta, pesquise trabalhos que contenham o prefixo 'prog', que incluiria títulos de negócios com os termos programação e programador. Não é possível usar um símbolo * ou ? como o primeiro caractere de uma pesquisa.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Consulta de caractere curinga](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Consultas com curingas não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="next-steps"></a>Próximas etapas
Tente especificar o Analisador de Consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e para a API REST. Os links usam a sintaxe simples padrão e, portanto, será necessário aplicar o que você aprendeu neste artigo para especificar o **queryType**.

* [Consulta seu índice usando o .NET SDK](search-query-dotnet.md)
* [Consulta seu índice usando a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe simples](search-query-simple-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)