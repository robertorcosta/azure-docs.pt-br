---
title: Exemplos do Armazenamento do Azure usando Python | Microsoft Docs
description: Exiba, baixe e execute exemplos de código e aplicativos para o Armazenamento do Azure. Descubra exemplos de introdução a blobs, filas, tabelas e arquivos usando as bibliotecas de clientes do armazenamento de Python.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 3fc18646270bbec3836845bcf317afc2c1d3a6df
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351167"
---
# <a name="azure-storage-samples-using-python"></a>Exemplos do Armazenamento do Azure usando Python

As tabelas a seguir fornecem uma visão geral de nosso repositório de exemplos e os cenários abordados em cada um deles. Clique nos links para exibir o código de exemplo correspondente no GitHub.

## <a name="blob-samples"></a>Exemplos de blob

| **Cenário** | **Código de exemplo** |
|--------------|-----------------|
| Acrescentar blob | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| Blob de blocos | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| Criptografia do cliente | [Gerenciando chaves de conta de armazenamento no Azure Key Vault com Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Copiar blob | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| Create Container | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Delete Blob | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Delete Container | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| Metadados/propriedades/estatísticas de blob | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| ACL/metadados/propriedades de contêiner | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| Get Page Ranges | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| Blob/contêiner de concessão | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| Blob/contêiner de lista | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| Blob de páginas | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Assinatura de Acesso Compartilhado em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| Propriedades do serviço | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Blob de instantâneo | [Introdução ao serviço Blob do Azure em Python](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples"></a>Exemplos de arquivo

| **Cenário** | **Código de exemplo** |
|--------------|-----------------|
| Criar compartilhamentos/diretórios/arquivos | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| Excluir compartilhamentos/diretórios/arquivos | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| Propriedades/metadados de diretório | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| Baixar arquivos | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| Metadados/propriedades/métricas de arquivo | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| Propriedades do serviço de arquivo | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| Listar diretórios e arquivos | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| Listar compartilhamentos | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| Compartilhar metadados/propriedades/estatísticas | [Introdução ao Serviço de Arquivo do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples"></a>Exemplos de fila

| **Cenário** | **Código de exemplo** |
|--------------|-----------------|
| Adicionar mensagem | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| Criptografia do cliente | [Gerenciando chaves de conta de armazenamento no Azure Key Vault com Python](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| Criar filas | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| Excluir mensagem/fila | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| Espiar mensagem | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| ACL/metadados/estatísticas de fila | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| Propriedades do serviço de fila | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| Atualização de mensagem | [Introdução ao serviço Fila do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples"></a>Exemplos de tabela

| **Cenário** | **Código de exemplo** |
|--------------|-----------------|
| Criar tabela | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| Excluir entidade/tabela | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| Inserir/mesclar/substituir entidade | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| Query Entities | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| Consultar tabelas | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| Propriedades/ACL de tabela | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| Update Entity | [Introdução ao serviço Tabela do Azure em Python](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Biblioteca de Exemplos de Código do Azure

Para visualizar a biblioteca completa de exemplos, vá para a biblioteca [Exemplos de código do Azure](https://azure.microsoft.com/resources/samples/?service=storage), que contém exemplos para o Armazenamento do Azure que podem ser baixados e executados localmente. A Biblioteca de Exemplo de Código fornece exemplo de código em formato .zip. Como alternativa, você pode navegar e clonar o repositório GitHub para cada exemplo.

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>Guias de introdução

Confira os guias a seguir se você estiver procurando por instruções sobre como instalar e começar a usar as Bibliotecas de cliente de armazenamento do Azure.

* [Introdução ao serviço Blob do Azure em Python](../blobs/storage-quickstart-blobs-python.md)
* [Introdução ao serviço Fila do Azure em Python](../queues/storage-python-how-to-use-queue-storage.md)
* [Introdução ao serviço Tabela do Azure em Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Introdução ao Serviço de Arquivo do Azure em Python](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre exemplos para outras linguagens:

* .NET: [Exemplos de Armazenamento do Azure usando .NET](storage-samples-dotnet.md)
* Java: [Exemplos de Armazenamento do Azure usando Java](storage-samples-java.md)
* JavaScript/Node.js: [Exemplos do Armazenamento do Azure que usam JavaScript](storage-samples-javascript.md)
* Todas as outras linguagens: [Exemplos do Armazenamento do Azure](storage-samples.md)
