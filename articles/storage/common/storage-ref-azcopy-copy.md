---
title: cópia azcopy| Microsoft Docs
description: Este artigo fornece informações de referência para o comando azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0325a71fb069f3d96f05d106afac1639fc38fe42
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253332"
---
# <a name="azcopy-copy"></a>azcopy copy

Copia os dados de origem para um local de destino.

## <a name="synopsis"></a>Sinopse

Copia os dados de origem para um local de destino. As direções suportadas são:

  - <-> Local Azure Blob (autenticação SAS ou OAuth)
  - arquivos azure > < locais (autenticação SAS de compartilhamento/diretório)
  - < local > ADLS Gen 2 (autenticação SAS, OAuth ou SharedKey)
  - Azure Blob (SAS ou público) -> Azure Blob (autenticação SAS ou OAuth)
  - Azure Blob (SAS ou público) -> Arquivos Azure (SAS)
  - Arquivos Azure (SAS) -> Arquivos Azure (SAS)
  - Arquivos Azure (SAS) -> Azure Blob (autenticação SAS ou OAuth)
  - AWS S3 (Chave de Acesso) -> Azure Block Blob (autenticação SAS ou OAuth)

Consulte os exemplos para obter mais informações.

## <a name="related-conceptual-articles"></a>Artigos conceituais relacionados

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)
- [Transferir dados com armazenamento AzCopy e Blob](storage-use-azcopy-blobs.md)
- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Avançado

O AzCopy detecta automaticamente o tipo de conteúdo dos arquivos ao fazer o upload do disco local, com base na extensão ou conteúdo do arquivo (se nenhuma extensão for especificada).

A tabela de análise incorporada é pequena, mas no Unix, ela é aumentada pelo arquivo mime.types do sistema local se disponível em um ou mais desses nomes:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

No Windows, os tipos MIME são extraídos do registro. Este recurso pode ser desligado com a ajuda de uma bandeira. Por favor, consulte a seção de bandeiras.

Se você definir uma variável de ambiente usando a linha de comando, essa variável será legível no histórico da linha de comando. Considere limpar variáveis que contenham credenciais do seu histórico de linha de comando. Para evitar que variáveis apareçam em seu histórico, você pode usar um script para solicitar ao usuário suas credenciais e definir a variável ambiente.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Exemplos

Faça upload de um único arquivo usando a autenticação OAuth. Se você ainda não fez login no AzCopy, execute o comando de login azcopy antes de executar o seguinte comando.

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

O mesmo que acima, mas desta vez também compute o hash MD5 do conteúdo do arquivo e salve-o como propriedade do Conteúdo-MD5 da bolha:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

Faça upload de um único arquivo usando um token SAS:

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Carregue um único arquivo usando um token SAS e tubulação (somente blobs de bloco):
  
- gato "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Carregue um diretório inteiro usando um token SAS:
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" -recursivo=verdadeiro

ou

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" --recursivo=verdadeiro --put-md5

Faça upload de um conjunto de arquivos usando um token SAS e caracteres curinga (*):

- azcopy cp "/path/*foo/* bar/**pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]"

Carregar arquivos e diretórios usando um token SAS e caracteres curinga (*):

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" -recursivo=verdadeiro

Baixe um único arquivo usando a autenticação OAuth. Se você ainda não fez login no AzCopy, execute o comando de login azcopy antes de executar o seguinte comando.

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

Baixe um único arquivo usando um token SAS:

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "/path/to/file.txt"

Baixe um único arquivo usando um token SAS e, em seguida, encanendo a saída para um arquivo (somente blobs de bloco):
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" > "/path/to/file.txt"

Baixe um diretório inteiro usando um token SAS:
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "/path/to/dir" --recursivo=verdadeiro

Uma nota sobre o uso de um caractere curinga (*) em URLs:

Há apenas duas maneiras suportadas de usar um personagem curinga em uma URL. 

- Você pode usar um logo após a barra final para frente (/) de uma URL. Isso copia todos os arquivos em um diretório diretamente para o destino sem colocá-los em um subdiretório.

