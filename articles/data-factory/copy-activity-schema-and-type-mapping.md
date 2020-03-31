---
title: Mapeamento de esquema na atividade de cópia
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados dos dados de origem para os dados do coletor ao copiar dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260807"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia

Este artigo descreve como a atividade de cópia da Fábrica de Dados do Azure faz mapeamento de esquemas e mapeamento de tipo de dados a partir de dados de origem para afundar dados ao executar a cópia de dados.

## <a name="schema-mapping"></a>Mapeamento de esquema

O mapeamento da coluna se aplica ao copiar dados da fonte para o afundamento. Por padrão, copie **os dados de origem do mapa de**atividade para afundar por nomes de coluna . Você pode especificar [mapeamento explícito](#explicit-mapping) para personalizar o mapeamento da coluna com base na sua necessidade. Mais especificamente, a atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem
2. Use o mapeamento padrão da coluna para mapear colunas por nome ou aplique mapeamento explícito de colunas se especificado.
3. Grava os dados no coletor

### <a name="explicit-mapping"></a>Mapeamento explícito

Você pode especificar as colunas para `translator`  ->  `mappings` mapear na propriedade > de atividade de cópia. O exemplo a seguir define uma atividade de cópia em um pipeline para copiar dados de texto delimitado para o Banco de Dados SQL do Azure.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

As seguintes propriedades `translator`  ->  `mappings` são suportadas `source` `sink`sob objeto -> com e:

| Propriedade | Descrição                                                  | Obrigatório |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Nome da coluna origem ou da pia.                           | Sim      |
| ordinal  | Índice da coluna. Comece com 1. <br>Aplique e requisite ao usar texto delimitado sem linha de cabeçalho. | Não       |
| caminho     | Expressão do caminho JSON para cada campo extrair ou mapear. Solicite dados hierárquicos, por exemplo, MongoDB/REST.<br>Para campos o objeto raiz, o caminho JSON começa com a raiz $; para campos dentro da `collectionReference` matriz escolhida pela propriedade, o caminho JSON começa a partir do elemento matriz. | Não       |
| type     | Data Factory tipo de dados provisórios da coluna origem ou sink. | Não       |
| culture  | Cultura da coluna fonte ou pia. <br>Aplicar quando `Datetime` o `Datetimeoffset`tipo é ou . O padrão é `en-us`. | Não       |
| format   | Seqüência de formato `Datetime` a `Datetimeoffset`ser usada quando o tipo é ou . Consulte [Data personalizada e cadeias de caracteres de formato de hora](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data e hora. | Não       |

As seguintes propriedades `translator`  ->  `mappings` são suportadas, `source` `sink`além de objeto, com e:

| Propriedade            | Descrição                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Suportado apenas quando dados hierárquicos, por exemplo, MongoDB/REST é fonte.<br>Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. | Não       |

### <a name="alternative-column-mapping"></a>Mapeamento de colunas alternativas

Você pode especificar `translator`  ->  `columnMappings` a atividade de cópia - > para mapear entre dados em forma de tabular . Neste caso, a seção "estrutura" é necessária para conjuntos de dados de entrada e saída. O mapeamento de coluna dá suporte ao **mapeamento de todas as colunas ou de um subconjunto de colunas na “structure” do conjunto de dados de origem para todas as colunas na “structure” do conjunto de dados do coletor**. Veja a seguir condições de erro que resultam em uma exceção:

* O resultado da consulta do armazenamento de dados de origem não tem um nome de coluna especificado na seção “structure” do conjunto de dados de entrada.
* O armazenamento de dados do coletor (se estiver com o esquema predefinido) não tem um nome de coluna especificado na seção “structure” do conjunto de dados de saída.
* Menos colunas ou mais colunas na seção "structure" do conjunto de dados do coletor do que o especificado no mapeamento.
* Mapeamento duplicado.

No exemplo a seguir, o conjunto de dados de entrada tem uma estrutura e aponta para uma tabela em um banco de dados Oracle no local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Nesta amostra, o conjunto de dados de saída tem uma estrutura e aponta para uma tabela em Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da fonte mapeadas para colunas na pia usando a propriedade **Translator** -> **columnMappings.**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se você está utilizando a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de coluna, ainda há suporte no estado em que se encontra.

### <a name="alternative-schema-mapping"></a>Mapeamento de esquemas alternativos

Você pode especificar `translator`  ->  `schemaMapping` a atividade de cópia - > para mapear entre dados em forma hierárquica e dados em forma de tabular, por exemplo, copiar do MongoDB/REST para arquivo de texto e copiar da API do Oracle para o Azure Cosmos DB para O MongoDB. As propriedades a seguir têm suporte na seção `translator` da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do tradutor de atividade de cópia deve ser definida como: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares de valor-chave, que representa a relação de mapeamento **do lado da origem para o lado da pia**.<br/>- **Chave:** representa a fonte. Para **fonte tabular,** especifique o nome da coluna como definido na estrutura do conjunto de dados; para **fonte hierárquica,** especifique a expressão de caminho JSON para cada campo para extrair e mapear.<br>- **Valor:** representa pia. Para **o dissipador tabular,** especifique o nome da coluna como definido na estrutura do conjunto de dados; para **sink hierárquico,** especifique a expressão de caminho JSON para cada campo para extrair e mapear. <br>No caso de dados hierárquicos, para campos objeto raiz, o caminho JSON começa com raiz $; para campos dentro da `collectionReference` matriz escolhida pela propriedade, o caminho JSON começa a partir do elemento matriz.  | Sim |
| collectionReference | Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. Essa propriedade só terá suporte quando os dados hierárquicos forem a origem. | Não |

**Exemplo: copiar do MongoDB para o Oracle:**

Por exemplo, se você tiver o documento do MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz *(order_pd e order_price)* e fazer uma união cruzada com as informações de raiz comuns *(número, data e cidade)*:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa o mapeamento dos tipos de origem para os tipos de coletor com a abordagem de duas etapas a seguir:

1. Converter de tipos de origem nativos para tipos de dados provisórios do Azure Data Factory
2. Converter de tipos de dados provisórios do Azure Data Factory para o tipo de coletor nativo

Você pode encontrar o mapeamento entre o tipo nativo para o tipo provisório na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados com suporte

A Fábrica de Dados suporta os seguintes tipos de dados provisórios: Você pode especificar valores abaixo ao configurar informações de tipo na configuração [da estrutura do conjunto de dados:](concepts-datasets-linked-services.md#dataset-structure-or-schema)

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
