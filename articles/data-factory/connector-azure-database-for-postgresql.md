---
title: Copiar dados de e para o Banco de Dados Do Azure para PostgreSQL
description: Aprenda a copiar dados de e para o Banco de Dados Azure para PostgreSQL usando uma atividade de cópia em um pipeline da Fábrica de Dados Do Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444385"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Copiar dados de e para o Banco de Dados Do Azure para PostgreSQL usando a Fábrica de Dados do Azure

Este artigo descreve como usar o recurso de atividade de cópia na Fábrica de Dados Do Azure para copiar dados do Banco de Dados Azure para PostgreSQL. Ele se baseia na atividade Copiar no artigo [da Fábrica de Dados Do Azure,](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

Este conector é especializado para o [Banco de Dados Azure para o serviço PostgreSQL](../postgresql/overview.md). Para copiar dados de um banco de dados postgresql genérico localizado no local ou na nuvem, use o [conector PostgreSQL](connector-postgresql.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este banco de dados Azure para conector PostgreSQL é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com uma [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Banco de Dados do Azure para PostgreSQL para qualquer armazenamento de dados de coletor com suporte. Ou, você pode copiar dados de qualquer armazenamento de dados de origem suportada para o Banco de Dados Azure para PostgreSQL. Para obter uma lista de armazenamentos de dados que a atividade de cópia suporta como fontes e sumidouros, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

O Azure Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente nenhum driver para usar este conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir oferecem detalhes sobre propriedades que são usadas para definir entidades de fábrica de dados específicas do Azure Database para conector PostgreSQL.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o banco de dados Azure para serviço vinculado ao PostgreSQL:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida **como: AzurePostgreSql**. | Sim |
| connectionString | Uma cadeia de conexão ODBC para se conectar ao Banco de Dados do Azure para PostgreSQL.<br/>Você também pode colocar uma senha no Azure Key Vault e puxar a `password` configuração para fora da seqüência de conexão. Veja as amostras a seguir e [as credenciais da Loja no Azure Key Vault](store-credentials-in-key-vault.md) para obter mais detalhes. | Sim |
| connectVia | Esta propriedade representa o [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Uma cadeia de conexão válida é `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Aqui estão mais propriedades que você pode definir por seu caso:

| Propriedade | Descrição | Opções | Obrigatório |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| O método que o driver usa para criptografar dados enviados entre o driver e o servidor de banco de dados. Por exemplo, `EncryptionMethod=<0/1/6>;`| 0 (Sem criptografia) **(Padrão)** / 1 (SSL) / 6 (RequestSSL) | Não |
| ValidateServerCertificate (VSC) | Determina se o driver valida o certificado enviado pelo servidor de banco de dados quando a criptografia SSL está ativada (Método de criptografia=1). Por exemplo, `ValidateServerCertificate=<0/1>;`| 0 (Desabilitado) **(Padrão)** / 1 (Habilitado) | Não |

**Exemplo**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Exemplo**:

***Armazenar senha no Azure Key Vault***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados na Fábrica de Dados do Azure](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades que o Banco de Dados Azure para PostgreSQL suporta em conjuntos de dados.

Para copiar dados de/para o Banco de Dados do Azure para PostgreSQL, defina o tipo da propriedade do conjunto de dados como **AzurePostgreSqlTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida **como AzurePostgreSqlTable** | Sim |
| tableName | Nome da tabela | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [Pipelines e atividades na Fábrica de Dados Do Azure](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas por um banco de dados Azure para fonte PostgreSQL.

### <a name="azure-database-for-postgresql-as-source"></a>O Banco de Dados do Azure para PostgreSQL como fonte

Para copiar dados do Banco de Dados do Azure para PostgreSQL, defina o tipo de fonte na atividade de cópia como **AzurePostgreSqlSource**. As seguintes propriedades são suportadas na seção **de origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida **como AzurePostgreSqlSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"` | Não (se a propriedade tableName no conjunto de dados for especificada) |

**Exemplo**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Banco de dados Azure para PostgreSQL como sink

Para copiar dados no Banco de Dados Do Azure para PostgreSQL, as seguintes propriedades são suportadas na seção **de sumidouro de** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do dissipador de atividade de cópia deve ser definida **como AzurePostgreSQLSink**. | Sim |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a ser executada antes de gravar dados no Banco de Dados Do Azure para PostgreSQL em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |
| writeBatchSize | Insere dados na tabela Azure Database for PostgreSQL quando o tamanho do buffer atinge writeBatchSize.<br>O valor permitido é um inteiro que representa o número de linhas. | Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br>Os valores permitidos são as strings Timespan. Um exemplo é 00:30:00 (30 minutos). | Não (padrão é 00:00:30) |

**Exemplo**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para obter mais informações sobre as propriedades, consulte [A atividade do Lookup na Fábrica de Dados do Azure](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados suportados como fontes e afundações pela atividade de cópia na Fábrica de Dados Do Azure, consulte [armazenamentode dados suportado](copy-activity-overview.md#supported-data-stores-and-formats).
