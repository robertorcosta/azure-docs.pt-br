---
title: Copiar dados do Google Cloud Storage utilizando o Azure Data Factory
description: Saiba mais sobre como copiar dados do Google Cloud Storage para armazenamentos de dados de coletor com suporte por meio do Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f6b488fc4817e360ed512441f67d2e00356e6134
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758598"
---
# <a name="copy-data-from-google-cloud-storage-using-azure-data-factory"></a>Copiar dados do Google Cloud Storage utilizando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como copiar dados a partir do Google Cloud Storage (GCS). Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de armazenamento doo Google Cloud Storage oferece suporte às seguintes atividades:

- [atividade de cópia](copy-activity-overview.md) de com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, este conector do Google Cloud Storage dá suporte à cópia de arquivos no estado em que se encontram ou à análise de arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md). Ele aproveita a interoperabilidade compatível com o S3 do GCS.

## <a name="prerequisites"></a>Pré-requisitos

A seguinte configuração é necessária em sua conta do Google Cloud Storage:

1. Permitir a interoperabilidade para sua conta do Google Cloud Storage
2. Definir o projeto padrão que contém os dados que você deseja copiar do pacote GCS de destino
3. Criar uma conta de serviço e definir os níveis certos de permissões usando a Nuvem IAM no GCP 
4. Gerar as chaves de acesso para esta conta de serviço

![Recuperar a chave de acesso para o Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados do Google Cloud Storage, verifique se você recebeu as permissões necessárias. As permissões definidas na conta de serviço podem conter `storage.buckets.get`, `storage.buckets.list`, `storage.objects.get` para Operações de Objeto.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google Cloud Storage.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Google Cloud Storage:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **GoogleCloudStorage**. | Sim |
| accessKeyId | ID da chave de acesso secreta. Para ver a chave de acesso e o segredo, confira [Prerrequisitos](#prerequisites). |Sim |
| secretAccessKey | A chave de acesso do secreta em si. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). |Sim |
| serviceUrl | Especifique o ponto de extremidade GCS personalizado como **`https://storage.googleapis.com`** . | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Veja um exemplo:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir oferecem suporte para Google Cloud Storage em configurações de `location` no conjunto de dados baseado no formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade de tipo em `location` no conjunto de dados deve ser definida como **GoogleCloudStorageLocation**. | Sim      |
| bucketName | O nome do bucket GCS.                                          | Sim      |
| folderPath | O caminho para a pasta sob o bucket fornecido. Se você quiser usar um caractere curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o bucket + folderPath fornecido. Se você quiser usar um caractere curinga para filtrar os arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
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

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção apresenta uma lista das propriedades com suporte da fonte do Google Cloud Storage.

### <a name="google-cloud-storage-as-source"></a>Google Cloud Storage como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As propriedades a seguir oferecem suporte para Google Cloud Storage em configurações de `storeSettings` na origem da cópia baseada no formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | A propriedade de tipo em `storeSettings` deve ser configurada com **GoogleCloudStorageReadSettings**. | Sim                                                         |
| ***Localize os arquivos a serem copiados:*** |  |  |
| OPÇÃO 1: caminho estático<br> | Copiar do caminho do bucket ou de pasta/arquivo especificado no conjunto de dados. Se quiser copiar todos os arquivos de um bucket/pasta, especifique também `wildcardFileName` como `*`. |  |
| OPÇÃO DOIS: Prefixo GCS<br>– prefix | Prefixo para o nome da chave GCS sob o bucket especificado configurado no conjunto de dados para filtrar os arquivos GCS de origem. As chaves GCS cujo nome começa com `bucket_in_dataset/this_prefix` são selecionadas. Utiliza o filtro do lado do serviço do GCS, que fornece melhor desempenho do que o filtro curinga. | Não |
| OPÇÃO 3: curinga<br>– wildcardFolderPath | O caminho da pasta com caracteres curinga no bucket fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| OPÇÃO 3: curinga<br>– wildcardFileName | O nome do arquivo com caracteres curinga sob o bucket + folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim |
| OPÇÃO 3: uma lista de arquivos<br>– fileListPath | Indica a cópia de um determinado conjunto de arquivos. Aponte para um arquivo de texto que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de dados.<br/>Ao usar essa opção, não especifique o nome do arquivo no conjunto de dados. Veja mais exemplos em [Exemplos de lista de arquivos](#file-list-examples). |Não |
| ***Configurações adicionais:*** |  | |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. <br>Os valores permitidos são **true** (padrão) e **false**.<br>Essa propriedade não se aplica quando você configura `fileListPath`. |Não |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: Última Modificação. <br>Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.<br/>Essa propriedade não se aplica quando você configura `fileListPath`. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                                          |
| maxConcurrentConnections | O número das conexões para se conectar ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                                          |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| bucket | chave | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em negrito são recuperados)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="file-list-examples"></a>Exemplos de lista de arquivos

Esta seção descreve o comportamento resultante do uso do caminho da lista de arquivos na origem da atividade de cópia.

Supondo que você tenha a seguinte estrutura de pasta de origem e queira copiar os arquivos em negrito:

| Estrutura de origem de exemplo                                      | Conteúdo em FileListToCopy.txt                             | Configuração ADF                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadados<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **No conjunto de dados:**<br>– Bucket: `bucket`<br>– Caminho da pasta: `FolderA`<br><br>**Origem da atividade de cópia:**<br>– Caminho da lista de arquivos: `bucket/Metadata/FileListToCopy.txt` <br><br>O caminho da lista de arquivos aponta para um arquivo de texto no mesmo armazenamento de dados, que inclui uma lista de arquivos que você deseja copiar, um arquivo por linha, que é o caminho relativo para o caminho configurado no conjunto de dados. |

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de arquivo

Para saber detalhes sobre as propriedades, verifique [Atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades de atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique [Atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para saber mais detalhes sobre as propriedades, marque [Excluir atividade](delete-activity.md)

## <a name="legacy-models"></a>Modelos herdados

Se você estava usando o conector do Amazon S3 para copiar dados do Google Cloud Storage, ainda há suporte para compatibilidade com versões anteriores. É recomendável usar o novo modelo mencionado nas seções acima no futuro, e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados que têm suporte como origens e coletores na atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
