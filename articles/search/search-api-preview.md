---
title: Recursos de visualização na API REST
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API Versão 2019-05-06-Preview inclui recursos experimentais, como armazenamento de conhecimento e cache de indexador para enriquecimento incremental.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162269"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Recursos de visualização no Azure Cognitive Search

Este artigo lista recursos atualmente em pré-visualização. Os recursos que fazem a transição da pré-visualização para a disponibilidade geral são removidos desta lista. Você pode verificar atualizações de [serviços](https://azure.microsoft.com/updates/?product=search) ou [novidades](whats-new.md) para anúncios sobre disponibilidade geral.

Embora alguns recursos de pré-visualização possam estar disponíveis no portal e no .NET SDK, a API REST sempre tem recursos de visualização.

+ Para operações [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) de pesquisa, é a versão de pré-visualização atual.
+ Para operações [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) de gerenciamento, é a versão de pré-visualização atual.

> [!IMPORTANT]
> A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Recursos de enriquecimento de IA

Explorar os aprimoramentos mais recentes para o enriquecimento de IA através da [API de visualização de pesquisa](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview).

|||
|-|-|
| [Habilidade de procurar entidades personalizadas (visualização)](cognitive-search-skill-custom-entity-lookup.md ) | Uma habilidade cognitiva que procura texto de uma lista personalizada de palavras e frases definidas pelo usuário. Usando esta lista, ele rotula todos os documentos com quaisquer entidades correspondentes. A habilidade também suporta um grau de correspondência difusa que pode ser aplicada para encontrar correspondências que são semelhantes, mas não muito exatas. | 
| [Habilidade de detecção de PII (visualização)](cognitive-search-skill-pii-detection.md) | Uma habilidade cognitiva usada durante a indexação que extrai informações pessoalmente identificáveis de um texto de entrada e lhe dá a opção de mascarar esse texto de várias maneiras.| 
| [Enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) | Adiciona cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização para um skillset ou outro objeto, não alterar o conteúdo. Caching se aplica apenas a documentos enriquecidos produzidos por um skillset.| 
| [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md) | Um novo destino de um gasoduto de enriquecimento baseado em IA. A estrutura de dados físicos existe no armazenamento Azure Blob e no armazenamento azure Table, e ela é criada e preenchida quando você executa um indexador que tem uma habilidade cognitiva anexada. A definição de um armazenamento de conhecimento em si é especificada dentro de uma definição skillset. Dentro da definição do armazenamento de conhecimento, você controla as estruturas físicas de seus dados através de elementos de *projeção* que determinam como os dados são moldados, se os dados são armazenados no armazenamento de tabela ou blob e se há várias visualizações.| 

## <a name="indexing-and-query-features"></a>Recursos de indexação e consulta

Os recursos de visualização do indexador estão disponíveis na API de visualização da pesquisa. 

|||
|-|-|
| [Indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md) | Suporte para os tipos de API (pré-visualização) do MongoDB, API Gremlin (pré-visualização) e API (visualização) de Cassandra. | 
|  [Indexador Azure Data Lake Storage Gen2 (visualização)](search-howto-index-azure-data-lake-storage.md) | Indexe o conteúdo e os metadados do Data Lake Storage Gen2.| 
| [maisLikeEste parâmetro de consulta (visualização)](search-more-like-this.md) | Encontra documentos relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. | 

## <a name="management-features"></a>Recursos de gestão

|||
|-|-|
| [Suporte privado de endpoint](service-create-private-endpoint.md) | Você pode criar uma rede virtual com um cliente seguro (como uma máquina virtual) e, em seguida, criar um serviço de pesquisa que usa o Private Endpoint. |
| Restrição de acesso ip | Usando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) a API Management REST, você pode criar um serviço que tenha restrições sobre os quais endereços IP têm acesso permitido. |

## <a name="earlier-preview-features"></a>Recursos de visualização anteriores

Os recursos anunciados em pré-visualizações anteriores, se não tiverem transicionado para disponibilidade geral, ainda estão em visualização pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de pesquisa cognitiva do Azure está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

Revise a documentação de referência da API Search REST. Se você encontrar problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [no suporte de contato](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência de API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)