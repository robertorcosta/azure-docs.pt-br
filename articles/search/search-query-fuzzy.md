---
title: Pesquisa difusa
titleSuffix: Azure Cognitive Search
description: Implemente uma experiência de pesquisa "você quis dizer" para corrigir automaticamente um termo ou erro de digitação mal escrito.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 32ad34bcfb42bf8fc45ba7fdb7fba5e797ee6106
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262427"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Pesquisa difusa para corrigir erros ortográficos e erros de digitação

O Azure Cognitive Search suporta pesquisa difusa, um tipo de consulta que compensa erros de digitação e termos mal escritos na seqüência de entrada. Ele faz isso escaneando termos com uma composição semelhante. Expandir a pesquisa para cobrir partidas próximas tem o efeito de corrigir automaticamente um erro de digitação quando a discrepância é apenas alguns caracteres extraviados. 

## <a name="what-is-fuzzy-search"></a>O que é busca difusa?

É um exercício de expansão que produz uma correspondência em termos de uma composição semelhante. Quando uma pesquisa difusa é especificada, o motor constrói um gráfico (baseado na [teoria determinista do autômato finito)](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)de termos similarmente compostos, para todos os termos inteiros na consulta. Por exemplo, se a sua consulta incluir três termos "universidade de Washington", um `search=university~ of~ washington~` gráfico é criado para cada termo na consulta (não há remoção de stop-word na pesquisa difusa, então "de" recebe um gráfico).

O gráfico consiste em até 50 expansões, ou permutações, de cada termo, capturando variantes corretas e incorretas no processo. Em seguida, o motor retorna as partidas mais relevantes na resposta. 

Para um termo como "universidade", o gráfico pode ter "desversty, universty, university, universe, inverso". Todos os documentos que correspondem aos do gráfico estão incluídos nos resultados. Em contraste com outras consultas que analisam o texto para lidar com diferentes formas da mesma palavra ("mouse" e "mouse"), as comparações em uma consulta difusa são feitas pelo valor facial sem qualquer análise linguística no texto. "Universo" e "inverso", que são semanticamente diferentes, corresponderão porque as discrepâncias sintáticas são pequenas.

Uma correspondência é bem-sucedida se as discrepâncias forem limitadas a duas ou menos edições, onde uma edição é um caractere inserido, excluído, substituído ou transposto. O algoritmo de correção de strings que especifica o diferencial é a métrica [de distância Damerau-Levenshtein,](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) descrita como o "número mínimo de operações (inserções, exclusões, substituições ou transposições de dois caracteres adjacentes) necessários para mudar uma palavra para a outra". 

Na pesquisa cognitiva do Azure:

+ A consulta fuzzy se aplica a termos completos, mas você pode apoiar frases através de construções e construções. Por exemplo, "Unviersty~ de~ "Wshington~" combinaria com "University of Washington".

+ A distância padrão de uma edição é 2. Um valor `~0` de não significa expansão (apenas o termo exato `~1` é considerado uma correspondência), mas você pode especificar para um grau de diferença, ou uma edição. 

+ Uma consulta difusa pode expandir um termo até 50 permutações adicionais. Este limite não é configurável, mas você pode reduzir efetivamente o número de expansões diminuindo a distância de edição para 1.

+ As respostas consistem em documentos contendo uma correspondência relevante (até 50).

Coletivamente, os gráficos são submetidos como critérios de correspondência contra tokens no índice. Como você pode imaginar, a pesquisa difusa é inerentemente mais lenta do que outros formulários de consulta. O tamanho e a complexidade do seu índice podem determinar se os benefícios são suficientes para compensar a latência da resposta.

> [!NOTE]
> Como a busca difusa tende a ser lenta, pode valer a pena investigar alternativas como indexação de n-gram, com sua progressão de sequências curtas de caracteres (duas e três sequências de caracteres para tokens bigram e trigrama). Dependendo do seu idioma e superfície de consulta, n-gram pode lhe dar melhor desempenho. A troca é que a indexação n-gram é muito intensiva em armazenamento e gera índices muito maiores.
>
> Outra alternativa, que você poderia considerar se quiser lidar apenas com os casos mais flagrantes, seria um [mapa sinônimo.](search-synonyms.md) Por exemplo, mapear "pesquisar" para "serach, serch, sarch" ou "recuperar" para "retreive".

## <a name="indexing-for-fuzzy-search"></a>Indexação para pesquisa difusa

