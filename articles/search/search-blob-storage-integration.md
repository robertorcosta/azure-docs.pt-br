---
title: Adicionar pesquisa de texto completo ao armazenamento de BLOBs do Azure
titleSuffix: Azure Search
description: Extraia o conteúdo e adicione a estrutura aos BLOBs do Azure ao criar um índice de pesquisa de texto completo no Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312191"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Adicionar pesquisa de texto completo aos dados de blob do Azure usando Azure Search

Pesquisar toda a variedade de tipos de conteúdo armazenados no Armazenamento de Blobs do Azure pode ser um problema difícil de solucionar. No entanto, você pode indexar e pesquisar o conteúdo de seus BLOBs em apenas alguns cliques usando [Azure Search](search-what-is-azure-search.md). Azure Search tem integração interna para indexação fora do armazenamento de BLOBs por meio de um [*indexador de blob*](search-howto-indexing-azure-blob-storage.md) que adiciona recursos de reconhecimento de fonte de dados à indexação.

## <a name="supported-content-types"></a>Tipos de conteúdo com suporte

Ao executar um indexador de BLOB em um contêiner, você pode extrair texto e metadados dos seguintes tipos de conteúdo com uma única consulta:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Opcionalmente, você pode anexar o [enriquecimento de ai](search-blob-ai-integration.md) na forma de um *qualificador* para criar novas informações e estrutura de BLOBs, incluindo representações de texto de arquivos de imagem. A adição do enriquecimento de ia expande a lista tipo de conteúdo para incluir JPEG e PNG. Ele adiciona habilidades de processamento de imagens, como [OCR (reconhecimento óptico de caracteres)](cognitive-search-skill-ocr.md) e identificação de [recursos visuais](cognitive-search-skill-image-analysis.md) , que possibilitam a indexação do conteúdo Visual encontrado em cada imagem.

## <a name="search-through-your-blob-metadata"></a>Pesquisar os metadados de blob
Um cenário comum que facilita a classificação de blobs de qualquer tipo de conteúdo é a indexação dos metadados personalizados e propriedades do sistema para cada blob. Dessa forma, as informações para todos os blobs são indexadas, independentemente do tipo de documento. Em seguida, você poderá classificar, filtrar e realizar a faceted navigation em todo o conteúdo do armazenamento de Blob.

[Saiba que mais sobre a indexação de metadados de blob.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indexar e pesquisar em blobs do JSON
O Azure Search pode ser configurado para extrair o conteúdo estruturado encontrado nos blobs que contêm JSON. O Azure Search pode ler blobs do JSON e analisar o conteúdo estruturado nos campos apropriados de um documento do Azure Search. O Azure Search também pode levar os blobs que contêm uma matriz de objetos JSON e mapeiam cada elemento para um documento do Azure Search separado.

Atualmente a análise de JSON não é configurável por meio do Portal. [Saiba mais sobre análise de JSON no Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Início rápido
O Azure Search pode ser adicionado para blobs diretamente da página do portal de Armazenamento de Blobs.

![](./media/search-blob-storage-integration/blob-blade.png)

Clique em **Adicionar Azure Search** para iniciar um fluxo onde você pode selecionar um serviço do Azure Search existente ou criar um novo serviço. Se você criar um novo serviço, você será direcionado para fora da experiência de portal da conta de Armazenamento. Você pode navegar de volta até a página do portal de Armazenamento e selecionar novamente a opção **Adicionar Azure Search**, onde você pode selecionar o serviço existente.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o Indexador de Blobs do Azure Search na [documentação](https://aka.ms/azsblobindexer) completa.
