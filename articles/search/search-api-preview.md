---
title: API REST versão 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e cache do indexador para aprimoramentos incrementais.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: eb73d614ca94bc1fa007a14f3705e50c74ab9e4f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922470"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST do serviço de Pesquisa Cognitiva do Azure – versão 2019-05-06-visualização

Este artigo descreve a versão `api-version=2019-05-06-Preview` da API REST do serviço de pesquisa, oferecendo recursos experimentais que ainda não estão disponíveis para o público em geral.

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. É altamente recomendável não usar APIs de versão prévia em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona o cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização de um conjunto de qualificações ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.

+ [Cosmos DB indexador](search-howto-index-cosmosdb.md) dá suporte à API do MongoDB (versão prévia), API Gremlin (versão prévia) e API do Cassandra (versão prévia).

+ [Azure data Lake Storage Gen2 indexador (versão prévia)](search-howto-index-azure-data-lake-storage.md) pode indexar conteúdo e metadados de data Lake Storage Gen2.

+ A [extração de documentos (visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conferente. Anteriormente, a quebra de documento ocorreu apenas antes da execução do concodificador. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conconjunto de habilidades.

+ A [conversão de texto (visualização)](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva usada durante a indexação que avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado.

+ A [loja de conhecimento](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

## <a name="earlier-preview-features"></a>Recursos anteriores de visualização

Os recursos anunciados em versões prévias anteriores ainda estão em versão prévia pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

+ [O parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 

+ A [indexação de BLOBs CSV](search-howto-index-csv-blobs.md) cria um documento por linha, em oposição a um documento por blob de texto.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximos passos

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)