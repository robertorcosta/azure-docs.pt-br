---
title: Recursos de visualização na API REST
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e cache do indexador para aprimoramentos incrementais.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935111"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Recursos de visualização no Azure Pesquisa Cognitiva

Este artigo lista os recursos atualmente em visualização. Os recursos que fazem a transição da visualização para a disponibilidade geral são removidos dessa lista. Você pode verificar [as atualizações de serviço](https://azure.microsoft.com/updates/?product=search) ou [o que há de novo](whats-new.md) para anúncios em relação à disponibilidade geral.

Embora alguns recursos de visualização possam estar disponíveis no portal e no SDK do .NET, a API REST sempre tem recursos de visualização. A versão da API de visualização atual é `2019-05-06-Preview`.

> [!IMPORTANT]
> A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-in-public-preview"></a>Recursos na visualização pública

+ [Pesquisa de entidade personalizada (versão prévia)](cognitive-search-skill-custom-entity-lookup.md ) procura texto de uma lista personalizada de palavras e frases definidas pelo usuário. Usando essa lista, ele rotula todos os documentos com qualquer entidade correspondente. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências que são semelhantes, mas não exatamente exatos. 

+ A [detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascarar o texto de várias maneiras.

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona o cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização de um conjunto de qualificações ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.

+ [Cosmos DB indexador](search-howto-index-cosmosdb.md) dá suporte à API do MongoDB (versão prévia), API Gremlin (versão prévia) e API do Cassandra (versão prévia).

+ [Azure data Lake Storage Gen2 indexador (versão prévia)](search-howto-index-azure-data-lake-storage.md) pode indexar conteúdo e metadados de data Lake Storage Gen2.

+ A [loja de conhecimento (versão prévia)](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

+ o [parâmetro de consulta moreLikeThis (visualização)](search-more-like-this.md) localiza documentos que são relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 

## <a name="earlier-preview-features"></a>Recursos anteriores de visualização

Os recursos anunciados em versões anteriores, se não tiverem sido transferidos para a disponibilidade geral, ainda estarão em visualização pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximos passos

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)