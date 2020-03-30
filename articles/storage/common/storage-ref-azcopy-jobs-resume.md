---
title: currículo de empregos em azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy jobs resume.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034151"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

Retoma o trabalho existente com o dIs de trabalho dado.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|--destination-sas string|Destino SAS do destino para dado JobId.|
|--excluir string|Filtro: Exclua essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por ';'.|
|-h, --help|Mostrar conteúdo de ajuda para o comando de currículo.|
|--incluir string|Filtro: inclua apenas essas transferências com falha ao retomar o trabalho. Os arquivos devem ser separados por ';'.|
|--seqüência de source-sas |fonte SAS da fonte para dado JobId.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
