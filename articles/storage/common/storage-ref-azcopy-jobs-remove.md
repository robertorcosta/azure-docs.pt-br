---
title: empregos de azcopy removido | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy jobs remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034168"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Remova todos os arquivos associados ao dado iD de trabalho.

> [!NOTE] 
> Você pode personalizar o local onde os arquivos de log e plano são salvos. Consulte o comando [azcopy env](storage-ref-azcopy-env.md) para saber mais.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Opções

**-h, -- ajuda**                Ajuda para remover.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

**--cap-mbps uint32**      Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.

**--cadeia de** string tipo de saída Formato da saída do comando. As opções incluem: texto, json. O valor padrão é 'texto'. (padrão "texto")

## <a name="see-also"></a>Confira também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
