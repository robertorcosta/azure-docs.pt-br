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
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ce32343faefbcf2484ec0b1b39f752654a2d8514
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303606"
---
# <a name="assets-in-azure-media-services"></a>Ativos nos serviços de mídia do Azure

Nos serviços de mídia do Azure, um [ativo](https://docs.microsoft.com/rest/api/media/assets) é um conceito básico. É onde você insere a mídia (por exemplo, por meio de carregamento ou ingestão dinâmica), mídia de saída (de uma saída de trabalho) e publicação de mídia de (para streaming). 

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Microsoft Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blob de blocos nesse contêiner. Os ativos contêm informações sobre arquivos digitais armazenados no armazenamento do Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada).

Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento de **arquivo** é adequado para arquivar arquivos de origem quando não for mais necessário (por exemplo, depois que eles tiverem sido codificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. Os BLOBs no contêiner de saída que você deseja associar a um ativo e usados para transmitir ou analisar seu conteúdo devem existir em uma camada de armazenamento **quente** ou **fria** .

## <a name="naming"></a>Nomenclatura 

### <a name="assets"></a>Ativos

Os nomes dos ativos devem ser exclusivos. Os nomes de recursos dos serviços de mídia v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a Azure Resource Manager restrições de nomenclatura. Para obter mais informações, consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobs

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome do blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de nome do [NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de BLOBs para um disco NTFS local para processamento.

## <a name="map-v3-asset-properties-to-v2"></a>Mapear Propriedades de ativos V3 para v2

A tabela a seguir mostra como as propriedades do [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)em v3 são mapeadas para as propriedades do ativo na v2.

|Propriedades v3|Propriedades de v2|
|---|---|
|`id`-(exclusivo) o caminho de Azure Resource Manager completo, consulte os exemplos no [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`-(exclusivo) consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-o valor (exclusivo) começa com o prefixo de `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opções de criação)|
|`type`||

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia:

|Opção de criptografia|Descrição|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos serviços de mídia.|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do lado do servidor oferecida pelo armazenamento do Azure, chave gerenciada pelo Azure ou por cliente.|Suportado|Suportado|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do lado do cliente oferecida pelo armazenamento do Azure, chave gerenciada por cliente no Key Vault.|Sem suporte|Sem suporte|

<sup>1</sup> enquanto os serviços de mídia dão suporte ao tratamento de conteúdo em claro/sem qualquer forma de criptografia, fazer isso não é recomendado.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. O que significa v3 funciona com ativos criptografados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

[Gerenciar ativos nos serviços de mídia](manage-asset-concept.md)

## <a name="see-also"></a>Consulte também

[Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
