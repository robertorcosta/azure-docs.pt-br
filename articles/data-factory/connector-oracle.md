---
title: Copiar dados de e para a Oracle usando a Fábrica de Dados do Azure
description: Saiba como copiar dados de lojas de origem suportadas para um banco de dados Oracle, ou da Oracle para lojas de pia suportadas, usando a Fábrica de Dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 534e5c913685eeac92022f6694ea31b24816da5d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011643"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versão atual](connector-oracle.md)

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados Do Azure para copiar dados de e para um banco de dados Oracle. Ele se baseia na visão geral da [atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Oracle é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Oracle. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou afundantes pela atividade de cópia, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector Oracle suporta:

- As seguintes versões de um banco de dados Oracle:
    - Oracle 19c R1 (19.1) e superior
    - Oracle 18c R1 (18.1) e superior
    - Oracle 12c R1 (12.1) e superior
    - Oracle 11g R1 (11.1) e superior
    - Oracle 10g R1 (10.1) e superior
    - Oracle 9i R2 (9.2) e superior
    - Oracle 8i R3 (8.1.7) e superior
    - Serviço de exadata de nuvem de banco de dados Oracle
- Cópia paralela de uma fonte Oracle. Consulte a [cópia Paralela da](#parallel-copy-from-oracle) seção Oracle para obter detalhes.

> [!Note]
> Não há suporte para servidor proxy do Oracle.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

O integration runtime fornece um driver interno do Oracle. Portanto, você não precisa instalar manualmente um driver quando você copia dados de e para Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O serviço vinculado oracle suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **Oracle**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do Banco de Dados Oracle. <br/>Você também pode colocar uma senha no Azure Key Vault e puxar a `password` configuração para fora da seqüência de conexões. Consulte as seguintes amostras e [as credenciais da Loja no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. <br><br>**Tipo de conexão com suporte**: você pode optar por usar a **Oracle SID** ou o **Oracle Service Name** para identificar o banco de dados:<br>-Se você usar a SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar Service Name:`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Para opções avançadas de conexão nativa Oracle, você pode optar por adicionar uma entrada no [TNSNAMES. O](http://www.orafaq.com/wiki/Tnsnames.ora) arquivo ORA no servidor Oracle e no serviço vinculado ao ADF Oracle, opta por usar o tipo de conexão Oracle Service Name e configurar o nome de serviço correspondente. | Sim |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não especificado, o Azure Integration Runtime padrão será usado. |Não |

>[!TIP]
>Se você tiver um erro, "ORA-01025: parâmetro UPI fora do alcance", `WireProtocolMode=1` e sua versão Oracle for 8i, adicione à sua seqüência de conexão. Em seguida, tente novamente.

Mais propriedades de conexão que você pode definir na seqüência de conexões de acordo com o seu caso:

| Propriedade | Descrição | Valores permitidos |
|:--- |:--- |:--- |
| ArraySize |O número de bytes que o conector pode buscar em uma única viagem de ida e volta em rede. Por exemplo, `ArraySize=‭10485760‬`.<br/><br/>Valores maiores aumentam o throughput reduzindo o número de vezes para buscar dados em toda a rede. Valores menores aumentam o tempo de resposta, pois há menos atraso esperando o servidor transmitir dados. | Um inteiro de 1 a 4294967296 (4 GB). O valor padrão é `60000`. O valor 1 não define o número de bytes, mas indica alocar espaço para exatamente uma linha de dados. |

Para habilitar a criptografia na conexão do Oracle, há duas opções:

-   Para usar **o Triple-DES Encryption (3DES) e o Advanced Encryption Standard (AES)**, no lado do servidor Oracle, vá para a Oracle Advanced Security (OAS) e configure as configurações de criptografia. Para obter detalhes, consulte esta [documentação oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). O conector ADF (Oracle Application Development Framework, estrutura de desenvolvimento de aplicativos) negocia automaticamente o método de criptografia para usar o que você configura na OEA ao estabelecer uma conexão com o Oracle.

-   Para usar **TLS:**

    1.  Obtenha as informações do certificado TLS/SSL. Obtenha as regras de codificação distintas (DER)-codificadas informações do certificado do seu cert TLS/SSL, e salve a saída (----- Begin Certificate ... End Certificate---) como um arquivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Exemplo:** Extrair informações cert de DERcert.cer e, em seguida, salvar a saída para cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Construa `keystore` `truststore`o or. O comando a `truststore` seguir cria o arquivo, com ou sem senha, no formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Exemplo:** Crie um arquivo PKCS12, `truststore` chamado MyTrustStoreFile, com uma senha.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque `truststore` o arquivo na máquina de RI auto-hospedada. Por exemplo, coloque o arquivo em C:\MyTrustStoreFile.
    4.  Na Fábrica de Dados Do Azure, `EncryptionMethod=1` configure `TrustStore` / `TrustStorePassword`a seqüência de conexões Oracle com e o valor correspondente. Por exemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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

Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Oracle. Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados](concepts-datasets-linked-services.md). 

Para copiar dados de e para a Oracle, `OracleTable`defina a propriedade do tipo do conjunto de dados como . Há suporte para as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto `OracleTable`de dados deve ser definida como . | Sim |
| esquema | Nome do esquema. |Não para fonte, Sim para o coletor  |
| tabela | Nome da tabela/exibição. |Não para fonte, Sim para o coletor  |
| tableName | Nome da tabela/exibição com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não para fonte, Sim para o coletor |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor Oracle. Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle como fonte

>[!TIP]
>Para carregar dados da Oracle de forma eficiente usando particionamento de dados, aprenda mais com [a cópia paralela da Oracle](#parallel-copy-from-oracle).

Para copiar dados da Oracle, defina o `OracleSource`tipo de origem na atividade de cópia para . As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de `OracleSource`atividade de cópia deve ser definida como . | Sim |
| oracleReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando você habilita a carga particionada, você precisa conectar quaisquer parâmetros de partição incorporados correspondentes em sua consulta. Por exemplo, consulte a cópia Paralela da seção [Oracle.](#parallel-copy-from-oracle) | Não |
| opções de partição | Especifica as opções de particionamento de dados usadas para carregar dados da Oracle. <br>Os valores permitidos são: **Nenhum** (padrão), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando uma opção de partição é `None`ativada (ou seja, não ), o grau de paralelismo para carregar simultaneamente dados de um banco de dados Oracle é controlado pela configuração [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) da atividade de cópia. | Não |
| partiçõesSettings | Especifique o grupo das configurações para particionamento de dados. <br>Aplicar quando a opção `None`de partição não for . | Não |
| partiçãoNomes | A lista de partições físicas que precisa ser copiada. <br>Aplicar quando a `PhysicalPartitionsOfTable`opção de partição for . Se você usar uma consulta para recuperar `?AdfTabularPartitionName` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Oracle.](#parallel-copy-from-oracle) | Não |
| partiçãoColunaNome | Especifique o nome da coluna de origem **no tipo inteiro** que será usado por particionamento de intervalo para cópia paralela. Se não for especificado, a chave principal da tabela é detectada automaticamente e usada como coluna de partição. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionColumnName` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Oracle.](#parallel-copy-from-oracle) | Não |
| partiçãoUpperBound | O valor máximo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionUpbound` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Oracle.](#parallel-copy-from-oracle) | Não |
| partiçãoLowerBound | O valor mínimo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionLowbound` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Oracle.](#parallel-copy-from-oracle) | Não |

**Exemplo: copiar dados usando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle como coletor

Para copiar dados para a Oracle, defina `OracleSink`o tipo de pia na atividade de cópia para . As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da pia de `OracleSink`atividade de cópia deve ser definida como . | Sim |
| writeBatchSize | Insere dados na tabela SQL quando `writeBatchSize`o tamanho do buffer atingir .<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | O tempo de espera para a operação de inserção em lotes a ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são período. Um exemplo é 00:30:00 (30 minutos). | Não |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia a ser executada antes de escrever dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Cópia paralela da Oracle

O conector Oracle da fábrica de dados fornece particionamento de dados incorporado para copiar dados da Oracle em paralelo. Você pode encontrar opções de particionamento de dados na guia **Origem** da atividade de cópia.

![Captura de tela de opções de partição](./media/connector-oracle/connector-oracle-partition-options.png)

Quando você habilita a cópia particionada, a Fábrica de Dados executa consultas paralelas contra sua fonte Oracle para carregar dados por partições. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela configuração da atividade de cópia. Por exemplo, se `parallelCopies` você definir para quatro, a Fábrica de Dados simultaneamente gera e executa quatro consultas com base na opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu banco de dados Oracle.

Você é sugerido para habilitar cópia paralela com particionamento de dados especialmente quando você carrega grande quantidade de dados do seu banco de dados Oracle. As seguintes configurações são sugeridas para diferentes cenários. Ao copiar dados no armazenamento de dados baseado em arquivos, ele é recomandado para gravar em uma pasta como vários arquivos (apenas especificar nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande, com divisórias físicas.          | **Opção partição:** Partições físicas da tabela. <br><br/>Durante a execução, a Fábrica de Dados detecta automaticamente as partições físicas e copia dados por partições. |
| Carga completa de mesa grande, sem partições físicas, enquanto com uma coluna inteiro para particionamento de dados. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Coluna partição**: Especifique a coluna usada para particionar dados. Se não for especificado, a coluna de tecla principal é usada. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, com partições físicas. | **Opção partição:** Partições físicas da tabela.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome da partição**: Especifique o nome da partição para copiar dados. Se não for especificado, o Data Factory detecta automaticamente as partições físicas na tabela especificada no conjunto de dados Oracle.<br><br>Durante a execução, `?AdfTabularPartitionName` a Fábrica de Dados substitui com o nome de partição real e envia para a Oracle. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada, sem partições físicas, enquanto com uma coluna inteira para particionamento de dados. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna partição**: Especifique a coluna usada para particionar dados. Você pode particionar contra a coluna com o tipo de dados inteiros.<br>**Limite superior de partição** e **partede partição inferior :** Especifique se deseja filtrar contra a coluna de partição para recuperar dados apenas entre o intervalo inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Fábrica de Dados substitui , e com o nome da coluna real e faixas de valor para cada partição, e envia para a Oracle. <br>Por exemplo, se a coluna de partição "ID" for definida com o limite inferior como 1 e o limite superior como 80, com conjunto de cópias paralelas como 4, data factory recuperará dados por 4 partições. Seus IDs estão entre [1,20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com partição física**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapeamento de tipo de dados para o Oracle

Quando você copia dados de e para a Oracle, os seguintes mapeamentos se aplicam. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados de Oracle | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(só tem suporte no Oracle 10g e superior) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| FLOAT |Decimal, cadeia de caracteres (se precisão > 28) |
| INTEGER |Decimal, cadeia de caracteres (se precisão > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, cadeia de caracteres (se precisão > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Número |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Os tipo de dados INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND não têm suporte.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
