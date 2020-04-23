---
title: sincronização azcopy | Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086021"
---
# <a name="azcopy-sync"></a>azcopy sync

Replica a localização de origem para o local de destino.

## <a name="synopsis"></a>Sinopse

Os últimos tempos modificados são usados para comparação. O arquivo é ignorado se o último tempo modificado no destino for mais recente.

Os pares suportados são:

- <-> Local Azure Blob (autenticação SAS ou OAuth pode ser usada)
- Azure Blob <-> Azure Blob (A Fonte deve incluir um SAS ou é acessível publicamente; ou a autenticação SAS ou OAuth pode ser usada para destino)
- Arquivo azure <-> Arquivo Azure (A fonte deve incluir um SAS ou é acessível publicamente; A autenticação SAS deve ser usada para o destino)

O comando sync difere do comando copy de várias maneiras:

1. Por padrão, o sinalizador recursivo é verdadeiro e a sincronização copia todos os subdiretórios. A sincronização só copia os arquivos de nível superior dentro de um diretório se o sinalizador recursivo for falso.
2. Ao sincronizar entre diretórios virtuais, adicione uma barra de arrasto ao caminho (consulte exemplos) se houver uma bolha com o mesmo nome de um dos diretórios virtuais.
3. Se o sinalizador 'deleteDestination' estiver definido como verdadeiro ou prompt, então a sincronização excluirá arquivos e blobs no destino que não estão presentes na origem.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

### <a name="advanced"></a>Avançado

Se você não especificar uma extensão de arquivo, o AzCopy detectará automaticamente o tipo de conteúdo dos arquivos ao fazer o upload do disco local, com base na extensão ou conteúdo do arquivo (se nenhuma extensão for especificada).

A tabela de análise incorporada é pequena, mas no Unix, ela é aumentada pelo arquivo mime.types do sistema local se disponível em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos MIME são extraídos do registro.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Exemplos

Sincronize um único arquivo:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> A bolha de destino *deve* existir. Use `azcopy copy` para copiar um único arquivo que ainda não existe no destino. Caso contrário, ocorre o `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`seguinte erro: .

O mesmo que acima, mas desta vez, também compute o hash MD5 do conteúdo do arquivo e salve-o como propriedade do Blob Content-MD5:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Sincronize um diretório inteiro, incluindo seus subdiretórios (note que o recursivo está ligado por padrão):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

ou

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Sincronize apenas os arquivos superiores dentro de um diretório, mas não seus subdiretórios:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Sincronize um subconjunto de arquivos em um diretório (Por exemplo: apenas arquivos jpg e pdf, ou se o nome do arquivo é "exactName"):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

Sincronize um diretório inteiro, mas exclua certos arquivos do escopo (Por exemplo: cada arquivo que começa com foo ou termina com barra):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

Sincronize uma única bolha:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Sincronize um diretório virtual:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Sincronize um diretório virtual que tenha o mesmo nome de uma bolha (adicione uma barra de arrasto ao caminho para desambiguar):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Sincronize um diretório de arquivo Azure (a mesma sintaxe que Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Se os sinalizadores include/exclusão forem usados juntos, apenas os arquivos correspondentes aos padrões de inclusão serão analisados, mas aqueles que correspondem aos padrões de exclusão serão sempre ignorados.

## <a name="options"></a>Opções

**flutuador de tamanho de bloco-mb** Use este tamanho de bloco (especificado no MiB) ao fazer upload no Azure Storage ou baixar no Azure Storage. O padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (Por exemplo: 0,25).

**--check-md5** string Especifica como os hashes estritamente MD5 devem ser validados ao baixar. Esta opção só está disponível no download. Os valores disponíveis incluem: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (padrão 'FailIfDifferent'). (padrão "FailIfDifferent")

**a seqüência de exclusão de destino** define se exclui arquivos extras do destino que não estão presentes na origem. Pode ser definido como verdadeiro, falso ou rápido. Se definido para solicitar, o usuário será perguntado antes de agendar arquivos e blobs para exclusão. (padrão 'falso'). (padrão "falso")

**--exclua-atributos** string (somente Windows) Exclua arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--exclua-caminho de** seqüência Exclua esses caminhos ao copiar. Esta opção não suporta caracteres curinga (*). Verifica o prefixo relativo do caminho (Por exemplo: myFolder;myFolder/subDirName/file.pdf). Quando usado em combinação com a travessia da conta, os caminhos não incluem o nome do contêiner.

**--excluir-padrão de** seqüência Desexcluir arquivos onde o nome corresponde à lista de padrões. Por exemplo: \*.jpg; \*.pdf;exactName

**-h, -- ajudar a** ajudar a sincronizar

**--incluir-atributos** string (somente Windows) Inclua apenas arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--incluir-seqüência de padrões** Inclua apenas arquivos onde o nome corresponda à lista de padrões. Por exemplo: \*.jpg; \*.pdf;exactName

**--log-level** string Defina a verbosidade de log para o arquivo log, níveis disponíveis: INFO(todas as solicitações e respostas), AVISO (respostas lentas), ERRO (apenas solicitações com falha) e NONE (sem registros de saída). (INFO padrão). (padrão "INFO")

**--put-md5**                     Crie um hash MD5 de cada arquivo e salve o hash como propriedade Content-MD5 da bolha de destino ou arquivo. (Por padrão, o hash NÃO é criado.) Só disponível quando o upload.

**--recursivo**                   True por padrão, olhe para subdiretórios recursivamente ao sincronizar entre diretórios. (padrão verdadeiro). (padrão verdadeiro)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

|Opção|Descrição|
|---|---|
|--cap-mbps uint32|Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.|
|--cadeia de tipo de saída|Formato da saída do comando. As opções incluem: texto, json. O valor padrão é "texto".|

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
