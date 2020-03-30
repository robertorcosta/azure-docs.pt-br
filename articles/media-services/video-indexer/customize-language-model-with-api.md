---
title: Personalize um modelo de idioma com API do indexador de vídeo
titlesuffix: Azure Media Services
description: Aprenda a personalizar um modelo de idioma com a API do Indexador de Vídeo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127971"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Personalize um modelo de idioma com a API do indexador de vídeo

O Video Indexer permite criar modelos de Linguagem personalizados para personalizar o reconhecimento de fala, carregando o texto de adaptação, ou seja, o texto do domínio a cujo vocabulário você deseja que o mecanismo se adapte. Depois de treinar seu modelo, novas palavras que apareçam no texto de adaptação serão reconhecidas.

Para obter uma visão geral detalhada e as melhores práticas para modelos de Linguagem personalizados, confira [Personalizar um modelo de Linguagem com o Video Indexer](customize-language-model-overview.md).

Use as APIs do Video Indexer para criar e editar modelos de Linguagem personalizados em sua conta, conforme descrito neste tópico. Use também o site, conforme descrito em [Personalizar o modelo de Linguagem usando o site do Video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Criar um modelo de Linguagem

A [aPI](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) de modelo de idioma cria um novo modelo de linguagem personalizado na conta especificada. Você pode carregar arquivos para o modelo de Linguagem nessa chamada. Como alternativa, você pode criar o modelo de Linguagem aqui e carregar arquivos para o modelo mais tarde, atualizando o modelo de Linguagem.

> [!NOTE]
> Você ainda deverá treinar o modelo com seus arquivos habilitados para o modelo para aprender o conteúdo dos arquivos. As instruções sobre como treinar uma linguagem se encontram na próxima seção.

Para carregar arquivos a serem adicionados ao modelo Language, você deve carregar arquivos no corpo usando FormData, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de fazer essa tarefa:

* A chave será o nome do arquivo e o valor será o arquivo txt.
* A chave será o nome do arquivo e o valor será uma URL para arquivo txt.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de Linguagem recém-criado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato desta saída JSON exemplo:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Treinar um modelo de Linguagem

O [trem de um modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API treina um modelo de idioma personalizado na conta especificada com o conteúdo nos arquivos que foram carregados e habilitados no modelo de idioma.

> [!NOTE]
> Primeiro é necessário criar o modelo de Linguagem e carregar seus arquivos. Você pode carregar arquivos ao criar o modelo Language ou atualizando o modelo Language.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de Linguagem recém-treinado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato desta saída JSON exemplo:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

O `id` retornado é um ID único usado `languageModelId` para distinguir entre modelos de idioma, enquanto é usado tanto para [carregar um vídeo para indexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) quanto [reindexar uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) APIs de vídeo (também conhecida como `linguisticModelId` em APIs de upload/reindexação de vídeo).

## <a name="delete-a-language-model"></a>Excluir um modelo de Linguagem

A [exclusão de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) API modelo de idioma exclui um modelo de idioma personalizado da conta especificada. Qualquer vídeo que estivesse usando o modelo de idioma excluído manterá o mesmo índice até que você reindexe o vídeo. Se você reindexar o vídeo, você pode atribuir um novo modelo de idioma ao vídeo. Caso contrário, o Video Indexer usará seu modelo padrão para reindexar o vídeo.

### <a name="response"></a>Resposta

Não há conteúdo retornado quando o modelo de Idioma é excluído com sucesso.

## <a name="update-a-language-model"></a>Atualizar um modelo de Linguagem

A atualização de uma API [modelo de idioma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) atualiza um modelo de pessoa de idioma personalizado na conta especificada.

> [!NOTE]
> Você já precisa ter criado um modelo de Linguagem. Use essa chamada para habilitar ou desabilitar todos os arquivos no modelo, atualizar o nome do modelo de Linguagem e carregar os arquivos a serem adicionados ao modelo de linguagem.

Para carregar arquivos a serem adicionados ao modelo Language, você deve carregar arquivos no corpo usando FormData, além de fornecer valores para os parâmetros necessários acima. Há duas maneiras de fazer essa tarefa:

* A chave será o nome do arquivo e o valor será o arquivo txt.
* A chave será o nome do arquivo e o valor será uma URL para arquivo txt.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de Linguagem recém-treinado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato desta saída JSON exemplo:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Use `id` os arquivos retornados na resposta para baixar o conteúdo do arquivo.

## <a name="update-a-file-from-a-language-model"></a>Atualizar um arquivo com base em um modelo de Linguagem

A [atualização de um arquivo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) permite `enable` que você atualize o nome e o estado de um arquivo em um modelo de idioma personalizado na conta especificada.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o arquivo que você atualizou seguindo o formato da saída JSON de exemplo abaixo.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Use `id` o arquivo retornado na resposta para baixar o conteúdo do arquivo.

## <a name="get-a-specific-language-model"></a>Obter um modelo de Linguagem específico

A API [obter](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) retorna informações sobre o modelo de idioma especificado na conta especificada, como idioma e os arquivos que estão no modelo De idioma.

### <a name="response"></a>Resposta

A resposta fornece metadados sobre o modelo de Linguagem especificado, juntamente com metadados em cada um dos arquivos do modelo seguindo o formato desta saída JSON exemplo:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Use `id` o arquivo retornado na resposta para baixar o conteúdo do arquivo.

## <a name="get-all-the-language-models"></a>Obter todos os modelos de Linguagem

A [aPI obter todas](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) as API retorna todos os modelos de idioma personalizados na conta especificada em uma lista.

### <a name="response"></a>Resposta

A resposta fornece uma lista de todos os modelos de Idioma em sua conta e cada um de seus metadados e arquivos seguindo o formato desta saída JSON exemplo:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Excluir um arquivo de um modelo de Linguagem

A API [excluir](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) exclui o arquivo especificado do modelo de Idioma especificado na conta especificada.

### <a name="response"></a>Resposta

Não há conteúdo retornado quando o arquivo é excluído do modelo de idioma com sucesso.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Obter os metadados sobre um arquivo de um modelo de Linguagem

Os [metadados de obter uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) API de arquivo devolveo o conteúdo e os metadados no arquivo especificado do modelo de Idioma escolhido em sua conta.

### <a name="response"></a>Resposta

A resposta fornece o conteúdo e os metadados do arquivo no formato JSON, semelhante a este exemplo:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> O conteúdo desse arquivo de exemplo são as palavras "olá" e "mundo" em duas linhas separadas.

## <a name="download-a-file-from-a-language-model"></a>Baixe um arquivo de um modelo de idioma

O [download de uma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) API de arquivo baixa um arquivo de texto contendo o conteúdo do arquivo especificado do modelo de Idioma especificado na conta especificada. Esse arquivo de texto deve corresponder ao conteúdo do arquivo de texto que foi originalmente carregado.

### <a name="response"></a>Resposta

A resposta será o download de um arquivo de texto com o conteúdo do arquivo no formato JSON.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Linguagem usando o site](customize-language-model-with-website.md)
