---
title: Criar uma consulta semântica
titleSuffix: Azure Cognitive Search
description: Defina um tipo de consulta semântica para anexar os modelos de aprendizado profundo ao processamento de consultas, à intenção de referência e ao contexto como parte da classificação e da relevância da pesquisa.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 7f7a09b9e20b461a8a1e448bf4a7b0747a35fbb1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487127"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Criar uma consulta semântica no Pesquisa Cognitiva

> [!IMPORTANT]
> O tipo de consulta semântica está em visualização pública, disponível por meio da API REST de visualização e portal do Azure. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Durante a inicialização de visualização inicial, não há nenhum encargo para a pesquisa semântica. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

Neste artigo, aprenda a formular uma solicitação de pesquisa que usa a classificação semântica e produz legendas semânticas e respostas.

As consultas semânticas tendem a funcionar melhor em índices de pesquisa que são criados com conteúdo com texto pesado, como PDFs ou documentos com grandes partes de texto.

## <a name="prerequisites"></a>Pré-requisitos

+ Um serviço de pesquisa em uma camada Standard (S1, S2, S3), localizado em uma destas regiões: Norte EUA Central, oeste dos EUA, oeste dos EUA 2, leste dos EUA 2, Europa Setentrional Europa Ocidental. Se você tiver um serviço S1 ou superior existente em uma dessas regiões, poderá solicitar acesso sem precisar criar um novo serviço.

