---
title: Recursos de visualização na API REST
titleSuffix: Azure Cognitive Search
description: API REST do serviço de Pesquisa Cognitiva do Azure versão 2019-05-06-Preview inclui recursos experimentais, como repositório de conhecimento e cache do indexador para aprimoramentos incrementais.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162269"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Recursos de visualização no Azure Pesquisa Cognitiva

Este artigo lista os recursos atualmente em visualização. Os recursos que fazem a transição da visualização para a disponibilidade geral são removidos dessa lista. Você pode verificar [as atualizações de serviço](https://azure.microsoft.com/updates/?product=search) ou [o que há de novo](whats-new.md) para anúncios em relação à disponibilidade geral.

Embora alguns recursos de visualização possam estar disponíveis no portal e no SDK do .NET, a API REST sempre tem recursos de visualização.

+ Para operações de pesquisa [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) , é a versão de visualização atual.
+ Para operações de gerenciamento [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) , é a versão de visualização atual.

> [!IMPORTANT]
> A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>Recursos de enriquecimento de ia

Explore os aprimoramentos mais recentes para o aprimoramento do ia por meio da [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)de versão prévia.

|||
|-|-|
| [Habilidades de pesquisa de entidade personalizada (visualização)](cognitive-search-skill-custom-entity-lookup.md ) | Uma habilidade cognitiva que procura texto de uma lista personalizada de palavras e frases definidas pelo usuário. Usando essa lista, ele rotula todos os documentos com qualquer entidade correspondente. A habilidade também dá suporte a um grau de correspondência difusa que pode ser aplicado para localizar correspondências que são semelhantes, mas não exatamente exatos. | 
| [Habilidade de detecção de PII (versão prévia)](cognitive-search-skill-pii-detection.md) | Uma habilidade cognitiva usada durante a indexação que extrai informações de identificação pessoal de um texto de entrada e oferece a opção de mascarar o texto de várias maneiras.| 
| [Enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) | Adiciona o cache a um pipeline de enriquecimento, permitindo que você reutilize a saída existente se uma modificação direcionada, como uma atualização de um conjunto de qualificações ou outro objeto, não alterar o conteúdo. O Caching aplica-se somente a documentos aprimorados produzidos por um configurador de qualificações.| 
| [Repositório de conhecimento (versão prévia)](knowledge-store-concept-intro.md) | Um novo destino de um pipeline de enriquecimento baseado em ia. A estrutura de dados física existe no armazenamento de BLOBs do Azure e no armazenamento de tabelas do Azure, e é criada e populada quando você executa um indexador que tem um associador cognitiva. A definição de uma loja de conhecimento é especificada em uma definição de contratação de habilidades. Na definição da loja de conhecimento, você controla as estruturas físicas de seus dados por meio de elementos de *projeção* que determinam como os dados são formatados, se os dados são armazenados no armazenamento de tabelas ou no armazenamento de BLOBs e se há várias exibições.| 

## <a name="indexing-and-query-features"></a>Recursos de indexação e consulta

Os recursos de visualização do indexador estão disponíveis na API de pesquisa de visualização. 

|||
|-|-|
| [Indexador do Azure Cosmos DB](search-howto-index-cosmosdb.md) | Suporte para API do MongoDB (visualização), API Gremlin (visualização) e tipos de API API do Cassandra (versão prévia). | 
|  [Indexador de Azure Data Lake Storage Gen2 (versão prévia)](search-howto-index-azure-data-lake-storage.md) | Indexe o conteúdo e os metadados do Data Lake Storage Gen2.| 
| [parâmetro de consulta moreLikeThis (versão prévia)](search-more-like-this.md) | Localiza documentos que são relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. | 

## <a name="management-features"></a>Recursos de gerenciamento

|||
|-|-|
| [Suporte de ponto de extremidade privado](service-create-private-endpoint.md) | Você pode criar uma rede virtual com um cliente seguro (como uma máquina virtual) e, em seguida, criar um serviço de pesquisa que usa o ponto de extremidade privado. |
| Restrição de acesso a IP | Usando [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) a API REST de gerenciamento, você pode criar um serviço que tenha restrições sobre quais endereços IP têm permissão de acesso. |

## <a name="earlier-preview-features"></a>Recursos anteriores de visualização

Os recursos anunciados em versões anteriores, se não tiverem sido transferidos para a disponibilidade geral, ainda estarão em visualização pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço de Pesquisa Cognitiva do Azure está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de referência da API REST de pesquisa. Se você tiver problemas, peça ajuda no [StackOverflow](https://stackoverflow.com/) ou [entre em contato com o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)