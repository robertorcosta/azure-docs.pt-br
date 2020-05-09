---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736970"
---
Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [escalabilidade e metas de desempenho para armazenamento de BLOBs](../articles/storage/blobs/scalability-targets.md) e [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | 2 PB para nós e Europa.<br>500 TB para todas as outras regiões, que inclui o Reino Unido.  <br> Isso inclui dados de todas as fontes, incluindo o Data Box.|
| Arquivos do Azure                                                          | Tamanho máximo de compartilhamentos de arquivos padrão 100TiB *, 5 TB, arquivos Premium compartilhamentos 100TiB por compartilhamento.<br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite.       |
