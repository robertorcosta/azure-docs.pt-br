---
title: Criar uma entrada de trabalho dos serviços de mídia do Azure de um arquivo local | Microsoft Docs
description: Este artigo demonstra como criar uma entrada de trabalho dos serviços de mídia do Azure de um arquivo local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80345918"
---
# <a name="create-a-job-input-from-a-local-file"></a>Criar uma entrada de trabalho de um arquivo local

Nos Serviços de Mídia v3, ao enviar Trabalhos para processar seus vídeos, você precisa informar os Serviços de Mídia onde encontrar o vídeo de entrada. O vídeo de entrada pode ser armazenado como um Ativo de Serviço de Mídia. Nesse caso, você cria um ativo de entrada com base em um arquivo (armazenado localmente ou no Armazenamento de Blobs do Azure). Este tópico mostra como criar uma entrada de trabalho de um arquivo local. Para obter um exemplo completo, confira esta [amostra do GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Pré-requisitos 

* [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).
* Examine [gerenciar ativos](manage-asset-concept.md).

## <a name="net-sample"></a>Exemplo de .NET

O código a seguir mostra como criar um ativo de entrada e usá-lo como entrada para o trabalho. A função CreateInputAsset executa as ações a seguir:

* Cria o ativo
* Obtém uma [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) gravável para o contêiner do ativo [no armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Carrega o arquivo para o contêiner no armazenamento usando a URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

O trecho de código a seguir criará um ativo de saída se ele ainda não existir:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

O trecho de código a seguir envia um trabalho de codificação:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Próximas etapas

[Crie uma entrada de trabalho de uma URL https](job-input-from-http-how-to.md).
