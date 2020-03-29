---
title: Quais são as APIs de Pesquisa do Bing?
titleSuffix: Azure Cognitive Services
description: Use este artigo para saber mais sobre as APIs de Pesquisa do Bing e como você pode habilitar pesquisas de Internet cognitivas em seus aplicativos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775533"
---
# <a name="what-are-the-bing-search-apis"></a>Quais são as APIs de Pesquisa do Bing?

Com as APIs de Pesquisa do Bing, é possível criar aplicativos e serviços conectados à Web que encontram páginas da Web, imagens, notícias, locais e muito mais, sem anúncios. Enviando solicitações de pesquisa usando os SDKs ou as APIs REST de Pesquisa do Bing, você pode obter conteúdo e informações relevantes para pesquisas na Web. Use este artigo para aprender sobre as diferentes APIs de pesquisa do Bing e como você pode integrar pesquisas cognitivas em seus aplicativos e serviços. Preços e limites de taxa podem variar entre as APIs.

## <a name="the-bing-web-search-api"></a>A API de Pesquisa na Web do Bing

A [API de Pesquisa na Web do Bing](../Bing-Web-Search/overview.md) retorna páginas da Web, imagens, vídeo, notícias e muito mais. Você pode filtrar as consultas de pesquisa enviadas a esta API para incluir ou excluir certos tipos de conteúdo.

Considere usar a API de Pesquisa na Web do Bing em aplicativos que talvez precisem pesquisar por todos os tipos de conteúdo da Web relevante. Se seu aplicativo pesquisa por um tipo específico de conteúdo online, considere uma das APIs de pesquisa abaixo:

## <a name="content-specific-bing-search-apis"></a>APIs de Pesquisa do Bing específicas a um conteúdo

As APIs de pesquisa de Bing a seguir retornam conteúdo específico da web, como imagens, notícias, empresas locais e vídeos.

| API do Bing | Descrição |
| -- | -- |
| [Pesquisa de entidades](../Bing-Entities-Search/overview.md) | A API de Pesquisa de Entidade do Bing retorna resultados da pesquisa contendo entidades, as quais podem ser pessoas, lugares ou coisas. Dependendo da consulta, a API retornará uma ou mais entidades que satisfaçam a consulta de pesquisa. A consulta de pesquisa pode incluir indivíduos notáveis, empresas locais, pontos turísticos, destinos e muito mais. |
| [Pesquisa de Imagem](../Bing-Image-Search/overview.md) | A API de pesquisa de imagens de bing permite que você pesquise e encontre imagens estáticas e animadas de alta qualidade semelhantes a [Bing.com/images](https://www.Bing.com/images). Você pode refinar a pesquisa para incluir ou excluir imagens por atributo, incluindo o tamanho, cor, licença e atualização. Você também pode pesquisar imagens mais populares, carregar imagens para obter informações sobre eles e exibir visualizações em miniatura. |
| [Pesquisa de Notícias](../Bing-News-Search/search-the-web.md) | A API de pesquisa de notícias de bing permite que você encontre notícias semelhantes a [Bing.com/news](https://www.Bing.com/news). A API retorna artigos de notícias de várias fontes ou domínios específicos. Você pode pesquisar em categorias para obter artigos populares, bem como as principais histórias e manchetes. |
| [Pesquisa de vídeo](../Bing-Video-Search/overview.md) | A API de pesquisa de vídeo do Bing permite que você encontre vídeos na web. Obtenha vídeos populares, conteúdo relacionado e visualizações em miniatura. |
| [Pesquisa Visual](../Bing-visual-search/overview.md) | Carregue uma imagem ou use uma URL para obter informações criteriosas sobre ela, tais como pesquisas relacionadas, imagens e produtos visualmente similares. |
 [Pesquisa de Empresas Locais](../bing-local-business-search/overview.md) | A API de pesquisa de negócios local do Bing permite que seus aplicativos encontrem informações de contato e localização sobre empresas locais com base em consultas de pesquisa. |

## <a name="the-bing-custom-search-api"></a>A API de Pesquisa Personalizada do Bing

Criar uma instância de pesquisa personalizada com a API [de pesquisa personalizada](../Bing-Custom-Search/overview.md) do Bing permite que você crie uma experiência de pesquisa focada apenas em conteúdo e tópicos que você se importa. Por exemplo, depois de especificar os domínios, sites e páginas da Web específicas que bing irá pesquisar, o Bing Custom Search adaptará os resultados a esse conteúdo específico. Você pode incorporar as APIs de Pesquisa de Vídeo, de Imagem e a Sugestão Automática Personalizada do Bing para personalizar ainda mais sua experiência de pesquisa.

## <a name="additional-bing-search-apis"></a>APIs de Pesquisa do Bing adicionais

As APIs de pesquisa do Bing Search a seguir permitem que você melhore sua experiência de pesquisa combinando-as com outras APIs de pesquisa do Bing.

| API | Descrição |
| -- | -- |
| [Sugestão Automática do Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Melhore a experiência de pesquisa do seu aplicativo com a API Bing Autosuggest retornando pesquisas sugeridas em tempo real.  |
| [Estatísticas do Bing](bing-web-stats.md) | As Estatísticas do Bing fornecem análise para as APIs de Pesquisa do Bing usadas pelo aplicativo. Algumas das análises disponíveis incluem o volume de chamadas, as principais cadeias de caracteres de consulta e a distribuição geográfica. |

## <a name="next-steps"></a>Próximas etapas

* [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) da API de Pesquisa do Bing
* Os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
