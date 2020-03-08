---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre limitações e problemas conhecidos de Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fd76be8d17dc1c632e555a56d038d4f5c1e1486
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669146"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo descreve as limitações e problemas conhecidos de Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Recursos de armazenamento de BLOBs com suporte

Um número crescente de recursos de armazenamento de BLOBs agora funciona com contas que têm um namespace hierárquico. Para obter uma lista completa, consulte [recursos de armazenamento de BLOBs disponíveis em Azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviço do Azure com suporte

O Data Lake Storage Gen2 é compatível com vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, consulte [Serviços do Azure que dão suporte a Azure data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Consulte [Serviços do Azure que dão suporte a Azure data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, consulte plataformas de software livre [que dão suporte a Azure data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Consulte [plataformas de software livre que dão suporte a Azure data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs de BLOB e Data Lake Storage Gen2 APIs para operar nos mesmos dados.

* Você não pode usar as APIs de BLOB e as APIs de Data Lake Storage para gravar na mesma instância de um arquivo. Se você gravar em um arquivo usando APIs Data Lake Storage Gen2, os blocos desse arquivo não ficarão visíveis para chamadas para a API de BLOB da [lista de blocos Get](https://docs.microsoft.com/rest/api/storageservices/get-block-list) . Você pode substituir um arquivo usando APIs Data Lake Storage Gen2 ou APIs de BLOB. Isso não afetará as propriedades do arquivo.

* Quando você usa a operação [listar BLOBs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão diretórios e blobs. Se você optar por usar um delimitador, use apenas uma barra (`/`). Esse é o único delimitador com suporte.

* Se você usar a API [excluir blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio. Isso significa que você não pode usar a API de BLOB para excluir diretórios recursivamente.

Essas APIs REST de BLOB não têm suporte:

* [Colocar BLOB (página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar Página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Obter intervalos de página](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob de cópia incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar página da URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar BLOB (acrescentar)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Acrescentar Bloco](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Anexar bloco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Não há suporte para discos de VM não gerenciados em contas que têm um namespace hierárquico. Se você quiser habilitar um namespace hierárquico em uma conta de armazenamento, coloque os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Suporte ao sistema de arquivos em SDKs

- [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) e [python](data-lake-storage-directory-file-acl-python.md)e [JavaScript](data-lake-storage-directory-file-acl-javascript.md) e suporte estão em visualização pública. Não há suporte para outros SDKs no momento.
- As operações obter e definir ACL não são recursivas no momento.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>Suporte ao sistema de arquivos no PowerShell e CLI do Azure

- O [PowerShell](data-lake-storage-directory-file-acl-powershell.md) e o suporte do [CLI do Azure](data-lake-storage-directory-file-acl-cli.md) estão em visualização pública.
- As operações obter e definir ACL não são recursivas no momento.

## <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

* Ainda não há suporte para a exclusão de instantâneos de BLOB.  

* Atualmente, existem alguns bugs que afetam as políticas de gerenciamento do ciclo de vida e a camada de acesso de arquivamento. 

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Gerenciador de Armazenamento do Azure 1,10. x não pode ser usado para exibir os logs de diagnóstico. Para exibir os logs, use AzCopy ou SDKs.

## <a name="blobfuse"></a>Blobfuse

Não há suporte para Blobfuse.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Use apenas a versão mais recente do AzCopy ([AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Não há suporte para versões anteriores do AzCopy, como AzCopy v 8.1.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Use somente versões `1.6.0` ou superior. Atualmente, há um bug de armazenamento que afeta a versão `1.11.0` que podem resultar em erros de autenticação em determinados cenários. Uma correção para o bug de armazenamento está sendo distribuída, mas, como alternativa, recomendamos que você use a versão `1.10.x` que está disponível como um [Download gratuito](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` não é afetado pelo bug de armazenamento.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Gerenciador de Armazenamento no portal do Azure

As ACLs ainda não têm suporte.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplicativos de terceiros

Aplicativos de terceiros que usam APIs REST para trabalhar continuarão a funcionar se você usá-los com Data Lake Storage Gen2 aplicativos que chamam APIs de blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>ACLs (listas de controle de acesso) e acesso de leitura anônimo

Se o [acesso de leitura anônimo](storage-manage-access-to-resources.md) tiver sido concedido a um contêiner, as ACLs não terão nenhum efeito nesse contêiner ou nos arquivos desse contêiner.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Driver do Windows Azure Storage Blob (WASB)

Atualmente, há vários problemas associados ao uso do driver WASB juntamente com contas que têm um namespace hierárquico. Recomendamos que você use o driver [ABFS (sistema de arquivos de blob do Azure)](data-lake-storage-abfs-driver.md) em suas cargas de trabalho. 





