---
title: Copiar dados da Teradata Vantage usando a Fábrica de Dados Do Azure
description: O conector Teradata do serviço Data Factory permite copiar dados de um Teradata Vantage para armazenamentos de dados suportados pela Data Factory como sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: c7c6cebf0a5c6371893dff52b2e8d7c064a40084
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257930"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Copiar dados da Teradata Vantage usando a Fábrica de Dados Do Azure
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
>
> * [Versão 1](v1/data-factory-onprem-teradata-connector.md)
> * [Versão atual](connector-teradata.md)

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados Do Azure para copiar dados do Teradata Vantage. Ele se baseia na visão geral da [atividade de cópia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Teradata é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Teradata Vantage para qualquer armazenamento de dados de pia suportado. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do Teradata dá suporte a:

- Teradata **versão 14.10, 15.0, 15.10, 16.0, 16.10 e 16.20**.
- Copiando dados usando **autenticação Básica** ou **Windows.**
- Cópia paralela de uma fonte teradata. Consulte a cópia Paralela da seção [Teradata](#parallel-copy-from-teradata) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Se você usar o Executtime de integração auto-hospedado, observe que ele fornece um driver Teradata incorporado a partir da versão 3.18. Você não precisa instalar manualmente nenhum driver. O driver requer "Visual C++ Redistributable 2012 Update 4" na máquina de tempo de execução de integração auto-hospedada. Se você ainda não o instalou, baixe-o a partir [daqui](https://www.microsoft.com/en-sg/download/details.aspx?id=30679).

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades da Fábrica de Dados específicas do conector Teradata.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O serviço vinculado ao Teradata suporta as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **Teradata**. | Sim |
| connectionString | Especifica as informações necessárias para se conectar à instância Teradata. Consulte os exemplos a seguir.<br/>Você também pode colocar uma senha no Azure Key Vault e puxar a `password` configuração para fora da seqüência de conexões. Consulte as [credenciais da Loja no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes. | Sim |
| Nome de Usuário | Especifique um nome de usuário para se conectar ao Teradata. Aplica-se quando você está usando a autenticação do Windows. | Não |
| password | Especifique uma senha para a conta de usuário especificada para o nome de usuário. Você também pode optar por fazer referência a [um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). <br>Aplica-se quando você está usando a autenticação do Windows ou fazendo referência a uma senha no Key Vault para autenticação básica. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

Mais propriedades de conexão que você pode definir na seqüência de conexões de acordo com o seu caso:

| Propriedade | Descrição | Valor padrão |
|:--- |:--- |:--- |
| CharacterSet | O personagem definido para usar para a sessão. Por exemplo, `CharacterSet=UTF16`.<br><br/>Esse valor pode ser um conjunto de caracteres definido pelo usuário ou um dos seguintes conjuntos de caracteres pré-definidos: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>LATIN1252_0A<br/>- LATIN9_0A<br/>LATIN1_0A<br/>- Shift-JIS (Windows, DOS compatível, KANJISJIS_0S)<br/>- EUC (compatível com Unix, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>SCHINESE936_6R0.<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>HANGUL949_7R0.<br/>ARABIC1256_6A0 ARABIC1256_6A0<br/>- O CYRILLIC1251_2A0<br/>- HEBREW1255_5A0.<br/>LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- O LATIN1258_8A0<br/>THAI874_4A0 | O valor padrão é `ASCII`. |
| MaxRespSize |O tamanho máximo do buffer de resposta para solicitações SQL, em kilobytes (KBs). Por exemplo, `MaxRespSize=‭10485760‬`.<br/><br/>Para teradata database versão 16.00 ou posterior, o valor máximo é 7361536. Para conexões que usam versões anteriores, o valor máximo é 1048576. | O valor padrão é `65536`. |

**Exemplo usando autenticação básica**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo de uso da autenticação do Windows**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> A seguinte carga útil ainda é suportada. Daqui para frente, no entanto, você deve usar o novo.

**Carga anterior:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
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

Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Teradata. Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados](concepts-datasets-linked-services.md).

Para copiar dados do Teradata, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto `TeradataTable`de dados deve ser definida como . | Sim |
| Banco de Dados | O nome da instância teradata. | Não (se "query" na fonte da atividade for especificada) |
| tabela | O nome da tabela na instância teradata. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable`o conjunto de dados de tipo ainda é suportado. No entanto, recomendamos que você use o novo conjunto de dados.

**Carga anterior:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Esta seção fornece uma lista das propriedades com suporte pela origem do Teradata. Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, veja [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata como origem

>[!TIP]
>Para carregar dados do Teradata de forma eficiente usando particionamento de dados, aprenda mais com a cópia paralela da seção [Teradata.](#parallel-copy-from-teradata)

Para copiar dados do Teradata, as seguintes propriedades são suportadas na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de `TeradataSource`atividade de cópia deve ser definida como . | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`.<br>Quando você habilita a carga particionada, você precisa conectar quaisquer parâmetros de partição incorporados correspondentes em sua consulta. Por exemplo, consulte a cópia Paralela da seção [Teradata.](#parallel-copy-from-teradata) | Não (se a tabela no conjunto de dados for especificada) |
| opções de partição | Especifica as opções de particionamento de dados usadas para carregar dados do Teradata. <br>Os valores de permitir são: **Nenhum** (padrão), **Hash** e **DynamicRange**.<br>Quando uma opção de partição é `None`ativada (ou seja, não ), o grau de paralelismo para carregar simultaneamente dados do Teradata é controlado pela configuração [`parallelCopies`](copy-activity-performance.md#parallel-copy) da atividade de cópia. | Não |
| partiçõesSettings | Especifique o grupo das configurações para particionamento de dados. <br>Aplicar quando a opção `None`de partição não for . | Não |
| partiçãoColunaNome | Especifique o nome da coluna de origem que será usada por partição de intervalo ou partição Hash para cópia paralela. Se não for especificado, o índice primário da tabela é detectado automaticamente e usado como coluna de partição. <br>Aplicar quando a `Hash` opção `DynamicRange`de partição for ou . Se você usar uma consulta para recuperar `?AdfHashPartitionCondition` `?AdfRangePartitionColumnName` os dados de origem, gancho ou na cláusula WHERE. Veja exemplo em Cópia Paralela da seção [Teradata.](#parallel-copy-from-teradata) | Não |
| partiçãoUpperBound | O valor máximo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição é . Se você usar consulta para recuperar `?AdfRangePartitionUpbound` dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Teradata.](#parallel-copy-from-teradata) | Não |
| partiçãoLowerBound | O valor mínimo da coluna partição para copiar dados. <br>Aplicar quando a `DynamicRange`opção de partição for . Se você usar uma consulta para recuperar `?AdfRangePartitionLowbound` os dados de origem, conecte-se à cláusula WHERE. Por exemplo, consulte a cópia Paralela da seção [Teradata.](#parallel-copy-from-teradata) | Não |

> [!NOTE]
>
> `RelationalSource`a fonte de cópia de tipo ainda é suportada, mas não suporta a nova carga paralela incorporada do Teradata (opções de partição). No entanto, recomendamos que você use o novo conjunto de dados.

**Exemplo: copiar dados usando uma consulta básica sem partição**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Cópia paralela de Teradata

O conector Data Factory Teradata fornece particionamento de dados incorporado para copiar dados do Teradata em paralelo. Você pode encontrar opções de particionamento de dados na tabela **Origem** da atividade de cópia.

![Captura de tela de opções de partição](./media/connector-teradata/connector-teradata-partition-options.png)

Quando você habilita a cópia particionada, a Fábrica de Dados executa consultas paralelas contra a fonte Teradata para carregar dados por partições. O grau paralelo é [`parallelCopies`](copy-activity-performance.md#parallel-copy) controlado pela configuração da atividade de cópia. Por exemplo, se `parallelCopies` você definir para quatro, a Fábrica de Dados simultaneamente gera e executa quatro consultas com base na opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados do seu Teradata.

Você é sugerido para habilitar cópia paralela com particionamento de dados especialmente quando você carrega grande quantidade de dados de seus Teradata. As seguintes configurações são sugeridas para diferentes cenários. Ao copiar dados no armazenamento de dados baseado em arquivos, ele é recomandado para gravar em uma pasta como vários arquivos (apenas especificar nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

| Cenário                                                     | Configurações sugeridas                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de mesa grande.                                   | **Opção de partição:** Hash. <br><br/>Durante a execução, a Data Factory detecta automaticamente a coluna PK, aplica um hash contra ela e copia dados por partições. |
| Carregue uma grande quantidade de dados usando uma consulta personalizada.                 | **Opção de partição:** Hash.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**Coluna partição**: Especifique a coluna usada para aplicar partição hash. Se não for especificado, a Fábrica de Dados detecta automaticamente a coluna PK da tabela especificada no conjunto de dados Teradata.<br><br>Durante a execução, `?AdfHashPartitionCondition` a Fábrica de Dados substitui a lógica de partição de hash e envia para teradata. |
| Carregue grande quantidade de dados usando uma consulta personalizada, tendo uma coluna inteiro com valor distribuído uniformemente para particionamento de intervalo. | **Opções de partição**: Partição de intervalo dinâmico.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Coluna partição**: Especifique a coluna usada para particionar dados. Você pode particionar contra a coluna com o tipo de dados inteiros.<br>**Limite superior de partição** e **partede partição limite inferior**: Especifique se deseja filtrar contra a coluna de partição para recuperar dados apenas entre o intervalo inferior e superior.<br><br>Durante a execução, `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound`a `?AdfRangePartitionLowbound` Fábrica de Dados substitui , e com o nome da coluna real e faixas de valor para cada partição, e envia para Teradata. <br>Por exemplo, se a coluna de partição "ID" definir com o limite inferior como 1 e o limite superior como 80, com conjunto de cópia sísvia como 4, data factory recuperará dados por 4 partições. Seus IDs estão entre [1,20], [21, 40], [41, 60] e [61, 80], respectivamente. |

**Exemplo: consulta com partição hash**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Exemplo: consulta com partição de intervalo dinâmico**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Mapeamento de tipo de dados para Teradata

Quando você copia dados do Teradata, os seguintes mapeamentos se aplicam. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados Teradata | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Data |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Integer |Int32 |
| Interval Day |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Intervalo - dia para hora |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Intervalo - dia para minuto |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Day To Second |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Hour |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Intervalo - hora para minuto |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Hour To Second |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Minute |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Minute To Second |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Month |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Second |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Year |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Interval Year To Month |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Número |Double |
| Período (Data) |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Período (Tempo) |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Período (tempo com fuso horário) |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Period (Timestamp) |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Período (carimbo de tempo com fuso horário) |Sem suporte. Aplique elenco explícito na consulta de origem. |
| SmallInt |Int16 |
| Hora |TimeSpan |
| Hora com fuso horário |TimeSpan |
| Timestamp |Datetime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Sem suporte. Aplique elenco explícito na consulta de origem. |
| Xml |Sem suporte. Aplique elenco explícito na consulta de origem. |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
