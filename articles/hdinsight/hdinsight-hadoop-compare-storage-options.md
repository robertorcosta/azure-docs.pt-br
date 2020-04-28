---
title: Comparar opções de armazenamento para uso com clusters do Azure HDInsight
description: Fornece uma visão geral dos tipos de armazenamento e como eles funcionam com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: bfbe311d9768923eee8c1b0cc4f3b4ec1a7ad69a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186298"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Comparar opções de armazenamento para uso com clusters do Azure HDInsight

Você pode escolher entre alguns serviços de armazenamento do Azure diferentes ao criar clusters HDInsight:

* [Armazenamento do Azure](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Este artigo fornece uma visão geral desses tipos de armazenamento e de seus recursos exclusivos.

## <a name="storage-types-and-features"></a>Tipos de armazenamento e recursos

A tabela a seguir resume os serviços de armazenamento do Azure que têm suporte com versões diferentes do HDInsight:

| Serviço de armazenamento | Tipo de conta | Tipo de namespace | Serviços com suporte | Níveis de desempenho compatíveis | Camadas de acesso compatíveis | Versão do HDInsight | Tipo de cluster |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Uso geral V2 | Hierárquico (Filesystem) | Blob | Standard | Quente, frio e arquivo morto | 3.6+ | Todos, exceto Spark 2,1 e 2,2|
|Armazenamento do Azure| Uso geral V2 | Objeto | Blob | Standard | Quente, frio e arquivo morto | 3.6+ | Todos |
|Armazenamento do Azure| Uso geral V1 | Objeto | Blob | Standard | N/D | Todos | Todos |
|Armazenamento do Azure| Armazenamento de BLOBs * * | Objeto | Blob de blocos | Standard | Quente, frio e arquivo morto | Todos | Todos |
|Azure Data Lake Storage Gen1| N/D | Hierárquico (Filesystem) | N/D | N/D | N/D | apenas 3,6 | Todos, exceto HBase |

* * Para clusters HDInsight, somente contas de armazenamento secundárias podem ser do tipo BlobStorage e o blob de página não é uma opção de armazenamento com suporte.

Para obter mais informações sobre os tipos de conta de armazenamento do Azure, consulte [visão geral da conta de armazenamento do Azure](../storage/common/storage-account-overview.md)

Para obter mais informações sobre as camadas de acesso de armazenamento do Azure, consulte [armazenamento de BLOBs do Azure: camadas Premium (visualização), quente, fria e arquivo morto](../storage/blobs/storage-blob-storage-tiers.md)

Você pode criar clusters usando combinações de serviços para armazenamento primário e secundário opcional. A tabela a seguir resume as configurações de armazenamento de cluster que atualmente têm suporte no HDInsight:

| Versão do HDInsight | Armazenamento primário | Armazenamento secundário | Com suporte |
|---|---|---|---|
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Uso Geral v1, Uso Geral v2 | Armazenamento do Data Lake Gen2 | Não |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Armazenamento do Data Lake Gen2 | Sim |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3,6 & 4,0 | Armazenamento do Data Lake Gen2 | Armazenamento do Data Lake Gen1 | Não |
| 3.6 | Armazenamento do Data Lake Gen1 | Armazenamento do Data Lake Gen1 | Sim |
| 3.6 | Armazenamento do Data Lake Gen1 | Uso Geral v1, Uso Geral v2, BlobStorage (BLOBs de blocos) | Sim |
| 3.6 | Armazenamento do Data Lake Gen1 | Armazenamento do Data Lake Gen2 | Não |
| 4,0 | Armazenamento do Data Lake Gen1 | Qualquer | Não |
| 4,0 | Uso Geral v1, Uso Geral v2 | Armazenamento do Data Lake Gen1 | Não |

* = Isso pode ser uma ou várias contas de Data Lake Storage Gen2, desde que todas as configurações usem a mesma identidade gerenciada para acesso ao cluster.

> [!NOTE]
> Não há suporte para o armazenamento primário Data Lake Storage Gen2 para clusters Spark 2,1 ou 2,2.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Armazenamento do Azure](./overview-azure-storage.md)
* [Visão geral do Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Visão geral do Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introdução ao Armazenamento do Azure](../storage/common/storage-introduction.md)
