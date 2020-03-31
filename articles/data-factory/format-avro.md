---
title: Formato Avro na Fábrica de Dados Azure
description: Este tópico descreve como lidar com o formato Avro na Fábrica de Dados Do Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jingwang
ms.openlocfilehash: 1717969aeb24a153f986c70ef60db1aac5c840fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267782"
---
# <a name="avro-format-in-azure-data-factory"></a>Formato Avro na Fábrica de Dados Azure

Siga este artigo quando quiser **analisar os arquivos Avro ou escrever os dados no formato Avro**. 

O formato Avro é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Avro.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida como **Avro**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em . **Veja detalhes na seção propriedades connector article-> Dataset**. | Sim      |
| avroCompressionCodec | O codec de compactação para usar ao escrever em arquivos Avro. Ao ler os arquivos Avro, a Data Factory determina automaticamente o codec de compactação com base nos metadados do arquivo.<br>Os tipos suportados são "**nenhum**" (padrão), "**deflate**", "**snappy**". Nota no momento A atividade copiar não suporta snappy quando ler/gravar arquivos Avro. | Não       |

> [!NOTE]
> O espaço branco no nome da coluna não é suportado para arquivos Avro.

Abaixo está um exemplo do conjunto de dados Avro no Azure Blob Storage:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e pia avro.

### <a name="avro-as-source"></a>Avro como fonte

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **AvroSource**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

### <a name="avro-as-sink"></a>Avro como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **AvroSink**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

## <a name="data-type-support"></a>Suporte do tipo de dados

### <a name="copy-activity"></a>Atividade de cópia
Os [tipos de dados complexos](https://avro.apache.org/docs/current/spec.html#schema_complex) do Avro não são suportados (registros, números, matrizes, mapas, sindicatos e fixos) no Copy Activity.

### <a name="data-flows"></a>Fluxos de dados
Ao trabalhar com arquivos Avro em fluxos de dados, você pode ler e escrever tipos de dados complexos, mas certifique-se de limpar o esquema físico do conjunto de dados primeiro. Em fluxos de dados, você pode definir sua projeção lógica e derivar colunas que são estruturas complexas, em seguida, mapear automaticamente esses campos para um arquivo Avro.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
