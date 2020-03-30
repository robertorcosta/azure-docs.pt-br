---
title: Indexação em vários idiomas para consultas de pesquisa não-inglesas
titleSuffix: Azure Cognitive Search
description: O Azure Cognitive Search suporta 56 idiomas, aproveitando analisadores de idiomas da tecnologia Lucene e Natural Language Processing da Microsoft.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793586"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para vários idiomas na Pesquisa Cognitiva do Azure

Os índices podem incluir campos que contêm conteúdo de vários idiomas, por exemplo, criando campos individuais para strings específicas do idioma. Para obter melhores resultados durante a indexação e consulta, atribua um analisador de idiomas que forneça as regras linguísticas apropriadas. 

O Azure Cognitive Search oferece uma grande seleção de analisadores de idiomas tanto da Lucene quanto da Microsoft que podem ser atribuídos a campos individuais usando a propriedade Analyzer. Você também pode especificar um analisador de idiomas no portal, conforme descrito neste artigo.

## <a name="add-analyzers-to-fields"></a>Adicionar analisadores aos campos

Um analisador de idiomas é especificado quando um campo é criado. Adicionar um analisador a uma definição de campo existente requer substituir (e recarregar) o índice ou criar um novo campo idêntico ao original, mas com uma atribuição de analisador. Você pode, então, excluir o campo não utilizado à sua conveniência.

1. Faça login no [portal Azure](https://portal.azure.com) e encontre seu serviço de busca.
1. Clique em **Adicionar índice** na barra de comandos localizada na parte superior do painel do serviço para iniciar um novo índice, ou abra um índice existente para definir um analisador nos novos campos que você está adicionando a um índice existente.
1. Inicie uma definição de campo fornecendo um nome.
1. Escolha o tipo de dados Edm.String. Apenas os campos de string são pesquisados por texto completo.
1. Defina o atributo **Searchable** para ativar a propriedade Analyzer. Um campo deve ser baseado em texto para fazer uso de um analisador de idiomas.
1. Escolha um dos analisadores disponíveis. 

![Assine analisadores de idiomas durante a definição de campo](media/search-language-support/select-analyzer.png "Assine analisadores de idiomas durante a definição de campo")

Por padrão, todos os campos pesquisáveis usam o [analisador Standard Lucene,](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que é agnóstico de linguagem. Para ver a lista completa de analisadores suportados, consulte [Adicionar analisadores de idiomas a um índice de pesquisa cognitiva do Azure](index-add-language-analyzers.md).

No portal, os analisadores devem ser usados como estão. Se você precisar de personalização ou uma configuração específica de filtros e tokenizadores, você deve [criar um analisador personalizado](index-add-custom-analyzers.md) em código. O portal não suporta selecionar ou configurar analisadores personalizados.

## <a name="query-language-specific-fields"></a>Consultar campos específicos do idioma

Quando o analisador de linguagem for selecionado para um campo, ele será usado com cada solicitação de indexação e pesquisa para esse campo. Quando uma consulta é emitida contra vários campos usando diferentes analisadores, a consulta será processada independentemente pelos analisadores atribuídos para cada campo.

Se o idioma do agente emissor de uma consulta for conhecido, uma solicitação de pesquisa pode ser definida como escopo para um campo específico usando o parâmetro de consulta **searchFields** . A seguinte consulta será emitida apenas com a descrição em polonês:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Você pode consultar seu índice a partir do portal, usando o [**Search explorer**](search-explorer.md) para colar em uma consulta semelhante à mostrada acima.

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos do idioma

Às vezes, o idioma do agente emissor de uma consulta não é conhecido; nesse caso, a consulta pode ser emitida em todos os campos simultaneamente. Se necessário, a preferência de resultados em um determinado idioma pode ser definida usando os [perfis de pontuação](index-add-scoring-profiles.md). No exemplo abaixo, as correspondências encontradas na descrição em inglês terão uma pontuação superior em relação às correspondências em polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Próximas etapas

Se você é um desenvolvedor .NET, observe que você pode configurar analisadores de idiomas usando o [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) e a propriedade [Analyzer.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 