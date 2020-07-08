---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378400"
---
Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [escalabilidade e metas de desempenho para armazenamento de BLOBs](../articles/storage/blobs/scalability-targets.md) e [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a assinatura do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) e inclui dados de todas as fontes, incluindo data box.|
| Arquivos do Azure                                                          | O tamanho máximo de compartilhamentos de arquivos padrão é de 5 TB <br> O tamanho máximo dos compartilhamentos de arquivos Premium é 100TiB por compartilhamento.<br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite.       |
