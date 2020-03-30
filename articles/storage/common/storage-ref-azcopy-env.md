---
title: azcopy env | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy env.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033754"
---
# <a name="azcopy-env"></a>azcopy env

Mostra as variáveis de ambiente que podem configurar o comportamento do AzCopy.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no histórico da linha de comando. Considere limpar variáveis que contenham credenciais do seu histórico de linha de comando. Para evitar que variáveis apareçam em seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável ambiente.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="options"></a>Opções

|Opção|Descrição|
|--|--|
|-h, --help|Mostra conteúdo de ajuda para o comando env. |
|--show-sensitive|Mostra variáveis de ambiente sensíveis/secretas.|

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
