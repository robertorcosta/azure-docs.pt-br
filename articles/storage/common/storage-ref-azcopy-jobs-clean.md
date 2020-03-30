---
title: trabalhos de azcopy limpos | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy jobs clean.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033716"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

Remova todos os arquivos de log e plano para todos os trabalhos

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>Opções

**-h, -- ajuda**                Ajuda para limpar.

**--com-status** string Apenas remova os trabalhos com este status, valores disponíveis: Cancelado, Concluído, Falhou, InProgress, Todos (padrão "Todos")

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

**--cap-mbps uint32**      Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.

**--cadeia de** string tipo de saída Formato da saída do comando. As opções incluem: texto, json. O valor padrão é 'texto'. (padrão "texto")

## <a name="see-also"></a>Confira também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
