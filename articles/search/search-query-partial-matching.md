---
title: Termos parciais, padrões e caracteres especiais
titleSuffix: Azure Cognitive Search
description: Use as consultas curinga, Regex e prefixo para corresponder a termos inteiros ou parciais em uma solicitação de consulta do Azure Pesquisa Cognitiva. Padrões de difícil correspondência que incluem caracteres especiais podem ser resolvidos usando a sintaxe de consulta completa e os analisadores personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 05ff56c904fc48a1041ad40f00110a8ff0fd01f1
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592036"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Pesquisa de termo parcial e padrões com caracteres especiais (curinga, Regex, padrões)

Uma *pesquisa de termo parcial* refere-se a consultas que consistem em fragmentos de termo, em que em vez de um termo inteiro, você pode ter apenas o início, o meio ou o fim do termo (às vezes chamados de prefixo, infixo ou consultas de sufixo). Um *padrão* pode ser uma combinação de fragmentos, geralmente com caracteres especiais, como traços ou barras que fazem parte da cadeia de caracteres de consulta. Os casos de uso comuns incluem a consulta de partes de um número de telefone, URL, pessoas ou códigos de produto ou palavras compostas.

A pesquisa parcial e de padrão pode ser problemática se o índice não tiver termos no formato esperado. Durante a [fase de análise lexical](search-lucene-query-architecture.md#stage-2-lexical-analysis) da indexação (supondo o analisador padrão padrão), os caracteres especiais são descartados, as cadeias composta e composto são divididas e o espaço em branco é excluído; tudo isso pode causar falha nas consultas de padrão quando nenhuma correspondência é encontrada. Por exemplo, um número de telefone `+1 (425) 703-6214` como (indexado `"1"`como `"425"`, `"703"`, `"6214"`,) não aparecerá em `"3-62"` uma consulta porque esse conteúdo não existe realmente no índice. 

A solução é invocar um analisador que preserva uma cadeia de caracteres completa, incluindo espaços e caracteres especiais, se necessário, para que você possa corresponder a termos e padrões parciais. Criar um campo adicional para uma cadeia de caracteres intacta, além de usar um analisador de preservação de conteúdo, é a base da solução.

> [!TIP]
> Familiarizado com as APIs de postmaster e REST? [Baixe a coleção de exemplos de consulta](https://github.com/Azure-Samples/azure-search-postman-samples/) para consultar os termos parciais e os caracteres especiais descritos neste artigo.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>O que é a pesquisa parcial no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, a pesquisa parcial e o padrão estão disponíveis nestes formulários:

+ [Pesquisa de prefixo](query-simple-syntax.md#prefix-search), como `search=cap*`, correspondência em "orla marítima Estalagem da Jack Cap'n" ou "GACC capital". Você pode usar a sintaxe de consulta simples ou a sintaxe de consulta Lucene completa para a pesquisa de prefixo.

+ [Pesquisa de curinga](query-lucene-syntax.md#bkmk_wildcard) ou [expressões regulares](query-lucene-syntax.md#bkmk_regex) que pesquisam um padrão ou partes de uma cadeia de caracteres inserida. O curinga e as expressões regulares exigem a sintaxe completa do Lucene. As consultas de sufixo e índice são formuladas como uma expressão regular.

  Alguns exemplos de pesquisa de termo parcial incluem o seguinte. Para uma consulta de sufixo, dado o termo "alfanumérico", você usaria uma pesquisa curinga (`search=/.*numeric.*/`) para encontrar uma correspondência. Para um termo parcial que inclui caracteres interiores, como um fragmento de URL, talvez seja necessário adicionar caracteres de escape. No JSON, uma barra `/` invertida é recortada com `\`uma barra invertida. Como tal, `search=/.*microsoft.com\/azure\/.*/` é a sintaxe para o fragmento de URL "Microsoft.com/Azure/".

Conforme observado, todos os itens acima exigem que o índice contenha cadeias de caracteres em um formato que conduza à correspondência de padrões, que o analisador padrão não fornece. Seguindo as etapas neste artigo, você pode garantir que exista o conteúdo necessário para dar suporte a esses cenários.

## <a name="solving-partialpattern-search-problems"></a>Resolvendo problemas de pesquisa de padrão parcial

Quando você precisar pesquisar em fragmentos ou padrões ou caracteres especiais, poderá substituir o analisador padrão por um analisador personalizado que opera com regras de geração de tokens mais simples, retendo toda a cadeia de caracteres. Voltando um pouco, a abordagem é parecida com esta:

+ Defina um campo para armazenar uma versão intacta da cadeia de caracteres (supondo que você deseja texto analisado e não analisado)
+ Escolha um analisador predefinido ou defina um analisador personalizado para gerar uma cadeia de caracteres intacta não analisada
+ Atribuir o analisador personalizado ao campo
+ Criar e testar o índice

> [!TIP]
> A avaliação de analisadores é um processo iterativo que exige recompilações de índice frequentes. Você pode tornar essa etapa mais fácil usando o postmaster, as APIs REST para [criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index), [excluir índice](https://docs.microsoft.com/rest/api/searchservice/delete-index),[carregar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)e [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents). Para carregar documentos, o corpo da solicitação deve conter um pequeno conjunto de dados representativos que você deseja testar (por exemplo, um campo com números de telefone ou códigos de produto). Com essas APIs na mesma coleção do postmaster, você pode percorrer essas etapas rapidamente.

## <a name="duplicate-fields-for-different-scenarios"></a>Campos duplicados para cenários diferentes

Os analisadores são atribuídos por campo, o que significa que você pode criar campos em seu índice para otimizar para cenários diferentes. Especificamente, você pode definir "featureCode" e "featureCodeRegex" para dar suporte à pesquisa de texto completo regular na primeira e à correspondência de padrão avançada no segundo.

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

Ao escolher um analisador que produz tokens de termo inteiro, os seguintes analisadores são opções comuns:

| Analyz | Comportamentos |
|----------|-----------|
| [Analisadores de idioma](index-add-language-analyzers.md) | Preserva hifens em palavras compostas ou cadeias de caracteres, mutações de vogal e formas de verbo. Se os padrões de consulta incluírem traços, usar um analisador de linguagem pode ser suficiente. |
| [chaves](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | O conteúdo de todo o campo é indexado como um único termo. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Separa somente em espaços em branco. Os termos que incluem traços ou outros caracteres são tratados como um único token. |
| [analisador personalizado](index-add-custom-analyzers.md) | aconselhável A criação de um analisador personalizado permite especificar o filtro de token e criador. Os analisadores anteriores devem ser usados no estado em que se encontram. Um analisador personalizado permite que você escolha quais filtros de criadores e token usar. <br><br>Uma combinação recomendada é a [palavra-chave criador](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) com um [filtro de token em](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)minúsculas. Por si só, o [analisador de palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) predefinido não faz minúsculas em texto em letras maiúsculas, o que pode fazer com que as consultas falhem. Um analisador personalizado oferece um mecanismo para adicionar o filtro de token em minúsculas. |

Se você estiver usando uma ferramenta de teste de API da Web como o postmaster, poderá adicionar a [chamada REST do analisador de teste](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) para inspecionar a saída com tokens.

Você deve ter um índice existente com o qual trabalhar. Dado um índice existente e um campo contendo traços ou termos parciais, você pode experimentar vários analisadores sobre termos específicos para ver quais tokens são emitidos.  

1. Verifique o analisador padrão para ver como os termos são indexados por padrão.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Avalie a resposta para ver como o texto é indexado no índice. Observe como cada termo é inferior e dividido.

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
1. Modifique a solicitação para usar o `whitespace` analisador ou `keyword` :

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Agora, a resposta consiste em um único token, com maiúsculas e minúsculas, com traços preservados como parte da cadeia de caracteres. Se você precisar pesquisar em um padrão ou em um termo parcial, o mecanismo de consulta agora terá a base para encontrar uma correspondência.


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
> Lembre-se de que os analisadores de consulta geralmente são os termos minúsculos em uma expressão de pesquisa ao criar a árvore de consulta. Se você estiver usando um analisador que não faz entradas de texto em letras minúsculas e não estiver obtendo resultados esperados, isso pode ser o motivo. A solução é adicionar um filtro de token de caso inferior, conforme descrito na seção "usar analisadores personalizados" abaixo.

## <a name="configure-an-analyzer"></a>Configurar um analisador
 
Se você estiver avaliando analisadores ou avançando com uma configuração específica, será necessário especificar o analisador na definição de campo e, possivelmente, configurar o próprio analisador se você não estiver usando um analisador interno. Ao trocar analisadores, normalmente você precisa recompilar o índice (remover, recriar e recarregar). 

### <a name="use-built-in-analyzers"></a>Usar analisadores internos

Os analisadores internos ou predefinidos podem ser especificados pelo nome em `analyzer` uma propriedade de uma definição de campo, sem nenhuma configuração adicional necessária no índice. O exemplo a seguir demonstra como você definiria `whitespace` o analisador em um campo. 

Para outros cenários e para saber mais sobre outros analisadores internos, consulte a [lista de analisadores predefinidos](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference). 

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

### <a name="use-custom-analyzers"></a>Usar analisadores personalizados

Se você estiver usando um [analisador personalizado](index-add-custom-analyzers.md), defina-o no índice com uma combinação definida pelo usuário de criador, filtro de token, com as definições de configuração possíveis. Em seguida, faça referência a ela em uma definição de campo, exatamente como você faria com um analisador interno.

Quando o objetivo é a geração de tokens de longo prazo, é recomendado um analisador personalizado que consiste em uma **palavra-chave criador** e um **filtro de token com letras minúsculas** .

+ A palavra-chave criador cria um único token para todo o conteúdo de um campo.
+ O filtro de token em minúsculas transforma letras maiúsculas em texto em minúsculas. Analisadores de consulta geralmente são minúsculos quaisquer entradas de texto em maiúsculas. Letras minúsculas homogenizes as entradas com os termos com token.

O exemplo a seguir ilustra um analisador personalizado que fornece a palavra-chave criador e um filtro de token em minúsculas.

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
> O `keyword_v2` criador e `lowercase` o filtro de token são conhecidos pelo sistema e usando suas configurações padrão, motivo pelo qual você pode referenciá-los por nome sem precisar defini-los primeiro.

## <a name="build-and-test"></a>Build e teste

Depois de definir um índice com analisadores e definições de campo que dão suporte ao seu cenário, carregue os documentos que têm cadeias de caracteres representativas para que você possa testar as consultas de cadeias parciais. 

As seções anteriores explicam a lógica. Esta seção percorre cada API que você deve chamar ao testar sua solução. Conforme observado anteriormente, se você usar uma ferramenta de teste na Web interativa, como o postmaster, poderá percorrer essas tarefas rapidamente.

+ [Excluir índice](https://docs.microsoft.com/rest/api/searchservice/delete-index) remove um índice existente de mesmo nome para que você possa recriá-lo.

+ [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index) cria a estrutura do índice em seu serviço de pesquisa, incluindo definições e campos do analisador com uma especificação do analisador.

+ [Carregar documentos](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importa documentos que têm a mesma estrutura que o índice, bem como conteúdo pesquisável. Após essa etapa, o índice estará pronto para consultar ou testar.

+ O [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) foi introduzido em [escolher um analisador](#choose-an-analyzer). Teste algumas das cadeias de caracteres no índice usando uma variedade de analisadores para entender como os termos são indexados.

+ [Documentos de pesquisa](https://docs.microsoft.com/rest/api/searchservice/search-documents) explica como construir uma solicitação de consulta, usando [sintaxe simples](query-simple-syntax.md) ou a [sintaxe Lucene completa](query-lucene-syntax.md) para caracteres curinga e expressões regulares.

  Para consultas de termo parcial, como consultar "3-6214" para encontrar uma correspondência em "+ 1 (425) 703-6214", você pode usar a sintaxe simples: `search=3-6214&queryType=simple`.

  Para consultas infixos e de sufixo, como consultar "num" ou "numeric para encontrar uma correspondência em" alfanumérico ", use a sintaxe Lucene completa e uma expressão regular:`search=/.*num.*/&queryType=full`

## <a name="tips-and-best-practices"></a>Dicas e práticas recomendadas

### <a name="tune-query-performance"></a>Desempenho de consulta de ajuste

Se você implementar a configuração recomendada que inclui o keyword_v2 criador e o filtro de token em letras minúsculas, poderá notar uma redução no desempenho da consulta devido ao processamento adicional de filtro de token sobre os tokens existentes no índice. 

O exemplo a seguir adiciona um [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) para fazer correspondências de prefixo mais rapidamente. Tokens adicionais são gerados em combinações de caracteres de 2-25 que incluem caracteres: (não apenas MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Como você pode imaginar, a geração de tokens adicional resulta em um índice maior.

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Usar analisadores diferentes para indexação e processamento de consulta

Os analisadores são chamados durante a indexação e durante a execução da consulta. É comum usar o mesmo analisador para ambos, mas você pode configurar analisadores personalizados para cada carga de trabalho. As substituições do analisador são especificadas na definição `analyzers` de [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) em uma seção e, em seguida, referenciadas em campos específicos. 

Quando a análise personalizada é necessária apenas durante a indexação, você pode aplicar o analisador personalizado para apenas indexar e continuar a usar o analisador Lucene padrão (ou outro analisador) para consultas.

Para especificar a análise específica de função, você pode definir propriedades no campo para cada uma, definindo `indexAnalyzer` e `searchAnalyzer` em vez da propriedade `analyzer` padrão.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como os analisadores contribuem para problemas de consulta e solucionam problemas de consulta. Como uma próxima etapa, examine atentamente o impacto do analisador sobre a indexação e o processamento de consultas. Em particular, considere usar a API de análise de texto para retornar a saída com tokens para que você possa ver exatamente o que um analisador está criando para o índice.

+ [Analisadores de linguagem](search-language-support.md)
+ [Analisadores para processamento de texto no Azure Pesquisa Cognitiva](search-analyzers.md)
+ [API de análise de texto (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Como funciona a pesquisa de texto completo (arquitetura de consulta)](search-lucene-query-architecture.md)