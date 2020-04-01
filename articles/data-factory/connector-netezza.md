---
title: Copiar dados do Netezza usando o Azure Data Factory
description: Saiba como copiar dados do Netezza para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c7e17f7c4493560bd6118b8d4837fd795a6ab0c8
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422854"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Copiar dados do Netezza usando o Azure Data Factory

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Netezza. O artigo baseia-se em [Atividade de Cópia no Azure Data Factory](copy-activity-overview.md), que apresenta uma visão geral da Atividade de Cópia.

>[!TIP]
>Para o cenário de migração de dados do Netezza para o Azure, saiba mais com [o Use Azure Data Factory para migrar dados do servidor Netezza no local para o Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Netezza é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)


Você pode copiar dados do Netezza para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores, consulte [Armazenamentos de dados e formatos compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).

O conector Netezza suporta cópia paralela da origem. Consulte a cópia Paralela da seção [Netezza](#parallel-copy-from-netezza) para obter detalhes.

O Azure Data Factory fornece um driver interno para habilitar a conectividade. Você não precisa instalar manualmente nenhum driver para usar este conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

Você pode criar um pipeline que usa uma atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou um modelo do Azure Resource Manager. Consulte o [tutorial Atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre como criar um pipeline que tenha uma atividade de cópia.

As seções a seguir fornecem detalhes sobre propriedades que você pode usar para definir entidades do Data Factory que são específicas para o conector Netezza.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado do Netezza:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | O **tipo** propriedade deve ser definida como **Netezza**. | Sim |
| connectionString | Uma cadeia de conexão ODBC para conectar-se ao Netezza. <br/>Você também pode colocar uma senha no Azure Key Vault e extrair a configuração `pwd` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| connectVia | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

Uma cadeia de conexão válida é `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. A tabela a seguir descreve as propriedades mais que você pode definir:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| SecurityLevel | O nível de segurança SSL/TLS () que usa o driver para a conexão ao repositório de dados. Exemplo: `SecurityLevel=preferredSecured`. Os valores com suporte são:<br/>- **Apenas inseguro** (**onlyUnSecured**): O driver não usa SSL.<br/>- **Preferencialmente não segura (preferredUnSecured) (padrão)**: se o servidor fornece uma opção, o driver não usa SSL. <br/>- **Garantia preferencial (preferredSecured)**: Se o servidor fornecer uma escolha, o driver usará SSL. <br/>- **Apenas protegido (onlySecured)**: O driver não se conecta a menos que uma conexão SSL esteja disponível. | Não |
| CaCertFile | O caminho completo para o certificado SSL que é usado pelo servidor. Exemplo: `CaCertFile=<cert path>;`| Sim, se o SSL estiver habilitado |

**Exemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a senha no Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Esta seção fornece uma lista de propriedades que o conjunto de dados do Netezza suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir conjuntos de dados, consulte [Conjuntos de Dados](concepts-datasets-linked-services.md).

Para copiar dados do Netezza, defina a propriedade do **tipo** do conjunto de dados como **NetezzaTable**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **NetezzaTable** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Use `schema` `table` e para nova carga de trabalho. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da Atividade de Cópia

Esta seção fornece uma lista de propriedades que a fonte do Netezza suporta.

Para obter uma lista completa de seções e propriedades que estão disponíveis para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como fonte

>[!TIP]
>Para carregar dados do Netezza de forma eficiente usando particionamento de dados, saiba mais da cópia paralela da seção [Netezza.](#parallel-copy-from-netezza)

Para copiar dados do Netezza, defina as **fonte** tipo de atividade de cópia para **NetezzaSource**. As seguintes propriedades são suportadas na seção Origem atividade **de** cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **tipo** da origem da Atividade de Cópia deve ser configurada para **NetezzaSource**. | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Exemplo: `"SELECT * FROM MyTable"` | Não (se "tableName" no conjunto de dados for especificado) |
| opções de partição | Especifica as opções de particionamento de dados usadas para carregar dados do Netezza. <br>Os valores de permitir são: **Nenhum** (padrão), **DataSlice**e **DynamicRange**.<br>Quando uma opção de partição é `None`habilitada (ou seja, não ), o grau de paralelismo para carregar simultaneamente dados de um banco de dados Netezza é controlado por [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) configuração da atividade de cópia. | Não |
| partiçõesSettings | Especifique o grupo das configurações para particionamento de dados. <br>Aplicar quando a opção `None`de partição não for . | Não |
| partiçãoColunaNome | Especifique o nome da coluna de origem **no tipo inteiro** que será usado por particionamento de intervalo para cópia paralela. Se não for especificado, a chave principal da tabela é detectada automaticamente e usada como coluna de partição. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionColumnName` os dados de origem, conecte-se à cláusula WHERE. Veja exemplo em Cópia Paralela da seção [Netezza.](#parallel-copy-from-netezza) | Não |
| partiçãoUpperBound | O valor máximo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição é . Se você usar consulta para recuperar `?AdfRangePartitionUpbound` dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Netezza.](#parallel-copy-from-netezza) | Não |
| partiçãoLowerBound | O valor mínimo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionLowbound` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Netezza.](#parallel-copy-from-netezza) | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Cópia paralela de Netezza

O conector Data Factory Netezza fornece particionamento de dados incorporado para copiar dados do Netezza em paralelo. Você pode encontrar opções de particionamento de dados na tabela **Origem** da atividade de cópia.

![Captura de tela de opções de partição](./media/connector-netezza/connector-netezza-partition-options.png)

Quando você habilita a cópia particionada, a Fábrica de Dados executa consultas paralelas contra a fonte Netezza para carregar dados por partições. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela configuração da atividade de cópia. Por exemplo, se `parallelCopies` você definir para quatro, a Fábrica de Dados simultaneamente gera e executa quatro consultas com base na opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu banco de dados Netezza.

Você é sugerido para habilitar cópia paralela com particionamento de dados especialmente quando você carrega grande quantidade de dados do seu banco de dados Netezza. As seguintes configurações são sugeridas para diferentes cenários. Ao copiar dados no armazenamento de dados baseado em arquivos, ele é recomandado para gravar em uma pasta como vários arquivos (apenas especificar nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande.                                   | **Opção partição:** Fatia de dados. <br><br/>Durante a execução, a Data Factory particiona automaticamente os dados com base nas [fatias de dados incorporadas da Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)e copia dados por partições. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada.                 | **Opção partição:** Fatia de dados.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante a execução, `?AdfPartitionCount` a Fábrica de Dados substitui `?AdfDataSliceCondition` (com o número de cópia paralelo definido na atividade de cópia) e com a lógica de partição de fatiade de dados, e envia para netezza. |
| Carregue grande quantidade de dados usando uma consulta personalizada, tendo uma coluna inteiro com valor distribuído uniformemente para particionamento de intervalo. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna partição**: Especifique a coluna usada para particionar dados. Você pode particionar contra a coluna com o tipo de dados inteiros.<br>**Limite superior de partição** e **partede partição limite inferior**: Especifique se deseja filtrar contra a coluna de partição para recuperar dados apenas entre o intervalo inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Fábrica de Dados substitui , e com o nome da coluna real e faixas de valor para cada partição, e envia para Netezza. <br>Por exemplo, se a coluna de partição "ID" definir com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia sísvia como 4, data factory recuperará dados por 4 partições. Seus IDs estão entre [1,20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com partição de fatia de dados**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas

Para obter uma lista de armazenamentos de dados que o Copy Activity suporta como fontes e coletores no Azure Data Factory, consulte [Armazenamentos e formatos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
