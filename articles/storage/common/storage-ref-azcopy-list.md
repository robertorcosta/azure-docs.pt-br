---
title: lista azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy list.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f02c1afadf18a7d3170eb178696487464e4a0bd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034089"
---
# <a name="azcopy-list"></a>azcopy list

Lista as entidades em um determinado recurso.

## <a name="synopsis"></a>Sinopse

Apenas os recipientes Blob são suportados na versão atual.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostrar conteúdo de ajuda para o comando da lista.|
|--máquina-legível|Lista tamanhos de arquivo em bytes.|
|--mega-unidades|Exibe unidades em ordens de 1000, não 1024.|
|--contagem de execução|Conta o número total de arquivos e seus tamanhos.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
