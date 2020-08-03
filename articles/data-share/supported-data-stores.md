---
title: Armazenamentos de dados com suporte no compartilhamento de dados do Azure
description: Saiba mais sobre os armazenamentos de dados com suporte para usar o compartilhamento de dados do Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 07/30/2020
ms.openlocfilehash: 967b2dceab1f1702120cd3121ccd64b4e7286bc6
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511897"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Armazenamentos de dados com suporte no compartilhamento de dados do Azure

O compartilhamento de dados do Azure fornece compartilhamento de dados aberto e flexível, incluindo a capacidade de compartilhar de e para armazenamentos de dados diferentes. Os provedores de dados podem compartilhar dados de um tipo de armazenamento de dados, e seus consumidores de dados podem escolher em qual armazenamento de dados receber dados. 

Neste artigo, você aprenderá sobre o rico conjunto de armazenamentos de dados do Azure que têm suporte no compartilhamento de dados do Azure. Você também pode encontrar informações sobre as combinações de armazenamentos de dados que podem ser aproveitadas por provedores de dados e consumidores de dados. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quais armazenamentos de dados têm suporte no compartilhamento de dados do Azure? 

A tabela abaixo detalha as fontes de dados com suporte para o compartilhamento de dados do Azure. 

| Armazenamento de dados | Compartilhamento baseado em instantâneo | Compartilhamento in-loco 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Banco de Dados SQL do Azure |Visualização pública | |
| Análise do Azure Synapse (anteriormente Azure SQL DW) |Visualização pública | |
| Azure Data Explorer | |Visualização pública |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure oferece flexibilidade aos consumidores de dados ao decidir sobre um armazenamento de dados para aceitar dados no. Por exemplo, os dados que estão sendo compartilhados do banco de dados SQL do Azure podem ser recebidos em Azure Data Lake Store Gen2, banco de dados SQL do Azure ou Azure Synapse Analytics. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha combinações diferentes e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjunto de dados, consulte [como configurar mapeamentos de conjunto](how-to-configure-mapping.md)de dados.

| Armazenamento de dados | Armazenamento do Blobs do Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Banco de Dados SQL do Azure | Azure Synapse Analytics | Azure Data Explorer
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Banco de Dados SQL do Azure | ✓ | | ✓ | ✓ | ✓ ||
| Análise do Azure Synapse (anteriormente Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Azure Data Explorer |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Compartilhar de uma conta de armazenamento
O compartilhamento de dados do Azure dá suporte ao compartilhamento de arquivos, pastas e sistemas de arquivos do Azure Data Lake Gen1 e Azure Data Lake Gen2. Ele também dá suporte ao compartilhamento de BLOBs, pastas e contêineres do armazenamento de BLOBs do Azure. No momento, só há suporte para blob de blocos. Quando os sistemas de arquivos, contêineres ou pastas são compartilhados no compartilhamento baseado em instantâneo, o consumidor de dados pode optar por fazer uma cópia completa dos dados de compartilhamento ou aproveitar a capacidade de instantâneo incremental para copiar somente arquivos novos ou atualizados. O instantâneo incremental é baseado na hora da última modificação dos arquivos. Os arquivos existentes com o mesmo nome serão substituídos.

## <a name="share-from-a-sql-based-source"></a>Compartilhar de uma fonte baseada em SQL
O compartilhamento de dados do Azure dá suporte ao compartilhamento de tabelas ou exibições do banco de dados SQL do Azure e do Azure Synapse Analytics (anteriormente conhecido como Azure SQL DW). Os consumidores de dados podem optar por aceitar os dados no Azure Data Lake Store Gen2 ou no armazenamento de BLOBs do Azure como CSV ou arquivo parquet. Observe que, por padrão, os formatos de arquivo são csv. O consumidor de dados pode optar por receber os dados no formato parquet, se desejado. Isso pode ser feito nas configurações de mapeamento do conjunto de dados durante o recebimento do dado. 

Ao aceitar dados no Azure Data Lake Store Gen2 ou no armazenamento de BLOBs do Azure, os instantâneos completos substituem o conteúdo do arquivo de destino. 

Um consumidor de dados pode optar por receber dados em uma tabela de sua escolha. Nesse cenário, se a tabela de destino ainda não existir, o compartilhamento de dados do Azure criará a tabela SQL com o esquema de origem. Se uma tabela de destino já existir com o mesmo nome, ela será descartada e substituída pelo instantâneo completo mais recente. Ao mapear a tabela de destino, um esquema alternativo e um nome de tabela podem ser especificados. Não há suporte para instantâneos incrementais no momento. 

O compartilhamento de fontes baseadas em SQL tem pré-requisitos relacionados às regras e permissões de firewall. Veja a seção pré-requisitos do tutorial [compartilhar seus dados](share-your-data.md) para obter detalhes.

## <a name="share-from-azure-data-explorer"></a>Compartilhar do Azure Data Explorer
O compartilhamento de dados do Azure dá suporte à capacidade de compartilhar bancos de dados no local a partir de clusters do Azure Data Explorer. O provedor de dados pode compartilhar no nível de cluster ou de banco de dado. Quando compartilhadas no nível de banco de dados, o consumidor do dado poderá acessar apenas os bancos de dados compartilhados pelo provedor de data. Quando compartilhadas no nível do cluster, o consumidor de dados pode acessar todos os bancos de dado do cluster do provedor, incluindo quaisquer bancos de dados futuros criados pelo provedor.

Para acessar bancos de dados compartilhados, os clientes precisam ter seu próprio cluster Data Explorer do Azure. O cluster de Data Explorer do Azure do consumidor de dados precisa localizar na mesma data center do Azure que o cluster de Data Explorer do Azure do provedor de dados. Quando a relação de compartilhamento é estabelecida, o compartilhamento de dados do Azure cria um link simbólico entre os clusters do provedor e do Azure Data Explorer do consumidor.

O Azure Data Explorer dá suporte a dois modos de ingestão de dados: lote e streaming. Os dados recebidos do lote no banco de dados compartilhado aparecerão entre alguns segundos e alguns minutos no lado do consumidor de dado. Os dados recebidos do streaming podem levar até 24 horas para serem exibidos no lado do consumidor de dados. 

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
