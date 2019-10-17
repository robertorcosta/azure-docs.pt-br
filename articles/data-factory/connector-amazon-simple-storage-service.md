---
title: Copiar dados do Amazon S3 (Simple Storage Service) usando o Azure Data Factory | Microsoft Docs
description: Saiba mais sobre como copiar dados do Amazon S3 (Simple Storage Service) para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: f8f7c33abda8d31d39051a024b9cc381c9f6b192
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387946"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Copiar dados do Amazon Simple Storage Service usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Versão atual](connector-amazon-simple-storage-service.md)

Este artigo descreve como copiar dados do Amazon S3 (Simple Storage Service). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

>[!TIP]
>Para o cenário de migração de dados do Amazon S3 para o armazenamento do Azure, saiba mais em [usar Azure data Factory para migrar dados do Amazon S3 para o armazenamento do Azure](data-migration-guidance-s3-azure-storage.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Esse conector Amazon S3 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, este conector do Amazon S3 dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md). Ele usa a [assinatura AWS versão 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) para autenticar solicitações para S3.

>[!TIP]
>Use esse conector do Amazon S3 para copiar dados de **qualquer provedor de armazenamento compatível com S3**, por exemplo, [Google Cloud Storage](connector-google-cloud-storage.md). Especifique a URL do serviço correspondente na configuração do serviço vinculado.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Amazon S3, verifique se você recebeu as permissões a seguir:

- **Para a execução de atividade de cópia:** : `s3:GetObject` e `s3:GetObjectVersion` para operações de objeto do Amazon S3.
- **Para a criação de GUI do Data Factory**: `s3:ListAllMyBuckets` e `s3:ListBucket`/`s3:GetBucketLocation` para operações de Bucket do Amazon S3 são adicionalmente necessárias permissões para operações como testar a conexão e procurar/navegar caminhos de arquivo. Se você não quiser conceder essa permissão, ignore a conexão de teste na página de criação de serviços vinculados e especifique o caminho diretamente nas configurações do conjunto de dados.

Parra detalhes sobre a lista completa e detalhada das permissões da Amazon S3,consulte [Especificar permissões em uma política](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções seguintes fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao Amazon S3.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon S3:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **AmazonS3**. | SIM |
| accessKeyId | ID da chave de acesso secreta. |SIM |
| secretAccessKey | A chave de acesso do secreta em si. Marque esse campo como um SecureString para armazená-lo de forma segura no Data Factory, ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |SIM |
| serviceUrl | Se você estiver copiando dados de um provedor de armazenamento compatível com S3 diferente do serviço oficial do Amazon S3, especifique o ponto de extremidade personalizado do S3. Por exemplo, para copiar dados do Google Cloud Storage, especifique `https://storage.googleapis.com`. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!TIP]
>Se você estiver copiando dados de um armazenamento compatível com S3 diferente do serviço oficial do Amazon S3, especifique a URL personalizado do serviço S3.

>[!NOTE]
>Este conector requer chaves de acesso da conta IAM para copiar dados do Amazon S3. [Não há suporte para a credencial de segurança temporária](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html).
>

Veja um exemplo:

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de dados](concepts-datasets-linked-services.md). 

- Para **parquet, texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e DataSet de formato binário](#format-based-dataset) .
- Para outros formatos, como o **formato Orc**, consulte outra seção do conjunto de um [formato](#other-format-dataset) .

### <a name="format-based-dataset"></a>Parquet, texto delimitado, JSON, Avro e conjunto de DataSet de formato binário

Para copiar dados do Amazon S3 em **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e com suporte Configurações. As propriedades a seguir têm suporte para o Amazon S3 em configurações `location` em conjunto de conjuntos de base de formato:

| Propriedade   | Descrição                                                  | obrigatórios |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type em `location` no DataSet deve ser definida como **AmazonS3Location**. | SIM      |
| bucketName | O nome do bucket S3.                                          | SIM      |
| folderPath | O caminho para a pasta sob o Bucket fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob a partição especificada + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. Se não for especificado, a versão mais recente será buscada. |Não |

> [!NOTE]
> **AmazonS3Object** tipo de conjunto de dados com parquet/formato de texto mencionado na próxima seção ainda tem suporte como é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas não funciona com o fluxo de dados de mapeamento. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de DataSet de formato

Para copiar dados do Amazon S3 no **formato Orc**, há suporte para as seguintes propriedades:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AmazonS3Object** |SIM |
| bucketName | O nome do bucket S3. O filtro curinga não é suportado. |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| chave | O **filtro de nome ou curinga** da chave do objeto S3 sob o bucket especificado. Aplica-se apenas quando a propriedade "prefix" não é especificada. <br/><br/>Há suporte ao filtro curinga para a parte da pasta e para a parte do nome do arquivo. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"key": "rootfolder/subfolder/*.csv"`<br/>– Exemplo 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Consulte mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). Use `^` como escape se o nome real da pasta/arquivo tiver curingas ou esse caractere de escape. |Não |
| prefixo | Prefixo da chave do objeto S3. Objetos cujas chaves começam com esse prefixo serão selecionados. Aplica-se apenas quando a propriedade "chave" não é especificada. |Não |
| version | A versão do objeto S3 se o controle de versão do S3 está habilitado. Se não for especificado, a versão mais recente será buscada. |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não |
| format | Se você quiser **copiar arquivos no estado em que se encontram** entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob formato como um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format), e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis com suporte são **Ideal** e **O mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **bucketName** para bucket e **prefixo** para a parte da pasta.<br>Para copiar um único arquivo com um determinado nome, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **bucketName** para bucket e **chave** para o nome da parte mais o arquivo de pasta.

**Exemplo: usando o prefixo**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Exemplo: usando a chave e a versão (opcional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do Amazon S3.

### <a name="amazon-s3-as-source"></a>Amazon S3 como fonte

- Para copiar de **parquet, de texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos, como o **formato Orc**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, JSON, Avro e fonte de formato binário

Para copiar dados do Amazon S3 em **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o Amazon S3 em configurações `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | obrigatórios                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AmazonS3ReadSetting**. | SIM                                                         |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                                          |
| prefixo                   | Prefixo da chave de objeto S3 sob o Bucket especificado configurado no conjunto de dados para filtrar objetos de origem. Objetos cujas chaves começam com esse prefixo serão selecionados. <br>Aplica-se somente quando as propriedades `wildcardFolderPath` e `wildcardFileName` não são especificadas. | Não                                                          |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no Bucket especificado configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                                          |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob a partição especificada + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` em DataSet e `prefix` não forem especificados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora. | Não                                                          |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                                          |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a fonte da atividade de cópia do tipo **FileSystemProvider** mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do Amazon S3 no **formato Orc**, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **FileSystemSource** |SIM |
| recursive | Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. Observe que quando o recursivo estiver definido como verdadeiro e o coletor for um armazenamento baseado em arquivo, subpasta/pasta vazia não será copiada/criada no coletor.<br/>Os valores permitidos são: **true** (padrão), **false** | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| bucket | chave | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | falso | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de armazenamentos de dados que têm suporte como origens e coletores na atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
