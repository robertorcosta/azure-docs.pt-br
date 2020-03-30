---
title: Personalize um modelo de pessoa com API do indexador de vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Pessoa com a API do Indexador de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: fa41fca7f8ad96cf507aa6f04059b1254c8c3961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127902"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Personalizar um modelo de Pessoa com a API do Video Indexer

O Video Indexer dá suporte à detecção facial e ao reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange cerca de um milhão de rostos com base em fontes de dados comumente solicitadas, como IMDB, Wikipedia e os principais influenciadores do LinkedIn. Rostos que não são reconhecidos pelo recurso de reconhecimento de celebridades são detectados, mas não identificados. Depois de enviar seu vídeo para o Video Indexer e obter resultados de volta, você pode voltar e nomear os rostos que não foram reconhecidos. Depois que você rotular um rosto com um nome, o rosto e o nome serão adicionados ao modelo de Pessoa de sua conta. Em seguida, o Video Indexer reconhecerá esse rosto em seus vídeos futuros e antigos.

Você pode usar a API do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Gerenciar vários modelos de Pessoa

O Video Indexer dá suporte a vários modelos de Pessoa por conta. Esse recurso está disponível atualmente apenas por meio de APIs do Video Indexer.

Se a conta lida com diferentes cenários de caso de uso, talvez seja conveniente que você crie vários modelos de Pessoa por conta. Por exemplo, se o seu conteúdo está relacionado a esportes, você pode então criar um modelo de Pessoa separada para cada esporte (futebol, basquete, futebol e assim por diante).

Depois de criar um modelo, você pode usá-lo, fornecendo a ID do modelo de um modelo específico de Pessoa ao carregar/indexar ou reindexar um vídeo. Treinar um novo rosto para um vídeo atualiza o modelo personalizado específico com o qual o vídeo foi associado.

Cada conta tem um limite de 50 modelos de Pessoa. Se você não precisar do suporte ao modelo de várias pessoas, não atribua um ID de modelo de pessoa ao seu vídeo ao carregar/indexar ou reindexar. Nesse caso, o Video Indexer usa o modelo de Pessoa personalizado padrão em sua conta.

## <a name="create-a-new-person-model"></a>Criar um novo modelo de Pessoa

Para criar um novo modelo Person na conta especificada, use a API [de modelo criar uma pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)

A resposta fornece o nome e a ID de modelo gerada do modelo de Pessoa que você acabou de criar, seguindo o formato do exemplo a seguir.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Em seguida, use o valor **de id** para o parâmetro **personModelId** [ao carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Excluir um modelo de Pessoa

Para excluir um modelo de Pessoa personalizada da conta especificada, use a API [do modelo de exclusão de uma pessoa.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)

Depois que o modelo de Pessoa é excluído com êxito, o índice de seus vídeos atuais que estavam usando o modelo excluído permanecerá inalterado até você reindexá-los. Ao reindexar, os rostos que foram nomeados no modelo excluído não serão reconhecidos pelo Video Indexer em seus vídeos atuais que foram indexados usando esse modelo, mas os rostos ainda serão detectados. Seus vídeos atuais que foram indexados usando o modelo excluído usarão agora o modelo de Pessoa padrão da sua conta. Se os rostos do modelo excluído também forem nomeados no modelo padrão da sua conta, esses rostos continuarão a ser reconhecidos nos vídeos.

Não há conteúdo retornado quando o modelo Person é excluído com sucesso.

## <a name="get-all-person-models"></a>Obter todos os modelos de Pessoa

Para obter todos os modelos Person na conta especificada, use a API [do modelo get a person.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)

A resposta fornece uma lista de todos os modelos de Pessoa em sua conta (incluindo o modelo person padrão na conta especificada) e cada um de seus nomes e IDs seguindo o formato do exemplo abaixo.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Você pode escolher qual modelo deseja usar para `id` um vídeo usando `personModelId` o valor do modelo Person para o parâmetro ao [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [reindexar um vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Atualizar um rosto

Esse comando permite que você atualize um rosto no vídeo com um nome usando a ID do vídeo e a ID do rosto. Essa ação então atualiza o modelo Person com o que o vídeo foi associado ao carregar/indexar ou reindexar. Se nenhum modelo de Pessoa foi atribuído, ele atualiza o modelo de Pessoa padrão da conta.

O sistema então reconhece as ocorrências do mesmo rosto em seus outros vídeos atuais que compartilham o mesmo modelo Person. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote.

Atualize um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que você fornecer terá precedência sobre o reconhecimento de celebridades interno.

Para atualizar o rosto, use a atualização de uma API [de rosto de vídeo.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)

Os nomes são únicos para modelos Person, portanto, se você `name` der duas faces diferentes no mesmo modelo person o mesmo valor de parâmetro, o Indexador de vídeo visualiza os rostos como a mesma pessoa e converge-os uma vez que você reindexar o seu vídeo.

## <a name="next-steps"></a>Próximas etapas

[Personalizar um modelo de Pessoa usando o site do Video Indexer](customize-person-model-with-website.md)
