---
title: Ativos
titleSuffix: Azure Media Services
description: Saiba quais são os ativos e como eles são usados pelo Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087916"
---
# <a name="assets-in-azure-media-services-v3"></a>Ativos no Azure Media Services v3

No Azure Media Services, um [Ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito central. É onde você insere mídia (por exemplo, através de upload ou live ingest), mídia de saída (a partir de uma saída de trabalho) e publica mídia de (para streaming). 

Um Ativo é mapeado para um contêiner blob na [conta Azure Storage](storage-account-concept.md) e os arquivos no Ativo são armazenados como blobs de bloco nesse contêiner. Os ativos contêm informações sobre arquivos digitais armazenados no Azure Storage (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legendas fechadas).

Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **O** armazenamento de arquivos é adequado para arquivar arquivos-fonte quando não for mais necessário (por exemplo, depois de codificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. As bolhas no recipiente de saída que você deseja associar a um Ativo e usar para transmitir ou analisar seu conteúdo devem existir em um **nível de** armazenamento Hot ou **Cool.**

## <a name="naming"></a>Nomenclatura 

### <a name="assets"></a>Ativos

Os nomes dos ativos devem ser únicos. Os nomes de recursos v3 do Media Services (por exemplo, Ativos, Empregos, Transformações) estão sujeitos às restrições de nomeação do Azure Resource Manager. Para obter mais informações, consulte [convenções de nomeação](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobs

Os nomes de arquivos/blobs dentro de um ativo devem seguir os requisitos de [nome blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os [requisitos de nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). A razão para esses requisitos é que os arquivos podem ser copiados do armazenamento blob para um disco NTFS local para processamento.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar ativos em Serviços de Mídia](manage-asset-concept.md)

## <a name="see-also"></a>Confira também

[Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
