---
title: azcopy jobs show | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy jobs show.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034131"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

Mostra informações detalhadas para a determinada carteira de trabalho.

## <a name="synopsis"></a>Sinopse

Se apenas a carteira de trabalho for fornecida sem bandeira, então o resumo do progresso do trabalho é devolvido.

As contagens de byte e porcentagem completa que aparece quando você executa este comando refletem apenas arquivos que são concluídos no trabalho. Eles não refletem arquivos parcialmente concluídos.

Se `with-status` a bandeira for definida, a lista de transferências no trabalho com o valor dado será mostrada.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostra conteúdo de ajuda para o comando do show.|
|--com-status string|Apenas liste as transferências de trabalho com este status, valores disponíveis: Iniciado, Sucesso, Falha|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcopy jobs](storage-ref-azcopy-jobs.md)
