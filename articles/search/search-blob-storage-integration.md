---
title: Adicionar pesquisa de texto completo ao Armazenamento de Blobs do Azure
titleSuffix: Azure Cognitive Search
description: Extraia o conteúdo e adicione a estrutura aos blobs do Azure quando criar um índice de pesquisa de texto completo no Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fd405513f8bdef09e6d3ab996fc2c04bd397db13
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659444"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Adicionar pesquisa de texto completo aos dados de blob do Azure usando o Azure Cognitive Search

Pesquisar toda a variedade de tipos de conteúdo armazenados no Armazenamento de Blobs do Azure pode ser um problema difícil de solucionar. No entanto, você pode indexar e pesquisar o conteúdo de seus Blobs em apenas alguns cliques usando o [Azure Cognitive Search](search-what-is-azure-search.md). O Azure Cognitive Search tem integração interna para indexação fora do armazenamento de Blobs por meio de um [*indexador de Blob*](search-howto-indexing-azure-blob-storage.md) que adiciona recursos de reconhecimento de fonte de dados à indexação.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Adicionar pesquisa de texto completo aos dados do blob

O Azure Cognitive Search é um serviço de pesquisa de nuvem que fornece mecanismos de indexação e consulta operando em índices definidos pelo usuário hospedados em seu serviço de pesquisa. A colocalização de seu conteúdo pesquisável com o mecanismo de consulta na nuvem é necessária para o desempenho, retornando resultados em uma velocidade que os usuários esperam das consultas de pesquisa.

O Azure Cognitive Search integra-se ao armazenamento de blobs do Azure na camada de indexação, importando o conteúdo do blob como documentos de pesquisa que são indexados em *índices invertidos* e outras estruturas de consulta que dão suporte a consultas de texto de forma livre e expressões de filtro. Como o conteúdo do blob é indexado em um índice de pesquisa, o acesso a ele pode utilizar uma gama completa de recursos de consulta no Azure Cognitive Search.

Depois que o índice é criado e preenchido, ele existe independentemente do seu contêiner de blob, mas você pode executar novamente as operações de indexação para atualizar o índice com as alterações no contêiner subjacente. As informações de carimbo de data/hora em blobs individuais são usadas para detecção de alteração. Você pode optar pela execução agendada ou indexação sob demanda como o mecanismo de atualização.

As entradas são seus blobs, em um único contêiner, no armazenamento de blobs do Azure. Os blobs podem ser praticamente qualquer tipo de dados de texto. Se seus blobs contiverem imagens, você poderá adicionar [enriquecimento de IA à indexação de blob](search-blob-ai-integration.md) para criar e extrair texto de imagens.

A saída é sempre um índice do Azure Cognitive Search usado para pesquisa rápida de texto, recuperação e exploração em aplicativos cliente. No meio disso, está a própria arquitetura de pipeline de indexação. O pipeline é baseado no recurso *indexador*, discutido mais adiante neste artigo.

## <a name="start-with-services"></a>Iniciar com serviços

Você precisa do Azure Cognitive Search e do armazenamento de Blobs do Azure. No armazenamento de Blobs, você precisa de um contêiner que forneça o conteúdo de origem.

Você pode iniciar diretamente em sua página do portal da conta de armazenamento. Na página de navegação à esquerda, em **Serviço Blob** clique em **Adicionar Azure Cognitive Search** para criar um novo serviço ou selecione um existente. 

