---
title: Termos parciais, padrões e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Use consultas curinga, regex e prefixo para corresponder em termos inteiros ou parciais em uma solicitação de consulta de pesquisa cognitiva do Azure. Padrões difíceis de combinar que incluem caracteres especiais podem ser resolvidos usando sintaxe de consulta completa e analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: faafc1e12f0703c38b4e602700b1e775bf13a061
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998342"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Busca parcial de termo e padrões com caracteres especiais (curinga, regex, padrões)

Uma *pesquisa de termo parcial* refere-se a consultas que consistem em fragmentos de termo, como a primeira, última ou partes interiores de uma string. Um *padrão* pode ser uma combinação de fragmentos, às vezes com caracteres especiais, como traços ou barras que fazem parte da consulta. Os casos de uso comuns incluem consultas para partes de um número de telefone, URL, pessoas ou códigos de produto ou palavras compostas.

A pesquisa parcial pode ser problemática se o índice não tiver termos no formato necessário para a correspondência de padrões. Durante a fase de análise de texto da indexação, usando o analisador padrão padrão, caracteres especiais são descartados, as seqüências compostas e compostas são divididas, fazendo com que as consultas de padrão falhem quando nenhuma correspondência é encontrada. Por exemplo, um `+1 (425) 703-6214`número de telefone `"1"` `"425"`como `"703"` `"6214"`(tokenizado como , `"3-62"` , ) não aparecerá em uma consulta porque esse conteúdo realmente não existe no índice. 

A solução é invocar um analisador que preserve uma seqüência completa, incluindo espaços e caracteres especiais, se necessário, para que você possa combinar em termos e padrões parciais. Criar um campo adicional para uma seqüência intacta, além de usar um analisador de preservação de conteúdo, é a base da solução.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>O que é pesquisa parcial na Pesquisa Cognitiva do Azure

Na Pesquisa Cognitiva do Azure, a pesquisa parcial e o padrão estão disponíveis nestes formulários:

