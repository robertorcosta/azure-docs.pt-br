---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76901419"
---
| Recurso | Compartilhamentos de arquivos padrão | Ações de arquivos premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | No mínimo; pagar como você vai | 100 GiB; Provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 100 TiB*, 5 TiB | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| IOPS máximo por ação | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivos | 5 | 5 |
| Throughput de destino para um único compartilhamento de arquivo | até 300 MiB/seg*, até 60 MiB/seg,  | Veja os valores de participação de arquivos premium e egressos|
| Saída máxima para um único compartilhamento de arquivos | Consulte o throughput de destino de compartilhamento de arquivos padrão | Até 6.204 MiB/s |
| A ingestão máxima para um único compartilhamento de arquivos | Consulte o throughput de destino de compartilhamento de arquivos padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |

\*Disponível na maioria das regiões, consulte [disponibilidade regional](../articles/storage/files/storage-files-planning.md#regional-availability) para os detalhes sobre as regiões disponíveis.