+ Acesso à visualização da pesquisa semântica: [inscrever-se](https://aka.ms/SemanticSearchPreviewSignup)

+ Um índice de pesquisa existente, contendo conteúdo em inglês

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve dar suporte a APIs REST de visualização na solicitação de consulta. Você pode usar o [postmaster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)ou código que você modificou para fazer chamadas REST para as APIs de visualização. Você também pode usar o [Gerenciador de pesquisa](search-explorer.md) no portal do Azure para enviar uma consulta semântica.

+ Uma solicitação de [pesquisa de documentos](/rest/api/searchservice/preview-api/search-documents) com a opção semântica e outros parâmetros descritos neste artigo.

## <a name="whats-a-semantic-query"></a>O que é uma consulta semântica?

No Pesquisa Cognitiva, uma consulta é uma solicitação parametrizada que determina o processamento da consulta e a forma da resposta. Uma *consulta semântica* adiciona parâmetros que invocam o modelo de reclassificação semântica que pode avaliar o contexto e o significado dos resultados correspondentes, promover correspondências mais relevantes na parte superior e retornar respostas e legendas semânticas.

A solicitação a seguir é representativa de uma consulta semântica básica (sem respostas).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Assim como acontece com todas as consultas no Pesquisa Cognitiva, a solicitação visa a coleção de documentos de um único índice. Além disso, uma consulta semântica passa da mesma sequência de análise, análise e verificação como uma consulta não semântica. A diferença está na forma como a relevância é calculada. Conforme definido nesta versão de visualização, uma consulta semântica é aquela cujos *resultados* são processados novamente usando algoritmos avançados, fornecendo uma maneira de trazer as correspondências consideradas mais relevantes pelo classificador semânticos, em vez das pontuações atribuídas pelo algoritmo de classificação de similaridade padrão. 

Somente as principais correspondências 50 dos resultados iniciais podem ser classificadas semanticamente e todas as legendas de inclusão na resposta. Opcionalmente, você pode especificar um **`answer`** parâmetro na solicitação para extrair uma resposta em potencial. Esse modelo Formula até cinco respostas possíveis para a consulta, que você pode optar por renderizar na parte superior da página de pesquisa.

## <a name="query-using-rest-apis"></a>Consultar usando APIs REST

A especificação completa da API REST pode ser encontrada em [Pesquisar documentos (visualização REST)](/rest/api/searchservice/preview-api/search-documents).

As consultas semânticas fornecem legendas e realces automaticamente. Se você quiser que a resposta inclua respostas, você pode adicionar um **`answer`** parâmetro opcional na solicitação. Esse parâmetro, mais a construção da própria cadeia de caracteres de consulta, produzirá uma resposta na resposta.

O exemplo a seguir usa o índice Hotéis-Sample-index para criar uma solicitação de consulta semântica com respostas semânticas e legendas:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>Formular a solicitação

Esta seção percorre os parâmetros de consulta necessários para a pesquisa semântica.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Etapa 1: definir QueryType e queryLanguage

Adicione os parâmetros a seguir ao restante. Ambos os parâmetros são obrigatórios.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

O queryLanguage deve ser consistente com quaisquer [analisadores de idioma](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. Se queryLanguage for "en-US", qualquer analisador de linguagem também deverá ser uma variante em inglês ("en. Microsoft" ou "en. Lucene"). Qualquer analisador independente de linguagem, como palavra-chave ou simples, não tem conflito com valores de queryLanguage.

Em uma solicitação de consulta, se você também estiver usando a [correção ortográfica](speller-how-to-add.md), o queryLanguage definido se aplicará igualmente ao verificador ortográfico, às respostas e às legendas. Não há nenhuma substituição para partes individuais. 

Embora o conteúdo em um índice de pesquisa possa ser composto em vários idiomas, a entrada da consulta provavelmente está em um. O mecanismo de pesquisa não verifica a compatibilidade do queryLanguage, do analisador de linguagem e do idioma em que o conteúdo é composto, portanto, certifique-se de fazer o escopo das consultas de forma adequada para evitar a produção de resultados incorretos.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Etapa 2: definir searchFields

Esse parâmetro é opcional, pois não há nenhum erro se você deixá-lo fora, mas fornecer uma lista ordenada de campos é altamente recomendável para legendas e respostas.

O parâmetro searchFields é usado para identificar passagens a serem avaliadas para "similaridade semântica" para a consulta. Para a versão prévia, não recomendamos deixar searchFields em branco, pois o modelo requer uma dica sobre quais campos são os mais importantes para serem processados.

A ordem de searchFields é fundamental. Se você já usa searchFields em consultas de Lucene simples ou completas existentes, certifique-se de revisitar esse parâmetro ao alternar para um tipo de consulta semântica.

Siga estas diretrizes para garantir os resultados ideais quando dois ou mais searchFields forem especificados:

+ Inclua somente campos de cadeia de caracteres e campos de cadeia de caracteres de nível superior em coleções. Se você estiver incluindo campos que não são de cadeia de caracteres ou campos de nível inferior em uma coleção, não haverá erro, mas esses campos não serão usados na classificação semântica.

+ O primeiro campo deve ser sempre conciso (como um título ou nome), idealmente menos de 25 palavras.

+ Se o índice tiver um campo de URL que seja textual (legível por humanos, como `www.domain.com/name-of-the-document-and-other-details` e não com foco no computador, como `www.domain.com/?id=23463&param=eis` ), coloque-o em segundo lugar na lista (ou primeiro se não houver nenhum campo de título conciso).

+ Siga esses campos por campos descritivos em que a resposta a consultas semânticas pode ser encontrada, como o conteúdo principal de um documento.

Se apenas um campo for especificado, use os campos descritivos em que a resposta às consultas semânticas pode ser encontrada, como o conteúdo principal de um documento. Escolha um campo que forneça conteúdo suficiente.

#### <a name="step-3-remove-orderby-clauses"></a>Etapa 3: remover cláusulas orderBy

Remova todas as cláusulas orderBy, se existirem em uma solicitação existente. A pontuação semântica é usada para ordenar os resultados e, se você incluir lógica de classificação explícita, um erro HTTP 400 será retornado.

#### <a name="step-4-add-answers"></a>Etapa 4: Adicionar respostas

Opcionalmente, adicione "respostas" se desejar incluir processamento adicional que forneça uma resposta. As respostas (e legendas) são formuladas de passagens encontradas nos campos listados em searchFields. Certifique-se de incluir campos ricos no conteúdo no searchFields para obter as melhores respostas e legendas em uma resposta.

Há condições explícitas e implícitas que produzem respostas. 

+ As condições explícitas incluem a adição de "Answers = extracção". Além disso, para especificar o número de respostas retornadas na resposta geral, adicione "Count" seguido por um número: `"answers=extractive|count=3"` .  O padrão é um. O máximo é cinco.

+ As condições implícitas incluem uma construção de cadeia de caracteres de consulta que se presta a uma resposta. Uma consulta composta de ' qual Hotel tem a sala verde ' tem mais probabilidade de ser "respondida" do que uma consulta composta de uma instrução como ' hotel com interiores decorativo '. Como você deve esperar, a consulta não pode ser não especificada ou nula.

O ponto importante a ser resumido é que, se a consulta não parecer uma pergunta, o processamento de resposta será ignorado, mesmo que o parâmetro "Answers" esteja definido.

#### <a name="step-5-add-other-parameters"></a>Etapa 5: adicionar outros parâmetros

Defina quaisquer outros parâmetros que você deseja na solicitação. Parâmetros como [Verificador ortográfico](speller-how-to-add.md), [Select](search-query-odata-select.md)e Count melhoram a qualidade da solicitação e a legibilidade da resposta.

Opcionalmente, você pode personalizar o estilo de realce aplicado às legendas. As legendas aplicam formatação de realce sobre passagens de chave no documento que resumem a resposta. O padrão é `<em>`. Se desejar especificar o tipo de formatação (por exemplo, plano de fundo amarelo), você poderá definir highlightPreTag e highlightPostTag.

### <a name="review-the-response"></a>Analisar a resposta

A resposta para a consulta acima retorna a seguinte correspondência como a seleção superior. As legendas são retornadas automaticamente, com texto sem formatação e versões realçadas. Para obter mais informações sobre respostas semânticas, consulte [classificação semântica e respostas](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parâmetros usados em uma consulta semântica

A tabela a seguir resume os parâmetros de consulta usados em uma consulta semântica para que você possa vê-los de forma holística. Para obter uma lista de todos os parâmetros, consulte [Pesquisar documentos (visualização REST)](/rest/api/searchservice/preview-api/search-documents)

| Parâmetro | Type | Descrição |
|-----------|-------|-------------|
| queryType | String | Os valores válidos incluem simples, completo e semântico. Um valor de "Semantic" é necessário para consultas semânticas. |
| queryLanguage | String | Necessário para consultas semânticas. Atualmente, apenas "en-US" é implementado. |
| searchFields | String | Uma lista delimitada por vírgulas de campos pesquisáveis. Opcional, mas recomendado. Especifica os campos em que a classificação semântica ocorre. </br></br>Em contraste com tipos de consulta simples e completos, a ordem na qual os campos são listados determina a precedência.|
| respectivas |String | Campo opcional para especificar se as respostas semânticas são incluídas no resultado. Atualmente, apenas "extração" é implementada. As respostas podem ser configuradas para retornar um máximo de cinco. O padrão é um. Este exemplo mostra uma contagem de três respostas: "extracçãoy \| Count3". |

## <a name="query-with-search-explorer"></a>Consultar com o Gerenciador de pesquisa

A consulta a seguir destina-se ao índice de exemplo interno de hotéis, usando a versão de API 2020-06-30-Preview e é executada no Gerenciador de pesquisa. A `$select` cláusula limita os resultados para apenas alguns campos, facilitando a verificação no JSON detalhado no Gerenciador de pesquisa.

### <a name="with-querytypesemantic"></a>Com QueryType = semântico

```json
search=nice hotel on water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

Os primeiros resultados são os seguintes.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Com QueryType (padrão)

Para comparação, execute a mesma consulta como acima, removendo `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Observe que não há nenhum `"@search.rerankerScore"` nesses resultados, e que diferentes Hotéis aparecem nas três primeiras posições.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Próximas etapas

Lembre-se de que a classificação semântica e as respostas são criadas sobre um conjunto de resultados inicial. Qualquer lógica que melhora a qualidade dos resultados iniciais será postergada para a pesquisa semântica. Como uma próxima etapa, examine os recursos que contribuem para resultados iniciais, incluindo analisadores que afetam como as cadeias de caracteres são indexadas, perfis de pontuação que podem ajustar os resultados e o algoritmo de relevância padrão.

+ [Analisadores para processamento de texto](search-analyzers.md)
+ [Similaridade e pontuação em Pesquisa Cognitiva](index-similarity-and-scoring.md)
+ [Adicionar perfis de pontuação](index-add-scoring-profiles.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Adicionar verificação ortográfica aos termos da consulta](speller-how-to-add.md)
