---
title: Copiar dados do SAP HANA
description: Saiba como copiar dados do SAP HANA para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: e1a3ff32956e8a8530684ba7f300f06d0c032227
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421122"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados do SAP HANA usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sap-hana-connector.md)
> * [Versão atual](connector-sap-hana.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP HANA. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para obter o suporte geral da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP usando o whitepaper da Fábrica de Dados Do Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector SAP HANA é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dados SAP HANA para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP HANA dá suporte à:

- Cópia de dados de qualquer versão do banco de dados SAP HANA.
- Copiando dados de modelos de **informações HANA** (como visualizações analíticas e de cálculo) e **tabelas de linha/coluna**.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.
- Cópia paralela de uma fonte SAP HANA. Consulte a cópia Paralela da seção [SAP HANA](#parallel-copy-from-sap-hana) para obter detalhes.

> [!TIP]
> Para copiar dados **no** armazenamento de dados do SAP HANA, use o conector ODBC genérico. Veja [coletor do SAP HANA](connector-odbc.md#sap-hana-sink) para saber mais detalhes. Observe que os serviços vinculados para o conector do SAP HANA e o conector ODBC são de tipos diferentes, portanto, não podem ser reutilizados.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector do SAP HANA, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte [o artigo 'Executtime de integração'.](create-self-hosted-integration-runtime.md)
- Instalar o driver ODBC do SAP HANA no computador do Integration Runtime. Baixe o driver ODBC do SAP HANA do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **CLIENTE SAP HANA para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do SAP HANA.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP HANA:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **SapHana** | Sim |
| connectionString | Especifique as informações necessárias para se conectar ao SAP HANA usando **autenticação básica** ou **autenticação do Windows**. Consulte os exemplos a seguir.<br>Na seqüência de conexões, servidor/porta é obrigatório (a porta padrão é 30015) e nome de usuário e senha são obrigatórios ao usar autenticação básica. Para obter configurações avançadas adicionais, consulte [propriedades de conexão SAP HANA ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>Você também pode colocar senha no Azure Key Vault e puxar a configuração de senha para fora da seqüência de conexão. Consulte as credenciais da Loja no artigo [do Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| userName | Especifique o nome do usuário ao usar a autenticação do Windows. Exemplo: `user@domain.com` | Não |
| password | Especifique a senha para a conta de usuário. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo: use autenticação básica**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: use a autenticação do Windows**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
            "password": { 
                "type": "SecureString", 
                "value": "<password>" 
            } 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Se você estava usando o serviço vinculado SAP HANA com a seguinte carga útil, ele ainda é suportado como está, enquanto você é sugerido a usar o novo daqui para frente.

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP HANA.

Para copiar dados do SAP HANA, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **SapHanaTable** | Sim |
| esquema | Nome do esquema no banco de dados SAP HANA. | Não (se "query" na fonte da atividade for especificada) |
| tabela | Nome da tabela no banco de dados SAP HANA. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você `RelationalTable` estava usando conjunto de dados digitado, ele ainda é suportado como está, enquanto você é sugerido para usar o novo daqui para frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

>[!TIP]
>Para ingerir dados do SAP HANA de forma eficiente usando particionamento de dados, aprenda mais da cópia paralela da seção [SAP HANA.](#parallel-copy-from-sap-hana)

Para copiar dados do SAP HANA, as seguintes propriedades são suportadas na seção **de origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como: **SapHanaSource** | Sim |
| Consulta | Especifica a consulta SQL para ler dados da instância do SAP HANA. | Sim |
| opções de partição | Especifica as opções de particionamento de dados usadas para ingerir dados do SAP HANA. Saiba mais na cópia paralela da seção [SAP HANA.](#parallel-copy-from-sap-hana)<br>Os valores de permitir são: **Nenhum** (padrão), **PhysicalPartitionsOfTable,** **SapHanaDynamicRange**. Saiba mais na cópia paralela da seção [SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`só pode ser usado ao copiar dados de uma tabela, mas não de consulta. <br>Quando uma opção de partição é `None`ativada (ou seja, não ), o grau de paralelismo para carregar simultaneamente dados do SAP HANA é controlado pela configuração [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) na atividade de cópia. | Falso |
| partiçõesSettings | Especifique o grupo das configurações para particionamento de dados.<br>Aplicar quando a `SapHanaDynamicRange`opção de partição é . | Falso |
| partiçãoColunaNome | Especifique o nome da coluna de origem que será usada por partição para cópia paralela. Se não for especificado, o índice ou a chave principal da tabela é detectado automaticamente e usado como coluna de partição.<br>Aplicar quando a `SapHanaDynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfHanaDynamicRangePartitionCondition` os dados de origem, conecte-se à cláusula WHERE. Veja exemplo na cópia paralela da seção [SAP HANA.](#parallel-copy-from-sap-hana) | Sim, `SapHanaDynamicRange` ao usar partição. |
| packetSize | Especifica o tamanho do pacote de rede (em Kilobytes) para dividir dados em vários blocos. Se você tiver uma grande quantidade de dados para copiar, aumentar o tamanho do pacote pode aumentar a velocidade de leitura do SAP HANA na maioria dos casos. Recomenda-se o teste de desempenho ao ajustar o tamanho do pacote. | Não.<br>O valor padrão é 2048 (2MB). |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você `RelationalSource` estava usando a fonte de cópia digitada, ela ainda é suportada como está, enquanto você é sugerido a usar a nova daqui para frente.

## <a name="parallel-copy-from-sap-hana"></a>Cópia paralela do SAP HANA

O conector SAP HANA da fábrica de dados fornece particionamento de dados incorporado para copiar dados do SAP HANA em paralelo. Você pode encontrar opções de particionamento de dados na tabela **Origem** da atividade de cópia.

![Captura de tela de opções de partição](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando você habilita a cópia particionada, a Fábrica de Dados executa consultas paralelas contra a fonte SAP HANA para recuperar dados por partições. O grau paralelo é [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controlado pela configuração da atividade de cópia. Por exemplo, se `parallelCopies` você definir para quatro, a Fábrica de Dados simultaneamente gera e executa quatro consultas com base na opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu SAP HANA.

Você é sugerido para habilitar cópia paralela com particionamento de dados especialmente quando você ingerir grande quantidade de dados do seu SAP HANA. As seguintes configurações são sugeridas para diferentes cenários. Ao copiar dados no armazenamento de dados baseado em arquivos, recomenda-se escrever em uma pasta como vários arquivos (apenas especifique o nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

| Cenário                                           | Configurações sugeridas                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Carga completa de mesa grande.                        | **Opção partição:** Partições físicas da tabela. <br><br/>Durante a execução, a Data Factory detecta automaticamente o tipo de partição física da tabela SAP HANA especificada e escolhe a estratégia de partição correspondente:<br>- **Partição de**intervalo : Obtenha as faixas de partição e partição definidas para a tabela e copie os dados por intervalo. <br>- **Partição hash**: Use a chave de partição hash como coluna de partição e, em seguida, partição e copie os dados com base em intervalos calculados pelo ADF. <br>- **Particionamento de round-robin** ou **sem partição**: Use a chave primária como coluna de partição e, em seguida, partição e copie os dados com base em intervalos calculados pelo ADF. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada. | **Opção partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Coluna partição**: Especifique a coluna usada para aplicar partição de intervalo dinâmico. <br><br>Durante a execução, a Fábrica de Dados calcula primeiro as faixas de valor da coluna de partição especificada, distribuindo uniformemente as linhas `?AdfHanaDynamicRangePartitionCondition` em um número de baldes de acordo com o número de valores de coluna de partição distintos e a configuração de cópia paralela do ADF, em seguida, substitui com a filtragem da faixa de valor da coluna de partição para cada partição e envia para O SAP HANA.<br><br>Se você quiser usar várias colunas como coluna de partição, você pode concatenar os valores de `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`cada coluna como uma coluna na consulta e especificar como coluna de partição no ADF, como . |

**Exemplo: consulta com partições físicas de uma tabela**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Ao copiar dados do SAP HANA, os seguintes mapeamentos são usados de tipos de dados do SAP HANA para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SAP HANA | Tipo de dados provisório do Data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | String                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | String                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | String                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | String                         |
| NVARCHAR           | String                         |
| real               | Single                         |
| SECONDDATE         | Datetime                       |
| TEXTO CURTO          | String                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| Stgeometrytype     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | String                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | String                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
