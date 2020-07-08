---
title: Formato do Excel no Azure Data Factory
description: Este tópico descreve como lidar com o formato do Excel no Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: jingwang
ms.openlocfilehash: 8b4876377501209e19ac496d605d228208d2323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670839"
---
# <a name="excel-format-in-azure-data-factory"></a>Formato do Excel no Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos do Excel**. Azure Data Factory dá suporte a ". xls" e ". xlsx".

Há suporte para o formato do Excel para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md). Há suporte como origem, mas não coletor.

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de banco de e do Excel.

| Property         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **Excel**.   | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . | Sim      |
| sheetName        | O nome da planilha do Excel para ler os dados.                       | Sim      |
| range            | O intervalo de células na planilha fornecida para localizar os dados seletivos, por exemplo, `A3:H5` (uma tabela de a3 a H5), `A3` (uma tabela que começa na célula a3), `A3:A3` (célula única). Se não for especificado, o ADF lerá da planilha inteira como uma tabela. | Não       |
| firstRowAsHeader | Especifica se deve tratar a primeira linha na planilha/intervalo fornecido como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **true** e **false** (padrão). | Não       |
| nullValue        | Especifica a representação de cadeia de caracteres do valor nulo. <br>O valor padrão é uma **cadeia de caracteres vazia**. | Não       |
| compressionCodec | O codec de compactação usado para ler arquivos do Excel. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snapshot**ou **lz4**. O padrão não é compactado. <br>**Observação** a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4" e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate". <br>**Observação** ao usar a atividade de cópia para descompactar arquivo (s) **ZipDeflate** e gravar no armazenamento de dados de coletor baseado em arquivo, os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source zip file>/` . | Não       |
| compressionLevel | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não       |

Veja abaixo um exemplo de conjunto de um banco de uma no armazenamento de BLOBs do Azure:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Excel.

### <a name="excel-as-source"></a>Excel como fonte 

As propriedades a seguir têm suporte na seção *** \* origem \* *** da atividade de cópia.

| Property      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **Excel**. | Sim      |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . | Não       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