Os analisadores não são usados durante o processamento de consultas para criar um gráfico de expansão, mas isso não significa que os analisadores devem ser ignorados em cenários de pesquisa difusos. Afinal, os analisadores são usados durante a indexação para criar tokens contra os quais a correspondência é feita, se a consulta é de forma livre, pesquisa filtrada ou uma pesquisa difusa com um gráfico como entrada. 

Geralmente, ao atribuir analisadores por campo, a decisão de ajustar a cadeia de análise é baseada no caso de uso primário (um filtro ou pesquisa completa de texto) em vez de formulários de consulta especializados como pesquisa embaçada. Por essa razão, não há uma recomendação específica do analisador para pesquisa difusa. 

No entanto, se as consultas de teste não estiverem produzindo as correspondências que você espera, você pode tentar variar o analisador de indexação, definindo-o como um [analisador de idiomas,](index-add-language-analyzers.md)para ver se você obter melhores resultados. Algumas línguas, particularmente aquelas com mutações vocálicas, podem se beneficiar da inflexão e formas irregulares de palavras geradas pelos processadores de linguagem natural da Microsoft. Em alguns casos, usar o analisador de idiomas certo pode fazer a diferença se um termo é tokenizado de uma forma compatível com o valor fornecido pelo usuário.

## <a name="how-to-use-fuzzy-search"></a>Como usar a pesquisa difusa

As consultas difusas são construídas usando a sintaxe completa da consulta Lucene, invocando o [analisador de consulta lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Defina o analisador completo de Lucene na consulta (`queryType=full`).

1. Opcionalmente, escopo a solicitação para campos específicos, utilizando este parâmetro (`searchFields=<field1,field2>`). 

1. Anexar o operador`~`de tilde ( ) no`search=<string>~`final de todo o prazo ( ).

   Inclua um parâmetro opcional, um número entre 0 e 2 (padrão),`~1`se quiser especificar a distância de edição ( ). Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

Na Pesquisa Cognitiva do Azure, além do termo e distância (máximo de 2), não há parâmetros adicionais para definir na consulta.

> [!NOTE]
> Durante o processamento da consulta, as consultas difusas não passam por [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). A entrada de consulta é adicionada diretamente à árvore de consulta e expandida para criar um gráfico de termos. A única transformação realizada é a menor carcaça.

## <a name="testing-fuzzy-search"></a>Testando pesquisa difusa

Para testes simples, recomendamos [o Search explorer](search-explorer.md) ou o [Carteiro](search-get-started-postman.md) para iterar sobre uma expressão de consulta. Ambas as ferramentas são interativas, o que significa que você pode rapidamente passar por várias variantes de um termo e avaliar as respostas que voltam.

Quando os resultados são ambíguos, [o destaque](search-pagination-page-layout.md#hit-highlighting) de impacto pode ajudá-lo a identificar a correspondência na resposta. 

> [!Note]
> O uso de destaque de hit para identificar partidas difusas tem limitações e só funciona para pesquisa básica. Se o seu índice tiver perfis de pontuação ou se você camada a consulta com sintaxe adicional, o "destaque" pode não identificar a correspondência. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exemplo 1: pesquisa difusa com o termo exato

Suponha que a `"Description"` seguinte seqüência existe em um campo em um documento de pesquisa:`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Comece com uma pesquisa difusa em "especial" e adicione o destaque de hit ao campo Descrição:

    search=special~&highlight=Description

Na resposta, porque você adicionou destaque de hit, a formatação é aplicada a "especial" como o termo correspondente.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Tente a solicitação novamente, soletrando errado "especial" tirando várias letras ("pe"):

    search=scial~&highlight=Description

Até agora, nenhuma mudança na resposta. Usando o padrão de distância de 2 graus, remover dois caracteres "pe" de "especial" ainda permite uma combinação bem sucedida nesse termo.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

Tentando mais uma solicitação, modifique ainda mais o termo de pesquisa, tirando um último caractere para um total de três exclusões (de "especial" para "scal"):

    search=scal~&highlight=Description

Observe que a mesma resposta é devolvida, mas agora em vez de combinar em "especial", a correspondência fuzzy está em "SQL".

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

O objetivo deste exemplo expandido é ilustrar a clareza que o destaque de sucesso pode trazer a resultados ambíguos. Em todos os casos, o mesmo documento é devolvido. Se você tivesse confiado em IDs de documentos para verificar uma correspondência, você poderia ter perdido a mudança de "especial" para "SQL".

## <a name="see-also"></a>Confira também

+ [Como funciona a pesquisa completa de texto no Azure Cognitive Search (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Explorador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
