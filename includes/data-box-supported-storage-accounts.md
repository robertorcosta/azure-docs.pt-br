---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200303"
---
A seguir, há uma lista dos tipos de armazenamento com suporte para o dispositivo Data Box. Para obter uma lista completa de todos os diferentes tipos de contas de armazenamento e suas funcionalidades completas, confira [Types of storage accounts](/azure/storage/common/storage-account-overview#types-of-storage-accounts) (Tipos de contas de armazenamento).

Para ordens de importação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas*** |**Arquivos do Azure** |**Observações**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | Y |
| Uso geral v1 Standard  | S | S | Y | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v1 Premium  |  | Y| | |
| Uso geral v2 Standard  | S | S | Y | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento de blobs Standard |S | | |Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente. |

\*  *– Os dados carregados para blobs de páginas devem ser de 512 bytes alinhados, como VHDs.*

Para ordens de exportação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas*** |**Arquivos do Azure** |**Camadas de acesso compatíveis**|
| --- | --- | -- | -- | -- |
| Clássico Standard | Y | Y | Y | |
| Uso geral v1 Standard  | Y | Y | Y | Quente, frio|
| Uso geral v1 Premium  |  | Y| | |
| Uso geral v2 Standard  | Y | Y | Y | Quente, frio|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento de blobs Standard |S | | |Quente, frio |
| Armazenamento de blobs de blocos Premium |Y | | |Quente, frio |
| Armazenamento de blobs de páginas Premium | |Y | | |

> [!IMPORTANT]
> - Para contas de uso geral, Data Box não oferece suporte a tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2.
> - Data Box não dá suporte a blobs de acréscimo para armazenamento de BLOB e contas de armazenamento de blob de bloco.
> - Data Box não dá suporte a contas de armazenamento de arquivos premium.
> - Os dados carregados em blobs de páginas devem ser 512 bytes alinhados como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de arquivos e instantâneos de BLOB não são exportados.


