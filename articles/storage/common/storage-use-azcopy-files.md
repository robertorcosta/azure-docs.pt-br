---
title: Transferir dados para ou a partir de arquivos Azure usando a ZCopy v10 | Microsoft Docs
description: Transferir dados com o AzCopy e o armazenamento de arquivos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 59f5733009424c60f2b9c48e68d70bbc29ad7095
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263362"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>Transferir dados com o AzCopy e o Armazenamento de Arquivos 

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos para ou de uma conta de armazenamento. Este artigo contém comandos de exemplo que funcionam com arquivos azure.

Antes de começar, consulte o artigo [Get started with AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e familiarizar-se com a ferramenta.

> [!TIP]
> Os exemplos deste artigo encerram os argumentos de caminho com aspas simples (''). Use aspas individuais em todos os shells de comando, exceto no Windows Command Shell (cmd.exe). Se você estiver usando um Shell de Comando do Windows (cmd.exe), encerre os argumentos de caminho com aspas duplas (""" em vez de aspas simples ('').

## <a name="create-file-shares"></a>Criar compartilhamentos de arquivos

Você pode usar o comando [azcopy make](storage-ref-azcopy-make.md) para criar um compartilhamento de arquivos. O exemplo nesta seção cria `myfileshare`um compartilhamento de arquivos chamado .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **Exemplo** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Para obter documentos de referência detalhados, consulte [a zcopy make](storage-ref-azcopy-make.md).

## <a name="upload-files"></a>Carregar arquivos

Você pode usar o comando [azcopy copy](storage-ref-azcopy-copy.md) para carregar arquivos e diretórios do seu computador local.

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Carregar um arquivo
> * Faça upload de um diretório
> * Faça upload do conteúdo de um diretório
> * Faça upload de um arquivo específico

> [!TIP]
> Você pode ajustar sua operação de upload usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar listas de controle de acesso (ACLs) junto com os arquivos.|**--preservar-smb-permissões verdadeiras**=\[\|falsas\]|
> |Copie as informações de propriedade do SMB junto com os arquivos.|**--preserve-smb-info**=\[\|verdadeiro falso\]|
> |Carregar arquivos como Apêndice Blobs ou Blobs de Página.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Faça upload para um nível de acesso específico (como o nível de arquivo).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archive\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> O AzCopy não calcula e armazena automaticamente o código de hash md5 do arquivo. Se você quiser que o AzCopy `--put-md5` faça isso, então anexar á bandeira a cada comando de cópia. Dessa forma, quando o arquivo é baixado, o AzCopy calcula um hash MD5 para dados baixados `Content-md5` e verifica se o hash MD5 armazenado na propriedade do arquivo corresponde ao hash calculado.

### <a name="upload-a-file"></a>Carregar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

Você também pode carregar um arquivo usando um símbolo curinga (*) em qualquer lugar do caminho do arquivo ou nome do arquivo. Por exemplo: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`, ou .

### <a name="upload-a-directory"></a>Faça upload de um diretório

Este exemplo copia um diretório (e todos os arquivos nesse diretório) para um compartilhamento de arquivos. O resultado é um diretório no compartilhamento de arquivos com o mesmo nome.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Para copiar para um diretório dentro do compartilhamento de arquivos, basta especificar o nome desse diretório em sua seqüência de comando.

|    |     |
|--------|-----------|
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

Se você especificar o nome de um diretório que não existe no compartilhamento de arquivos, o AzCopy criará um novo diretório com esse nome.

### <a name="upload-the-contents-of-a-directory"></a>Faça upload do conteúdo de um diretório

Você pode carregar o conteúdo de um diretório sem copiar o próprio diretório contendo usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> Anexar o `--recursive` sinalizador para carregar arquivos em todos os subdiretórios.

### <a name="upload-specific-files"></a>Carregar arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe nomes de arquivos individuais`;`usando um ponto e vírgula ( ).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

Neste exemplo, o AzCopy transfere o `C:\myDirectory\photos` diretório e o `C:\myDirectory\documents\myFile.txt` arquivo. Você precisa incluir `--recursive` a opção de `C:\myDirectory\photos` transferir todos os arquivos no diretório.

Você também pode excluir `--exclude-path` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use personagens curingas

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres curinga. Nomes separados usando um`;`semicolin ( .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir `--exclude-pattern` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e se aplicam apenas a nomes de arquivos e não ao caminho.  Se você quiser copiar todos os arquivos de texto que `–recursive` existem em uma árvore de diretório, `–include-pattern` use `*.txt` a opção para obter toda a árvore do diretório e, em seguida, use o e especifique para obter todos os arquivos de texto.

## <a name="download-files"></a>Baixar arquivos

Você pode usar o comando [azcopy copy](storage-ref-azcopy-copy.md) para baixar arquivos, diretórios e compartilhamentos de arquivos para o seu computador local.

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
> |Copiar listas de controle de acesso (ACLs) junto com os arquivos.|**--preservar-smb-permissões verdadeiras**=\[\|falsas\]|
> |Copie as informações de propriedade do SMB junto com os arquivos.|**--preserve-smb-info**=\[\|verdadeiro falso\]|
> |Descompactar automaticamente arquivos.|**--descomprimir**=\[\|gzip esvaziar\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

> [!NOTE]
> Se `Content-md5` o valor de propriedade de um arquivo contiver um hash, o AzCopy calcula um hash MD5 `Content-md5` para dados baixados e verifica se o hash MD5 armazenado na propriedade do arquivo corresponde ao hash calculado. Se esses valores não corresponderem, o download falhará `--check-md5=NoCheck` a `--check-md5=LogOnly` menos que você anule esse comportamento anexando ou no comando copy.

### <a name="download-a-file"></a>Baixar um arquivo

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Baixe um diretório

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

Este exemplo resulta em `C:\myDirectory\myFileShareDirectory` um diretório chamado que contém todos os arquivos baixados.

### <a name="download-the-contents-of-a-directory"></a>Baixe o conteúdo de um diretório

Você pode baixar o conteúdo de um diretório sem copiar o próprio diretório contendo usando o símbolo curinga (*).

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> Anexar o `--recursive` sinalizador para baixar arquivos em todos os subdiretórios.

### <a name="download-specific-files"></a>Baixe arquivos específicos

Você pode especificar nomes de arquivo completos ou usar nomes parciais com caracteres curinga (*).

#### <a name="specify-multiple-complete-file-names"></a>Especificar vários nomes de arquivo completos

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-path` opção. Separe nomes de arquivos individuais`;`usando um semicolin ().

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

Neste exemplo, o AzCopy transfere o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` diretório e o `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` arquivo. Você precisa incluir `--recursive` a opção de `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` transferir todos os arquivos no diretório.

Você também pode excluir `--exclude-path` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

#### <a name="use-wildcard-characters"></a>Use personagens curingas

Use o comando [azcopy copy](storage-ref-azcopy-copy.md) com a `--include-pattern` opção. Especifique nomes parciais que incluem os caracteres curinga. Nomes separados usando um`;`semicolin ( .

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Exemplo** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

Você também pode excluir `--exclude-pattern` arquivos usando a opção. Para saber mais, consulte documentos de referência de [cópia de azcopy.](storage-ref-azcopy-copy.md)

As `--include-pattern` `--exclude-pattern` opções e se aplicam apenas a nomes de arquivos e não ao caminho.  Se você quiser copiar todos os arquivos de texto que `–recursive` existem em uma árvore de diretório, `–include-pattern` use `*.txt` a opção para obter toda a árvore do diretório e, em seguida, use o e especifique para obter todos os arquivos de texto.

## <a name="copy-files-between-storage-accounts"></a>Copiar arquivos entre as contas de armazenamento

Você pode usar o AzCopy para copiar arquivos para outras contas de armazenamento. A operação de cópia é síncrona, portanto, quando o comando retorna, isso indica que todos os arquivos foram copiados.

O AzCopy usa [APIs](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) [de servidor para servidor,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) para que os dados sejam copiados diretamente entre servidores de armazenamento. Essas operações de cópia não usam a largura de banda da rede do seu computador. Você pode aumentar o throughput dessas operações `AZCOPY_CONCURRENCY_VALUE` definindo o valor da variável ambiente. Para saber mais, consulte [Optimize throughput](storage-use-azcopy-configure.md#optimize-throughput).

Esta seção contém os seguintes exemplos:

> [!div class="checklist"]
> * Copie um arquivo para outra conta de armazenamento
> * Copie um diretório para outra conta de armazenamento
> * Copie um compartilhamento de arquivo para outra conta de armazenamento
> * Copie todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

> [!TIP]
> Você pode ajustar sua operação de cópia usando sinalizadores opcionais. Aqui estão alguns exemplos.
>
> |Cenário|Sinalizador|
> |---|---|
> |Copiar listas de controle de acesso (ACLs) junto com os arquivos.|**--preservar-smb-permissões verdadeiras**=\[\|falsas\]|
> |Copie as informações de propriedade do SMB junto com os arquivos.|**--preserve-smb-info**=\[\|verdadeiro falso\]|
> |Copiar arquivos como Apêndice Blobs ou Blobs de Página.|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Copie para um nível de acesso específico (como o nível de arquivo).|**--block-blob-tier**=\[\|None\|\|Hot Cool Archive\]|
> 
> Para obter uma lista completa, consulte [opções](storage-ref-azcopy-copy.md#options).

### <a name="copy-a-file-to-another-storage-account"></a>Copie um arquivo para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Copie um diretório para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>Copie um compartilhamento de arquivo para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>Copie todos os compartilhamentos de arquivos, diretórios e arquivos para outra conta de armazenamento

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **Exemplo** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>Sincronizar arquivos

Você pode sincronizar o conteúdo de um compartilhamento de arquivos com outro compartilhamento de arquivo. Você também pode sincronizar o conteúdo de um diretório em um compartilhamento de arquivos com o conteúdo de um diretório que está localizado em outro compartilhamento de arquivos. A sincronização é unidirecional. Em outras palavras, você escolhe qual desses dois pontos finais é a fonte e qual é o destino. A sincronização também usa APIs de servidor para servidor.

> [!NOTE]
> Atualmente, esse cenário é suportado apenas para contas que não possuem um namespace hierárquico. A versão atual do AzCopy não sincroniza entre arquivos Azure e Blob Storage.

O comando [sync](storage-ref-azcopy-sync.md) compara nomes de arquivos e últimos carimbos de tempo modificados. Defina `--delete-destination` o sinalizador opcional `true` `prompt` como um valor ou exclua arquivos no diretório de destino se esses arquivos não existirem mais no diretório de origem.

Se você `--delete-destination` definir `true` o sinalizador como AzCopy, excluirá arquivos sem fornecer um prompt. Se você quiser que um prompt apareça antes que `--delete-destination` o `prompt`AzCopy exclua um arquivo, defina o sinalizador para .

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

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>Atualize um compartilhamento de arquivos com alterações para outro compartilhamento de arquivo

O primeiro compartilhamento de arquivo que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Atualize um diretório com alterações em um diretório em outro compartilhamento de arquivos

O primeiro diretório que aparece neste comando é a fonte. O segundo é o destino.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>Atualize um compartilhamento de arquivos para corresponder ao conteúdo de um instantâneo de compartilhamento

O primeiro compartilhamento de arquivo que aparece neste comando é a fonte. No final do URI, anexar `&sharesnapshot=` a seqüência seguida pelo valor **DateTime** do snapshot. 

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **Exemplo** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

Para saber mais sobre os instantâneos de compartilhamento, consulte [Visão geral de instantâneos de compartilhamento para Arquivos Azure](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um desses artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)
