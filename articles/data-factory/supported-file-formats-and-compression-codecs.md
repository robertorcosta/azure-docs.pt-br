---
title: Formatos de arquivo suportados na fábrica de dados do Azure
description: Este tópico descreve os formatos de arquivo e os códigos de compactação com suporte nos conectores baseados em arquivo no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: dbcfad3dd3db9f5c9431e07d85d77a77a10283c4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419027"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Formatos de arquivo e codecs de compactação com suporte no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Este artigo se aplica aos seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a [atividade Copiar](copy-activity-overview.md) para copiar arquivos como está entre dois armazenamentos de dados baseados em arquivos, nesse caso os dados são copiados eficientemente sem qualquer serialização ou desserialização. 

Além disso, você também pode analisar ou gerar arquivos de um determinado formato. Por exemplo, você pode executar o seguinte:

* Copie os dados de um banco de dados SQL Server no local e escreva para o Azure Data Lake Storage Gen2 no formato Parquet.
* Copiar arquivos no formato CSV (text) de um sistema de arquivos no local e gravar para o armazenamento Azure Blob no formato Avro.
* Copie arquivos fechados de um sistema de arquivos no local, descompacte-os em tempo real e escreva arquivos extraídos para o Azure Data Lake Storage Gen2.
* Copie os dados no formato Gzip compactpressed-text (CSV) do armazenamento Azure Blob e escreva-os no Azure SQL Database.
* Muitas outras atividades que requerem serialização/desserialização ou compressão/descompressão.

## <a name="next-steps"></a>Próximas etapas

Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
