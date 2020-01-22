---
title: Usar o Azure Video Indexer para personalizar o modelo de marcas
titleSuffix: Azure Media Services
description: Este artigo demonstra como usar o Video Indexer do Azure para personalizar o modelo de marcas.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 81ba4cc7be5f9361d21aaea2ba78d0fd6f0f8c95
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289910"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft é mencionada em um conteúdo de áudio ou de vídeo ou se ela aparece no texto visual em um vídeo, o Video Indexer detecta-a como uma marca presente no conteúdo. Um modelo de marcas personalizado permite que você exclua algumas marcas da lista daquelas a serem detectadas, além de incluir marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing.

Para obter uma visão geral detalhada, confira [Visão geral](customize-brands-model-overview.md).

Você pode usar a API do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de marcas usando o site do Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

A API [criar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) cria uma nova marca personalizada e a adiciona ao modelo de marcas personalizadas para a conta especificada. 

> [!NOTE]
> A configuração **habilitado** (no corpo) como true coloca a marca na lista de *inclusão* para video indexer a ser detectada. Definir **enabled** como false coloca a marca na lista *Exclusões*, de modo que o Video Indexer não a detectará.

Alguns outros parâmetros que podem ser definidos no corpo:

* O valor **referenceUrl** pode ser qualquer site de referência para a marca, assim como um link para sua página da Wikipédia.
* O valor **tags** é uma lista de marcas para a marca comercial. Isso é mostrado no campo *Categoria* da marca no site do Video Indexer. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você acabou de criar de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Excluir uma marca

A API [excluir uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) remove uma marca do modelo de marcas personalizadas da conta especificada. A conta é especificada no parâmetro **accountId**. Quando chamada com êxito, a marca não estará mais nas listas de marcas *Inclusões* ou *Exclusões*.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a marca é excluída com êxito.

## <a name="get-a-specific-brand"></a>Obter uma marca específica

A API [obter uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece informações sobre a marca que você pesquisou (usando a ID da marca) de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **enabled** sendo definido como **true** significa que a marca está na lista *Incluir* para o Video Indexer detectar, enquanto **enabled** sendo false significa que a marca está na lista *Excluir*, portanto, o Video Indexer não a detectará.

## <a name="update-a-specific-brand"></a>Atualiza uma marca específica

A API [atualizar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) permite pesquisar os detalhes de uma marca no modelo de marcas personalizadas para a conta especificada usando a ID da marca.

### <a name="response"></a>Resposta

A resposta fornece as informações atualizadas sobre a marca que você acabou de criar de acordo com o formato do exemplo a seguir.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Obter todas as marcas

A API [obter todas as marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) retorna todas as marcas no modelo de marcas personalizadas para a conta especificada, independentemente de a marca estar destinada a estar na lista *incluir* ou *excluir* marcas.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todas as marcas na sua conta e cada um dos seus detalhes, seguindo o formato do exemplo a seguir.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A marca nomeada *Exemplo* está na lista de *Inclusões* a serem detectadas pelo Video Indexer, enquanto a marca nomeada *Exemplo2* está na lista *Exclusões* e, portanto, não será detectada pelo Video Indexer.

## <a name="get-brands-model-settings"></a>Obter configurações de modelo de marcas

A API [obter configurações de marcas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) retorna as configurações do modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, o Video Indexer só detectará marcas do modelo de marcas personalizado da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas do Bing estão habilitadas seguindo o formato do exemplo a seguir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing estarão desabilitadas. O valor **state** pode ser ignorado, pois ele foi preterido.

## <a name="update-brands-model-settings"></a>Atualizar configurações de modelo de marcas

As [marcas de atualização](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) atualiza as configurações do modelo de marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas do Bing não estiverem habilitadas, o Video Indexer só detectará marcas do modelo de marcas personalizado da conta especificada.

O sinalizador **useBuiltIn** definido como true representa que as marcas do Bing estão habilitadas. Se *useBuiltin* for false, as marcas do Bing estarão desabilitadas.

### <a name="response"></a>Resposta

Não há nenhum conteúdo retornado quando a configuração de modelo de marca é atualizada com êxito.

## <a name="next-steps"></a>Próximos passos

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
