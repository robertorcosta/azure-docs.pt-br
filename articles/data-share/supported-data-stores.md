---
title: Armazenamentos de dados com suporte no Azure Data Share
description: Saiba mais sobre os armazenamentos de dados com suporte para usar o compartilhamento de dados do Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 12/7/2020
ms.openlocfilehash: 566fd2c9c31933420769f7200a0434cc53f8c2f3
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853128"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados com suporte no Azure Data Share

O compartilhamento de dados do Azure fornece compartilhamento de dados aberto e flexível, incluindo a capacidade de compartilhar de e para armazenamentos de dados diferentes. Os provedores de dados podem compartilhar dados de um tipo de armazenamento de dados, e seus consumidores de dados podem escolher em qual armazenamento de dados receber dados. 

Neste artigo, você aprenderá sobre o rico conjunto de armazenamentos de dados do Azure que têm suporte no compartilhamento de dados do Azure. Você também pode encontrar informações sobre as combinações de armazenamentos de dados que podem ser aproveitadas por provedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quais armazenamentos de dados têm suporte no compartilhamento de dados do Azure? 

A tabela abaixo detalha as fontes de dados com suporte para o compartilhamento de dados do Azure. 

| Armazenamento de dados | Compartilhamento baseado em instantâneo (instantâneo completo) | Compartilhamento baseado em instantâneo (instantâneo incremental) | Compartilhamento in-loco 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Banco de Dados SQL do Azure |✓ | | |
| Análise do Azure Synapse (anteriormente Azure SQL DW) |✓ | | |
| Pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho) |✓ | | |
| Azure Data Explorer | | |✓ |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure oferece flexibilidade aos consumidores de dados ao decidir sobre um armazenamento de dados para aceitar dados no. Por exemplo, os dados que estão sendo compartilhados do banco de dados SQL do Azure podem ser recebidos em Azure Data Lake Store Gen2, banco de dados SQL do Azure ou Azure Synapse Analytics. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha combinações diferentes e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjunto de dados, consulte [como configurar mapeamentos de conjunto](how-to-configure-mapping.md)de dados.

| Armazenamento de dados | Armazenamento do Blobs do Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Banco de Dados SQL do Azure | Análise do Azure Synapse (anteriormente Azure SQL DW) | Pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho) | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Armazenamento de Blobs do Azure | ✓ || ✓ |||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ |||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ |||
| Banco de Dados SQL do Azure | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Análise do Azure Synapse (anteriormente Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure Data Explorer ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Compartilhamento por meio de uma conta de armazenamento
O compartilhamento de dados do Azure dá suporte ao compartilhamento de arquivos, pastas e sistemas de arquivos do Azure Data Lake Gen1 e Azure Data Lake Gen2. Ele também dá suporte ao compartilhamento de BLOBs, pastas e contêineres do armazenamento de BLOBs do Azure. No momento, só há suporte para blob de blocos. Quando os sistemas de arquivos, contêineres ou pastas são compartilhados no compartilhamento baseado em instantâneo, o consumidor de dados pode optar por fazer uma cópia completa dos dados de compartilhamento ou aproveitar a capacidade de instantâneo incremental para copiar somente arquivos novos ou atualizados. O instantâneo incremental é baseado na hora da última modificação dos arquivos. Os arquivos existentes com o mesmo nome serão substituídos.

Veja [compartilhar e receber dados do armazenamento de BLOBs do Azure e Azure data Lake Storage](how-to-share-from-storage.md) para obter detalhes.

## <a name="share-from-a-sql-based-source"></a>Compartilhamento por meio de uma fonte baseada em SQL
O compartilhamento de dados do Azure dá suporte ao compartilhamento de tabelas e exibições do banco de dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW) e compartilhamento de tabelas do pool SQL dedicado do Azure Synapse Analytics (espaço de trabalho). O compartilhamento do pool SQL sem servidor do Azure Synapse Analytics (espaço de trabalho) não tem suporte no momento. Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Storage Gen2 ou no armazenamento de BLOBs do Azure como arquivos CSV ou parquet, bem como no banco de dados SQL do Azure e no Azure Synapse Analytics como tabelas.

Ao aceitar dados no Azure Data Lake Store Gen2 ou no armazenamento de BLOBs do Azure, instantâneos completos substituem o conteúdo do arquivo de destino, se já existir.
Quando os dados são recebidos na tabela e se a tabela de destino ainda não existir, o compartilhamento de dados do Azure criará a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, ela será descartada e substituída pelo instantâneo completo mais recente. Não há suporte para instantâneos incrementais no momento.

Consulte [compartilhar e receber dados do banco de dados SQL do Azure e do Azure Synapse Analytics](how-to-share-from-sql.md) para obter detalhes.

## <a name="share-from-azure-data-explorer"></a>Compartilhar do Azure Data Explorer
O compartilhamento de dados do Azure dá suporte à capacidade de compartilhar bancos de dados no local a partir de clusters do Azure Data Explorer. O provedor de dados pode compartilhar no nível de cluster ou de banco de dado. Quando compartilhadas no nível de banco de dados, o consumidor do dado poderá acessar apenas os bancos de dados compartilhados pelo provedor de data. Quando compartilhadas no nível do cluster, o consumidor de dados pode acessar todos os bancos de dado do cluster do provedor, incluindo quaisquer bancos de dados futuros criados pelo provedor.

Para acessar bancos de dados compartilhados, os clientes precisam ter seu próprio cluster Data Explorer do Azure. O cluster de Data Explorer do Azure do consumidor de dados precisa localizar na mesma data center do Azure que o cluster de Data Explorer do Azure do provedor de dados. Quando a relação de compartilhamento é estabelecida, o compartilhamento de dados do Azure cria um link simbólico entre os clusters do provedor e do Azure Data Explorer do consumidor. Dados ingeridos usando o modo de lote no cluster de Data Explorer de origem do Azure serão exibidos no cluster de destino dentro de alguns segundos a alguns minutos.

Veja [compartilhar e receber dados do Azure data Explorer](/azure/data-explorer/data-share) para obter detalhes. 

## <a name="next-steps"></a>Próximas etapas

Para saber como começar a compartilhar dados, continue no tutorial [compartilhar seus dados](share-your-data.md) .
