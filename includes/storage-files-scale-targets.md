---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9c130fb2e032a24384f52f11957632319093e1e3
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226393"
---
| Resource | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | Sem mínimo; pré-pago | 100 GiB; provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 100 TiB *, 5 TiB | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| IOPS máximo por compartilhamento | 10.000 IOPS *, 1.000 IOPS | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivos | 5 | 5 |
| Taxa de transferência de destino para um único compartilhamento de arquivos | até 300 MiB/s *, até 60 MiB/s,  | Consulte valores de entrada e saída do compartilhamento de arquivos Premium|
| Egresso máxima de um único compartilhamento de arquivo | Consulte taxa de transferência de destino de compartilhamento de arquivos padrão | Até 6.204 MiB/s |
| Máximo de entrada para um único compartilhamento de arquivos | Consulte taxa de transferência de destino de compartilhamento de arquivos padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo ou diretório | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |

\*O padrão em compartilhamentos de arquivos padrão é 5 TiB, consulte [habilitar e criar grandes compartilhamentos de arquivos](../articles/storage/files/storage-files-how-to-create-large-file-share.md) para obter os detalhes sobre como aumentar os compartilhamentos de arquivos padrão escalando verticalmente para 100 Tib.
