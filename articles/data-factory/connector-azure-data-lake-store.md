---
title: Copiar dados de ou para Azure Data Lake Storage Gen1
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
ms.date: 12/12/2019
ms.openlocfilehash: 4dae0d10f103710a0e6039127c5c1cacb63c03c4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893100"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Copiar dados de ou para Azure Data Lake Storage Gen1 usando Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-datalake-connector.md)
> * [Versão atual](connector-azure-data-lake-store.md)

Este artigo descreve como copiar dados de e para Azure Data Lake Storage Gen1. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de Azure Data Lake Storage Gen1 tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md) 
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, com esse conector, você pode:

- Copie arquivos usando um dos seguintes métodos de autenticação: entidade de serviço ou identidades gerenciadas para recursos do Azure.
- Copie os arquivos como estão ou analise ou gere arquivos com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).
- [Preserve as ACLs](#preserve-acls-to-data-lake-storage-gen2) ao copiar para o Azure data Lake Storage Gen2.

> [!IMPORTANT]
> Se você copiar dados usando o tempo de execução de integração auto-hospedado, configure o firewall corporativo para permitir o tráfego de saída para `<ADLS account name>.azuredatalakestore.net` e `login.microsoftonline.com/<tenant>/oauth2/token` na porta 443. O último é o Serviço de Token de Segurança do Azure com que o runtime de integração precisa se comunicar para obter o token de acesso.

## <a name="get-started"></a>Comece agora

> [!TIP]
> Para obter um passo a passo de como usar o conector de Azure Data Lake Store, consulte [carregar dados em Azure data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem informações sobre as propriedades que são usadas para definir Data Factory entidades específicas para Azure Data Lake Store.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Azure Data Lake Store:

| Propriedade | Description | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` deve ser definida como: **AzureDataLakeStore**. | Sim |
| dataLakeStoreUri | Informações sobre a conta do Azure Data Lake Store. Essas informações usam um dos seguintes formatos: `https://[accountname].azuredatalakestore.net/webhdfs/v1` ou `adl://[accountname].azuredatalakestore.net/`. | Sim |
| subscriptionId | A ID de assinatura do Azure à qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| resourceGroupName | O nome do grupo de recursos do Azure ao qual a conta do Data Lake Store pertence. | Obrigatório para coletor |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o tempo de execução de integração do Azure ou um tempo de execução de integração auto-hospedado se o armazenamento de dados estiver localizado em uma rede privada. Se essa propriedade não for especificada, o tempo de execução de integração do Azure padrão será usado. |Não |

### <a name="use-service-principal-authentication"></a>Usar a autenticação de entidade de serviço

Para usar a autenticação de entidade de serviço, siga estas etapas.

1. Registre uma entidade de aplicativo no Azure Active Directory e conceda a ela acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade de serviço. Veja exemplos de como a permissão funciona em Data Lake Storage Gen1 do [controle de acesso no Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte**: no **Data Explorer** > **acesso**, conceda pelo menos a permissão **executar** para todas as pastas upstream, incluindo a raiz, junto com a permissão de **leitura** para os arquivos a serem copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há nenhum requisito no controle de acesso no nível da conta (IAM).
    - **Como coletor**: no **Data Explorer** > **acesso**, conceda pelo menos a permissão **executar** para todas as pastas upstream, incluindo a raiz, junto com a permissão de **gravação** para a pasta do coletor. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (tanto a origem quanto o coletor estão na nuvem), em IAM, conceda pelo menos a função **leitor** para permitir que data Factory detecte a região para data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Por exemplo, se o Data Lake Store estiver em Europa Ocidental, crie um tempo de execução de integração do Azure com o local definido como "Europa Ocidental". Associe-os no serviço vinculado Data Lake Store, conforme mostrado no exemplo a seguir.

Há suporte para as seguintes propriedades:

| Propriedade | Description | Obrigatório |
|:--- |:--- |:--- |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque este campo como uma `SecureString` para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| locatário | Especifique as informações de locatário, como nome de domínio ou ID de locatário, sob a qual seu aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

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

### <a name="managed-identity"></a> Usar identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada diretamente para Data Lake Store autenticação, semelhante ao uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de ou para o seu Data Lake Store.

Para usar identidades gerenciadas para a autenticação de recursos do Azure, siga estas etapas.

1. [Recupere o data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor da "ID do aplicativo de identidade de serviço" gerado junto com sua fábrica.

2. Conceda acesso de identidade gerenciada a Data Lake Store. Veja exemplos de como a permissão funciona em Data Lake Storage Gen1 do [controle de acesso no Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **Como fonte**: no **Data Explorer** > **acesso**, conceda pelo menos a permissão **executar** para todas as pastas upstream, incluindo a raiz, junto com a permissão de **leitura** para os arquivos a serem copiados. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Não há nenhum requisito no controle de acesso no nível da conta (IAM).
    - **Como coletor**: no **Data Explorer** > **acesso**, conceda pelo menos a permissão **executar** para todas as pastas upstream, incluindo a raiz, junto com a permissão de **gravação** para a pasta do coletor. Você pode optar por adicionar a **Esta pasta e todos os filhos** para recursivo e adicionar como **uma permissão de acesso e uma entrada de permissão padrão**. Se você usar um tempo de execução de integração do Azure para copiar (tanto a origem quanto o coletor estão na nuvem), em IAM, conceda pelo menos a função **leitor** para permitir que data Factory detecte a região para data Lake Store. Se você quiser evitar essa função IAM, escolha explicitamente [criar um Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) com o local do Data Lake Store. Associe-os no serviço vinculado Data Lake Store, conforme mostrado no exemplo a seguir.

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

As propriedades a seguir têm suporte para Azure Data Lake Store Gen1 em configurações de `location` no conjunto de entrada baseado em formato:

| Propriedade   | Description                                                  | Obrigatório |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type em `location` no DataSet deve ser definida como **AzureDataLakeStoreLocation**. | Sim      |
| folderPath | O caminho para uma pasta. Se você quiser usar um curinga para filtrar pastas, ignore essa configuração e especifique-a nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o folderPath fornecido. Se você quiser usar um curinga para filtrar arquivos, ignore essa configuração e especifique-a nas configurações de origem da atividade. | Não       |

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

As propriedades a seguir têm suporte para Azure Data Lake Store Gen1 em configurações de `storeSettings` na fonte de cópia baseada em formato:

| Propriedade                 | Description                                                  | Obrigatório                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureDataLakeStoreReadSettings**. | Sim                                           |
| recursiva                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando recursivo é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga para filtrar as pastas de origem. <br>Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou este caractere de escape dentro de. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga sob o folderPath/wildcardFolderPath fornecido para filtrar os arquivos de origem. <br>Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou este caractere de escape dentro de. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime estão selecionados. Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime são selecionados. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

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

As propriedades a seguir têm suporte para Azure Data Lake Store Gen1 em configurações de `storeSettings` no coletor de cópia com base em formato:

| Propriedade                 | Description                                                  | Obrigatório |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureDataLakeStoreWriteSettings**. | Sim      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

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

| folderPath | fileName | recursiva | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Vazio, usar padrão) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | (Vazio, usar padrão) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Exemplos de comportamento da operação de cópia

Esta seção descreve o comportamento resultante da operação de Cópia para diferentes combinações de valores `recursive` e `copyBehavior`.

| recursiva | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5. |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 + arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo, com um nome de arquivo gerado automaticamente. |
| false |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |
| false |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;o conteúdo de file1 + arquivo2 é mesclado em um arquivo com o nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>Subpasta1 com arquivo3, Arquivo4 e Arquivo5 não são selecionados. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Preservar ACLs para Data Lake Storage Gen2

>[!TIP]
>Para copiar dados do Azure Data Lake Storage Gen1 para o Gen2 em geral, consulte [copiar dados de Azure data Lake Storage Gen1 para Gen2 com Azure data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) para obter uma orientação e práticas recomendadas.

Se você quiser replicar as listas de controle de acesso (ACLs) junto com os arquivos de dados ao atualizar de Data Lake Storage Gen1 para Data Lake Storage Gen2, consulte [preservar ACLs de data Lake Storage Gen1](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Ao transformar dados no fluxo de dados de mapeamento, você pode ler e gravar arquivos de Azure Data Lake Storage Gen1 no formato JSON, Avro, texto delimitado ou parquet. Para obter mais informações, consulte [transformação de origem](data-flow-source.md) e transformação de [coletor](data-flow-sink.md) no recurso de fluxo de dados de mapeamento.

### <a name="source-transformation"></a>Transformação de origem

Na transformação origem, você pode ler de um contêiner, pasta ou arquivo individual em Azure Data Lake Storage Gen1. A guia **Opções de origem** permite que você gerencie como os arquivos são lidos. 

![Opções de origem](media/data-flow/sourceOptions1.png "Opções de origem")

**Caminho curinga:** Usar um padrão curinga instruirá o ADF a executar um loop em cada pasta e arquivo correspondentes em uma única transformação de origem. Essa é uma maneira eficaz de processar vários arquivos dentro de um único fluxo. Adicione vários padrões de correspondência de curingas com o sinal de + que aparece ao passar o mouse sobre o padrão de curinga existente.

Em seu contêiner de origem, escolha uma série de arquivos que correspondem a um padrão. Somente o contêiner pode ser especificado no DataSet. O caminho curinga, portanto, também deve incluir o caminho da pasta da pasta raiz.

Exemplos de curinga:

* ```*``` representa qualquer conjunto de caracteres
* ```**``` representa o aninhamento de diretório recursivo
* ```?``` substitui um caractere
* ```[]``` corresponde a um ou mais caracteres entre colchetes

* ```/data/sales/**/*.csv``` Obtém todos os arquivos CSV em/data/Sales
* ```/data/sales/20??/**``` Obtém todos os arquivos no século 20
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtém todos os arquivos CSV em 2004 em dezembro, começando com X ou Y prefixados por um número de dois dígitos

**Caminho raiz da partição:** Se você tiver pastas particionadas em sua fonte de arquivo com um formato de ```key=value``` (por exemplo, Year = 2019), poderá atribuir o nível superior dessa árvore de pastas de partição a um nome de coluna no fluxo de dados do fluxo de dados.

Primeiro, defina um curinga para incluir todos os caminhos que são as pastas particionadas mais os arquivos folha que você deseja ler.

![Configurações do arquivo de origem da partição](media/data-flow/partfile2.png "Configuração do arquivo de partição")

Use a configuração caminho raiz da partição para definir qual é o nível superior da estrutura de pastas. Ao exibir o conteúdo de seus dados por meio de uma visualização de dados, você verá que o ADF adicionará as partições resolvidas encontradas em cada um dos níveis de pasta.

![Caminho raiz da partição](media/data-flow/partfile1.png "Visualização do caminho raiz da partição")

**Lista de arquivos:** Este é um conjunto de arquivos. Crie um arquivo de texto que inclua uma lista de arquivos de caminho relativo a serem processados. Aponte para este arquivo de texto.

**Coluna para armazenar o nome do arquivo:** Armazene o nome do arquivo de origem em uma coluna em seus dados. Insira um novo nome de coluna aqui para armazenar a cadeia de caracteres de nome de arquivo.

**Após a conclusão:** Escolha não fazer nada com o arquivo de origem após a execução do fluxo de dados, exclua o arquivo de origem ou mova o arquivo de origem. Os caminhos para a movimentação são relativos.

Para mover os arquivos de origem para outro local de pós-processamento, primeiro selecione "mover" para a operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando curingas para o caminho, a configuração "de" será a mesma pasta que a pasta de origem.

Se você tiver um caminho de origem com curinga, sua sintaxe terá a seguinte aparência:

```/data/sales/20??/**/*.csv```

Você pode especificar "from" como

```/data/sales```

E "para" como

```/backup/priorSales```

Nesse caso, todos os arquivos que foram originados em/data/Sales são movidos para/backup/priorSales.

> [!NOTE]
> As operações de arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (uma depuração de pipeline ou execução de execução) que usa a atividade executar fluxo de dados em um pipeline. As operações de arquivo *não são* executadas no modo de depuração de fluxo de dados.

**Filtrar por última modificação:** Você pode filtrar quais arquivos são processados especificando um intervalo de datas de quando eles foram modificados pela última vez. Todas as datas-hora estão em UTC. 

### <a name="sink-properties"></a>Propriedades do coletor

Na transformação do coletor, você pode gravar em um contêiner ou pasta em Azure Data Lake Storage Gen1. a guia **configurações** permite que você gerencie como os arquivos são gravados.

![opções de coletor](media/data-flow/file-sink-settings.png "opções de coletor")

**Limpe a pasta:** Determina se a pasta de destino é limpa ou não antes de os dados serem gravados.

**Opção de nome de arquivo:** Determina como os arquivos de destino são nomeados na pasta de destino. As opções de nome de arquivo são:
   * **Padrão**: permitir que o Spark nomeie arquivos com base em padrões de parte.
   * **Padrão**: Insira um padrão que enumere os arquivos de saída por partição. Por exemplo, **empréstimos [n]. csv** criará loans1. csv, loans2. csv e assim por diante.
   * **Por partição**: Insira um nome de arquivo por partição.
   * **Como dados na coluna**: defina o arquivo de saída para o valor de uma coluna. O caminho é relativo ao contêiner de conjunto de um, não à pasta de destino.
   * **Saída para um único arquivo**: Combine os arquivos de saída particionados em um único arquivo nomeado. O caminho é relativo à pasta do conjunto de um. Lembre-se de que a operação de mesclagem de te pode falhar com base no tamanho do nó. Essa opção não é recomendada para grandes conjuntos de altos.

**Cotar tudo:** Determina se todos os valores entre aspas devem ser delimitados

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="legacy-models"></a>Modelos herdados

>[!NOTE]
>Os modelos a seguir ainda têm suporte como estão para compatibilidade com versões anteriores. É recomendável usar o novo modelo mencionado nas seções acima no futuro e a interface do usuário de criação do ADF mudou para gerar o novo modelo.

### <a name="legacy-dataset-model"></a>Modelo de conjunto de DataSet herdado

| Propriedade | Description | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como **AzureDataLakeStoreFile**. |Sim |
| folderPath | Caminho para a pasta no Data Lake Store. Se não especificado, apontará para a raiz. <br/><br/>Há suporte para o filtro curinga. Os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere). Use `^` para escapar se o nome real da pasta tiver um curinga ou este caractere de escape dentro de. <br/><br/>Por exemplo: RootFolder/subfolder/. Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). |Não |
| fileName | Filtro de nome ou curinga para os arquivos sob o "folderPath" especificado. Se você não especificar um valor para essa propriedade, o conjunto de dados apontará para todos os arquivos na pasta. <br/><br/>Para o filtro, os curingas permitidos são `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou a um único caractere).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para escapar se o nome de arquivo real tiver um curinga ou este caractere de escape dentro de.<br/><br/>Quando fileName não é especificado para um conjunto de dados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do arquivo com o seguinte padrão: "*Data. [ GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo," Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz ". Se você copiar de uma fonte de tabela usando um nome de tabela em vez de uma consulta, o padrão de nome será " *[nome da tabela]. [ formato]. [compactação se configurada]* ", por exemplo," MyTable. csv ". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime estão selecionados. Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime são selecionados.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo modificado pela última vez. Os arquivos serão selecionados se a hora da última modificação estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário UTC no formato "2018-12-01T05:00:00Z". <br/><br/> O desempenho geral da movimentação de dados é afetado pela habilitação dessa configuração quando você deseja fazer o filtro de arquivo com grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo é aplicado ao conjunto de valores. Quando `modifiedDatetimeStart` tem um valor DateTime, mas `modifiedDatetimeEnd` é NULL, isso significa que os arquivos cujo último atributo modificado é maior ou igual ao valor DateTime estão selecionados. Quando `modifiedDatetimeEnd` tem um valor DateTime, mas `modifiedDatetimeStart` é NULL, isso significa que os arquivos cujo último atributo modificado é menor do que o valor DateTime são selecionados.| Não |
| format | Se você quiser copiar arquivos como estão entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas definições de conjunto de dados de entrada e saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os arquivos em uma pasta, especifique **folderPath** somente.<br>Para copiar um único arquivo com um nome específico, especifique **FolderPath** com uma parte da pasta **e o nome do** arquivo com um nome de arquivos.<br>Para copiar um subconjunto de arquivos em uma pasta, especifique **FolderPath** com uma parte de pasta e um **nome de arquivo** com um filtro curinga. 

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

### <a name="legacy-copy-activity-source-model"></a>Modelo de origem da atividade de cópia herdada

| Propriedade | Description | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` da fonte da atividade de cópia deve ser definida como **AzureDataLakeStoreSource**. |Sim |
| recursiva | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Quando `recursive` é definido como true e o coletor é um armazenamento baseado em arquivo, uma pasta ou subpasta vazia não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

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

### <a name="legacy-copy-activity-sink-model"></a>Modelo de coletor de atividade de cópia herdado

| Propriedade | Description | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade `type` do coletor da atividade de cópia deve ser definida como **AzureDataLakeStoreSink**. |Sim |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, o nome do arquivo será gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões para se conectar ao repositório de dados simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

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

## <a name="next-steps"></a>Próximos passos

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
