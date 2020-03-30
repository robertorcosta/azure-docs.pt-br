---
title: Armazenamento de dados suportado no Azure Data Share
description: Conheça os armazenamentos de dados que são suportados para uso do Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501815"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamento de dados suportado no Azure Data Share

O Azure Data Share fornece compartilhamento de dados aberto e flexível, incluindo a capacidade de compartilhar de e para diferentes armazenamentos de dados. Os provedores de dados podem compartilhar dados de um tipo de armazenamento de dados, e seus consumidores de dados podem escolher em qual armazenamento de dados receber dados. 

Neste artigo, você aprenderá sobre o rico conjunto de armazenamentos de dados do Azure que são suportados no Azure Data Share. Você também pode encontrar informações sobre as combinações de armazenamentos de dados que podem ser aproveitadas por provedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quais data stores são suportados no Azure Data Share? 

A tabela abaixo detalha as fontes de dados suportadas para o Azure Data Share. 

| Armazenamento de dados | Compartilhamento baseado em instantâneo | Compartilhamento no local 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Banco de Dados SQL do Azure |Visualização pública | |
| Azure Synapse Analytics (anteriormente Azure SQL DW) |Visualização pública | |
| Azure Data Explorer | |Visualização pública |

## <a name="data-store-support-matrix"></a>Matriz de suporte ao armazenamento de dados

O Azure Data Share oferece aos consumidores de dados flexibilidade ao decidir em um armazenamento de dados para aceitar dados. Por exemplo, os dados que estão sendo compartilhados do Azure SQL Database podem ser recebidos no Azure Data Lake Store Gen2, No Azure SQL Database ou no Azure Synapse Analytics. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha diferentes combinações e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjuntos de dados, consulte [como configurar mapeamentos de conjuntos de dados](how-to-configure-mapping.md).

|  | Armazenamento do Blobs do Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Banco de Dados SQL do Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Banco de Dados SQL do Azure | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (anteriormente Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Compartilhar de uma conta de armazenamento
O Azure Data Share suporta o compartilhamento de arquivos, pastas e sistemas de arquivos do Azure Data Lake Gen1 e do Azure Data Lake Gen2. Ele também suporta o compartilhamento de blobs, pastas e contêineres do Azure Blob Storage. Aúnica bolha de bloco é suportada no momento. Quando as pastas são compartilhadas no compartilhamento baseado em instantâneos, o consumidor de dados pode optar por fazer uma cópia completa dos dados de compartilhamento ou aproveitar o recurso de instantâneo incremental para copiar apenas arquivos novos ou atualizados. Os arquivos existentes com o mesmo nome serão substituídos.

## <a name="share-from-a-sql-based-source"></a>Compartilhar de uma fonte baseada em SQL
O Azure Data Share suporta o compartilhamento de tabelas ou visualizações do Azure SQL Database e do Azure Synapse Analytics (anteriormente Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Store Gen2 ou no Azure Blob Storage como csv ou arquivo parquet. Observe que, por padrão, os formatos de arquivo são csv. O consumidor de dados pode optar por receber os dados em formato parquet, se desejar. Isso pode ser feito nas configurações de mapeamento do conjunto de dados ao receber os dados. 

Ao aceitar dados no Azure Data Lake Store Gen2 ou no Azure Blob Storage, os snapshots completos sobreescrevem o conteúdo do arquivo de destino. 

Um consumidor de dados pode optar por receber dados em uma tabela de sua escolha. Nesse cenário, se a tabela de destino ainda não existir, o Azure Data Share cria a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, ela será descartada e substituída com o snapshot completo mais recente. Ao mapear a tabela de destino, um esquema alternativo e o nome da tabela podem ser especificados. Os instantâneos incrementais não são suportados no momento. 

O compartilhamento de fontes baseadas em SQL tem pré-requisitos relacionados às regras e permissões de firewall. Consulte a seção de pré-requisitos do tutorial de [compartilhamento de dados](share-your-data.md) para obter detalhes.

## <a name="share-from-azure-data-explorer"></a>Compartilhar do Azure Data Explorer
O Azure Data Share suporta a capacidade de compartilhar bancos de dados no local a partir de clusters do Azure Data Explorer. O provedor de dados pode compartilhar no banco de dados ou no nível de cluster. Quando compartilhado sem o banco de dados, o consumidor de dados só poderá acessar os bancos de dados específicos compartilhados pelo provedor de dados. Quando compartilhado sem acordo, o consumidor de dados pode acessar todos os bancos de dados do cluster do provedor, incluindo quaisquer bancos de dados futuros criados pelo provedor de dados.

Para acessar bancos de dados compartilhados, o consumidor de dados precisa ter seu próprio cluster Azure Data Explorer. O cluster Azure Data Explorer do consumidor de dados precisa localizar no mesmo data center do Azure que o cluster Azure Data Explorer do provedor de dados. Quando o relacionamento de compartilhamento é estabelecido, o Azure Data Share cria um link simbólico entre os clusters Azure Data Explorer do provedor e do consumidor.

O Azure Data Explorer suporta dois modos de ingestão de dados: lote e streaming. Os dados recebidos do lote no banco de dados compartilhado aparecerão entre alguns segundos e alguns minutos no lado do consumidor de dados. Os dados recebidos do streaming podem levar até 24 horas para aparecer no lado do consumidor de dados. 

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
