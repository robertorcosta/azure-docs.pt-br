---
title: Transferir dados para ou a partir do armazenamento Azure Blob usando o AzCopy v10 | Microsoft Docs
description: Este artigo contém uma coleção de comandos de exemplo do AzCopy que ajudam a criar contêineres, copiar arquivos e sincronizar diretórios entre sistemas de arquivos locais e contêineres.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 73685f124f93bb541f33b3b70727d90ce22b3cdd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263430"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Transferir dados com armazenamento AzCopy e Blob

O AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre contas de armazenamento. Este artigo contém comandos de exemplo que funcionam com o armazenamento Blob.

> [!TIP]
> Os exemplos deste artigo encerram os argumentos de caminho com aspas simples (''). Use aspas individuais em todos os shells de comando, exceto no Windows Command Shell (cmd.exe). Se você estiver usando um Shell de Comando do Windows (cmd.exe), encerre os argumentos de caminho com aspas duplas (""" em vez de aspas simples ('').

## <a name="get-started"></a>Introdução

Consulte o [artigo Get started with AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e aprender sobre as maneiras pelas quais você pode fornecer credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo supõem que você autenticasua `AzCopy login` identidade usando o comando. Em seguida, o AzCopy usa sua conta Azure AD para autorizar o acesso aos dados no armazenamento Blob.
>
> Se você preferir usar um token SAS para autorizar o acesso a dados blob, então você pode anexar esse token à URL de recurso em cada comando AzCopy.
>
> Por exemplo: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Criar um contêiner

Você pode usar o comando [azcopy make](storage-ref-azcopy-make.md) para criar um contêiner. Os exemplos nesta seção criam `mycontainer`um contêiner chamado .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Exemplo** (namespace hierárquico) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Para obter documentos de referência detalhados, consulte [a zcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando [azcopy copy](storage-ref-azcopy-copy.md) para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório 
> * Carregar arquivos específicos

> [!TIP]
> Você pode ajustar sua operação de upload usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Carregar arquivos como Apêndice Blobs ou Blobs de Página.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Faça upload para um nível de acesso específico (como o nível de arquivo).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archive\]|
> |Descompactar automaticamente arquivos.|**--descomprimir**=\[\|gzip esvaziar\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar do caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, ou .

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um recipiente blob. O resultado é um diretório no recipiente com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Para copiar para um diretório dentro do contêiner, basta especificar o nome desse diretório em sua seqüência de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Se você especificar o nome de um diretório que não existe no contêiner, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contendo usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Anexar o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe nomes de arquivos individuais`;`usando um ponto e vírgula ( ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, o AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` arquivo. Você precisa incluir `--recursive` a opção de `C:\myDirectory\photos` transferir todos os arquivos no diretório.

Você também pode excluir `--exclude-path` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use personagens curingas

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres curinga. Nomes separados usando um`;`semicolin ( . 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir `--exclude-pattern` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e se aplicam apenas a nomes de arquivos e não ao caminho.  Se você quiser copiar todos os arquivos de texto que `–recursive` existem em uma árvore de diretório, `–include-pattern` use `*.txt` a opção para obter toda a árvore do diretório e, em seguida, use o e especifique para obter todos os arquivos de texto.

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando [azcopy copy](storage-ref-azcopy-copy.md) para baixar blobs, diretórios e contêineres para o seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Baixar um arquivo
> * Baixe um diretório
> * Baixe o conteúdo de um diretório
> * Baixe arquivos específicos

> [!TIP]
> Você pode ajustar sua operação de download usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Descompactar automaticamente arquivos.|**--descomprimir**=\[\|gzip esvaziar\]|
> |Especifique o quão detalhadas você deseja que suas entradas de log relacionadas à cópia sejam.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE\]|
> |Especifique se e como substituir os arquivos e blobs conflitantes no destino.|**--substituir**=\[falso\|\|verdadeiro sePromptSourceNewer\|\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se `Content-md5` o valor de propriedade de uma bolha contiver um hash, o AzCopy calcula um hash MD5 para `Content-md5` dados baixados e verifica se o hash MD5 armazenado na propriedade da bolha corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará `--check-md5=NoCheck` a `--check-md5=LogOnly` menos que você anule esse comportamento anexando ou no comando copy.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixe um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em `C:\myDirectory\myBlobDirectory` um diretório chamado que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixe o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contendo usando o símbolo curinga (*).

> [!NOTE]
> Atualmente, esse cenário é suportado apenas para contas que não possuem um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Anexar o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

### <a name="download-specific-files"></a>Baixe arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe nomes de arquivos individuais`;`usando um semicolin ().

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

Neste exemplo, o AzCopy transfere o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` diretório e o `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` arquivo. Você precisa incluir `--recursive` a opção de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` transferir todos os arquivos no diretório.

Você também pode excluir `--exclude-path` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use personagens curingas

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres curinga. Nomes separados usando um`;`semicolin ( .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir `--exclude-pattern` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e se aplicam apenas a nomes de arquivos e não ao caminho.  Se você quiser copiar todos os arquivos de texto que `–recursive` existem em uma árvore de diretório, `–include-pattern` use `*.txt` a opção para obter toda a árvore do diretório e, em seguida, use o e especifique para obter todos os arquivos de texto.

## <a name="copy-blobs-between-storage-accounts"></a>Copiar o blob entre contas de armazenamento

Você pode usar o AzCopy para copiar blobs para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados. 

O AzCopy usa [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [de servidor para servidor,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) para que os dados sejam copiados diretamente entre servidores de armazenamento. Essas operações de cópia não usam a largura de banda da rede do seu computador. Você pode aumentar o throughput dessas operações `AZCOPY_CONCURRENCY_VALUE` definindo o valor da variável ambiente. Para saber mais, consulte [Optimize throughput](storage-use-azcopy-configure.md#optimize-throughput).

> [!NOTE]
> Este cenário tem as seguintes limitações na versão atual.
>
> - Você tem que anexar um token SAS a cada URL de origem. Se você fornecer credenciais de autorização usando o Azure Active Directory (AD), você pode omitir o token SAS apenas da URL de destino.
>-  As contas de armazenamento blob de bloco premium não suportam níveis de acesso. Omita a camada de acesso de uma `s2s-preserve-access-tier` bolha `false` da operação `--s2s-preserve-access-tier=false`de cópia definindo o a (Por exemplo: ).

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copie uma bolha para outra conta de armazenamento
> * Copie um diretório para outra conta de armazenamento
> * Copie um contêiner para outra conta de armazenamento
> * Copie todos os contêineres, diretórios e arquivos para outra conta de armazenamento

Esses exemplos também funcionam com contas que têm um namespace hierárquico. [O acesso multiprotocolo no Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) permite que`blob.core.windows.net`você use a mesma sintaxe de URL ( ) nessas contas.

> [!TIP]
> Você pode ajustar sua operação de cópia usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar arquivos como Apêndice Blobs ou Blobs de Página.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Copie para um nível de acesso específico (como o nível de arquivo).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archive\]|
> |Descompactar automaticamente arquivos.|**--descomprimir**=\[\|gzip esvaziar\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-blob-to-another-storage-account"></a>Copie uma bolha para outra conta de armazenamento

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copie um diretório para outra conta de armazenamento

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Copie um contêiner para outra conta de armazenamento

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Copie todos os contêineres, diretórios e blobs para outra conta de armazenamento

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um sistema de arquivos local com um recipiente blob. Você também pode sincronizar contêineres e diretórios virtuais entre si. A sincronização é unidirecional. Em outras palavras, você escolhe qual desses dois pontos finais é a fonte e qual é o destino. A sincronização também usa APIs de servidor para servidor. Os exemplos apresentados nesta seção também funcionam com contas que possuem um namespace hierárquico. 

> [!NOTE]
> A versão atual do AzCopy não sincroniza entre outras fontes e destinos (Por exemplo: armazenamento de arquivos ou baldes S3 do Amazon Web Services (AWS).

O comando [sync](storage-ref-azcopy-sync.md) compara nomes de arquivos e últimos carimbos de tempo modificados. Defina `--delete-destination` o sinalizador opcional `true` `prompt` como um valor ou exclua arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você `--delete-destination` definir `true` o sinalizador como AzCopy, excluirá arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes que `--delete-destination` o `prompt`AzCopy exclua um arquivo, defina o sinalizador para .

> [!NOTE]
> Para evitar exclusões acidentais, certifique-se de `--delete-destination=prompt|true` ativar o recurso [de exclusão suave](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) antes de usar o sinalizador.

> [!TIP]
> Você pode ajustar sua operação de sincronização usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Especifique como os hashes estritamente MD5 devem ser validados ao baixar.|**--check-md5**=\[NoCheck\|\|LogOnly\|FailIfDifferent FailIfDifferentDifferentOrMissing\]|
> |Exclua arquivos com base em um padrão.|**--exclua-caminho**|
> |Especifique o quão detalhadas você deseja que suas entradas de log relacionadas à sincronização sejam.|**--log-level**=\[\|WARNING\|\|ERROR INFO NONE\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-sync.md#options).

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Atualize um contêiner com alterações em um sistema de arquivos local

Neste caso, o contêiner é o destino, e o sistema de arquivos local é a fonte. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Atualize um sistema de arquivos local com alterações em um contêiner

Neste caso, o sistema de arquivos local é o destino, e o contêiner é a fonte.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Exemplo** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Atualize um contêiner com alterações em outro contêiner

O primeiro contêiner que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualize um diretório com alterações em um diretório em outro compartilhamento de arquivos

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um desses artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Tutorial: Migrar os dados locais para o armazenamento em nuvem usando o AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