+ [Pesquisa por prefixo,](query-simple-syntax.md#prefix-search)como, `search=cap*`combinando em "Cap'n Jack's Waterfront Inn" ou "Gacc Capital". Você pode usar a sintaxe de consulta simples para pesquisa por prefixo.

+ [Pesquisa curinga](query-lucene-syntax.md#bkmk_wildcard) ou [expressões regulares](query-lucene-syntax.md#bkmk_regex) que procuram um padrão ou partes de uma seqüência incorporada, incluindo o sufixo. Curinga e expressões regulares requerem a sintaxe de Lucene completa. 

  Alguns exemplos de pesquisa a termo parcial incluem o seguinte. Para uma consulta de sufixo, dado o termo "alfanumérico", você usaria uma pesquisa curinga (`search=/.*numeric.*/`) para encontrar uma correspondência. Para um termo parcial que inclui caracteres, como um fragmento de URL, você pode precisar adicionar caracteres de fuga. Em JSON, uma `/` barra para a `\`frente é escapada com uma barra para trás . Como tal, `search=/.*microsoft.com\/azure\/.*/` é a sintaxe do fragmento de URL "microsoft.com/azure/".

Como observado, todos os acima requerem que o índice contenha strings em um formato propício à correspondência de padrões, que o analisador padrão não fornece. Seguindo as etapas deste artigo, você pode garantir que o conteúdo necessário exista para suportar esses cenários.

## <a name="solving-partial-search-problems"></a>Resolvendo problemas parciais de busca

Quando você precisa pesquisar em padrões ou caracteres especiais, você pode substituir o analisador padrão por um analisador personalizado que opera sob regras de tokenização mais simples, mantendo toda a seqüência. Dando um passo atrás, a abordagem se parece com isso:

+ Defina um campo para armazenar uma versão intacta da string (supondo que você queira texto analisado e não analisado)
+ Escolha um analisador predefinido ou defina um analisador personalizado para produzir uma seqüência intacta
+ Atribuir o analisador ao campo
+ Construa e teste o índice

> [!TIP]
> Avaliar analisadores é um processo iterativo que requer reconstruções frequentes de índices. Você pode facilitar essa etapa usando o Carteiro, as APIs REST para [Criar Índice,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Excluir Índice,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Carregar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e Documentos [de Pesquisa.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Para documentos de carga, o órgão de solicitação deve conter um pequeno conjunto de dados representativo que você deseja testar (por exemplo, um campo com números de telefone ou códigos de produto). Com essas APIs na mesma coleção de Carteiro, você pode percorrer essas etapas rapidamente.

## <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para diferentes cenários

Os analisadores são atribuídos por campo, o que significa que você pode criar campos em seu índice para otimizar para diferentes cenários. Especificamente, você pode definir "featureCode" e "featureCodeRegex" para suportar a pesquisa completa regular de texto no primeiro, e correspondência avançada de padrões no segundo.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Escolha um analisador

Ao escolher um analisador que produz tokens a longo prazo, os seguintes analisadores são escolhas comuns:

| Analyzer | Comportamentos |
|----------|-----------|
| [Palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é tokenizado como um único termo. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa-se apenas em espaços brancos. Termos que incluem traços ou outros caracteres são tratados como um único token. |
| [analisador personalizado](index-add-custom-analyzers.md) | (recomendado) A criação de um analisador personalizado permite especificar o tokenizador e o filtro de token. Os analisadores anteriores devem ser usados como estão. Um analisador personalizado permite escolher quais tokenizadores e filtros de token usar. <br><br>Uma combinação recomendada é o [tokenizador de palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de token minúsculo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Por si só, o analisador de [palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não reduz qualquer texto maiúsculo, o que pode fazer com que as consultas falhem. Um analisador personalizado lhe dá um mecanismo para adicionar o filtro de token minúsculo. |

Se você estiver usando uma ferramenta de teste de API da Web como o Carteiro, você pode adicionar a [chamada Test Analyzer REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída tokenizada.

Você deve ter um índice existente para trabalhar. Dado um índice existente e um campo contendo traços ou termos parciais, você pode tentar vários analisadores em termos específicos para ver quais tokens são emitidos.  

1. Verifique o analisador Padrão para ver como os termos são tokenizados por padrão.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Avalie a resposta para ver como o texto é tokenizado dentro do índice. Observe como cada termo é minúsculo e quebrado.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Modificar a solicitação `whitespace` para `keyword` usar o analisador:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Agora, a resposta consiste em um único token, maiúsculo, com traços preservados como parte da seqüência. Se você precisar pesquisar em um padrão ou um termo parcial, o mecanismo de consulta agora tem a base para encontrar uma correspondência.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Esteja ciente de que os analisadores de consulta geralmente reduzem os termos em uma expressão de pesquisa ao construir a árvore de consulta. Se você estiver usando um analisador que não reduz a entrada de texto, e você não está obtendo resultados esperados, esta pode ser a razão. A solução é adicionar um filtro de token minúsculo, conforme descrito na seção "Use analisadores personalizados" abaixo.

## <a name="configure-an-analyzer"></a>Configure um analisador
 
Se você está avaliando analisadores ou avançando com uma configuração específica, você precisará especificar o analisador na definição de campo e, possivelmente, configurar o analisador em si se você não estiver usando um analisador embutido. Ao trocar analisadores, você normalmente precisa reconstruir o índice (queda, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Use analisadores embutidos

Analisadores incorporados ou predefinidos podem ser especificados por nome em uma `analyzer` propriedade de uma definição de campo, sem necessidade de configuração adicional no índice. O exemplo a seguir demonstra `whitespace` como você definiria o analisador em um campo. Para obter mais informações sobre analisadores incorporados disponíveis, consulte [lista de analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Use analisadores personalizados

Se você estiver usando um [analisador personalizado,](index-add-custom-analyzers.md)defina-o no índice com uma combinação definida pelo usuário de tokenizer, filtro de token, com possíveis configurações. Em seguida, faça referência a uma definição de campo, assim como você faria com um analisador embutido.

Quando o objetivo é a tokenização a longo prazo, recomenda-se um analisador personalizado que consiste em um **tokenizador de palavras-chave** e **filtro de token minúsculo.**

+ O tokenizador de palavras-chave cria um único token para todo o conteúdo de um campo.
+ O filtro de token minúsculo transforma letras maiúsculas em texto minúsculo. Os analisadores de consulta normalmente são minúsculos em qualquer entrada de texto maiúscula. O invólucro inferior homogeneiza as entradas com os termos tokenizados.

O exemplo a seguir ilustra um analisador personalizado que fornece o tokenizador de palavras-chave e um filtro de token minúsculo.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> O `keyword_v2` tokenizador e `lowercase` o filtro de token são conhecidos pelo sistema e usando suas configurações padrão, e é por isso que você pode referencia-los pelo nome sem ter que defini-los primeiro.

## <a name="build-and-test"></a>Build e teste

Depois de definir um índice com analisadores e definições de campo que suportam seu cenário, carregue documentos que tenham strings representativas para que você possa testar consultas parciais de strings. 

As seções anteriores explicaram a lógica. Esta seção passa por cada API que você deve chamar ao testar sua solução. Como observado anteriormente, se você usar uma ferramenta de teste web interativa, como o Carteiro, você pode passar por essas tarefas rapidamente.

+ [O Índice de](https://docs.microsoft.com/rest/api/searchservice/delete-index) Exclusão remove um índice existente com o mesmo nome para que você possa recriá-lo.

+ [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) cria a estrutura de índice em seu serviço de pesquisa, incluindo definições de analisadores e campos com uma especificação do analisador.

+ [Carregar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importa documentos com a mesma estrutura do seu índice, bem como conteúdo pesquisável. Após esta etapa, seu índice está pronto para consultar ou testar.

+ [O Analisador de Teste](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) foi introduzido em [Escolher um analisador](#choose-an-analyzer). Teste algumas das strings do seu índice usando uma variedade de analisadores para entender como os termos são tokenizados.

+ [Documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) explicacomo construir uma solicitação de consulta, usando [sintaxe simples](query-simple-syntax.md) ou [sintaxe lucene completa](query-lucene-syntax.md) para curinga e expressões regulares.

## <a name="tips-and-best-practices"></a>Dicas e práticas recomendadas

### <a name="tune-query-performance"></a>Desempenho de consulta de ajuste

Se você implementar a configuração recomendada que inclui o tokenizador keyword_v2 e o filtro de token em minúsculas, poderá notar uma diminuição no desempenho da consulta devido ao processamento adicional do filtro de token em relação aos tokens existentes em seu índice. 

O exemplo a seguir adiciona um [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) para tornar as correspondências de prefixo mais rápidas. Tokens adicionais são gerados para combinações de caracteres de 2 a 25 caracteres que incluem caracteres: (não apenas MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Como você pode imaginar, a tokenização adicional resulta em um índice maior.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Use diferentes analisadores para indexação e processamento de consultas

Os analisadores são chamados durante a indexação e durante a execução da consulta. É comum usar o mesmo analisador para ambos, mas você pode configurar analisadores personalizados para cada carga de trabalho. As substituições do analisador são especificadas na definição do [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) em uma `analyzers` seção e, em seguida, referenciadas em campos específicos. 

Quando a análise personalizada é necessária apenas durante a indexação, você pode aplicar o analisador personalizado apenas para indexar e continuar a usar o analisador lucene padrão (ou outro analisador) para consultas.

Para especificar uma análise específica da função, você pode `indexAnalyzer` `searchAnalyzer` definir propriedades `analyzer` no campo para cada uma, configuração e, em vez da propriedade padrão.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como os analisadores contribuem tanto para problemas de consulta quanto para resolver problemas de consulta. Como próximo passo, dê uma olhada mais de perto no impacto do analisador na indexação e processamento de consultas. Em particular, considere usar a API Analisar texto para retornar a saída tokenizada para que você possa ver exatamente o que um analisador está criando para o seu índice.

+ [Analisadores de linguagem](search-language-support.md)
+ [Analisadores para processamento de texto na Pesquisa Cognitiva do Azure](search-analyzers.md)
+ [Analisar API de texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa completa de texto (arquitetura de consulta)](search-lucene-query-architecture.md)