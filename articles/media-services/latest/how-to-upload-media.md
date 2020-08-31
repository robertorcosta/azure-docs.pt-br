---
title: Carregar mídia
titleSuffix: Azure Media Services
description: Saiba como carregar mídia para streaming ou codificação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: a046a3caba9a0909d873356bda8d1fa6cf1f9860
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719250"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Carregar mídia para streaming ou codificação

Nos Serviços de Mídia, você pode carregar seus arquivos digitais (mídia) em um contêiner de blob associado a um ativo. A entidade [Asset](/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados no contêiner do ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais.

No entanto, antes de começar, você precisará coletar ou pensar em alguns valores.

1. O caminho do arquivo local até o arquivo que você deseja carregar
1. A ID do ativo para o ativo (contêiner)
1. O nome que você deseja usar para o arquivo carregado, incluindo a extensão
1. O nome da conta de armazenamento que você está usando
1. A chave de acesso da sua conta de armazenamento

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Depois que você tiver [criado um ativo usando o Postman ou outro método REST e tiver obtido a URL SAS para o ativo](how-to-create-asset.md?tabs=rest), use os SDKs ou as APIs do Armazenamento do Microsoft Azure (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Para outros métodos, confira a [Documentação do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/blobs/) para trabalhar com blobs em [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python) e [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Próximas etapas

> [Visão geral dos Serviços de Mídia](media-services-overview.md)
