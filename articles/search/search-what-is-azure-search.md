---
title: Introdução à Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: O Azure Cognitive Search é um serviço de pesquisa em nuvem totalmente gerenciado da Microsoft. Leia sobre casos de uso, o fluxo de trabalho de desenvolvimento, comparações com outro produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/24/2020
ms.custom: contperfq1
ms.openlocfilehash: 19be1155476ca7c295e2d0311e8285bc2128dd1d
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030757"
---
# <a name="what-is-azure-cognitive-search"></a>O que é a Pesquisa Cognitiva do Azure?

O Azure Cognitive Search ([chamado anteriormente de “Azure Search”](whats-new.md#new-service-name)) é um serviço de pesquisa de nuvem que oferece aos desenvolvedores APIs e ferramentas para criar uma experiência de pesquisa avançada para conteúdo privado e heterogêneo em aplicativos Web, móveis e empresariais. 

Ao criar um serviço do Cognitive Search, você obtém:

+ um mecanismo de pesquisa que executa a indexação e a execução da consulta
+ análise e transformação de imagens centradas em IA e texto não diferenciado durante a indexação
+ armazenamento persistente de índices de pesquisa criados e gerenciados por você
+ uma linguagem de consulta para compor consultas simples a complexas

Em termos de arquitetura, um serviço de pesquisa reside entre os armazenamentos de dados externos que contêm seus dados não indexados e um aplicativo cliente que envia solicitações de consulta para um índice de pesquisa e manipula a resposta.

![Arquitetura da Pesquisa Cognitiva do Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura da Pesquisa Cognitiva do Azure")

Externamente, um serviço de pesquisa integra-se a outros serviços do Azure na forma de *indexadores* que automatizam a ingestão/recuperação de dados de fontes de dados do Azure e *conjuntos de habilidades* que incorporam a IA consumível por meio de Serviços Cognitivos, como análise de imagem e texto, ou IA personalizada criada por você no Azure Machine Learning ou que são encapsulados no Azure Functions.

No próprio serviço de pesquisa, as duas principais cargas de trabalho são *indexação* e *consulta*. 

+ A indexação leva o texto para o serviço de pesquisa e o torna pesquisável. Internamente, o texto de entrada é processado em tokens e armazenados em índices invertidos para verificações rápidas. 

  Na indexação, você tem a opção de adicionar o *enriquecimento de IA* por meio de [habilidades cognitivas](cognitive-search-working-with-skillsets.md), predefinidas da Microsoft ou habilidades personalizadas criadas por você. A análise e as transformações subsequentes podem resultar em novas informações e estruturas que não existiam, fornecendo grande utilidade para vários cenários de mineração de pesquisa e conhecimento.

+ Depois que um índice é preenchido com os dados pesquisáveis, seu aplicativo cliente envia solicitações de consulta para um serviço de pesquisa e manipula as respostas. Toda a execução da consulta é feita em um índice de pesquisa que você cria, detém e armazena em seu serviço. No aplicativo cliente, a experiência de pesquisa é definida usando APIs do Azure Cognitive Search e pode incluir ajuste de relevância, preenchimento automático, correspondência de sinônimos, correspondência difusa, correspondência de padrões, filtro e classificação.

A funcionalidade é exposta por meio de uma [API REST](/rest/api/searchservice/) ou um [SDK do .NET](search-howto-dotnet-sdk.md) simples que mascara a complexidade inerente da tecnologia de pesquisa. Você também pode usar o portal do Azure para administração de serviços e gerenciamento de conteúdo, com ferramentas para protótipo e consulta de seus índices e habilidades. Porque o serviço é executado na nuvem, infraestrutura e disponibilidade são gerenciados pela Microsoft.

## <a name="why-use-cognitive-search"></a>Por que usar o Cognitive Search

O Azure Cognitive Search é adequado para os seguintes cenários de aplicativos:

+ Consolide um conteúdo heterogêneo em um índice de pesquisa particular definido pelo usuário. Você pode preencher um índice de pesquisa com fluxos de documentos JSON de qualquer origem. Para fontes com suporte no Azure, use um *indexador* para automatizar a indexação. O controle sobre o esquema de índice e o agendamento de atualização é um motivo importante para usar o Azure Cognitive Search.

+ Implementação fácil de recursos relacionados à pesquisa. As APIs de Pesquisa simplificam a construção da consulta, a faceted navigation, os filtros (incluindo a pesquisa geoespacial), o mapeamento de sinônimos, o preenchimento automático e o ajuste de relevância. Usando recursos internos, você pode satisfazer as expectativas do usuário final para uma experiência de pesquisa semelhante aos mecanismos de pesquisa da Web comercial.

+ Conteúdo bruto consiste em grandes arquivos de texto ou de imagem não diferenciados ou arquivos de aplicativos armazenados no Armazenamento de Blobs do Azure ou no Cosmos DB. Você pode aplicar [habilidades cognitivas](cognitive-search-concept-intro.md) durante a indexação para identificar e extrair texto, criar estrutura ou criar informações, como texto traduzido ou entidades.

+ O conteúdo precisa de uma análise linguística ou de texto personalizado. Se você tiver conteúdo que não está em inglês, a Pesquisa Cognitiva do Azure será compatível com os analisadores Lucene e com os processadores de linguagem natural da Microsoft. Você também pode configurar analisadores para obter processamento especializado de conteúdo bruto, como filtragem de sinais diacríticos ou reconhecimento e preservação de padrões em cadeias de caracteres.

Para obter mais informações sobre funcionalidades específicas, confira [Recursos do Azure Cognitive Search](search-features-list.md)

## <a name="how-to-get-started"></a>Como começar

Uma exploração de ponta a ponta dos principais recursos de pesquisa pode ser obtida em quatro etapas:

1. [**Crie um serviço de pesquisa**](search-create-service-portal.md) na Camada gratuita, compartilhado com outros assinantes, ou em uma [camada paga](https://azure.microsoft.com/pricing/details/search/), para os recursos dedicados usados somente pelo seu serviço. Todos os tutoriais e inícios rápidos podem ser concluídos no serviço gratuito.

1. [**Crie um índice de pesquisa**](search-what-is-an-index.md) usando o portal ou a [API REST](/rest/api/searchservice/create-index). [SDK do .NET](search-howto-dotnet-sdk.md) ou outro SDK. O esquema de índice define a estrutura do conteúdo pesquisável.

1. [**Carregue o conteúdo**](search-what-is-data-import.md) no índice. Use o [modelo de "push"](tutorial-optimize-indexing-push-api.md) para enviar por push documentos JSON de qualquer fonte ou use o [modelo de "pull" (indexadores)](search-indexer-overview.md) se os dados de origem estiverem no Azure.

1. [**Consulte um índice**](search-query-overview.md) usando o [Gerenciador de pesquisa](search-explorer.md) no portal, na [API REST](search-get-started-rest.md), no [SDK do .NET](/dotnet/api/azure.search.documents.searchclient.search) ou em outro SDK.

> [!TIP]
> Consolide as etapas iniciando com o [**Assistente para Importação de Dados**](search-get-started-portal.md) e uma fonte de dados do Azure para criar, carregar e consultar um índice em minutos.

## <a name="how-it-compares"></a>Como ele se compara

Os clientes frequentemente perguntam como a Pesquisa Cognitiva do Azure se compara a outras soluções relacionadas a pesquisa. A tabela a seguir resume as principais diferenças.

| Em comparação com | Principais diferenças |
|-------------|-----------------|
| Pesquisa da Microsoft | [A Pesquisa da Microsoft](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) é para usuários do Microsoft 365 autenticados que precisam consultar o conteúdo no SharePoint. Ela é oferecida como uma experiência de pesquisa pronta para uso, habilitada e configurada por administradores, com a capacidade de aceitar conteúdo externo por meio de conectores da Microsoft e de outras fontes. Se isso descrever seu cenário, a Pesquisa da Microsoft com Microsoft 365 é uma opção atraente para explorar.<br/><br/>Em contraste, a Azure Cognitive Search faz consultas em um índice que você define, preenchido com os dados e documentos que você tem, geralmente de diversas fontes. A Azure Cognitive Search tem funcionalidades de rastreador para algumas fontes de dados do Azure por meio de [indexadores](search-indexer-overview.md), mas você pode enviar por push qualquer documento JSON que esteja de acordo com seu esquema de índice em um recurso pesquisável único e consolidado. Você também pode personalizar o pipeline de indexação para incluir aprendizado de máquina e analisadores léxicos. Como a Cognitive Search é criada para ser um componente de plug-in em soluções maiores, você pode integrá-la em praticamente qualquer aplicativo e plataforma.|
|Bing | A [API de Pesquisa na Web do Bing](../cognitive-services/bing-web-search/index.yml) pesquisa os índices no Bing.com para os termos correspondentes que você enviar. Os índices são criados de HTML, XML e outros conteúdos da web em sites públicos. Usando o mesmo conceito, a [Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia de rastreador para tipos de conteúdo da Web no escopo para sites individuais.<br/><br/>Na Cognitive Search, você pode definir e preencher o índice. Você pode usar [indexadores](search-indexer-overview.md) para rastrear dados em fontes de dados do Azure ou enviar por push qualquer documento JSON de acordo com o índice para o serviço de pesquisa. |
|Pesquisa de banco de dados | Muitas plataformas de banco de dados incluem uma experiência de pesquisa interna. O SQL Server possui uma [pesquisa de texto completa](/sql/relational-databases/search/full-text-search). O Cosmos DB e tecnologias semelhantes possuem índices passíveis de consulta. Ao avaliar os produtos que combinam pesquisa e armazenamento, pode ser difícil determinar qual a forma de proceder. Muitas soluções usam ambos: O DBMS para armazenamento e a Pesquisa Cognitiva do Azure para recursos de pesquisa especializados.<br/><br/>Em comparação com a pesquisa DBMS, a Pesquisa Cognitiva do Azure armazena conteúdo de fontes heterogêneas e oferece recursos de processamento de texto especializados, como processamento de texto linguístico (derivação, lematização, formas de palavra) em [56 idiomas](/rest/api/searchservice/language-support). Ele também suporta correção automática de ortografia, [sinônimos](/rest/api/searchservice/synonym-map-operations), [sugestões](/rest/api/searchservice/suggestions), [controles de pontuação](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](./search-filters-facets.md) e [geração de tokens personalizada](/rest/api/searchservice/custom-analyzers-in-azure-search). O [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) na Pesquisa Cognitiva do Azure se baseia no Apache Lucene, um padrão de recuperação de informações do setor. No entanto, embora o Azure Cognitive Search persista os dados na forma de um índice invertido, ele não substitui o verdadeiro armazenamento de dados e não é recomendável usá-lo nessa capacidade. Para saber mais, consulte este [post do fórum](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Utilização de recursos é outro ponto de inflexão nesta categoria. Algumas operações de consulta e indexação costumam exigir bastante computação. Descarregar pesquisa do DBMS para uma solução dedicada na nuvem preserva recursos do sistema para processamento de transações. Além disso, ao externalizar a pesquisa, é possível dimensionar facilmente para encontrar uma correspondência ao volume da consulta.|
|Solução de pesquisa dedicada | Supondo que você decidiu usar a pesquisa dedicada com todas as funcionalidades, uma comparação categórica final seria entre soluções locais ou um serviço de nuvem. Muitas tecnologias de pesquisa oferecem controle sobre pipelines de indexação e consulta, acesso à consulta mais avançada e sintaxe de filtragem, controle sobre a classificação e a relevância e recursos para pesquisa autodirecionada e inteligente. <br/><br/>Um serviço de nuvem é a escolha certa se você deseja uma solução completa com sobrecarga e manutenção mínimas e escala ajustável. <br/><br/>No paradigma da nuvem, diversos provedores oferecem recursos de linha de base comparáveis, com a pesquisa de texto completo, pesquisa geográfica e a capacidade de lidar com algum nível de ambiguidade nas entradas de pesquisa. Normalmente, é um [recurso especializado](search-features-list.md) ou a facilidade e simplicidade geral das APIs, ferramentas e gerenciamento que determinam o melhor ajuste. |

Entre os provedores de nuvem, a Pesquisa Cognitiva do Azure é mais forte para cargas de trabalho de pesquisa de texto completo em relação aos repositórios de conteúdo e bancos de dados do Azure, para aplicativos que se baseiam principalmente na pesquisa para recuperação de informações e navegação de conteúdo. 

As principais vantagens incluem:

+ Integração dos dados do Azure (rastreadores) na camada de indexação
+ Portal do Azure para o gerenciamento central
+ Escala do Azure, confiabilidade e disponibilidade superior
+ O processamento de AI de dados brutos para torná-los mais pesquisáveis, incluindo texto de imagens ou localização de padrões em conteúdo não estruturado.
+ Análise linguística e personalizada, com analisadores para a pesquisa sólida de texto completo em 56 idiomas
+ [Principais recursos comuns aos aplicativos centrados em pesquisa](search-features-list.md): pontuação, facetamento, sugestões, sinônimos, pesquisa geográfica e outros.

Entre nossos clientes, aqueles que conseguem aproveitar a mais ampla gama de recursos da Pesquisa Cognitiva do Azure incluem catálogos online, programas de linha de negócios e aplicativos de descoberta de documentos.

## <a name="watch-this-video"></a>Assista a este vídeo

Neste vídeo de 15 minutos, o Gerenciador de programas Luis Cabrera apresenta o Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]