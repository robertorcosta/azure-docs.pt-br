---
title: Preservar metadados e ACLs usando a atividade de cópia na fábrica de dados do Azure
description: Saiba como preservar metadados e ACLs durante a cópia usando a atividade de cópia na Fábrica de Dados do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: 5ce1b85394a7bb604841f7fb941bdebf12c0bca2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414163"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Preservar metadados e ACLs usando a atividade de cópia na fábrica de dados do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Quando você usa a atividade de cópia do Azure Data Factory para copiar dados da origem para o afundamento, nos seguintes cenários, você também pode preservar os metadados e As ACLs junto.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Preservar metadados para migração do lago

Quando você migra dados de um lago de dados para outro, incluindo [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)e [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md)você pode optar por preservar os metadados do arquivo junto com os dados.

A atividade de cópia suporta a preservação dos seguintes atributos durante a cópia de dados:

- **Todos os metadados especificados pelo cliente** 
- E as **cinco propriedades do sistema incorporada do armazenamento de dados**a seguir: `contentType` `contentLanguage` (exceto amazon S3), `contentEncoding`, `contentDisposition`, . `cacheControl`

Quando você copia arquivos como está da Amazon S3/Azure Data Lake Storage Gen2/Azure Blob para Azure Data Lake Storage Gen2/Azure Blob com formato binário, você pode encontrar a opção **Preservar** na guia**Configurações de** **atividade** > de cópia para a criação de atividades ou a página **Configurações** na Ferramenta de Dados de Cópia.

![Copiar metadados de atividade](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Aqui está um exemplo da configuração JSON de atividade de cópia (veja `preserve`): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Preservar ACLs de Data Lake Storage Gen1/Gen2 para Gen2

Quando você faz upgrade do Azure Data Lake Storage Gen1 para o Gen2 ou copia dados entre o ADLS Gen2, você pode optar por preservar as listas de controle de acesso POSIX (ACLs) juntamente com arquivos de dados. Para obter mais informações sobre controle de acesso, consulte [controle de acesso no Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) e controle de acesso no [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

A atividade de cópia suporta a preservação dos seguintes tipos de ACLs durante a cópia de dados. Você pode selecionar um ou mais tipos:

- **ACL**: Copie e preserve listas de controle de acesso POSIX em arquivos e diretórios. Ele copia as ACLs completas existentes de origem para pia. 
- **Proprietário**: Copie e preserve o usuário proprietário de arquivos e diretórios. O acesso do superusuário ao sink Data Lake Storage Gen2 é necessário.
- **Grupo**: Copiar e preservar o grupo de arquivos e diretórios. O acesso do superusuário ao sink Data Lake Storage Gen2 ou ao usuário dono (se o usuário que possui também é um membro do grupo-alvo) é necessário.

Se você especificar para copiar a partir de uma pasta, data factory replicará as ACLs para a pasta dada e os arquivos e diretórios sob ela, se `recursive` for definido como verdadeiro. Se você especificar para copiar a partir de um único arquivo, as ACLs nesse arquivo serão copiadas.

>[!NOTE]
>Quando você usa ADF para preservar ACLs de Data Lake Storage Gen1/Gen2 para Gen2, as ACLs existentes na pasta/arquivos correspondentes do Sink Gen2 serão substituídas.

>[!IMPORTANT]
>Quando você optar por preservar As ACLs, certifique-se de conceder permissões altas o suficiente para que a Fábrica de Dados opere contra sua conta do Sink Data Lake Storage Gen2. Por exemplo, use a autenticação da chave da conta ou atribua a função Deproprietário de dados do Blob de armazenamento ao principal do serviço ou identidade gerenciada.

Quando você configura a origem como Data Lake Storage Gen1/Gen2 com formato binário ou a opção de cópia binária, e afunda como Data Lake Storage Gen2 com formato binário ou a opção de cópia binária, você pode encontrar a opção **Preservar** na página **Configurações** na Ferramenta copiar dados ou na guia **Configurações** > de atividade de cópia para a criação de**atividades.**

![Data Lake Storage Gen1/Gen2 para Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Aqui está um exemplo da configuração JSON de atividade de cópia (veja `preserve`): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Desempenho da atividade de cópia](copy-activity-performance.md)
