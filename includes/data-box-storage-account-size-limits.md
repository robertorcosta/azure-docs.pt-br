---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469153"
---
Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, consulte [metas de escalabilidade e desempenho para o armazenamento Blob](../articles/storage/blobs/scalability-targets.md) e [metas de escalabilidade e desempenho do Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de blocos e blob de páginas                                            | 2 PB para EUA e Europa.<br>500 TB para todas as outras regiões, o que inclui o Reino Unido.  <br> Isso inclui dados de todas as fontes, incluindo o Data Box.|
| Arquivos do Azure                                                          | 5 TB por ação.<br> Todas as pastas *StorageAccount_AzureFiles* precisam seguir esse limite.       |