- Você também pode usar um em nome de um contêiner, desde que a URL se refira apenas a um contêiner e não a uma bolha. Você pode usar esta abordagem para obter arquivos de um subconjunto de contêineres.

Baixe o conteúdo de um diretório sem copiar o próprio diretório que contém.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*? [SAS]" "/path/to/dir"

Baixe uma conta de armazenamento inteira.

- azcopy cp "https://[srcaccount].blob.core.net/" "/path/to/dir" --recursivo

Baixe um subconjunto de contêineres dentro de uma conta de armazenamento usando um símbolo curinga (*) no nome do contêiner.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursivo

Copie uma única bolha para outra bolha usando um token SAS.

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Copie uma única bolha para outra bolha usando um token SAS e um token OAuth. Você tem que usar um token SAS no final da URL da conta de origem, mas a conta de destino não precisa de uma se você fizer login no AzCopy usando o comando de login azcopy. 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

Copie um diretório virtual blob para outro usando um token SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" -recursivo=verdadeiro

Copie todos os recipientes, diretórios e blobs da conta de armazenamento para outra usando um token SAS:

- azcopy cp "https://[srcaccount].blob.core.windows.net? [SAS]" "https://[destaccount].blob.core.windows.net? [SAS]" -recursivo=verdadeiro

Copie um único objeto para o Blob Storage do Amazon Web Services (AWS) S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.
  
- azcopy cphttps://s3.amazonaws.com/" [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]? [SAS]"

Copie um diretório inteiro para o Blob Storage do AWS S3 usando uma chave de acesso e um token SAS. Primeiro, defina a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]? [SAS]" -recursivo=verdadeiro

Por favor, consulte para https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html entender melhor o espaço reservado [pasta].

Copie todos os baldes para blob storage da Amazon Web Services (AWS) usando uma chave de acesso e um token SAS. Primeiro, defina a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" " "https://[destaccount].blob.core.windows.net? [SAS]" -recursivo=verdadeiro

Copie todos os baldes para o Blob Storage de uma região aWS (Amazon Web Services) usando uma chave de acesso e um token SAS. Primeiro, defina a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3-" [região].amazonaws.com/" "https://[destaccount].blob.core.windows.net? [SAS]" -recursivo=verdadeiro

Copie um subconjunto de baldes usando um símbolo curinga (*) no nome do balde. Como os exemplos anteriores, você precisará de uma chave de acesso e um token SAS. Certifique-se de definir a variável ambiente AWS_ACCESS_KEY_ID e AWS_SECRET_ACCESS_KEY para a fonte AWS S3.

- azcopy cphttps://s3.amazonaws.com/" [bucket*name]/" "https://[destaccount].blob.core.windows.net? [SAS]" -recursivo=verdadeiro

## <a name="options"></a>Opções

**--backup**                               Ativa o SeBackupPrivilege do Windows para uploads, ou SeRestorePrivilege para downloads, para permitir que o AzCopy veja ler todos os arquivos, independentemente das permissões do sistema de arquivos, e restaurar todas as permissões. Requer que a conta em execução do AzCopy já tenha essas permissões (por exemplo, tem direitos de administrador ou é membro do grupo 'Operadores de backup'). Tudo o que essa bandeira faz é ativar privilégios que a conta já tem.

**--string tipo bolha** define o tipo de bolha no destino. Isso é usado para carregar blobs e ao copiar entre contas (padrão 'Detectar'). Os valores válidos incluem 'Detectar', 'BlockBlob', 'PageBlob' e 'AppendBlob'. Ao copiar entre contas, um valor de 'Detectar' faz com que o AzCopy use o tipo de bolha de origem para determinar o tipo de bolha de destino. Ao carregar um arquivo, 'Detect' determina se o arquivo é um VHD ou um arquivo VHDX com base na extensão do arquivo. Se o arquivo for éter um arquivo VHD ou VHDX, o AzCopy trata o arquivo como uma bolha de página. (padrão "Detectar")

