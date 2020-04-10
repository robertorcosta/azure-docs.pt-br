---
title: Copiar e transformar dados no Azure Data Lake Storage Gen2
description: Aprenda a copiar dados para e a partir do Azure Data Lake Storage Gen2 e transforme dados no Azure Data Lake Storage Gen2 usando o Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: a061df302680488377c3006dcef57b818a101d67
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011524"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Copiar e transformar dados no Azure Data Lake Storage Gen2 usando a fábrica de dados do Azure

O Azure Data Lake Storage Gen2 (ADLS Gen2) é um conjunto de recursos dedicados à análise de big data incorporada ao [armazenamento Azure Blob.](../storage/blobs/storage-blobs-introduction.md) Você pode usá-lo para interagir com seus dados usando paradigmas de armazenamento de arquivos e objetos.

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados de e para o Azure Data Lake Storage Gen2 e usar o Fluxo de Dados para transformar dados no Azure Data Lake Storage Gen2. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

>[!TIP]
>Para o cenário de migração de data lake ou data warehouse, saiba mais na Fábrica de [Dados use o Azure para migrar dados do seu lago de dados ou data warehouse para o Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Azure Data Lake Storage Gen2 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Para a atividade Copiar, com este conector você pode:

- Copie dados de/para O Zure Data Lake Storage Gen2 usando a chave da conta, o principal de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar arquivos como está ou analisar ou gerar arquivos com [formatos de arquivo suportados e codecs de compactação](supported-file-formats-and-compression-codecs.md).
- [Preservar metadados de arquivo durante a cópia](#preserve-metadata-during-copy).
- [Preserve As ACLs](#preserve-acls) ao copiar do Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Se você **habilitar os serviços confiáveis** da Microsoft para acessar essa opção de conta de armazenamento nas configurações de firewall do Azure Storage e quiser usar o tempo de execução de integração do Azure para se conectar ao Data Lake Storage Gen2, você deve usar [a autenticação de identidade gerenciada](#managed-identity) para o ADLS Gen2.


## <a name="get-started"></a>Introdução

>[!TIP]
>Para obter um passo a passo de como usar o conector Data Lake Storage Gen2, consulte [Os dados de carga no Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre propriedades usadas para definir entidades da Fábrica de Dados específicas do Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector Azure Data Lake Storage Gen2 suporta os seguintes tipos de autenticação. Consulte as seções correspondentes para obter detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação principal do serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>Ao usar o PolyBase para carregar dados no SQL Data Warehouse, se o data lake storage gen2 de origem estiver configurado com o ponto final da Rede Virtual, você deve usar a autenticação de identidade gerenciada conforme exigido pelo PolyBase. Consulte a seção [de autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| accountKey | Chave da conta para Data Lake Storage Gen2. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração padrão do Azure será usado. |Não |

>[!NOTE]
>O ponto final do sistema de arquivos ADLS secundário não é suportado ao usar a autenticação da chave da conta. Você pode usar outros tipos de autenticação.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para usar a autenticação principal do serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo as etapas do [Registro de sua aplicação com um inquilino Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda ao diretor de serviço a devida permissão. Veja exemplos de como a permissão funciona no Data Lake Storage Gen2 a partir de listas de controle de [acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Como fonte:** No Storage Explorer, conceda pelo menos a permissão **Execute** para TODAS as pastas upstream e para o sistema de arquivos, juntamente com a permissão **de Leitura** para que os arquivos copiem. Alternativamente, no controle de acesso (IAM), conceda pelo menos a função **de Leitor de Dados blob de armazenamento.**
    - **Como sink**: No Storage Explorer, conceda pelo menos **execute** a permissão para TODAS as pastas upstream e para o sistema de arquivos, juntamente com a permissão **write** para a pasta sink. Alternativamente, no controle de acesso (IAM), conceda pelo menos a função **de Contribuinte de Dados blob de armazenamento.**

>[!NOTE]
>Se você usar a UI da Fábrica de Dados para autor e o principal de serviço não estiver definido com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer pastas de conexão de teste ou navegação/navegação, escolha "Testar conexão com o caminho do arquivo" ou "Navegar a partir do caminho especificado" e especifique um caminho com a permissão **Read + Execute** para continuar.

Essas propriedades são suportadas para o serviço vinculado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este `SecureString` campo como um para armazená-lo com segurança na Fábrica de Dados. Ou, você pode [referenciar um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Recupere-o pairando o mouse no canto superior direito do portal Azure. | Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver em uma rede privada. Se não for especificado, o tempo de execução de integração padrão do Azure é usado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar diretamente essa identidade gerenciada para autenticação Data Lake Storage Gen2, semelhante ao uso do seu próprio diretor de serviço. Ele permite que esta fábrica designada acesse e copie dados para ou a partir de seu Data Lake Storage Gen2.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga essas etapas.

1. [Recupere as informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) da Fábrica de Dados copiando o valor do **ID** do objeto de identidade gerenciado gerado junto com sua fábrica.

2. Conceda a identidade gerenciada permissão adequada. Veja exemplos de como a permissão funciona no Data Lake Storage Gen2 a partir de listas de controle de [acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Como fonte:** No Storage Explorer, conceda pelo menos a permissão **Execute** para TODAS as pastas upstream e para o sistema de arquivos, juntamente com a permissão **de Leitura** para que os arquivos copiem. Alternativamente, no controle de acesso (IAM), conceda pelo menos a função **de Leitor de Dados blob de armazenamento.**
    - **Como sink**: No Storage Explorer, conceda pelo menos **execute** a permissão para TODAS as pastas upstream e para o sistema de arquivos, juntamente com a permissão **write** para a pasta sink. Alternativamente, no controle de acesso (IAM), conceda pelo menos a função **de Contribuinte de Dados blob de armazenamento.**

>[!NOTE]
>Se você usar a UI da Fábrica de Dados para autor e a identidade gerenciada não estiver definida com a função "Storage Blob Data Reader/Contributor" no IAM, ao fazer pastas de conexão de teste ou navegação/navegação, escolha "Testar conexão com o caminho do arquivo" ou "Navegar a partir do caminho especificado" e especifique um caminho com permissão **Read + Execute** para continuar.

>[!IMPORTANT]
>Se você usar o PolyBase para carregar dados do Data Lake Storage Gen2 no SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para data lake storage Gen2, certifique-se de também seguir as etapas 1 e 2 [nesta orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) para 1) registre seu servidor de banco de dados SQL com o Azure Active Directory (Azure AD) e 2) atribua a função de Contribuinte de Dados do Blob de Armazenamento ao seu servidor de banco de dados SQL; o resto é tratado pela Data Factory. Se o Data Lake Storage Gen2 estiver configurado com um ponto final da Rede Virtual Do Azure, para usar o PolyBase para carregar dados a partir dele, você deve usar a autenticação de identidade gerenciada conforme exigido pelo PolyBase.

Essas propriedades são suportadas para o serviço vinculado:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como **AzureBlobFS**. |Sim |
| url | Ponto final para Data Lake Storage `https://<accountname>.dfs.core.windows.net`Gen2 com o padrão de . | Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver em uma rede privada. Se não for especificado, o tempo de execução de integração padrão do Azure é usado. |Não |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `location` Data Lake Storage Gen2 em configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade `location` de tipo no conjunto de dados deve ser definida **como AzureBlobFSLocation**. | Sim      |
| Filesystem | O nome do sistema de arquivos Data Lake Storage Gen2.                              | Não       |
| folderPath | O caminho para uma pasta sob o sistema de arquivos dado. Se você quiser usar um curinga para filtrar pastas, pule essa configuração e especifique-a nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo em fileSystem + folderPath. Se você quiser usar um curinga para filtrar arquivos, pule essa configuração e especifique-a nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [Configurações](copy-activity-overview.md#configuration) de atividade de copiar e [pipelines e atividades](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades com suporte pelo coletor e origem do Data Lake Store Gen2.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 como um tipo de origem

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `storeSettings` Data Lake Storage Gen2 em configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade `storeSettings` do tipo em baixo deve ser definida **como AzureBlobFSReadSettings**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em arquivos, uma pasta ou subpasta vazia não é copiada ou criada na pia. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| curingaFolderPath       | O caminho da pasta com caracteres curinga sob o sistema de arquivos dado configurado no conjunto de dados para filtrar pastas de origem. <br>Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga dentro. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| curingaNome de arquivo         | O nome do arquivo com caracteres curinga sob o sistema de arquivos dado + folderPath/curingaFolderPath para filtrar arquivos de origem. <br>Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga dentro. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor da data são selecionados. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
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
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Store Gen2 como um tipo de coletor

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para `storeSettings` Data Lake Storage Gen2 em configurações no dissipador de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade `storeSettings` do tipo em baixo deve ser definida **como AzureBlobFSWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| blockSizeInMB | Especifique o tamanho do bloco em MB usado para gravar dados no ADLS Gen2. Saiba mais [sobre Block Blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>O valor permitido é **entre 4 e 100 MB**. <br/>Por padrão, o ADF determina automaticamente o tamanho do bloco com base no tipo e dados do seu armazenamento de origem. Para cópia não binária no ADLS Gen2, o tamanho padrão do bloco é de 100 MB para se encaixar no máximo dados de 4,95 TB. Pode não ser ideal quando seus dados não são grandes, especialmente quando você usa o Tempo de Execução de Integração Auto-hospedado com uma rede ruim, resultando em tempo de operação ou problema de desempenho. Você pode especificar explicitamente um tamanho de bloco, enquanto garantir que o blocoSizeInMB*50000 é grande o suficiente para armazenar os dados, caso contrário, a execução da atividade de cópia falhará. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, use padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (Vazio, use padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursivos e copyBehavior.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não é pego. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não é pego. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subfolder1 com File3, File4 e File5 não é pego. |

## <a name="preserve-metadata-during-copy"></a>Preservar metadados durante a cópia

Quando você copia arquivos do Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 para o Azure Data Lake Storage Gen2/Azure Blob, você pode optar por preservar os metadados do arquivo junto com os dados. Saiba mais com [os metadados preserve](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Preservar ACLs do Data Lake Storage Gen1/Gen2

Quando você copia arquivos do Azure Data Lake Storage Gen1/Gen2 para o Gen2, você pode optar por preservar as listas de controle de acesso POSIX (ACLs) juntamente com os dados. Saiba mais em [Preserve ACLs de Data Lake Storage Gen1/Gen2 para Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2 em geral, consulte [Copiar dados do Azure Data Lake Storage Gen1 para gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter um passo a passo e práticas recomendadas.

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar arquivos do Azure Data Lake Storage Gen2 em formato JSON, Avro, Delimited Text ou Parquet. Para obter mais informações, consulte [a transformação da fonte](data-flow-source.md) e a [transformação](data-flow-sink.md) do sink no recurso de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação de origem, você pode ler a partir de um contêiner, pasta ou arquivo individual no Azure Data Lake Storage Gen2. A guia **Deops** Source permite gerenciar como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho curinga:** O uso de um padrão curinga instruirá o ADF a fazer um loop em cada pasta e arquivo correspondentes em uma única transformação de Origem. Esta é uma maneira eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência curinga com o sinal + que aparece ao pairar sobre o padrão curinga existente.

No seu contêiner de origem, escolha uma série de arquivos que correspondam a um padrão. Somente o contêiner pode ser especificado no conjunto de dados. Seu caminho curinga deve, portanto, incluir também o caminho da pasta a partir da pasta raiz.

Exemplos curingas:

* ```*```Representa qualquer conjunto de caracteres
* ```**```Representa aninhamento de diretório recursivo
* ```?```Substitui um caractere
* ```[]```Corresponde a um dos mais personagens nos suportes

* ```/data/sales/**/*.csv```Recebe todos os arquivos csv sob /data/vendas
* ```/data/sales/20??/**/```Recebe todos os arquivos do século 20
* ```/data/sales/*/*/*.csv```Recebe arquivos csv dois níveis em /data/vendas
* ```/data/sales/2004/*/12/[XY]1?.csv```Recebe todos os arquivos csv em 2004 em dezembro começando com X ou Y prefixados por um número de dois dígitos

**Caminho de raiz de partição:** Se você tiver pastas particionadas na fonte do arquivo com um ```key=value``` formato (por exemplo, ano=2019), então você pode atribuir o nível superior dessa árvore de pasta de partição a um nome de coluna no fluxo de dados do fluxo de dados.

Primeiro, defina um curinga para incluir todos os caminhos que são as pastas particionadas mais os arquivos de folha que você deseja ler.

![Configurações do arquivo de origem de partição](media/data-flow/partfile2.png "Configuração do arquivo de partição")

Use a configuração 'Caminho de raiz de partição' para definir qual é o nível superior da estrutura da pasta. Quando você visualiza o conteúdo de seus dados através de uma visualização de dados, você verá que o ADF adicionará as partições resolvidas encontradas em cada um dos níveis de suas pastas.

![Caminho raiz de partição](media/data-flow/partfile1.png "Visualização do caminho da raiz da partição")

**Lista de arquivos:** Este é um conjunto de arquivos. Crie um arquivo de texto que inclua uma lista de arquivos de caminho relativos para processar. Aponte para este arquivo de texto.

**Coluna para armazenar nome do arquivo:** Armazene o nome do arquivo de origem em uma coluna em seus dados. Digite um novo nome de coluna aqui para armazenar a seqüência de nomes do arquivo.

**Após a conclusão:** Escolha não fazer nada com o arquivo de origem após a corrida do fluxo de dados, exclua o arquivo de origem ou mova o arquivo de origem. Os caminhos para a mudança são relativos.

Para mover arquivos de origem para outro local após o processamento, primeiro selecione "Mover" para operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando nenhum curinga para o seu caminho, então a configuração "de" será a mesma pasta que sua pasta de origem.

Se você tem um caminho de origem com curinga, sua sintaxe será assim abaixo:

```/data/sales/20??/**/*.csv```

Você pode especificar "de" como

```/data/sales```

E "para" como

```/backup/priorSales```

Neste caso, todos os arquivos que foram originados em /data/vendas são movidos para /backup/priorSales.

> [!NOTE]
> As operações de arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (uma depuração ou execução de pipeline) que usa a atividade Execute Data Flow em um pipeline. As operações de *arquivo não são* executadas no modo de depuração do fluxo de dados.

**Filtrar por último modificado:** Você pode filtrar quais arquivos você processa especificando um intervalo de data de quando eles foram modificados pela última vez. Todos os horários de data estão em UTC. 

### <a name="sink-properties"></a>Propriedades do dissipador

Na transformação da pia, você pode escrever para um contêiner ou pasta no Azure Data Lake Storage Gen2. a guia **Configurações** permite gerenciar como os arquivos são gravados.

![opções de pia](media/data-flow/file-sink-settings.png "opções de pia")

**Limpe a pasta:** Determina se a pasta de destino é ou não limpa antes que os dados sejam gravados.

**Opção nome do arquivo:** Determina como os arquivos de destino são nomeados na pasta de destino. As opções de nome do arquivo são:
   * **Padrão**: Permitir que o Spark nomeie arquivos com base em padrões PART.
   * **Padrão**: Digite um padrão que enumera seus arquivos de saída por partição. Por exemplo, **os empréstimos[n].csv** criarão empréstimos1.csv, empréstimos2.csv, e assim por diante.
   * **Por partição**: Digite um nome de arquivo por partição.
   * **Como dados na coluna**: Defina o arquivo de saída para o valor de uma coluna. O caminho é relativo ao contêiner do conjunto de dados, não à pasta de destino. Se você tiver um caminho de pasta em seu conjunto de dados, ele será substituído.
   * **Saída para um único arquivo**: Combine os arquivos de saída particionados em um único arquivo nomeado. O caminho é relativo à pasta conjunto de dados. Por favor, esteja ciente de que a operação de fusão te pode possivelmente falhar com base no tamanho do nó. Esta opção não é recomendada para grandes conjuntos de dados.

**Citar todos:** Determina se deve envolver todos os valores entre aspas

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Obter propriedades de atividade do Metadata

Para saber detalhes sobre as propriedades, verifique a [atividade getMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [a atividade Excluir](delete-activity.md)

## <a name="legacy-models"></a>Modelos legados

>[!NOTE]
>Os modelos a seguir ainda são suportados como-é para compatibilidade retrógrada. Sugere-se que você use o novo modelo mencionado nas seções acima daqui para frente, e a ui de autoria da ADF passou a gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de dados legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **AzureBlobFSFile**. |Sim |
| folderPath | Caminho para a pasta em Data Lake Storage Gen2. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga é suportado. Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga estiver dentro. <br/><br/>Exemplos: sistema de arquivos/pasta/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro curinga para os arquivos sob o "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` `?` (corresponde a zero ou mais caracteres) e (corresponde a zero ou único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de arquivo real tem um curinga ou este char de fuga está dentro.<br/><br/>Quando o arquivoName não é especificado para um conjunto de dados de saída e **preservaA hierarquia** não é especificada no dissipador de atividades, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte padrão: "*Dados.[ executar atividade ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compactação se configurada]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar de uma fonte tabular usando um nome de tabela em vez de uma consulta, o padrão de nome é "*[nome da tabela].[ formato]. [compactação se configurada]*", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivos com enormes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor de data-hora são selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivos com enormes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor de data-hora são selecionados.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **do tipo** em **formato** para um desses valores. Para obter mais informações, consulte o [formato Text,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e seções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome dado, especifique **folderPath** com uma parte de pasta e **arquivoNome** com um nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com uma parte de pasta e **fileName** com um filtro curinga. 

**Exemplo:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem de atividade de cópia legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da origem da atividade de cópia deve ser definida como **AzureBlobFSSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em arquivos, uma pasta ou subpasta vazia não é copiada ou criada na pia.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>Modelo de pia de atividade de cópia legado

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do coletor de atividade de cópia deve ser definida como **AzureBlobFSSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
