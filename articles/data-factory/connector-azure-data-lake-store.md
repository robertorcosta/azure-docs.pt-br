---
title: Copiar dados para ou de Azure Data Lake Storage Gen1
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Azure Data Lake Store ou do Data Lake Store para armazenamentos de coletor com suporte usando o Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: a8ba8b212a504a8f8e4e29fbd50126189998e81a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065474"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados para ou a partir do Azure Data Lake Storage Gen1 usando a fábrica de dados do Azure

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como copiar dados para e de Azure Data Lake Storage Gen1. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Azure Data Lake Storage Gen1 é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md) 
- [Mapeamento do fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, com este conector você pode:

- Copiar arquivos usando um dos seguintes métodos de autenticação: principal de serviço ou identidades gerenciadas para recursos do Azure.
- Copiar arquivos como é ou analisar ou gerar arquivos com os [formatos de arquivo suportados e codecs de compactação](supported-file-formats-and-compression-codecs.md).
- [Preserve As ACLs](#preserve-acls-to-data-lake-storage-gen2) ao copiar no Azure Data Lake Storage Gen2.

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução de integração auto-hospedado, configure o firewall corporativo para permitir tráfego de saída para e `<ADLS account name>.azuredatalakestore.net` `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Token de Segurança do Azure com que o runtime de integração precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Introdução

> [!TIP]
> Para obter um passo a passo de como usar o conector Azure Data Lake Store, consulte [Os dados de carga no Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre propriedades que são usadas para definir entidades da Fábrica de Dados específicas do Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` deve ser definida como: **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | A ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | O nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o seu armazenamento de dados estiver localizado em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração padrão do Azure será usado. |Não |

### <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação principal do serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory e conceda-lhe acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda ao diretor de serviço a devida permissão. Veja exemplos de como a permissão funciona no Data Lake Storage Gen1 a partir do controle de [acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte:** No > **Acesso**ao explorador **de dados,** conceda pelo menos **execute** a permissão para todas as pastas upstream, incluindo a raiz, juntamente com a permissão **De Leitura** para que os arquivos sejam copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há necessidade de controle de acesso em nível de conta (IAM).
    - **Como sink**: No**Acesso** **ao explorador de** > dados, conceda pelo menos **execute** a permissão para todas as pastas upstream, incluindo a raiz, juntamente com a permissão **de gravação** para a pasta sink. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (tanto a origem quanto o dissipador estão na nuvem), no IAM, conceda pelo menos a função **Reader** para permitir que a Fábrica de Dados detecte a região para o Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Por exemplo, se o data lake store estiver na Europa Ocidental, crie um tempo de execução de integração do Azure com a localização definida como "Europa Ocidental". Associá-los no serviço vinculado ao Data Lake Store, conforme mostrado no exemplo a seguir.

Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma `SecureString` para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações do inquilino, como nome de domínio ou ID do inquilino, a qual seu aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar diretamente essa identidade gerenciada para autenticação do Data Lake Store, semelhante ao uso do seu próprio diretor de serviço. Ele permite que este alocador designado acesse e copie dados de ou para o seu Data Lake Store.

Para usar identidades gerenciadas para autenticação de recursos do Azure, siga essas etapas.

1. [Recupere as informações de identidade gerenciadas da fábrica](data-factory-service-identity.md#retrieve-managed-identity) de dados copiando o valor do "ID de aplicativo de identidade de serviço" gerado junto com sua fábrica.

2. Conceda o acesso de identidade gerenciado à Data Lake Store. Veja exemplos de como a permissão funciona no Data Lake Storage Gen1 a partir do controle de [acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte:** No > **Acesso**ao explorador **de dados,** conceda pelo menos **execute** a permissão para todas as pastas upstream, incluindo a raiz, juntamente com a permissão **De Leitura** para que os arquivos sejam copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há necessidade de controle de acesso em nível de conta (IAM).
    - **Como sink**: No**Acesso** **ao explorador de** > dados, conceda pelo menos **execute** a permissão para todas as pastas upstream, incluindo a raiz, juntamente com a permissão **de gravação** para a pasta sink. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (tanto a origem quanto o dissipador estão na nuvem), no IAM, conceda pelo menos a função **Reader** para permitir que a Fábrica de Dados detecte a região para o Data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Associá-los no serviço vinculado ao Data Lake Store, conforme mostrado no exemplo a seguir.

No Azure Data Factory, você não precisa especificar nenhuma propriedade além das informações do Data Lake Store gerais no serviço vinculado.

**Exemplo:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o `location` Azure Data Lake Store Gen1 em configurações no conjunto de dados baseado em formato:

| Propriedade   | Descrição                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade `location` de tipo no conjunto de dados deve ser definida como **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para uma pasta. Se você quiser usar um curinga para filtrar pastas, pule essa configuração e especifique-a nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo a pastadaPath. Se você quiser usar um curinga para filtrar arquivos, pule essa configuração e especifique-a nas configurações de origem da atividade. | Não       |

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor do Azure Data Lake Store.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store como fonte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o `storeSettings` Azure Data Lake Store Gen1 em configurações na fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade `storeSettings` do tipo em baixo deve ser definida **como AzureDataLakeStoreReadSettings**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando o recursivo é definido como verdadeiro e a pia é uma loja baseada em arquivos, uma pasta ou subpasta vazia não é copiada ou criada na pia. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| curingaFolderPath       | O caminho da pasta com caracteres curinga para filtrar pastas de origem. <br>Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga dentro. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| curingaNome de arquivo         | O nome do arquivo com caracteres curinga a pastadaPath/curingaFolderPath para filtrar arquivos de origem. <br>Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga dentro. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim, `fileName` se não for especificado no conjunto de dados |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor de data-hora são selecionados. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não                                            |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSettings",
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

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store como coletor

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

As seguintes propriedades são suportadas para o `storeSettings` Azure Data Lake Store Gen1 em configurações no dissipador de cópia baseado em formato:

| Propriedade                 | Descrição                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade `storeSettings` do tipo em baixo deve ser definida como **AzureDataLakeStoreWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| expiraçãoDateTime | Especifica o tempo de validade dos arquivos escritos. O tempo é aplicado ao tempo UTC no formato "2020-03-01T08:00:00Z". Por padrão, é NULL, o que significa que os arquivos escritos nunca expiram. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não       |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSettings",
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

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta seção descreve o comportamento resultante da operação de Cópia para diferentes combinações de valores `recursive` e `copyBehavior`.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 contentes são mesclados em um arquivo, com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não são pegos. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subfolder1 com File3, File4 e File5 não são pegos. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + O conteúdo do File2 é mesclado em um arquivo com nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subfolder1 com File3, File4 e File5 não são pegos. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar ACLs para Data Lake Storage Gen2

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2 em geral, consulte [Copiar dados do Azure Data Lake Storage Gen1 para gen2 com a Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter um passo a passo e práticas recomendadas.

Se você quiser replicar as listas de controle de acesso (ACLs) juntamente com arquivos de dados quando você atualizar de Data Lake Storage Gen1 para Data Lake Storage Gen2, consulte [Preservar ACLs do Data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapeamento de propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar arquivos do Azure Data Lake Storage Gen1 no formato JSON, Avro, Delimited Text ou Parquet. Para obter mais informações, consulte [a transformação da fonte](data-flow-source.md) e a [transformação](data-flow-sink.md) do sink no recurso de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação de origem, você pode ler a partir de um contêiner, pasta ou arquivo individual no Azure Data Lake Storage Gen1. A guia **Deops** Source permite gerenciar como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho curinga:** O uso de um padrão curinga instruirá o ADF a fazer um loop em cada pasta e arquivo correspondentes em uma única transformação de Origem. Esta é uma maneira eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência curinga com o sinal + que aparece ao pairar sobre o padrão curinga existente.

No seu contêiner de origem, escolha uma série de arquivos que correspondam a um padrão. Somente o contêiner pode ser especificado no conjunto de dados. Seu caminho curinga deve, portanto, incluir também o caminho da pasta a partir da pasta raiz.

Exemplos curingas:

* ```*```Representa qualquer conjunto de caracteres
* ```**```Representa aninhamento de diretório recursivo
* ```?```Substitui um caractere
* ```[]```Corresponde a um dos mais personagens nos suportes

* ```/data/sales/**/*.csv```Recebe todos os arquivos csv /data/vendas
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

Na transformação da pia, você pode escrever para um contêiner ou pasta no Azure Data Lake Storage Gen1. a guia **Configurações** permite gerenciar como os arquivos são gravados.

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
| type | A propriedade do tipo do conjunto de dados deve ser definida **como AzureDataLakeStoreFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não especificado, apontará para a raiz. <br/><br/>O filtro curinga é suportado. Curingas `*` permitidos são (corresponde `?` a zero ou mais caracteres) e (corresponde a zero ou único caractere). Use `^` para escapar se o nome da sua pasta real tiver um curinga ou este char de fuga dentro. <br/><br/>Por exemplo: rootfolder/subfolder/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Nome ou filtro curinga para os arquivos o folderPath especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para filtro, os curingas permitidos são `*` `?` (corresponde a zero ou mais caracteres) e (corresponde a zero ou único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o seu nome de arquivo real tem um curinga ou este char de fuga dentro.<br/><br/>Quando o arquivoName não é especificado para um conjunto de dados de saída e **preservaA hierarquia** não é especificada no dissipador de atividades, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte padrão: "*Dados.[ executar atividade ID GUID]. [GUID se FlattenHierarchy]. [formato se configurado]. [compactação se configurada]*", por exemplo, "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Se você copiar de uma fonte tabular usando um nome de tabela em vez de uma consulta, o padrão de nome é "*[nome da tabela].[ formato]. [compactação se configurada]*", por exemplo, "MyTable.csv". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivos com enormes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor de data-hora são selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo Last Modified. Os arquivos são selecionados se o último `modifiedDatetimeStart` tempo `modifiedDatetimeEnd`modificado estiver dentro do intervalo de tempo entre e . O horário é aplicado ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivos com enormes quantidades de arquivos. <br/><br/> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de dados. Quando `modifiedDatetimeStart` tem um valor `modifiedDatetimeEnd` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é maior ou igual ao valor da data são selecionados. Quando `modifiedDatetimeEnd` tem um valor `modifiedDatetimeStart` de data-hora, mas é NULO, significa que os arquivos cujo último atributo modificado é menor do que o valor de data-hora são selecionados.| Não |
| format | Se você quiser copiar arquivos como está entre lojas baseadas em arquivos (cópia binária), pule a seção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **do tipo** em **formato** para um desses valores. Para obter mais informações, consulte o [formato Text](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formato JSON,](supported-file-formats-and-compression-codecs-legacy.md#json-format) [formato Avro,](supported-file-formats-and-compression-codecs-legacy.md#avro-format) [formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format)e seções [de formato Parquet.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome específico, especifique **folderPath** com uma parte de pasta e **arquivoNome** com um nome de arquivo.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **folderPath** com uma parte de pasta e **fileName** com um filtro curinga. 

**Exemplo:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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
| type | A `type` propriedade da fonte de atividade de cópia deve ser definida **como AzureDataLakeStoreSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando `recursive` é definido como verdadeiro e a pia é uma loja baseada em arquivos, uma pasta ou subpasta vazia não é copiada ou criada na pia. Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
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
| type | A `type` propriedade do dissipador de atividadede cópia deve ser definida **como AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao armazenamento de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea ao armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
