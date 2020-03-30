---
title: azcópia remover | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033991"
---
# <a name="azcopy-remove"></a>azcopy remove

Exclua blobs ou arquivos de uma conta de armazenamento do Azure.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Remova uma única bolha com SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remova um diretório virtual inteiro com um SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remova apenas as bolhas superiores dentro de um diretório virtual, mas não seus subdiretórios:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remova um subconjunto de blobs em um diretório virtual (Por exemplo: apenas arquivos jpg e pdf, ou se o nome blob é "exactName"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

Remova um diretório virtual inteiro, mas exclua certas bolhas do escopo (Por exemplo: cada bolha que começa com foo ou termina com barra):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Remova blobs e diretórios virtuais específicos colocando seus caminhos relativos (NÃO codificados por URL) em um arquivo:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Remova um único arquivo de uma conta blob Storage que tenha um namespace hierárquico (include/exclua não suportado).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remova um único diretório de uma conta blob Storage que tenha um namespace hierárquico (include/exclua não suportado):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opções

**--exclua-caminho string**      Exclua esses caminhos ao remover. Esta opção não suporta caracteres curinga (*). Verifica o prefixo de caminho relativo. Por exemplo: myFolder;myFolder/subDirName/file.pdf.

**--excluir-padrão de** seqüência Desexcluir arquivos onde o nome corresponde à lista de padrões. Por exemplo: *.jpg;*. pdf;exactName

**-h,-- ajudar a** remover

**--incluir-caminho** de seqüência Inclua apenas esses caminhos ao remover. Esta opção não suporta caracteres curinga (*). Verifica o prefixo de caminho relativo. Por exemplo: myFolder;myFolder/subDirName/file.pdf

**--incluir-seqüência de padrões** Inclua apenas arquivos onde o nome corresponda à lista de padrões. Por exemplo: *.jpg;*. pdf;exactName

**a seqüência de arquivos** define a localização de um arquivo que contém a lista de arquivos e diretórios a serem excluídos. Os caminhos relativos devem ser delimitados por quebras de linha, e os caminhos NÃO devem ser codificados por URL.

**cadeia de nível de log** Defina a verbosidade de log para o arquivo log. Os níveis disponíveis incluem: INFO(todas as solicitações/respostas), AVISO (respostas lentas), ERRO (apenas solicitações com falha) e NONE (sem registros de saída). ('INFO') padrão (padrão "INFO")

**--recursivo**                Olhe para subdiretórios recursivamente ao sincronizar entre diretórios.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcópia](storage-ref-azcopy.md)
