---
title: Personalizar um modelo de Pessoa no Video Indexer – Azure
titleSuffix: Azure Media Services
description: Este artigo fornece uma visão geral do que é um modelo de Pessoa no Video Indexer e como personalizá-lo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 3fabba98cb137975da749411ca9accb5a951742d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838305"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Personalizar um modelo de Pessoa no Video Indexer

O Video Indexer suporta o reconhecimento de celebridades em seus vídeos. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Rostos que não são reconhecidos pelo Video Indexer ainda são detectados, mas são deixados sem nome. Os clientes podem criar modelos personalizados de pessoa e permitir que o Video Indexer reconheça rostos que não são reconhecidos por padrão. Os clientes podem construir esses modelos person emparelhando o nome de uma pessoa com arquivos de imagem do rosto da pessoa.  

Se sua conta atender a diferentes casos de uso, você pode se beneficiar de ser capaz de criar vários modelos de pessoa por conta. Por exemplo, se o conteúdo da sua conta for destinado a ser classificado em diferentes canais, você pode querer criar um modelo de Pessoa separada para cada canal. 

> [!NOTE]
> Cada modelo pessoa suporta até 1 milhão de pessoas e cada conta tem um limite de modelos de 50 Pessoas. 

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Treinando um novo rosto para um vídeo, atualiza o modelo personalizado específico com o que o vídeo foi associado. 

Se você não precisar de suporte ao modelo de várias pessoas, não atribua uma ID do modelo de Pessoa ao vídeo ao carregar/indexar ou reindexar. Neste caso, o Video Indexer usará o modelo person padrão em sua conta. 

Você pode usar o site do Indexador de Vídeo para editar rostos detectados em um vídeo e gerenciar vários modelos personalizados de Pessoa em sua conta, conforme descrito no modelo Personalizar uma Pessoa usando um tópico [do site.](customize-person-model-with-website.md) Você também pode usar a API, conforme descrito no [modelo Personalizar uma Pessoa usando APIs](customize-person-model-with-api.md).
