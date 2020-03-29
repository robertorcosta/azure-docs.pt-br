---
title: Padrões de correspondência e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Use consultas curinga e prefixo para corresponder em termos inteiros ou parciais em uma solicitação de consulta de pesquisa cognitiva do Azure. Padrões difíceis de combinar que incluem caracteres especiais podem ser resolvidos usando sintaxe de consulta completa e analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289304"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Combinar em padrões e caracteres especiais (traços)

Para consultas que incluem`-, *, (, ), /, \, =`caracteres especiais ( ), ou para padrões de consulta com base em termos parciais dentro de um termo maior, etapas adicionais de configuração são normalmente necessárias para garantir que o índice contenha o conteúdo esperado, no formato certo. 

Por padrão, um `+1 (425) 703-6214` número de telefone `"1"` `"425"`como `"703"` `"6214"`é tokenizado como , , . Como você pode imaginar, pesquisar em `"3-62"`termos parciais que incluem um traço, falhará porque esse conteúdo realmente não existe no índice. 

Quando você precisa pesquisar em strings parciais ou caracteres especiais, você pode substituir o analisador padrão por um analisador personalizado que opera regras de tokenização mais simples, preservando termos inteiros, necessários quando as strings de consulta incluem partes de um termo ou especial Caracteres. Dando um passo atrás, a abordagem se parece com isso:

+ Escolha um analisador predefinido ou defina um analisador personalizado que produza a saída desejada
+ Atribuir o analisador ao campo
+ Construa o índice e teste

Este artigo orienta você através dessas tarefas. A abordagem descrita aqui é útil em outros cenários: curinga e consultas de expressão regulares também precisam de termos inteiros como base para a correspondência de padrões. 

> [!TIP]
> Avaliar analyers é um processo iterativo que requer reconstruções freqüentes do índice. Você pode facilitar essa etapa usando o Carteiro, as APIs REST para [Criar Índice,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Excluir Índice,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Carregar Documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e Documentos [de Pesquisa.](https://docs.microsoft.com/rest/api/searchservice/search-documents) Para documentos de carga, o órgão de solicitação deve conter um pequeno conjunto de dados representativo que você deseja testar (por exemplo, um campo com números de telefone ou códigos de produto). Com essas APIs na mesma coleção de Carteiro, você pode percorrer essas etapas rapidamente.

## <a name="choosing-an-analyzer"></a>Escolhendo um analisador

Ao escolher um analisador que produz tokens a longo prazo, os seguintes analisadores são escolhas comuns:

| Analyzer | Comportamentos |
|----------|-----------|
| [Palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é tokenizado como um único termo. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa-se apenas em espaços brancos. Termos que incluem traços ou outros caracteres são tratados como um único token. |
| [analisador personalizado](index-add-custom-analyzers.md) | (recomendado) A criação de um analisador personalizado permite especificar o tokenizador e o filtro de token. Os analisadores anteriores devem ser usados como estão. Um analisador personalizado permite escolher quais tokenizadores e filtros de token usar. <br><br>Uma combinação recomendada é o [tokenizador de palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de token minúsculo](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Por si só, o analisador de [palavras-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não reduz qualquer texto maiúsculo, o que pode fazer com que as consultas falhem. Um analisador personalizado lhe dá um mecanismo para adicionar o filtro de token minúsculo. |

Se você estiver usando uma ferramenta de teste de API da Web como o Carteiro, você pode adicionar a [chamada Test Analyzer REST](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída tokenizada. Dado um índice existente e um campo contendo traços ou termos parciais, você pode tentar vários analisadores em termos específicos para ver quais tokens são emitidos.  

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
> Esteja ciente de que os analisadores de consulta geralmente reduzem os termos em uma expressão de pesquisa ao construir a árvore de consulta. Se você estiver usando um analisador que não reduz a entrada de texto, e você não está obtendo resultados esperados, esta pode ser a razão. A solução é adicionar um filtro de token de caixa lwower.

## <a name="analyzer-definitions"></a>Definições do analisador
 
Se você está avaliando analisadores ou avançando com uma configuração específica, você precisará especificar o analisador na definição de campo e, possivelmente, configurar o analisador em si se você não estiver usando um analisador embutido. Ao trocar analisadores, você normalmente precisa reconstruir o índice (queda, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Use analisadores embutidos

Analisadores incorporados ou predefinidos podem ser especificados por nome em uma `analyzer` propriedade de uma definição de campo, sem necessidade de configuração adicional no índice. O exemplo a seguir demonstra `whitespace` como você definiria o analisador em um campo.

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
Para obter mais informações sobre todos os analisadores incorporados disponíveis, consulte [lista de analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

### <a name="use-custom-analyzers"></a>Use analisadores personalizados

Se você estiver usando um [analisador personalizado,](index-add-custom-analyzers.md)defina-o no índice com uma combinação definida pelo usuário de tokenizer, tokenfilter, com possíveis configurações. Em seguida, faça referência a uma definição de campo, assim como você faria com um analisador embutido.

Quando o objetivo é a tokenização a longo prazo, recomenda-se um analisador personalizado que consiste em um **tokenizador de palavras-chave** e **filtro de token minúsculo.**

+ O tokenizador de palavras-chave cria um único token para todo o conteúdo de um campo.
+ O filtro de token minúsculo transforma letras maiúsculas em texto minúsculo. Os analisadores de consulta normalmente são minúsculos em qualquer entrada de texto maiúscula. A redução da caixa homogeneiza as entradas com os termos tokenizados.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para diferentes cenários

Outra opção aproveita a atribuição do analisador por campo para otimizar para diferentes cenários. Especificamente, você pode definir "featureCode" e "featureCodeRegex" para suportar a pesquisa completa regular de texto no primeiro, e correspondência avançada de padrões no segundo.

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
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como os analisadores contribuem tanto para problemas de consulta quanto para resolver problemas de consulta. Como próximo passo, dê uma olhada mais de perto no impacto do analisador na indexação e processamento de consultas. Em particular, considere usar a API Analisar texto para retornar a saída tokenizada para que você possa ver exatamente o que um analisador está criando para o seu índice.

+ [Analisadores de linguagem](search-language-support.md)
+ [Analisadores para processamento de texto na Pesquisa Cognitiva do Azure](search-analyzers.md)
+ [Analisar API de texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa completa de texto (arquitetura de consulta)](search-lucene-query-architecture.md)