**--block-blob-tier** string Upload blobs block blobs diretamente para o nível de [acesso](../blobs/storage-blob-storage-tiers.md) de sua escolha. (padrão 'Nenhum'). Os valores válidos incluem 'Nenhum', 'Quente', 'Legal' e 'Arquivo'. Se 'Nenhum' ou nenhum nível for aprovado, a bolha herdará o nível da conta de armazenamento.

**flutuador de tamanho de bloco-mb** Use este tamanho de bloco (especificado no MiB) ao fazer upload no Azure Storage e baixar do Azure Storage. O valor padrão é calculado automaticamente com base no tamanho do arquivo. Frações decimais são permitidas (Por exemplo: 0,25).

**--seqüência de controle de cache** Defina o cabeçalho de controle de cache. Retornou no download.

**--check-length**                         Verifique a duração de um arquivo no destino após a transferência. Se houver uma incompatibilidade entre a origem e o destino, a transferência será marcada como falha. (padrão verdadeiro)

**--check-md5** string Especifica como os hashes estritamente MD5 devem ser validados ao baixar. Só disponível para download. Opções disponíveis: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (padrão "FailIfDifferent")

**cadeia de descarte de conteúdo** Defina o cabeçalho de disposição de conteúdo. Retornou no download.

**cadeia de codificação de conteúdo** Defina o cabeçalho de codificação de conteúdo. Retornou no download.

**cadeia de idiomas de conteúdo** Defina o cabeçalho do idioma de conteúdo. Retornou no download.

**a seqüência de tipo de conteúdo** especifica o tipo de conteúdo do arquivo. Implica no-guess-mime-type. Retornou no download.

**--decompressão**                           Descompacte automaticamente os arquivos ao baixar, se a codificação de conteúdo indicar que eles estão compactados. Os valores de codificação de conteúdo suportados são 'gzip' e 'deflate'. As extensões de arquivo de '.gz'/'.gzip' ou '.zz' não são necessárias, mas serão removidas se presentes.

**--exclua-atributos** string (somente Windows) Exclua arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--exclua-blob-type** string Opcionalmente especifica o tipo de bolha (BlockBlob/ PageBlob/ AppendBlob) para excluir ao copiar bolhas do contêiner ou da conta. O uso deste sinalizador não é aplicável para copiar dados de serviço sem azure-service. Mais de uma bolha deve ser separada por ';'.

**--exclua-caminho de** seqüência Exclua esses caminhos ao copiar. Esta opção não suporta caracteres curinga (*). Verifica o prefixo relativo do caminho (Por exemplo: myFolder;myFolder/subDirName/file.pdf). Quando usado em combinação com a travessia da conta, os caminhos não incluem o nome do contêiner.

**--exclua-padrão** de seqüência Exclua esses arquivos ao copiar. Esta opção suporta caracteres curinga (*)

**--follow-symlinks**                      Siga links simbólicos ao fazer o upload do sistema de arquivos local.

**-de cadeia** opcionalmente especifica a combinação de destino de origem. Por exemplo: Localblob, BlobLocal, LocalBlobFs.

**-h, -- ajuda** para copiar

**--incluir-atributos** string (somente Windows) Incluir arquivos cujos atributos correspondem à lista de atributos. Por exemplo: A; S; R

**--incluir-caminho** string Inclua apenas esses caminhos ao copiar. Esta opção não suporta caracteres curinga (*). Verifica o prefixo de caminho relativo (Por exemplo: myFolder;myFolder/subDirName/file.pdf).

**--incluir-seqüência de padrões** Inclua apenas esses arquivos ao copiar. Esta opção suporta caracteres curinga (*). Arquivos separados usando um ';'.

**--log-level** string Defina a verbosidade de log para o arquivo log, níveis disponíveis: INFO(todas as solicitações/respostas), AVISO (respostas lentas), ERRO (apenas solicitações com falha) e NONE (sem registros de saída). (padrão "INFO")

**--seqüência de metadados** Upload para o Azure Storage com esses pares de valor-chave como metadados.

**--no-guess-mime-type**                   Impede que o AzCopy detecte o tipo de conteúdo com base na extensão ou conteúdo do arquivo.

