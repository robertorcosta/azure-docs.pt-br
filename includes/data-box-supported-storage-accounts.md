---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028135"
---
A seguir, há uma lista dos tipos de armazenamento com suporte para o dispositivo Data Box. Para obter uma lista completa de todos os diferentes tipos de contas de armazenamento e suas funcionalidades completas, confira [Types of storage accounts](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts) (Tipos de contas de armazenamento).

Para ordens de importação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Observações**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | S |
| Uso geral v1 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | S | S | Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente.|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento de blobs Standard |S | | |Há suporte para a camada de acesso esporádico e a camada de armazenamento frequente. |

\*  *– Os dados carregados para blobs de páginas devem ser de 512 bytes alinhados, como VHDs.*

Para ordens de exportação, a tabela a seguir mostra as contas de armazenamento com suporte.

| **Conta de armazenamento/Tipos de armazenamento com suporte** | **Blob de blocos** |**Blob de páginas** _ |_ *Arquivos do Azure** |**Camadas de acesso compatíveis**|
| --- | --- | -- | -- | -- |
| Clássico Standard | S | S | S | |
| Uso geral v1 Standard  | S | S | S | Quente, frio|
| Uso geral v1 Premium  |  | S| | |
| Uso geral v2 Standard  | S | S | S | Quente, frio|
| Uso geral v2 Premium  |  |S | | |
| Armazenamento de blobs Standard |S | | |Quente, frio |
| Armazenamento de blobs de blocos Premium |S | | |Quente, frio |
| Armazenamento de blobs de páginas Premium | |S | | |

> [!IMPORTANT]
> - Para contas de uso geral, Data Box não oferece suporte a tipos de armazenamento de fila, tabela e disco para ordens de importação. Para ordens de exportação, Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas de uso geral.
> - Data Box não dá suporte a blobs de acréscimo para armazenamento de BLOB e contas de armazenamento de blob de bloco.
> - Data Box não dá suporte a contas de armazenamento de arquivos premium.
> - Os dados carregados em blobs de páginas devem ser 512 bytes alinhados como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de arquivos e instantâneos de BLOB não são exportados.