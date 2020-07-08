---
title: azcopy remover | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy remove.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 05/04/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ab085b9a41120a9f56c1c2e39a89def8c3893747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221077"
---
# <a name="azcopy-remove"></a>azcopy remove

Exclua BLOBs ou arquivos de uma conta de armazenamento do Azure.

## <a name="synopsis"></a>Sinopse

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com o armazenamento de BLOBs e AzCopy](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Exemplos

Remova um único blob com SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Remova um diretório virtual inteiro com uma SAS:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Remova somente os BLOBs superiores dentro de um diretório virtual, mas não seus subdiretórios:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Remova um subconjunto de BLOBs em um diretório virtual (por exemplo: somente arquivos jpg e PDF, ou se o nome do blob for "exactname"):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include-pattern="*.jpg;*.pdf;exactName"
```

Remova um diretório virtual inteiro, mas exclua determinados BLOBs do escopo (por exemplo: cada blob que começa com foo ou termina com bar):

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude-pattern="foo*;*bar"
```

Remova BLOBs específicos e diretórios virtuais colocando seus caminhos relativos (não codificados em URL) em um arquivo:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

Remova um único arquivo de uma conta de armazenamento de BLOBs que tenha um namespace hierárquico (include/Exclude sem suporte).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Remover um único diretório de uma conta de armazenamento de BLOBs que tem um namespace hierárquico (include/Exclude sem suporte):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Opções

**--delete –** cadeia de caracteres de instantâneos por padrão, a operação de exclusão falhará se um blob tiver instantâneos. Especifique ' include ' para remover o blob raiz e todos os seus instantâneos; Alternativamente, especifique ' only ' para remover apenas os instantâneos, mas manter o blob raiz.

**--Exclude-String de caminho** exclui esses caminhos ao remover. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo do caminho relativo. Por exemplo: MyFolder; MyFolder/subDirName/file.pdf.

**--Exclude-Pattern** cadeia de caracteres excluir arquivos onde o nome corresponde à lista padrão. Por exemplo: *. jpg;*. PDF; exatoname

**--Force-If-somente leitura**    Ao excluir um arquivo ou pasta de arquivos do Azure, force a exclusão para funcionar mesmo que o objeto existente tenha seu atributo somente leitura definido

**-h,-** ajuda ajuda para remover

--a cadeia de caracteres **include-path** inclui apenas esses caminhos ao remover. Essa opção não dá suporte a caracteres curinga (*). Verifica o prefixo do caminho relativo. Por exemplo: MyFolder; MyFolder/subDirName/file.pdf

**--include-** a cadeia de caracteres de padrão inclui apenas arquivos em que o nome corresponde à lista de padrões. Por exemplo: *. jpg;*. PDF; exatoname

**--a cadeia de caracteres de lista de arquivos** define o local de um arquivo que contém a lista de arquivos e diretórios a serem excluídos. Os caminhos relativos devem ser delimitados por quebras de linha e os caminhos não devem ser codificados em URL.

**--** cadeia de caracteres de nível de log defina o detalhamento de log para o arquivo de log. Os níveis disponíveis incluem: informações (todas as solicitações/respostas), aviso (respostas lentas), erro (somente solicitações com falha) e nenhum (nenhum log de saída). (padrão ' INFO ') (padrão "INFO")

**--recursivo**                Examine os subdiretórios recursivamente ao sincronizar entre diretórios.

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos pai

|Opção|Descrição|
|---|---|
|--Cap-Mbps UInt32|Limita a taxa de transferência, em megabits por segundo. A taxa de transferência por minuto pode variar um pouco a partir do limite. Se essa opção for definida como zero ou for omitida, a taxa de transferência não será limitada.|
|--Cadeia de caracteres de tipo de saída|Formato da saída do comando. As opções incluem: Text, JSON. O valor padrão é "text".|
|--Cadeia de caracteres de sufixos confiáveis da Microsoft   |Especifica sufixos de domínio adicionais onde Azure Active Directory tokens de logon podem ser enviados.  O padrão é '*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Todos listados aqui são adicionados ao padrão. Por segurança, você só deve colocar Microsoft Azure domínios aqui. Separe várias entradas com ponto e vírgula.|

## <a name="see-also"></a>Consulte também

- [azcopy](storage-ref-azcopy.md)
