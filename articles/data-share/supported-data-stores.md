---
title: Armazenamentos de dados com suporte no compartilhamento de dados do Azure
description: Saiba mais sobre os armazenamentos de dados com suporte para usar o compartilhamento de dados do Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516390"
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
| Banco de dados SQL do Azure |Visualização pública | |
| SQL Data Warehouse do Azure |Visualização pública | |
| Azure Data Explorer | |[Visualização limitada](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>Matriz de suporte do armazenamento de dados

O compartilhamento de dados do Azure oferece flexibilidade aos consumidores de dados ao decidir sobre um armazenamento de dados para aceitar dados no. Por exemplo, os dados que estão sendo compartilhados do banco de dados SQL do Azure podem ser recebidos em Azure Data Lake Store Gen2, banco de dados SQL do Azure ou SQL Data Warehouse do Azure. Os clientes podem escolher em qual formato receber dados ao configurar um compartilhamento de dados recebido. 

A tabela abaixo detalha combinações diferentes e escolhas que os consumidores de dados têm ao aceitar e configurar seu compartilhamento de dados. Para obter mais informações sobre como configurar mapeamentos de conjunto de dados, consulte [como configurar mapeamentos de conjunto](how-to-configure-mapping.md)de dados.

|  | Armazenamento do Blobs do Azure | Gen1 SQL do Azure Data Lake | Gen2 SQL do Azure Data Lake | Banco de dados SQL do Azure | SQL Data Warehouse do Azure 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Armazenamento de Blobs do Azure |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Banco de dados SQL do Azure |✓ | |✓|✓|✓|
| SQL Data Warehouse do Azure |✓ | |✓|✓|✓|

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).