Depois de adicionar o Azure Cognitive Search à sua conta de armazenamento, você pode seguir o processo padrão para indexar dados de blob. Recomendamos o assistente **Importar dados** no Azure Cognitive Search para uma introdução fácil, ou chamar as APIs REST usando uma ferramenta como o Postman. Este tutorial orienta você pelas etapas de chamada da API REST no Postman: [Indexar e pesquisar dados semiestruturados (blobs JSON) no Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Configurar um indexador de Blob

Um *indexador* é um subserviço com reconhecimento de fonte de dados equipado com lógica interna para dados de amostragem, leitura de dados de metadados, recuperação de dados e serialização de dados de formatos nativos em documentos JSON para importação subsequente. 

Os blobs no Armazenamento do Azure são indexados usando o [indexador de armazenamento de Blobs do Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Você pode invocar esse indexador usando o assistente **Importar dados**, uma API REST ou o SDK do .NET. No código, use esse indexador definindo o tipo e fornecendo informações de conexão que incluem uma conta de Armazenamento do Azure com um contêiner de blobs. É possível subagrupar seus blobs criando um diretório virtual, que você pode então passar como um parâmetro, ou filtrando uma extensão de tipo de arquivo.

Um indexador faz a "quebra de documento", abrindo um blob para inspecionar o conteúdo. Depois de se conectar à fonte de dados, é a primeira etapa no pipeline. Para dados de blob, este é o local onde são detectados PDFs, documentos do Office e outros tipos de conteúdo. A quebra de documento com a extração de texto não é cobrada. Se seus blobs contiverem conteúdo de imagem, as imagens serão ignoradas, a menos que você [adicione enriquecimento de IA](search-blob-ai-integration.md). A indexação padrão aplica-se somente ao conteúdo de texto.

O indexador de Blob vem com parâmetros de configuração e oferece suporte ao controle de alterações, caso os dados subjacentes forneçam informações suficientes. Você pode saber mais sobre a funcionalidade básica no [indexar de armazenamento de Blobs do Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Tipos de conteúdo com suporte

Quando você executa um indexador de Blob em um contêiner, pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexação dos metadados de blobs

Um cenário comum que facilita a classificação de blobs de qualquer tipo de conteúdo é a indexação dos metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações de todos os blobs são indexadas, independentemente do tipo de documento, armazenadas em um índice no seu serviço de pesquisa. Usando o novo índice, você poderá classificar, filtrar e aprimorar em todo o conteúdo do armazenamento de Blob.

> [!NOTE]
> As marcas de índice de blob são indexadas nativamente pelo serviço de armazenamento de Blob e expostas para consulta. Se os atributos de chave/valor de seus blobs exigem recursos de indexação e filtragem, as marcas de índice de blob devem ser utilizadas em vez de metadados.
>
> Para saber mais sobre o Índice de Blobs, confira [Gerenciar e localizar dados no Armazenamento de Blobs do Azure com o Índice de Blobs](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indexação de dados JSON
Os indexadores podem ser configurados para extrair o conteúdo estruturado encontrado nos blobs que contêm JSON. Um indexador pode ler blobs do JSON e analisar o conteúdo estruturado nos campos apropriados de um documento de pesquisa. Os indexadores também podem levar os blobs, que contêm uma matriz de objetos JSON e mapeiam cada elemento, para um documento de pesquisa separado. Você pode definir um modo de análise para afetar o tipo de objeto JSON criado pelo indexador.

## <a name="search-blob-content-in-a-search-index"></a>Pesquisar conteúdo de blob em um índice de pesquisa 

A saída de uma indexação é um índice de pesquisa, usado para exploração interativa usando texto livre e consultas filtradas em um aplicativo cliente. Para a exploração e a verificação iniciais do conteúdo, é recomendável começar com o [Gerenciador de Pesquisa](search-explorer.md) no portal para examinar a estrutura do documento. Você pode usar a [sintaxe de consulta simples](query-simple-syntax.md), a [sintaxe de consulta completa](query-lucene-syntax.md) e a [sintaxe de expressão de filtro](query-odata-filter-orderby-syntax.md) no Gerenciador de Pesquisa.

Uma solução mais permanente é coletar entradas de consulta e apresentar a resposta como resultado da pesquisa em um aplicativo cliente. O tutorial C# a seguir explica como criar um aplicativo de pesquisa: [Criar seu primeiro aplicativo no Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Próximas etapas

+ [Carregar, baixar e listar blobs com o portal do Azure (armazenamento de blobs do Azure)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Configurar um indexador de blob (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
