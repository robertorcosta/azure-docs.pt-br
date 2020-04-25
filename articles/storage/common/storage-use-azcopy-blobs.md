---
title: Transferir dados de ou para o armazenamento de BLOBs do Azure usando AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo AzCopy que ajudam a criar contêineres, copiar arquivos e sincronizar diretórios entre sistemas de arquivos locais e contêineres.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137188"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferir dados com o armazenamento de BLOBs e AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre as contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento de BLOBs.

> [!TIP]
> Os exemplos neste artigo incluem argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd. exe). Se você estiver usando um shell de comando do Windows (cmd. exe), coloque argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

## <a name="get-started"></a>Introdução

Consulte o artigo [introdução ao AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e saiba mais sobre as maneiras como você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos neste artigo pressupõem que você autenticou sua identidade usando o `AzCopy login` comando. Em seguida, o AzCopy usa sua conta do Azure AD para autorizar o acesso aos dados no armazenamento de BLOBs.
>
> Se você preferir usar um token SAS para autorizar o acesso a dados BLOB, poderá acrescentar esse token à URL do recurso em cada comando AzCopy.
>
> Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contêiner

Você pode usar o comando [azcopy Make](storage-ref-azcopy-make.md) para criar um contêiner. Os exemplos nesta seção criam um contêiner chamado `mycontainer`.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exemplo** (namespace hierárquico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Para obter documentos de referência detalhados, consulte [Make azcopy](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Carregar um diretório
> * Carregar o conteúdo de um diretório 
> * Carregar arquivos específicos

> [!TIP]
> Você pode ajustar sua operação de carregamento usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Carregar arquivos como BLOBs de acréscimo ou BLOBs de página.|**--blob-Type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Carregue para uma camada de acesso específica (como a camada de arquivo morto).|**--Block-blob-camada**=\[nenhum\|arquivo\|frio\|quente\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar no caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'`, ou `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Carregar um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um contêiner de BLOB. O resultado é um diretório no contêiner com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Para copiar para um diretório dentro do contêiner, basta especificar o nome desse diretório na cadeia de caracteres de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se você especificar o nome de um diretório que não existe no contêiner, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Carregar o conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de arquivo individuais usando um ponto`;`-e-vírgula ().

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` arquivo. Você precisa incluir a `--recursive` opção para transferir todos os arquivos no `C:\myDirectory\photos` diretório.

Você também pode excluir arquivos usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin (`;`). 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` opções `--exclude-pattern` e aplicam-se somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida `–include-pattern` , use `*.txt` o e especifique para obter todos os arquivos de texto.

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando [azcopy Copy](storage-ref-azcopy-copy.md) para baixar BLOBs, diretórios e contêineres em seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Baixar um arquivo
> * Baixar um diretório
> * Baixar o conteúdo de um diretório
> * Baixar arquivos específicos

> [!TIP]
> Você pode ajustar sua operação de download usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Descompacte arquivos automaticamente.|**--descompactar**|
> |Especifique o quão detalhado você deseja que suas entradas de log relacionadas à cópia sejam.|**--**=\[\|\|informações\|de erro de aviso em nível de log nenhum\]|
> |Especifique se e como substituir os arquivos conflitantes e os BLOBs no destino.|**--substituir**=\[o\|prompt\|true\|false ifSourceNewer\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se o `Content-md5` valor da propriedade de um blob contiver um hash, AzCopy calculará um hash MD5 para os dados baixados e verificará se o hash MD5 armazenado `Content-md5` na Propriedade do blob corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará, a menos que você `--check-md5=NoCheck` substitua `--check-md5=LogOnly` esse comportamento acrescentando ou ao comando de cópia.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixar um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em um diretório chamado `C:\myDirectory\myBlobDirectory` que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixar o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contido usando o símbolo curinga (*).

> [!NOTE]
> Atualmente, esse cenário tem suporte apenas para contas que não têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Acrescente o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

### <a name="download-specific-files"></a>Baixar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe os nomes de arquivo individuais usando um semicolin`;`().

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, AzCopy transfere o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório e o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` arquivo. Você precisa incluir a `--recursive` opção para transferir todos os arquivos no `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório.

Você também pode excluir arquivos usando a `--exclude-path` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Usar caracteres curinga

Use o comando [azcopy Copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluam os caracteres curinga. Separe os nomes usando um semicolin (`;`).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir arquivos usando a `--exclude-pattern` opção. Para saber mais, consulte [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

As `--include-pattern` opções `--exclude-pattern` e aplicam-se somente a nomes de filename e não ao caminho.  Se você quiser copiar todos os arquivos de texto que existem em uma árvore de diretório, use a `–recursive` opção para obter a árvore de diretórios inteira e, em seguida `–include-pattern` , use `*.txt` o e especifique para obter todos os arquivos de texto.

## <a name="copy-blobs-between-storage-accounts"></a>Copiar o blob entre contas de armazenamento

Você pode usar AzCopy para copiar BLOBs para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados. 

O AzCopy usa [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)de [servidor para servidor](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , portanto, os dados são copiados diretamente entre os servidores de armazenamento. Essas operações de cópia não usam a largura de banda de rede do seu computador. Você pode aumentar a taxa de transferência dessas operações definindo o valor da variável `AZCOPY_CONCURRENCY_VALUE` de ambiente. Para saber mais, consulte [otimizar a taxa de transferência](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Esse cenário tem as seguintes limitações na versão atual.
>
> - Você precisa acrescentar um token SAS a cada URL de origem. Se você fornecer credenciais de autorização usando o Azure Active Directory (AD), poderá omitir o token SAS somente da URL de destino.
>-  As contas de armazenamento de blob de blocos Premium não dão suporte a camadas de acesso. Omita a camada de acesso de um blob da operação de cópia definindo o `s2s-preserve-access-tier` como `false` (por exemplo: `--s2s-preserve-access-tier=false`).

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copiar um blob para outra conta de armazenamento
> * Copiar um diretório para outra conta de armazenamento
> * Copiar um contêiner para outra conta de armazenamento
> * Copiar todos os contêineres, diretórios e arquivos para outra conta de armazenamento

Esses exemplos também funcionam com contas que têm um namespace hierárquico. O [acesso de vários protocolos no data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) permite que você use a mesma sintaxe de`blob.core.windows.net`URL () nessas contas.

> [!TIP]
> Você pode ajustar sua operação de cópia usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copie arquivos como BLOBs de acréscimo ou BLOBs de página.|**--blob-Type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Copie para uma camada de acesso específica (como a camada de arquivo morto).|**--Block-blob-camada**=\[nenhum\|arquivo\|frio\|quente\]|
> |Descompacte arquivos automaticamente.|**--descompactar**=\[gzip\|desinflado\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Copiar um blob para outra conta de armazenamento

Use a mesma sintaxe de URL`blob.core.windows.net`() para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copiar um diretório para outra conta de armazenamento

Use a mesma sintaxe de URL`blob.core.windows.net`() para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copiar um contêiner para outra conta de armazenamento

Use a mesma sintaxe de URL`blob.core.windows.net`() para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copiar todos os contêineres, diretórios e BLOBs para outra conta de armazenamento

Use a mesma sintaxe de URL`blob.core.windows.net`() para contas que têm um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um sistema de arquivos local com um contêiner de BLOB. Você também pode sincronizar contêineres e diretórios virtuais entre si. A sincronização é unidirecional. Em outras palavras, você escolhe quais desses dois pontos de extremidade são a origem e qual deles é o destino. A sincronização também usa APIs de servidor para servidor. Os exemplos apresentados nesta seção também funcionam com contas que têm um namespace hierárquico. 

> [!NOTE]
> A versão atual do AzCopy não sincroniza entre outras origens e destinos (por exemplo: armazenamento de arquivos Amazon Web Services ou AWS) S3 buckets).

O comando [Sync](storage-ref-azcopy-sync.md) compara os nomes de arquivo e os últimos carimbos de data/hora. Defina o `--delete-destination` sinalizador opcional como um valor de `true` ou `prompt` para excluir arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você definir o `--delete-destination` sinalizador como `true` AzCopy exclui arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes de AzCopy excluir um arquivo, defina `--delete-destination` o sinalizador `prompt`como.

> [!NOTE]
> Para evitar exclusões acidentais, certifique-se de habilitar o recurso de [exclusão reversível](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) antes de usar `--delete-destination=prompt|true` o sinalizador.

> [!TIP]
> Você pode ajustar a operação de sincronização usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Especifique como os hashes MD5 estritamente devem ser validados durante o download.|**--check-MD5**=\[NOCHECK\|\|FailIfDifferent\|logon FailIfDifferentOrMissing\]|
> |Excluir arquivos com base em um padrão.|**--Exclude-caminho**|
> |Especifique o quão detalhado você deseja que suas entradas de log relacionadas à sincronização sejam.|**--**=\[\|\|informações\|de erro de aviso em nível de log nenhum\]|
> 
> Para obter uma lista completa, consulte [Opções](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualizar um contêiner com alterações em um sistema de arquivos local

Nesse caso, o contêiner é o destino e o sistema de arquivos local é a origem. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualizar um sistema de arquivos local com alterações em um contêiner

Nesse caso, o sistema de arquivos local é o destino e o contêiner é a origem.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Atualizar um contêiner com alterações em outro contêiner

O primeiro contêiner que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualizar um diretório com alterações em um diretório em outro compartilhamento de arquivos

O primeiro diretório que aparece nesse comando é a origem. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um destes artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
