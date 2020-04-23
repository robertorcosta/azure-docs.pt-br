---
title: Comparar opções de armazenamento para uso com clusters do Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869836"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparar opções de armazenamento para uso com clusters do Azure HDInsight

Você pode escolher entre alguns serviços de armazenamento diferentes do Azure ao criar clusters HDInsight:

* [Armazenamento do Azure](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Este artigo fornece uma visão geral desses tipos de armazenamento e de seus recursos exclusivos.

## <a name="storage-types-and-features"></a>Tipos e recursos de armazenamento

A tabela a seguir resume os serviços de armazenamento do Azure que são suportados com diferentes versões do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de namespace | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis | Versão do HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Uso geral V2 | Hierárquico (sistema de arquivos) | Blob | Standard | Quente, legal, arquivo | 3.6+ | Todos exceto Faísca 2.1 e 2.2|
|Armazenamento do Azure| Uso geral V2 | Objeto | Blob | Standard | Quente, legal, arquivo | 3.6+ | Todos |
|Armazenamento do Azure| Uso geral V1 | Objeto | Blob | Standard | N/D | Todos | Todos |
|Armazenamento do Azure| Armazenamento blob** | Objeto | Blob de blocos | Standard | Quente, legal, arquivo | Todos | Todos |
|Azure Data Lake Storage Gen1| N/D | Hierárquico (sistema de arquivos) | N/D | N/D | N/D | 3.6 Somente | Todos, exceto HBase |

**Para clusters HDInsight, apenas contas de armazenamento secundárias podem ser do tipo BlobStorage e Page Blob não é uma opção de armazenamento suportado.

Para obter mais informações sobre os tipos de conta do Azure Storage, consulte [a visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre os níveis de acesso do Azure Storage, consulte [o armazenamento Azure Blob: Premium (visualização), níveis de armazenamento Hot, Cool e Archive](../storage/blobs/storage-blob-storage-tiers.md)

Você pode criar clusters usando combinações de serviços para armazenamento secundário primário e opcional. A tabela a seguir resume as configurações de armazenamento de cluster que atualmente são suportadas no HDInsight:

| Versão do HDInsight | Armazenamento primário | Armazenamento secundário | Com suporte |
|---|---|---|---|
| 3.6 & 4.0 | Propósito Geral V1, Propósito Geral V2 | Propósito Geral V1, Propósito Geral V2, BlobStorage (Blobs de bloco) | Sim |
| 3.6 & 4.0 | Propósito Geral V1, Propósito Geral V2 | Armazenamento do Data Lake Gen2 | Não |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Armazenamento do Data Lake Gen2 | Sim |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Propósito Geral V1, Propósito Geral V2, BlobStorage (Blobs de bloco) | Sim |
| 3.6 & 4.0 | Armazenamento do Data Lake Gen2 | Armazenamento do Data Lake Gen1 | Não |
| 3.6 | Armazenamento do Data Lake Gen1 | Armazenamento do Data Lake Gen1 | Sim |
| 3.6 | Armazenamento do Data Lake Gen1 | Propósito Geral V1, Propósito Geral V2, BlobStorage (Blobs de bloco) | Sim |
| 3.6 | Armazenamento do Data Lake Gen1 | Armazenamento do Data Lake Gen2 | Não |
| 4,0 | Armazenamento do Data Lake Gen1 | Qualquer | Não |
| 4,0 | Propósito Geral V1, Propósito Geral V2 | Armazenamento do Data Lake Gen1 | Não |

*=Esta pode ser uma ou várias contas do Data Lake Storage Gen2, desde que todas estejam configuradas para usar a mesma identidade gerenciada para acesso a clusters.

> [!NOTE]
> O armazenamento principal do Data Lake Storage Gen2 não é suportado para clusters Spark 2.1 ou 2.2.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Armazenamento Azure](./overview-azure-storage.md)
* [Visão geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Visão geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
