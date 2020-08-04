---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: ae431d27af4a1717a32e348ba852f9b2b9fe9331
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87451121"
---
| Recurso | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivo | Sem mínimo; pagamento conforme o uso | 100 GiB; provisionado |
| Tamanho máximo de um compartilhamento de arquivo | 100 TiB*, 5 TiB | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivo | 1 TiB | 4 TiB |
| Número máximo de arquivos em um compartilhamento de arquivo | Sem limite | Sem limite |
| IOPS máxima por compartilhamento | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivo | 5 | 5 |
| Taxa de transferência de destino para um compartilhamento de arquivo único | até 300 MiB/s*, até 60 MiB/s,  | Consulte os valores de entrada e de saída do compartilhamento de arquivo Premium|
| Saída máxima para um compartilhamento de arquivo | Consulte a taxa de transferência de destino do compartilhamento de arquivo padrão | Até 6.204 MiB/s |
| Entrada máxima para um compartilhamento de arquivo | Consulte a taxa de transferência de destino do compartilhamento de arquivo padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo ou diretório | 2\.000 identificadores abertos | 2\.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 2\.048 caracteres | 2\.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |

\* O padrão em compartilhamentos de arquivo padrão é 5 TiB. Confira [Habilitar e criar compartilhamentos de arquivos grandes](../articles/storage/files/storage-files-how-to-create-large-file-share.md) para ver detalhes de como aumentar a escala dos compartilhamentos de arquivos padrão para até 100 TiB.
