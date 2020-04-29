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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77597762"
---
Os compartilhamentos de arquivos do Azure são implantados em *contas de armazenamento*, que são objetos de nível superior que representam um pool de armazenamento compartilhado. Esse pool de armazenamento pode ser usado para implantar vários compartilhamentos de arquivos, bem como outros recursos de armazenamento, como contêineres de BLOB, filas ou tabelas. Todos os recursos de armazenamento implantados em uma conta de armazenamento compartilham os limites que se aplicam a essa conta de armazenamento. Para ver os limites atuais de uma conta de armazenamento, consulte [metas de desempenho e escalabilidade de arquivos do Azure](../articles/storage/files/storage-files-scale-targets.md).

Há dois tipos principais de contas de armazenamento que serão usadas para implantações de arquivos do Azure: 
- **Contas de armazenamento de uso geral versão 2 (GPv2)**: as contas de armazenamento do GPv2 permitem que você implante compartilhamentos de arquivos do Azure em hardware padrão/baseado em disco rígido (baseado em HDD). Além de armazenar compartilhamentos de arquivos do Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, como contêineres de BLOB, filas ou tabelas. 
- **Contas de armazenamento**de armazenamento em FileStorage: as contas de armazenamento de armazenamento de arquivo permitem que você implante compartilhamentos de arquivos do Azure em hardware Premium/de estado sólido baseado em disco (SSD). Contas de armazenamento de arquivo só podem ser usadas para armazenar compartilhamentos de arquivos do Azure; nenhum outro recurso de armazenamento (contêineres de BLOB, filas, tabelas, etc.) pode ser implantado em uma conta de armazenamento de File.

Há vários outros tipos de conta de armazenamento que podem ser incluídos no portal do Azure, no PowerShell ou na CLI. Dois tipos de conta de armazenamento, contas de armazenamento BlockBlobStorage e BlobStorage, não podem conter compartilhamentos de arquivos do Azure. Os outros dois tipos de conta de armazenamento que você pode ver são GPv1 (uso geral versão 1) e contas de armazenamento clássicas, ambos podem conter compartilhamentos de arquivos do Azure. Embora as contas de armazenamento GPv1 e clássico possam conter compartilhamentos de arquivos do Azure, a maioria dos novos recursos dos arquivos do Azure está disponível apenas nas contas de armazenamento GPv2 e FileStorage. Portanto, recomendamos que você use apenas contas de armazenamento GPv2 e FileStorage para novas implantações e atualize as contas de armazenamento GPv1 e clássico se elas já existirem em seu ambiente.  