---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre limitações e problemas conhecidos do Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061504"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo descreve limitações e problemas conhecidos do Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento Blob suportados

Um número crescente de recursos de armazenamento Blob agora funciona com contas que têm um namespace hierárquico. Para obter uma lista completa, consulte [os recursos de armazenamento blob disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviços azure suportadas

O Data Lake Storage Gen2 é compatível com vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços azure suportados, consulte [os serviços do Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Consulte [os serviços do Azure que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, consulte [plataformas de código aberto que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Consulte [plataformas de código aberto que suportam o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs blob e APIs gen2 de armazenamento de data lake podem operar com os mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs blob e APIs de armazenamento de data lake gen2 para operar nos mesmos dados.

* Você não pode usar as APIs blob e as APIs de armazenamento do Lago de Dados para escrever na mesma instância de um arquivo. Se você escrever para um arquivo usando APIs do Data Lake Storage Gen2, os blocos desse arquivo não serão visíveis para chamadas para a API [de bolha da lista de blocos.](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Você pode substituir um arquivo usando APIs do Data Lake Storage Gen2 ou APIs blob. Isso não afetará as propriedades do arquivo.

* Quando você usa a operação [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão ambos os diretórios e blobs. Se você optar por usar um delimitador,`/`use apenas uma barra para a frente (). Este é o único delimitador suportado.

* Se você usar a API [Excluir Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio. Isso significa que você não pode usar os diretórios de exclusão da API Blob recursivamente.

Estas APIs Blob REST não são suportadas:

* [Colocar Blob (Página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar Página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob de cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página da URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar Blob (Apêndice)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Acrescentar Bloco](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Anexar bloco de URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Discos VM não gerenciados não são suportados em contas que tenham um namespace hierárquico. Se você quiser ativar um namespace hierárquico em uma conta de armazenamento, coloque discos VM não gerenciados em uma conta de armazenamento que não tenha o recurso hierárquico namespace ativado.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Suporte ao sistema de arquivos em SDKs

As operações de ACL não são atualmente recursivas.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Suporte ao sistema de arquivos no PowerShell e no Azure CLI

- O suporte ao [PowerShell](data-lake-storage-directory-file-acl-powershell.md) e [ao Azure CLI](data-lake-storage-directory-file-acl-cli.md) está em pré-visualização pública.
- As operações de ACL não são atualmente recursivas.

## <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

* A exclusão de instantâneos blob ainda não é suportada.  

## <a name="archive-tier"></a>Nível de arquivo

Atualmente, há um bug que afeta o nível de acesso ao arquivo.


## <a name="blobfuse"></a>Blobfuse

Blobfuse não é suportado.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Use apenas a versão mais recente do AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)).Versões anteriores do AzCopy, como a AzCopy v8.1, não são suportadas.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Use apenas `1.6.0` versões ou superiores.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Explorador de armazenamento no portal Azure

As ACLs ainda não são suportadas.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplicações de terceiros

Aplicativos de terceiros que usam APIs REST para funcionar continuarão a funcionar se você usá-las com aplicativos Data Lake Storage Gen2 que chamam de APIs Blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Listas de controle de acesso (ACL) e acesso de leitura anônima

Se [o acesso de leitura anônima](storage-manage-access-to-resources.md) foi concedido a um contêiner, então as ACLs não têm efeito sobre esse contêiner ou os arquivos naquele contêiner.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Driver de Blob de armazenamento do Windows Azure (WASB)

Atualmente, existem vários problemas associados ao uso do driver WASB, juntamente com contas que possuem um namespace hierárquico. Recomendamos que você use o [driver Azure Blob File System (ABFS)](data-lake-storage-abfs-driver.md) em suas cargas de trabalho. 





