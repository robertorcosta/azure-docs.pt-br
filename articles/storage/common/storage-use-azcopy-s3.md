---
title: Copiar dados do Amazon S3 para o Azure Storage usando o AzCopy | Microsoft Docs
description: Transferir dados com o AzCopy e os buckets do Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941499"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiar dados do Amazon S3 para o Armazenamento Azure usando o AzCopy

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos para ou de uma conta de armazenamento. Este artigo ajuda você a copiar objetos, diretórios e baldes do armazenamento de blob s3 do Amazon Web Services (AWS) para o Azure blob usando o AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como você fornecerá credenciais de autorização

* Para autorizar com o Armazenamento Azure, use o Azure Active Directory (AD) ou um token SAS (SAS) de assinatura de acesso compartilhado.

* Para autorizar com o AWS S3, use uma chave de acesso AWS e uma chave de acesso secreta.

### <a name="authorize-with-azure-storage"></a>Autorizar com o Armazenamento Azure

Consulte o [artigo Get started with AzCopy](storage-use-azcopy-v10.md) para baixar o AzCopy e escolha como você fornecerá credenciais de autorização para o serviço de armazenamento.

> [!NOTE]
> Os exemplos deste artigo supõem que você autenticasua `AzCopy login` identidade usando o comando. Em seguida, o AzCopy usa sua conta Azure AD para autorizar o acesso aos dados no armazenamento Blob.
>
> Se você preferir usar um token SAS para autorizar o acesso a dados blob, então você pode anexar esse token à URL de recurso em cada comando AzCopy.
>
> Por exemplo: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizar com a AWS S3

Reúna sua chave de acesso AWS e a chave de acesso secreta e defina as variáveis do ambiente:

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiar objetos, diretórios e baldes

O AzCopy usa a API [Put Block From URL,](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) para que os dados sejam copiados diretamente entre o AWS S3 e os servidores de armazenamento. Essas operações de cópia não usam a largura de banda da rede do seu computador.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. Se você decidir remover dados de seus baldes S3 após uma operação de cópia, certifique-se de verificar se os dados foram corretamente copiados para sua conta de armazenamento antes de remover os dados.

> [!TIP]
> Os exemplos nesta seção encerram os argumentos de caminho com aspas simples (''). Use aspas individuais em todos os shells de comando, exceto no Windows Command Shell (cmd.exe). Se você estiver usando um Shell de Comando do Windows (cmd.exe), encerre os argumentos de caminho com aspas duplas (""" em vez de aspas simples ('').

 Esses exemplos também funcionam com contas que têm um namespace hierárquico. [O acesso multiprotocolo no Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) permite que`blob.core.windows.net`você use a mesma sintaxe de URL ( ) nessas contas. 

### <a name="copy-an-object"></a>Copiar um objeto

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Exemplo** (namespace hierárquico) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Exemplos neste artigo usam URLs estilo caminho para baldes AWS `http://s3.amazonaws.com/<bucket-name>`S3 (Por exemplo: ). 
>
> Você também pode usar URLs de estilo virtual `http://bucket.s3.amazonaws.com`hospedado também (Por exemplo: ). 
>
> Para saber mais sobre hospedagem virtual de baldes, consultehttps://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)[Hospedagem Virtual de Baldes].

### <a name="copy-a-directory"></a>Copiar um diretório

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Copiar um balde

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copiar todos os baldes em todas as regiões

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copie todos os baldes em uma região S3 específica

Use a mesma sintaxe de URL ()`blob.core.windows.net`para contas que tenham um namespace hierárquico.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Exemplo** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Exemplo** (namespace hierárquico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Lidar com diferenças nas regras de nomeação de objetos

AAWS S3 tem um conjunto diferente de convenções de nomeação para nomes de baldes em comparação com os contêineres blob do Azure. Você pode ler sobre eles [aqui.](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules) Se você optar por copiar um grupo de baldes para uma conta de armazenamento do Azure, a operação de cópia pode falhar devido às diferenças de nomeação.

O AzCopy lida com dois dos problemas mais comuns que podem surgir; baldes que contêm períodos e baldes que contêm hífens consecutivos. Os nomes dos baldes AWS S3 podem conter períodos e hífens consecutivos, mas um recipiente no Azure não pode. O AzCopy substitui períodos por hífens e hífens consecutivos por um número que `my----bucket` `my-4-bucket`representa o número de hífens consecutivos (Por exemplo: um balde chamado torna-se . 

Além disso, como o AzCopy copia sobre arquivos, ele verifica se há colisões de nomeação e tentativas de resolvê-las. Por exemplo, se houver baldes `bucket-name` `bucket.name`com o nome e , `bucket.name` AzCopy resolve um balde chamado primeiro para `bucket-name` e depois para `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Lidar com diferenças nos metadados do objeto

AWS S3 e Azure permitem diferentes conjuntos de caracteres nos nomes das teclas de objeto. Você pode ler sobre os caracteres que o AWS S3 usa [aqui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). No lado Azure, as teclas do objeto blob aderem às regras de nomeação para [identificadores C#.](https://docs.microsoft.com/dotnet/csharp/language-reference/)

Como parte de um `copy` comando AzCopy, você `s2s-invalid-metadata-handle` pode fornecer um valor para o opcionais o sinalizador que especifica como você gostaria de lidar com arquivos onde os metadados do arquivo contêm nomes de chave incompatíveis. A tabela a seguir descreve cada valor da bandeira.

| Valor da bandeira | Descrição  |
|--------|-----------|
| **ExcluaifInvalid** | (Opção padrão) Os metadados não estão incluídos no objeto transferido. O AzCopy registra um aviso. |
| **FalhaIfInvalid** | Os objetos não são copiados. O AzCopy registra um erro e inclui esse erro na contagem de falhas que aparece no resumo da transferência.  |
| **RenomeouIfInvalid**  | O AzCopy resolve a chave de metadados inválida e copia o objeto para o Azure usando o par de valores de chave de metadados resolvido. Para saber exatamente quais etapas o AzCopy leva para renomear as teclas do objeto, consulte a seção Como o [AzCopy renomeia as teclas de objeto](#rename-logic) abaixo. Se o AzCopy não conseguir renomear a chave, o objeto não será copiado. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Como o AzCopy renomeia as teclas de objeto

O AzCopy executa estas etapas:

1. Substitui caracteres inválidos por '_'.

2. Adiciona a `rename_` string ao início de uma nova tecla válida.

   Esta chave será usada para salvar o **valor**original de metadados .

3. Adiciona a `rename_key_` string ao início de uma nova tecla válida.
   Esta chave será usada para salvar a chave inválida de metadados **originais**.
   Você pode usar essa chave para tentar recuperar os metadados no lado do Azure, já que a tecla de metadados é preservada como um valor no serviço de armazenamento Blob.

## <a name="next-steps"></a>Próximas etapas

Encontre mais exemplos em qualquer um desses artigos:

- [Introdução ao AzCopy](storage-use-azcopy-v10.md)

- [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)