**--substituir** string Substituir os arquivos e blobs conflitantes no destino se este sinalizador for definido como verdadeiro. Os valores possíveis incluem 'true', 'false', 'ifSourceNewer' e 'prompt'. (padrão "verdadeiro")

**--page-blob-tier** string Upload page Blob to Azure Storage usando este blob tier. (padrão "Nenhum")

**--preservar-último-modificado-tempo**          Só disponível quando o destino é o sistema de arquivos.

**--preserve-smb-permissões** corda Falsa por padrão. Preserva ACLs SMB entre recursos cientes (Arquivos Windows e Azure). Para downloads, você também precisará `--backup` usar o sinalizador para restaurar permissões onde o novo Proprietário não será o usuário que está executando o AzCopy. Este sinalizador se aplica a arquivos e pastas, a menos que um `include-pattern`filtro somente para arquivos seja especificado (por exemplo).

**--preserve-smb-info** string False por padrão. Preserva as informações de propriedade smb (tempo de gravação passado, tempo de criação, bits de atributo) entre recursos com reconhecimento de SMB (Arquivos Windows e Azure). Somente os bits de atributo suportados pelos Arquivos Azure serão transferidos; qualquer outro será ignorado. Esse sinalizador se aplica a arquivos e pastas, a menos que um filtro somente de arquivo seja especificado (por exemplo, incluir-padrão). As informações transferidas para pastas são as mesmas para arquivos, exceto para O Tempo de Gravação Última, que nunca é preservada para pastas.

**--preservar-proprietário**                       Só tem um efeito no download de `--preserve-smb-permissions` dados, e somente quando o usado é usado. Se verdadeiro (o padrão), o arquivo Proprietário e Grupo são preservados em downloads. Se este sinalizador for `--preserve-smb-permissions` definido como falso, ainda preservará ACLs, mas Proprietário e Grupo serão baseados no usuário que está executando o AzCopy.

**--put-md5**                             Crie um hash MD5 de cada arquivo e salve o hash como propriedade Content-MD5 da bolha de destino ou arquivo. (Por padrão, o hash NÃO é criado.) Só disponível quando o upload.

**--recursivo**                            Olhe para subdiretórios recursivamente ao fazer o upload do sistema de arquivos local.

**--s2s-detect-fonte-alterada**           Verifique se a fonte mudou após a enumeração.

A seqüência de **metadados --s2s-handle-invalid-metadata** especifica como as chaves de metadados inválidas são tratadas. Opções disponíveis: ExcluaIfInvalid, FailIfInvalid, RenameIfInvalid. (padrão "ExcluaIfInvalid")

**--s2s-preserve-access-tier**             Preserve o nível de acesso durante o serviço para a cópia do serviço. Consulte o [armazenamento Azure Blob: níveis de acesso quentes, legais e de arquivamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) para garantir que a conta de armazenamento de destino suporte a configuração de nível de acesso. Nos casos em que a configuração do nível de acesso não é suportada, use s2sPreserveAccessTier=false para contornar a camada de acesso de cópia. (padrão verdadeiro)

**--s2s-preserve-propriedades**              Preservar propriedades completas durante o serviço para copiar o serviço. Para a fonte de arquivo não-single do AWS S3 e do Azure File, a operação da lista não retorna propriedades completas de objetos e arquivos. Para preservar as propriedades completas, o AzCopy precisa enviar uma solicitação adicional por objeto ou arquivo. (padrão verdadeiro)

## <a name="options-inherited-from-parent-commands"></a>Opções herdadas de comandos parentais

**--cap-mbps uint32**      Limita a taxa de transferência, em megabits por segundo. O throughput momento a momento pode variar ligeiramente da tampa. Se esta opção estiver definida como zero, ou for omitida, o throughput não será limitado.

**--cadeia de** string tipo de saída Formato da saída do comando. As opções incluem: texto, json. O valor padrão é 'texto'. (padrão "texto")

## <a name="see-also"></a>Confira também

- [azcopy](storage-ref-azcopy.md)
