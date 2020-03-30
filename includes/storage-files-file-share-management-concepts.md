---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597762"
---
Os compartilhamentos de arquivos do Azure são implantados em contas de *armazenamento,* que são objetos de alto nível que representam um pool compartilhado de armazenamento. Esse pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivos, bem como outros recursos de armazenamento, como contêineres blob, filas ou tabelas. Todos os recursos de armazenamento que são implantados em uma conta de armazenamento compartilham os limites aplicáveis a essa conta de armazenamento. Para ver os limites atuais de uma conta de armazenamento, consulte metas [de escalabilidade e desempenho do Azure Files](../articles/storage/files/storage-files-scale-targets.md).

Existem dois tipos principais de contas de armazenamento que você usará para implantações de arquivos do Azure: 
- **Contas de armazenamento versão 2 (GPv2): As contas**de armazenamento GPv2 permitem que você implante compartilhamentos de arquivos Azure em hardware baseado em disco padrão/rígido (baseado em HDD). Além de armazenar compartilhamentos de arquivos Do Zure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como recipientes blob, filas ou tabelas. 
- **Contas de armazenamento de armazenamento de arquivos**: As contas de armazenamento de armazenamento de arquivos permitem que você implante compartilhamentos de arquivos do Azure em hardware baseado em disco premium/sólido (baseado em SSD). As contas de armazenamento de arquivos só podem ser usadas para armazenar compartilhamentos de arquivos do Azure; nenhum outro recurso de armazenamento (recipientes blob, filas, tabelas, etc.) pode ser implantado em uma conta de armazenamento de arquivos.

Existem vários outros tipos de conta de armazenamento que você pode encontrar no portal Dozure, PowerShell ou CLI. Dois tipos de conta de armazenamento, BlockBlobStorage e BlobStorage storage, não podem conter compartilhamentos de arquivos Azure. Os outros dois tipos de conta de armazenamento que você pode ver são a versão 1 (GPv1) e contas de armazenamento clássicas, ambas podem conter compartilhamentos de arquivos Do Zure. Embora o GPv1 e as contas de armazenamento clássicas possam conter compartilhamentos de arquivos Do Zure, a maioria dos novos recursos do Azure Files estão disponíveis apenas em contas de armazenamento GPv2 e FileStorage. Por isso, recomendamos usar apenas contas de armazenamento GPv2 e FileStorage para novas implantações e atualizar contas de armazenamento GPv1 e clássicas se elas já existirem em seu ambiente.  