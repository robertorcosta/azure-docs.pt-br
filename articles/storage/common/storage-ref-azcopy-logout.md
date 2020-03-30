---
title: logout de azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando de logout azcopy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 538c54c338540059baa17b9818d444cc863b7309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034068"
---
# <a name="azcopy-logout"></a>azcopy logout

Registra o usuário e encerra o acesso aos recursos do Azure Storage.

## <a name="synopsis"></a>Sinopse

Este comando removerá todas as informações de login armazenadas em cache para o usuário atual.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostrar conteúdo de ajuda para o comando logout.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
