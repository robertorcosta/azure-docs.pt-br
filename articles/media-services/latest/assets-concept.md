---
title: Ativos
titleSuffix: Azure Media Services
description: Saiba quais são os ativos e como eles são usados pelos serviços de mídia do Azure.
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
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087916"
---
# <a name="assets-in-azure-media-services-v3"></a>Ativos nos serviços de mídia do Azure v3

Nos serviços de mídia do Azure, um [ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito básico. É onde você insere a mídia (por exemplo, por meio de carregamento ou ingestão dinâmica), mídia de saída (de uma saída de trabalho) e publicação de mídia de (para streaming). 

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Microsoft Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blob de blocos nesse contêiner. Os ativos contêm informações sobre arquivos digitais armazenados no armazenamento do Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada).

Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento de **arquivo** é adequado para arquivar arquivos de origem quando não for mais necessário (por exemplo, depois que eles tiverem sido codificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. Os BLOBs no contêiner de saída que você deseja associar a um ativo e usados para transmitir ou analisar seu conteúdo devem existir em uma camada de armazenamento **quente** ou **fria** .

## <a name="naming"></a>Nomenclatura 

### <a name="assets"></a>Ativos

Os nomes dos ativos devem ser exclusivos. Os nomes de recursos dos serviços de mídia v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a Azure Resource Manager restrições de nomenclatura. Para obter mais informações, consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobs

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome do blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de nome do [NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de BLOBs para um disco NTFS local para processamento.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[Gerenciar ativos nos serviços de mídia](manage-asset-concept.md)

## <a name="see-also"></a>Confira também

[Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
