---
title: Formato binário na fábrica de dados do Azure
description: Este tópico descreve como lidar com o formato Binário na Fábrica de Dados Do Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 4560560b3677030a66e277e96eb552d39f5c82c1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416332"
---
# <a name="binary-format-in-azure-data-factory"></a>Formato binário na fábrica de dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O formato binário é suportado para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

Você pode usar conjunto de dados binários na [atividade Copiar,](copy-activity-overview.md) [obter metadados ou](control-flow-get-metadata-activity.md)excluir [atividades](delete-activity.md). Ao usar o conjunto de dados Binário, o ADF não analisa o conteúdo do arquivo, mas o trata como está. 

>[!NOTE]
>Ao usar o conjunto de dados binário sumindo na atividade de cópia, você só pode copiar do conjunto de dados binário para o conjunto de dados binário.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Binário.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida **como Binária**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em . **Veja detalhes na seção propriedades connector article-> Dataset**. | Sim      |
| compactação | Grupo de propriedades para configurar compactação de arquivos. Configure esta seção quando quiser fazer compressão/descompressão durante a execução da atividade. | Não |
| type | O codec de compressão usado para ler/gravar arquivos binários. <br>Os valores permitidos são **bzip2,** **gzip,** **deflate,** **ZipDeflate**. para usar ao salvar o arquivo.<br>Nota ao usar a atividade de cópia para descompactar arquivos ZipDeflate e gravar no armazenamento `<path specified in dataset>/<folder named as source zip file>/`de dados do sink baseado em arquivos, os arquivos serão extraídos para a pasta: . | Não       |
| level | A relação de compressão. Aplicar quando o conjunto de dados for usado no dissipador de atividades do Copiar.<br>Os valores permitidos são **ótimos** ou **mais rápidos**.<br>- **O mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não esteja compactado de forma ideal.<br>- **Ideal**: A operação de compressão deve ser compactada de forma ideal, mesmo que a operação deva mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não       |

Abaixo está um exemplo de conjunto de dados binários no Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte binária e sink.

>[!NOTE]
>Ao usar o conjunto de dados binário sumindo na atividade de cópia, você só pode copiar do conjunto de dados binário para o conjunto de dados binário.

### <a name="binary-as-source"></a>Binário como fonte

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **BinarySource**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

### <a name="binary-as-sink"></a>Binário como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade tipo da fonte de atividade de cópia deve ser definida como **BinarySink**. | Sim      |
| configurações de armazenamento | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em . **Veja detalhes na seção propriedades de atividade do conector -> Copiar**propriedades de atividade . | Não       |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)
