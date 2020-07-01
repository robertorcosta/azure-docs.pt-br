---
title: O que é a API de Pesquisa de Locais e Negócios do Bing?
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Locais e Negócios do Bing é um serviço RESTful que permite que seus aplicativos encontrem informações sobre locais e negócios com base em consultas de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 685ee0c616234563981e55f14213e424daae32f5
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85611264"
---
# <a name="what-is-bing-local-business-search"></a>O que é o Bing Local Business Search?
A API da Pesquisa de empresa local do Bing é um serviço RESTful que permite que seus aplicativos localizem informações sobre empresas locais com base em consultas de pesquisa. Por exemplo, `q=<business-name> in Redmond, Washington`, ou `q=Italian restaurants near me`. 

## <a name="features"></a>Recursos
| Recurso | Descrição |  
| -- | -- | 
| [Encontre empresas e locais](quickstarts/local-quickstart.md) | A API da Pesquisa de empresa local do Bing obtém resultados localizados de uma consulta. Os resultados incluem uma URL para o site da empresa e exibem texto, número de telefone e localização geográfica, incluindo: Coordenadas de GPS, cidade, endereço |  
| [Filtre resultados locais com limites geográficos](specify-geographic-search.md) | Adicione coordenadas como parâmetros de pesquisa para limitar os resultados a uma área geográfica específica, especificada por uma área delimitadora circular ou quadrada. | 
| [Filtrar os resultados de negócios local por categoria](local-categories.md) | Pesquisar resultados de negócios local por categoria. Essa opção usa a localização inversa do IP ou coordenadas de GPS do chamador para retornar resultados localizados em várias categorias de negócios.|

## <a name="workflow"></a>Fluxo de trabalho
Chame a API de Pesquisa de Empresa Local do Bing em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar respostas JSON. Esse serviço é acessível por meio da API REST.
 
1. Crie uma [conta da API de serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/).   
2. URL codifique seus termos de pesquisa para o parâmetro de consulta `q=""`. Por exemplo, `q=nearby+restaurant` ou `q=nearby%20restaurant`. Defina a paginação também, se necessário. 
3. Envie uma [solicitação para a API da Pesquisa de empresa local do Bing](quickstarts/local-quickstart.md) 
4. Analisar a resposta JSON 

> [!NOTE]
> Atualmente, a Pesquisa de Negócios Locais: 
> * Só dá suporte ao mercado `en-US`. 
> * Não dá suporte à Sugestão Automática do Bing. 

## <a name="next-steps"></a>Próximas etapas
- [Consulta e resposta](local-search-query-response.md)
- [ Início Rápido da Pesquisa de empresa local ](quickstarts/local-quickstart.md)
- [Referência de API de pesquisa de negócios local](local-search-reference.md)
- [Requisitos de uso e exibição](use-display-requirements.md)
