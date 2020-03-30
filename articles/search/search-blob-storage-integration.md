---
title: Adicionar pesquisa de texto completa ao Armazenamento Azure Blob
titleSuffix: Azure Cognitive Search
description: Extrair conteúdo e adicionar estrutura aos blobs do Azure ao construir um índice completo de pesquisa de texto no Azure Cognitive earch.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496464"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Adicionar pesquisa de texto completa aos dados blob do Azure usando a Pesquisa Cognitiva do Azure

Pesquisar toda a variedade de tipos de conteúdo armazenados no Armazenamento de Blobs do Azure pode ser um problema difícil de solucionar. No entanto, você pode indexar e pesquisar o conteúdo de seus Blobs em apenas alguns cliques usando [a Pesquisa Cognitiva do Azure](search-what-is-azure-search.md). O Azure Cognitive Search tem integração incorporada para indexação de armazenamento Blob através de um [*indexador Blob*](search-howto-indexing-azure-blob-storage.md) que adiciona recursos de reconhecimento de origem de dados à indexação.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>O que significa adicionar pesquisa completa de texto a dados blob

O Azure Cognitive Search é um serviço de pesquisa em nuvem que fornece mecanismos de indexação e consulta que operam acima de índices definidos pelo usuário hospedados em seu serviço de pesquisa. Co-localizar seu conteúdo pesquisável com o mecanismo de consulta na nuvem é necessário para o desempenho, retornando resultados em uma velocidade que os usuários esperam de consultas de pesquisa.

O Azure Cognitive Search integra-se ao armazenamento Azure Blob na camada de indexação, importando seu conteúdo blob como documentos de pesquisa indexados em *índices invertidos* e outras estruturas de consulta que suportam consultas de texto de formulário livre e expressões de filtro. Como seu conteúdo blob é indexado em um índice de pesquisa, o acesso ao conteúdo blob pode aproveitar toda a gama de recursos de consulta na Pesquisa Cognitiva do Azure.

Uma vez que o índice é criado e preenchido, ele existe independentemente do seu contêiner blob, mas você pode reexecutar operações de indexação para atualizar seu índice com alterações no contêiner subjacente. As informações de carimbo de tempo em blobs individuais são usadas para detecção de alterações. Você pode optar pela execução programada ou indexação demanda como mecanismo de atualização.

As entradas são suas bolhas, em um único recipiente, no armazenamento Azure Blob. Blobs podem ser quase qualquer tipo de dados de texto. Se suas bolhas contiverem imagens, você pode adicionar [o enriquecimento de IA à indexação de bolhas](search-blob-ai-integration.md) para criar e extrair texto de imagens.

A saída é sempre um índice de Pesquisa Cognitiva do Azure, usado para pesquisa rápida de texto, recuperação e exploração em aplicativos clientes. No meio está a própria arquitetura de pipeline de indexação. O pipeline é baseado no recurso *indexador,* discutido mais adiante neste artigo.

## <a name="start-with-services"></a>Comece com serviços

Você precisa de Pesquisa Cognitiva Azure e armazenamento Azure Blob. Dentro do armazenamento Blob, você precisa de um contêiner que forneça conteúdo de origem.

Você pode começar diretamente na página do portal da conta de armazenamento. Na página de navegação à esquerda, em **serviço Blob** clique em **Adicionar pesquisa cognitiva do Azure** para criar um novo serviço ou selecionar um já existente. 

Depois de adicionar o Azure Cognitive Search à sua conta de armazenamento, você pode seguir o processo padrão para indexar dados blob. Recomendamos o **assistente de dados importação** no Azure Cognitive Search para uma introdução inicial fácil ou ligue para as APIs REST usando uma ferramenta como O Carteiro. Este tutorial orienta você através das etapas de chamar a API REST em Carteiro: [Index e pesquise dados semi-estruturados (blobs JSON) na Pesquisa Cognitiva do Azure](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Use um indexador Blob

Um *indexador* é um subserviço de reconhecimento de origem de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. 

Blobs no Azure Storage são indexados usando o [indexador de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md). Você pode invocar este indexador usando o assistente **de dados Importação,** uma API REST ou o .NET SDK. Em código, você usa este indexador definindo o tipo e fornecendo informações de conexão que incluem uma conta do Azure Storage juntamente com um contêiner blob. Você pode subdefinir suas bolhas criando um diretório virtual, que você pode então passar como um parâmetro, ou filtrando uma extensão de tipo de arquivo.

Um indexador faz o "quebra-documento", abrindo uma bolha para inspecionar o conteúdo. Depois de se conectar à fonte de dados, é o primeiro passo no pipeline. Para dados blob, é aí que PDF, documentos de escritório e outros tipos de conteúdo são detectados. A quebra de documento com extração de texto não é custo. Se suas bolhas contiverem conteúdo de imagem, as imagens serão ignoradas a menos que você [adicione o enriquecimento da IA](search-blob-ai-integration.md). A indexação padrão se aplica apenas ao conteúdo do texto.

O indexador Blob vem com parâmetros de configuração e suporta o rastreamento de alterações se os dados subjacentes fornecerem informações suficientes. Você pode aprender mais sobre a funcionalidade principal no [indexador de armazenamento Azure Cognitive Search Blob](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipos de conteúdo com suporte

Ao executar um indexador Blob sobre um contêiner, você pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexação de metadados blob

Um cenário comum que facilita a classificação através de bolhas de qualquer tipo de conteúdo é indexar metadados personalizados e propriedades do sistema para cada bolha. Desta forma, as informações para todas as bolhas são indexadas independentemente do tipo de documento, armazenadas em um índice em seu serviço de pesquisa. Usando seu novo índice, você pode proceder para classificar, filtrar e faceta em todo o conteúdo de armazenamento Blob.

### <a name="indexing-json-blobs"></a>Indexando bolhas JSON
Os indexadores podem ser configurados para extrair conteúdo estruturado encontrado em bolhas que contêm JSON. Um indexador pode ler bolhas JSON e analisar o conteúdo estruturado nos campos apropriados de um documento de pesquisa. Os indexadores também podem pegar blobs que contêm uma matriz de objetos JSON e mapear cada elemento para um documento de pesquisa separado. Você pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexador.

## <a name="search-blob-content-in-a-search-index"></a>Pesquisar conteúdo blob em um índice de pesquisa 

A saída de uma indexação é um índice de pesquisa, usado para exploração interativa usando texto gratuito e consultas filtradas em um aplicativo cliente. Para exploração inicial e verificação de conteúdo, recomendamos começar com o [Search Explorer](search-explorer.md) no portal para examinar a estrutura do documento. Você pode usar [sintaxe de consulta simples,](query-simple-syntax.md) [sintaxe de consulta completa](query-lucene-syntax.md)e [sintaxe de expressão de filtro](query-odata-filter-orderby-syntax.md) no Explorador de Pesquisa.

Uma solução mais permanente é coletar entradas de consulta e apresentar a resposta como resultados de pesquisa em um aplicativo cliente. O tutorial C# a seguir explica como criar um aplicativo de pesquisa: [Crie seu primeiro aplicativo na Pesquisa Cognitiva do Azure](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Próximas etapas

+ [Carregar, baixar e listar bolhas com o portal Azure (armazenamento Azure Blob)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configure um indexador blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
