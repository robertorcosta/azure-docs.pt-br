---
title: Recursos de visualização na API REST
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e cache do indexador para aprimoramentos incrementais.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fd21a4b821e1911e94d542a0922e5269786c365d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991058"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Recursos de visualização no Azure Pesquisa Cognitiva

Este artigo lista os recursos atualmente em visualização. Os recursos que fazem a transição da visualização para a disponibilidade geral são removidos dessa lista. Você pode verificar [as atualizações de serviço](https://azure.microsoft.com/updates/?product=search) ou [o que há de novo](whats-new.md) para anúncios em relação à disponibilidade geral.

Embora alguns recursos de visualização possam estar disponíveis no portal e no SDK do .NET, a API REST sempre tem recursos de visualização. 

+ Para operações de pesquisa, a versão da API de visualização atual é [`2019-05-06-Preview`](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)
+ Para operações de gerenciamento, a versão de visualização atual é [`2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview)

> [!IMPORTANT]
> A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Recursos de enriquecimento de ia

Explore os aprimoramentos mais recentes para o aprimoramento do ia por meio da [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)de versão prévia.

+ A [habilidade de pesquisa de entidade personalizada (versão prévia)](cognitive-search-skill-custom-entity-lookup.md ) é uma habilidade cognitiva que procura texto de uma lista personalizada de palavras e frases definidas pelo usuário. Usando essa lista, ele rotula todos os documentos com qualquer entidade correspondente. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências que são semelhantes, mas não exatamente exatos. 

+ A [habilidade de detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) é uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascarar o texto de várias maneiras.

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona o cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização de um conjunto de qualificações ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.

+ A [loja de conhecimento (versão prévia)](knowledge-store-concept-intro.md) é um novo destino de um pipeline de enriquecimento baseado em ia. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.

## <a name="indexing-and-query-features"></a>Recursos de indexação e consulta

Os recursos de visualização do indexador estão disponíveis na API de pesquisa de visualização. 

+ [Cosmos DB indexador](search-howto-index-cosmosdb.md) dá suporte à API do MongoDB (versão prévia), API Gremlin (versão prévia) e API do Cassandra (versão prévia).

+ [Azure data Lake Storage Gen2 indexador (versão prévia)](search-howto-index-azure-data-lake-storage.md) pode indexar conteúdo e metadados de data Lake Storage Gen2.

+ o [parâmetro de consulta moreLikeThis (visualização)](search-more-like-this.md) localiza documentos que são relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 

## <a name="management-features"></a>Recursos de gerenciamento

+ [Suporte de ponto de extremidade privado](service-create-private-endpoint.md) por meio de [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) da API REST de gerenciamento. Você pode criar um serviço que tenha restrições sobre como seu ponto de extremidade é acessado.

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