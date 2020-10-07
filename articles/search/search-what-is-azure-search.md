---
title: Introdução à Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: O Azure Cognitive Search é um serviço de pesquisa em nuvem totalmente gerenciado da Microsoft. Leia sobre casos de uso, o fluxo de trabalho de desenvolvimento, comparações com outro produtos de pesquisa da Microsoft e como começar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 26a448ded06b32fef80fee06568655067a727620
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91320397"
---
# <a name="what-is-azure-cognitive-search"></a>O que é a Pesquisa Cognitiva do Azure?

O Azure Cognitive Search ([chamado anteriormente de “Azure Search”](whats-new.md#new-service-name)) é um serviço de pesquisa de nuvem que oferece aos desenvolvedores APIs e ferramentas para criar uma experiência de pesquisa avançada para conteúdo privado e heterogêneo em aplicativos Web, móveis e empresariais.

Ao criar um serviço do Cognitive Search, você obtém um mecanismo de pesquisa que executa indexação e execução de consulta, armazenamento persistente de índices que você cria e gerencia e uma linguagem de consulta para compor consultas simples a complexas. Opcionalmente, um serviço de pesquisa integra-se a outros serviços do Azure na forma de *indexadores* que automatizam a ingestão/recuperação de dados de fontes de dados do Azure e *habilidades* que incorporam IA consumível de Serviços Cognitivos, como análise de imagem e texto, ou IA personalizada que você cria no Azure Machine Learning ou encapsula dentro do Azure Functions.

![Arquitetura da Pesquisa Cognitiva do Azure](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitetura da Pesquisa Cognitiva do Azure")

Em termos de arquitetura, um serviço de pesquisa reside entre os armazenamentos de dados externos que contêm seus dados não indexados e um aplicativo cliente que envia solicitações de consulta para um índice de pesquisa e manipula a resposta.  Um esquema de índice determina a estrutura de conteúdo pesquisável. 

As duas cargas de trabalho primárias de um serviço de pesquisa são *indexação* e *consulta*.

+ A indexação leva o texto para o serviço de pesquisa e o torna pesquisável. Internamente, o texto de entrada é processado em tokens e armazenado em índices invertidos para verificações rápidas. Durante a indexação, você tem a opção de adicionar *habilidades cognitivas*, sejam predefinidas da Microsoft ou habilidades personalizadas que você cria. A análise e as transformações subsequentes podem resultar em novas informações e estruturas que não existiam, fornecendo grande utilidade para vários cenários de mineração de pesquisa e conhecimento.

+ Depois que um índice é preenchido com os dados pesquisáveis, seu aplicativo cliente envia solicitações de consulta para um serviço de pesquisa e manipula as respostas. Toda a execução da consulta é feita em um índice de pesquisa que você cria, detém e armazena em seu serviço. No aplicativo cliente, a experiência de pesquisa é definida usando APIs do Azure Cognitive Search e pode incluir ajuste de relevância, preenchimento automático, correspondência de sinônimos, correspondência difusa, correspondência de padrões, filtro e classificação.

A funcionalidade é exposta por meio de uma [API REST](/rest/api/searchservice/) ou um [SDK do .NET](search-howto-dotnet-sdk.md) simples que mascara a complexidade inerente da tecnologia de pesquisa. Você também pode usar o portal do Azure para administração de serviços e gerenciamento de conteúdo, com ferramentas para protótipo e consulta de seus índices e habilidades. Porque o serviço é executado na nuvem, infraestrutura e disponibilidade são gerenciados pela Microsoft.

## <a name="when-to-use-cognitive-search"></a>Quando usar o Cognitive Search

A Pesquisa Cognitiva do Azure é adequada para os seguintes cenários de aplicativo:

+ Consolidação de tipos de conteúdo heterogêneos em um índice de pesquisa privado definido pelo usuário. Você pode preencher um índice de pesquisa com fluxos de documentos JSON de qualquer origem. Para fontes com suporte no Azure, use um *indexador* para automatizar a indexação. O controle sobre o esquema de índice e o agendamento de atualização é um motivo importante para usar o Azure Cognitive Search.

+ Implementação fácil de recursos relacionados à pesquisa. As APIs de Pesquisa simplificam a construção da consulta, a faceted navigation, os filtros (incluindo a pesquisa geoespacial), o mapeamento de sinônimos, o preenchimento automático e o ajuste de relevância. Usando recursos internos, você pode satisfazer as expectativas do usuário final para uma experiência de pesquisa semelhante aos mecanismos de pesquisa da Web comercial.

+ Conteúdo bruto consiste em grandes arquivos de texto ou de imagem não diferenciados ou arquivos de aplicativos armazenados no Armazenamento de Blobs do Azure ou no Cosmos DB. Você pode aplicar [habilidades cognitivas](cognitive-search-concept-intro.md) durante a indexação para identificar e extrair texto, criar estrutura ou criar informações, como texto traduzido ou entidades.

+ O conteúdo precisa de uma análise linguística ou de texto personalizado. Se você tiver conteúdo que não está em inglês, a Pesquisa Cognitiva do Azure será compatível com os analisadores Lucene e com os processadores de linguagem natural da Microsoft. Você também pode configurar analisadores para obter processamento especializado de conteúdo bruto, como filtragem de sinais diacríticos ou reconhecimento e preservação de padrões em cadeias de caracteres.

Para obter mais informações sobre funcionalidades específicas, confira [Recursos do Azure Cognitive Search](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Quando usar Cognitive Search

### <a name="step-1-provision-service"></a>Etapa 1: Provisionar serviços

É possível [criar um serviço gratuito](search-create-service-portal.md) compartilhado com outros assinantes ou uma [camada paga](https://azure.microsoft.com/pricing/details/search/) que dedica os recursos utilizados apenas pelo seu serviço. Todos os tutoriais e inícios rápidos podem ser concluídos no serviço gratuito.

Para as camadas pagas, você pode dimensionar um serviço em duas dimensões para calibrar a origem com base nos requisitos de produção:

+ Adicionar Réplicas para aumentar a capacidade de manipular cargas de consulta pesadas
+ Adicionar Partições para aumentar o armazenamento para mais documentos

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice

Defina um esquema de índice a ser mapeado para refletir a estrutura dos documentos que você deseja pesquisar, semelhante a campos em um banco de dados. Um índice de pesquisa é uma estrutura de dados especializada otimizada para execução de consulta rápida.

É comum [criar o esquema de índice no portal do Azure](search-what-is-an-index.md) ou usar programaticamente o [SDK do .NET](search-howto-dotnet-sdk.md) ou a [API REST](/rest/api/searchservice/).

> [!TIP]
> Comece com o [Início Rápido: assistente de importação de dados](search-get-started-portal.md) para criar, carregar e consultar um índice em minutos.

### <a name="step-3-load-data"></a>Etapa 3: Carregar dados

Depois de definir um índice, você estará pronto para carregar o conteúdo. É possível usar um modelo push ou pull.

O modelo de push "efetua push" de documentos JSON para um índice usando APIs de um [SDK](search-howto-dotnet-sdk.md) ou [REST](/rest/api/searchservice/addupdate-or-delete-documents). O conjunto de dados externo pode ser praticamente qualquer fonte de dados, desde que os documentos sejam JSON.

O modelo de pull "efetua pull" dos dados de fontes no Azure e os envia para um índice de pesquisa. O modelo de pull é implementado por meio de [*indexadores*](/rest/api/searchservice/Indexer-operations) que simplificam e automatizam aspectos da ingestão de dados, como se conectar a dados, lê-los ou serializá-los. As fontes de dados com suporte incluem Azure Cosmos DB, Azure SQL e Armazenamento do Azure.

### <a name="step-4-send-queries-and-handle-responses"></a>Etapa 4: Enviar consultas e lidar com respostas

Depois de preencher um índice, você pode [emitir consultas de pesquisa](search-query-overview.md) para o ponto de extremidade de serviço usando solicitações HTTP simples com a [API REST](/rest/api/searchservice/Search-Documents) ou o [SDK do .NET](/dotnet/api/microsoft.azure.search.idocumentsoperations).

Percorra as etapas de [Criar seu primeiro aplicativo de pesquisa](tutorial-csharp-create-first-app.md) para criar e, em seguida, estender uma página da Web que coleta a entrada do usuário e manipula os resultados. Use também o [Postman para chamadas REST interativas](search-get-started-postman.md) ou o [Gerenciador de Pesquisa](search-explorer.md) interno no portal do Azure para consultar um índice existente.

## <a name="how-it-compares"></a>Como ele se compara

Os clientes frequentemente perguntam como a Pesquisa Cognitiva do Azure se compara a outras soluções relacionadas a pesquisa. A tabela a seguir resume as principais diferenças.

| Em comparação com | Principais diferenças |
|-------------|-----------------|
|Bing | A [API de Pesquisa na Web do Bing](../cognitive-services/bing-web-search/index.yml) pesquisa os índices no Bing.com para os termos correspondentes que você enviar. Os índices são criados de HTML, XML e outros conteúdos da web em sites públicos. Usando o mesmo conceito, a [Pesquisa Personalizada do Bing](/azure/cognitive-services/bing-custom-search/) oferece a mesma tecnologia de rastreador para tipos de conteúdo da Web no escopo para sites individuais.<br/><br/>A Pesquisa Cognitiva do Azure pesquisa um índice que você define, preenchido com os dados e documentos que você tem, geralmente de diversas fontes. A Pesquisa Cognitiva do Azure tem funcionalidades de rastreador para algumas fontes de dados por meio de [indexadores](search-indexer-overview.md), mas você pode enviar por push qualquer documento JSON que esteja de acordo com seu esquema de índice em um recurso pesquisável único e consolidado. |
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