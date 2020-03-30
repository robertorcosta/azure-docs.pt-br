---
title: Personalize um modelo de marcas com API do indexador de vídeo
titleSuffix: Azure Media Services
description: Aprenda a personalizar um modelo de Marcas com a API do Indexador de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: anzaman
ms.openlocfilehash: 79c3a7934e9152a4908f895c20ee6fbdc0f360cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128002"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Personalizar um modelo de marcas com a API do Video Indexer

O Video Indexer dá suporte à detecção de marca de fala e texto visual durante a indexação e a reindexação de conteúdo de áudio e de vídeo. O recurso de detecção de marca identifica menções a produtos, serviços e empresas sugeridos pelo banco de dados de marcas do Bing. Por exemplo, se a Microsoft for mencionada em conteúdo de vídeo ou áudio ou se aparecer em texto visual em um vídeo, o Video Indexer detecta-o como uma marca no conteúdo. Um modelo de marcas personalizado permite que você exclua algumas marcas da lista daquelas a serem detectadas, além de incluir marcas que devem ser parte de seu modelo e que podem não estar no banco de dados de marcas do Bing.

Para obter uma visão geral detalhada, confira [Visão geral](customize-brands-model-overview.md).

Você pode usar a API do Video Indexer para criar, usar e editar modelos personalizados de marcas detectados em um vídeo, conforme descrito neste tópico. Você também pode usar o site do Video Indexer, conforme descrito em [Personalizar o modelo de marcas usando o site do Video Indexer](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Criar uma marca

A [aPI criar uma marca](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand) cria uma nova marca personalizada e a adiciona ao modelo de Marcas personalizadas para a conta especificada.

> [!NOTE]
> A `enabled` configuração (no corpo) para a verdade coloca a marca na lista *Incluir* para indexador de vídeo para detectar. A `enabled` configuração para falso coloca a marca na lista *Exclusão,* para que o Indexador de vídeo não a detecte.

Alguns outros parâmetros que você pode definir no corpo:

* O `referenceUrl` valor pode ser qualquer site de referência para a marca, como um link para sua página na Wikipédia.
* O `tags` valor é uma lista de tags para a marca. Esta tag aparece no campo *Categoria* da marca no site do Indexador de Vídeo. Por exemplo, a marca "Azure" pode ser marcada ou categorizada como "Nuvem".

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

A [exclusão de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?) API da marca remove uma marca do modelo marcas personalizadas para a conta especificada. A conta está especificada no `accountId` parâmetro. Quando chamada com êxito, a marca não estará mais nas listas de marcas *Inclusões* ou *Exclusões*.

### <a name="response"></a>Resposta

Não há conteúdo retornado quando a marca é excluída com sucesso.

## <a name="get-a-specific-brand"></a>Obter uma marca específica

A [API get a brand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?) permite que você procure os detalhes de uma marca no modelo de Marcas personalizadas para a conta especificada usando o ID da marca.

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
> `enabled`sendo definido `true` para significar que a marca está na lista *Incluir* para indexador de vídeo para detectar, e `enabled` ser falso significa que a marca está na lista *Excluir,* para que o Indexador de vídeo não a detecte.

## <a name="update-a-specific-brand"></a>Atualiza uma marca específica

A [atualização de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?) API da marca permite que você procure os detalhes de uma marca no modelo de Marcas personalizadas para a conta especificada usando o ID da marca.

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

A [API get all brands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?) retorna todas as marcas no modelo de Marcas personalizadas para a conta especificada, independentemente de a marca estar na lista De *incluir* ou *excluir* marcas.

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
> A marca nomeada *Exemplo* está na lista *Incluir* para indexador de vídeo para detectar, e a marca chamada *Example2* está na lista *Excluir,* para que o Indexador de vídeo não o detecte.

## <a name="get-brands-model-settings"></a>Obter configurações de modelo de marcas

A API [de configurações de marcas obter](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands) retorna as configurações do modelo Marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas Bing não forem habilitadas, o Video Indexer só detectará marcas do modelo de Marcas personalizadas da conta especificada.

### <a name="response"></a>Resposta

A resposta mostra se as marcas do Bing estão habilitadas seguindo o formato do exemplo a seguir.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn`sendo definido como verdadeiro representa que as marcas Bing estão habilitadas. Se `useBuiltin` for falso, as marcas Bing são desativadas. O `state` valor pode ser ignorado porque foi preterido.

## <a name="update-brands-model-settings"></a>Atualizar configurações de modelo de marcas

A API [das marcas de atualização](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?) atualiza as configurações do modelo Marcas na conta especificada. As configurações de modelo de marcas representam o estado habilitado ou desabilitado da detecção do banco de dados de marcas do Bing. Se as marcas Bing não forem habilitadas, o Video Indexer só detectará marcas do modelo de Marcas personalizadas da conta especificada.

A `useBuiltIn` bandeira definida como verdadeira significa que as marcas Bing estão habilitadas. Se `useBuiltin` for falso, as marcas Bing são desativadas.

### <a name="response"></a>Resposta

Não há conteúdo retornado quando a configuração do modelo Marcas é atualizada com sucesso.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Marcas usando o site](customize-brands-model-with-website.md)
