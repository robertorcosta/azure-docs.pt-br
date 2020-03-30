---
title: Formato de texto delimitado na fábrica de dados do Azure
description: Este tópico descreve como lidar com o formato de texto delimitado na Fábrica de Dados Do Azure.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: f2e70a7b900ad918cda05ce34204e2de1e6e67ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830183"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Formato de texto delimitado na fábrica de dados do Azure

Siga este artigo quando quiser **analisar os arquivos de texto delimitados ou escrever os dados em formato de texto delimitado**. 

O formato de texto delimitado é suportado para os seguintes conectores: [Amazon S3,](connector-amazon-simple-storage-service.md) [Azure Blob,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure File Storage](connector-azure-file-storage.md), File [System,](connector-file-system.md) [FTP,](connector-ftp.md) [Google Cloud Storage,](connector-google-cloud-storage.md) [HDFS,](connector-hdfs.md) [HTTP](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados de texto delimitado.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade do tipo do conjunto de dados deve ser definida **como DelimitedText**. | Sim      |
| local         | Configurações de localização dos arquivos(s). Cada conector baseado em arquivo tem seu próprio `location`tipo de localização e propriedades suportadas em .  | Sim      |
| columnDelimiter  | Os caracteres(s) usados para separar colunas em um arquivo. Atualmente, o delimitador multi-char é suportado apenas para mapear o fluxo de dados, mas não a atividade do Copiar. <br>O valor padrão é **comma `,` **, Quando o delimitador da coluna é definido como string vazio, o que significa que não há delimitador, toda a linha é tomada como uma única coluna. | Não       |
| rowDelimiter     | O caractere único ou "\r\n" usado para separar linhas em um arquivo.<br>O valor padrão é qualquer um dos seguintes valores **na leitura: ["\r\n", "\r", "\n"]** e **"\n" ou "\r\n" na gravação,** mapeando o fluxo de dados e a atividade copiar, respectivamente. <br>Quando `rowDelimiter` é definido como nenhum delimitador `columnDelimiter` (string vazio), o deve ser definido como nenhum delimitador (string vazio) também, o que significa tratar todo o conteúdo como um único valor. | Não       |
| quoteChar        | O caractere único para citar valores da coluna se contiver delimitador de coluna. <br>O valor padrão é **as paspas duplas** `"`. <br>Para mapear o `quoteChar` fluxo de dados, não pode ser uma seqüência vazia. <br>Para a atividade `quoteChar` Copiar, quando é definida como string vazia, significa que não `escapeChar` há char de citação e valor da coluna não é citado, e é usado para escapar do delimitador da coluna e em si. | Não       |
| escapeChar       | O único caractere para escapar das citações dentro de um valor citado.<br>O valor padrão é **barra invertida `\` **. <br>Para mapear o `escapeChar` fluxo de dados, não pode ser uma seqüência vazia. <br/>Para a atividade `escapeChar` Copiar, quando for `quoteChar` definida como string vazia, a string deve ser definida como string vazia também, nesse caso, certifique-se de que todos os valores da coluna não contenham delimitadores. | Não       |
| firstRowAsHeader | Especifica se deve tratar/fazer a primeira linha como uma linha de cabeçalho com nomes de colunas.<br>Os valores permitidos são **verdadeiros** e **falsos** (padrão). | Não       |
| nullValue        | Especifica a representação de string de valor nulo. <br>O valor padrão é **string vazio**. | Não       |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM870", "IBM 011140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859 -6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Note que o fluxo de dados de mapeamento não suporta codificação UTF-7. | Não       |
| compressionCodec | O codec de compactação usado para ler/gravar arquivos de texto. <br>Os valores permitidos são **bzip2,** **gzip,** **deflate**, **ZipDeflate,** **snappy,** ou **lz4**. para usar ao salvar o arquivo. <br>Nota atualmente A atividade de cópia não suporta "snappy" & "lz4", e o fluxo de dados de mapeamento não suporta "ZipDeflate". <br>Nota ao usar a atividade de cópia para descompactar arquivos ZipDeflate e gravar no armazenamento `<path specified in dataset>/<folder named as source zip file>/`de dados do sink baseado em arquivos, os arquivos serão extraídos para a pasta: . | Não       |
| compactaçãoNível | A relação de compressão. <br>Os valores permitidos são **ótimos** ou **mais rápidos**.<br>- **O mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não esteja compactado de forma ideal.<br>- **Ideal**: A operação de compressão deve ser compactada de forma ideal, mesmo que a operação deva mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Não       |

Abaixo está um exemplo de conjunto de dados de texto delimitado no Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte de texto delimitada e afundamento.

### <a name="delimited-text-as-source"></a>Texto delimitado como fonte 

As seguintes propriedades são suportadas na seção *** \*de origem de\* *** atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A propriedade tipo da fonte de atividade de cópia deve ser definida **como DelimitedTextSource**. | Sim      |
| Formatsettings | Um grupo de propriedades. Consulte a **tabela de configurações de leitura de texto delimitada** abaixo. | Não       |
| configurações de armazenamento  | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de leitura suportadas em . | Não       |

Configurações **de leitura detexto delimitadas** suportadas em: `formatSettings`

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | O tipo de formatoConfigurações devem ser definidas **como DelimitedTextReadSettings**. | Sim      |
| skipLineCount | Indica o número de linhas **não vazias** a serem ignoradas ao ler dados de arquivos de entrada. <br>Se skipLineCount e firstRowAsHeader forem especificados, primeiro as linhas serão ignoradas e, em seguida, as informações de cabeçalho serão lidas no arquivo de entrada. | Não       |

### <a name="delimited-text-as-sink"></a>Texto delimitado como pia

As seguintes propriedades são suportadas na seção *** \*de dissipação de\* *** atividade de cópia.

| Propriedade       | Descrição                                                  | Obrigatório |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | A propriedade tipo da fonte de atividade de cópia deve ser definida **como DelimitedTextSink**. | Sim      |
| Formatsettings | Um grupo de propriedades. Consulte a **tabela de configurações de gravação de texto delimitada** abaixo. |          |
| configurações de armazenamento  | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas `storeSettings`próprias configurações de gravação suportadas em .  | Não       |

Configurações **de gravação detexto delimitadas** suportadas em: `formatSettings`

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatoConfigurações devem ser definidas **como DelimitedTextWriteSettings**. | Sim                                                   |
| Fileextension | A extensão de arquivo usada para nomear `.csv` `.txt`os arquivos de saída, por exemplo, . Ele deve ser especificado quando o `fileName` conjunto de dados DelimitedText de saída não estiver especificado. Quando o nome do arquivo estiver configurado no conjunto de dados de saída, ele será usado como o nome do arquivo sink e a configuração de extensão do arquivo será ignorada.  | Sim, quando o nome do arquivo não é especificado no conjunto de dados de saída |

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Aprenda detalhes sobre [a transformação](data-flow-source.md) da fonte e [a transformação do sumidouro](data-flow-sink.md) no mapeamento do fluxo de dados.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
