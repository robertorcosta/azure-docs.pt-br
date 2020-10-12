---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: acaebcea59e765f5544f1bfbd692c6508f66a84a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025145"
---
Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [escalabilidade e metas de desempenho para armazenamento de BLOBs](../articles/storage/blobs/scalability-targets.md) e [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | O limite máximo é o mesmo que o [limite de armazenamento definido para a assinatura do Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits) e inclui dados de todas as fontes, incluindo data box. |
| Arquivos do Azure                                                          | O Data Box dá suporte a 100TiB (compartilhamentos de arquivos grandes), se estiver habilitado, antes da criação da ordem de Data Box. <br> Se não estiver habilitado antes da criação do pedido, o tamanho máximo do compartilhamento de arquivos com suporte é 5 TiB. <br> Ainda não há suporte para compartilhamentos de arquivos premium.<br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite. <br> Para obter mais informações, consulte [habilitar e criar compartilhamentos de arquivos grandes](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
