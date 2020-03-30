---
title: Copiar dados de uma tabela SAP
description: Aprenda a copiar dados de uma tabela SAP para armazenamento de dados sink suportado usando uma atividade de cópia em um pipeline da Fábrica de Dados Do Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371555"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Copiar dados de uma tabela SAP usando a Fábrica de Dados do Azure

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados Do Azure para copiar dados de uma tabela SAP. Para obter mais informações, consulte [A visão geral da atividade do Copy](copy-activity-overview.md).

>[!TIP]
>Para obter o suporte geral da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP usando o whitepaper da Fábrica de Dados Do Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de tabela SAP é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de uma tabela SAP para qualquer armazenamento de dados do sink suportado. Para obter uma lista dos armazenamentos de dados que são suportados como fontes ou afundantes pela atividade de cópia, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de tabela SAP suporta:

- Copiando dados de uma tabela SAP em:

  - SAP ERP Central Component (SAP ECC) versão 7.01 ou posterior (em um recente SAP Support Package Stack lançado após 2015).
  - SAP Business Warehouse (SAP BW) versão 7.01 ou posterior (em um recente SAP Support Package Stack lançado após 2015).
  - SAP S/4HANA.
  - Outros produtos no SAP Business Suite versão 7.01 ou posterior (em um recente SAP Support Package Stack lançado após 2015).

- Copiando dados de uma tabela transparente SAP, uma tabela agrupada, uma tabela agrupada e uma exibição.
- Copiando dados usando autenticação básica ou SNC (Secure Network Communications, comunicações de rede segura), se o SNC estiver configurado.
- Conectando-se a um servidor de aplicativo SAP ou servidor de mensagens SAP.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector de tabela SAP, você precisa:

- Configure um tempo de execução de integração auto-hospedado (versão 3.17 ou posterior). Para obter mais informações, consulte [Criar e configurar um tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md).

- Baixe o Conector SAP de 64 [bits para Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) no site da SAP e instale-o na máquina de tempo de execução de integração auto-hospedada. Durante a instalação, certifique-se de selecionar a opção **Install Assemblies to GAC** na janela **Desfiladeiro opcional.**

  ![Instale o conector SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- O usuário SAP que está sendo usado no conector de tabela SAP da Fábrica de Dados deve ter as seguintes permissões:

  - Autorização para o uso de destinos de Chamada de Função Remota (RFC).
  - Permissões para a atividade Executar do objeto de autorização S_SDSAUTH.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir as entidades da Fábrica de Dados específicas do conector de tabela SAP.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado ao SAP BW Open Hub:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` deve ser definida como `SapTable`. | Sim |
| `server` | O nome do servidor no qual a instância SAP está localizada.<br/>Use para se conectar a um servidor de aplicativo SAP. | Não |
| `systemNumber` | O número do sistema do sistema SAP.<br/>Use para se conectar a um servidor de aplicativo SAP.<br/>Valor permitido: Um número decimal de dois dígitos representado como uma seqüência. | Não |
| `messageServer` | O nome de hospedagem do servidor de mensagens SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `messageServerService` | O nome do serviço ou o número da porta do servidor de mensagens.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `systemId` | O ID do sistema SAP onde a tabela está localizada.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `logonGroup` | O grupo de logon para o sistema SAP.<br/>Use para se conectar a um servidor de mensagens SAP. | Não |
| `clientId` | O ID do cliente no sistema SAP.<br/>Valor permitido: Um número decimal de três dígitos representado como uma seqüência. | Sim |
| `language` | A linguagem que o sistema SAP usa.<br/>O valor padrão é `EN`.| Não |
| `userName` | O nome do usuário que tem acesso ao servidor SAP. | Sim |
| `password` | A senha do usuário. Marque este `SecureString` campo com o tipo para armazená-lo com segurança na Fábrica de Dados ou faça referência a [um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| `sncMode` | O indicador de ativação SNC para acessar o servidor SAP onde a tabela está localizada.<br/>Use se quiser usar snc para se conectar ao servidor SAP.<br/>Os valores permitidos são `0` `1` (desligados, o padrão) ou (ligados). | Não |
| `sncMyName` | O nome SNC do iniciador para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncPartnerName` | O nome SNC do parceiro de comunicação para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncLibraryPath` | A biblioteca do produto de segurança externa para acessar o servidor SAP onde a tabela está localizada.<br/>Aplica-se `sncMode` quando está ligado. | Não |
| `sncQop` | O nível de Qualidade de Proteção snc para aplicar.<br/>Aplica-se `sncMode` quando está ligado. <br/>Os valores permitidos são `1` (Autenticação), `2` (Integridade), `3` (Privacidade), `8` (Padrão) `9` (Máximo). | Não |
| `connectVia` | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um tempo de execução de integração auto-hospedado é necessário, como mencionado anteriormente em [Pré-requisitos](#prerequisites). |Sim |

**Exemplo 1: Conecte-se a um servidor de aplicativos SAP**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>Exemplo 2: Conecte-se a um servidor de mensagens SAP

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>Exemplo 3: Conecte-se usando SNC

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades para definir conjuntos de dados, consulte [Conjuntos de dados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades suportadas pelo conjunto de dados da tabela SAP.

Para copiar dados do serviço vinculado ao SAP BW Open Hub, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` deve ser definida como `SapTableResource`. | Sim |
| `tableName` | O nome da tabela SAP para copiar dados. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades para definir atividades, consulte [Pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades suportadas pela fonte da tabela SAP.

### <a name="sap-table-as-source"></a>Tabela SAP como fonte

Para copiar dados de uma tabela SAP, as seguintes propriedades são suportadas:

| Propriedade                         | Descrição                                                  | Obrigatório |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | A propriedade `type` deve ser definida como `SapTableSource`.         | Sim      |
| `rowCount`                         | O número de linhas a serem recuperadas.                              | Não       |
| `rfcTableFields`                   | Os campos (colunas) para copiar da tabela SAP. Por exemplo, `column0, column1`. | Não       |
| `rfcTableOptions`                  | As opções para filtrar as linhas em uma tabela SAP. Por exemplo, `COLUMN0 EQ 'SOMEVALUE'`. Veja também a tabela do operador de consulta SAP mais tarde neste artigo. | Não       |
| `customRfcReadTableFunctionModule` | Um módulo de função RFC personalizado que pode ser usado para ler dados de uma tabela SAP.<br>Você pode usar um módulo de função RFC personalizado para definir como os dados são recuperados do seu sistema SAP e devolvidos à Fábrica de Dados. O módulo de função personalizado deve ter uma interface implementada (importação, exportação, tabelas) semelhante a `/SAPDS/RFC_READ_TABLE2`, que é a interface padrão usada pela Data Factory. | Não       |
| `partitionOption`                  | O mecanismo de partição para ler a partir de uma tabela SAP. As opções compatíveis incluem: <ul><li>`None`</li><li>`PartitionOnInt`(valores inteiros ou inteiros normais com preenchimento zero `0000012345`à esquerda, tais como )</li><li>`PartitionOnCalendarYear`(4 dígitos no formato "YYYY")</li><li>`PartitionOnCalendarMonth`(6 dígitos no formato "YYYYMM")</li><li>`PartitionOnCalendarDate`(8 dígitos no formato "YYYYMMDD")</li></ul> | Não       |
| `partitionColumnName`              | O nome da coluna usado para particionar os dados.                | Não       |
| `partitionUpperBound`              | O valor máximo da coluna `partitionColumnName` especificada em que será usado para continuar com particionamento. | Não       |
| `partitionLowerBound`              | O valor mínimo da coluna `partitionColumnName` especificada em que será usado para continuar com particionamento. (Nota: `partitionLowerBound` não pode ser "0" quando a opção de partição é `PartitionOnInt`) | Não       |
| `maxPartitionsNumber`              | O número máximo de partições para dividir os dados em.     | Não       |

>[!TIP]
>Se sua tabela SAP tiver um grande volume de dados, como vários bilhões de linhas, use `partitionOption` e `partitionSetting` divida os dados em partições menores. Neste caso, os dados são lidos por partição, e cada partição de dados é recuperada do servidor SAP através de uma única chamada RFC.<br/>
<br/>
>`partitionOption` Tomando `partitionOnInt` como exemplo, o número de linhas em cada partição é calculado `partitionUpperBound` `partitionLowerBound`com`maxPartitionsNumber`esta fórmula: (linhas totais caindo entre e )/ .<br/>
<br/>
>Para carregar partições de dados em paralelo para acelerar [`parallelCopies`](copy-activity-performance.md#parallel-copy) a cópia, o grau paralelo é controlado pela configuração na atividade de cópia. Por exemplo, se `parallelCopies` você definir para quatro, a Fábrica de Dados simultaneamente gera e executa quatro consultas com base na opção e configurações de partição especificadas, e cada consulta recupera uma parte dos dados da sua tabela SAP. Recomendamos fortemente `maxPartitionsNumber` fazer um múltiplo `parallelCopies` do valor do imóvel. Ao copiar dados no armazenamento de dados baseado em arquivos, ele também é recomandado para gravar em uma pasta como vários arquivos (apenas especificar nome da pasta), nesse caso, o desempenho é melhor do que escrever em um único arquivo.

Em `rfcTableOptions`, você pode usar os seguintes operadores comuns de consulta SAP para filtrar as linhas:

| Operador | Descrição |
| :------- | :------- |
| `EQ` | Igual a |
| `NE` | É diferente de |
| `LT` | Menor que |
| `LE` | Menor que ou igual a |
| `GT` | Maior que |
| `GE` | Maior que ou igual a |
| `LIKE` | Como em`LIKE 'Emma%'` |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>Mapeamentos de tipo de dados para uma tabela SAP

Quando você está copiando dados de uma tabela SAP, os seguintes mapeamentos são usados dos tipos de dados da tabela SAP para os tipos de dados provisórios da Fábrica de Dados Do Azure. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo SAP ABAP | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `C`(String) | `String` |
| `I`(Inteiro) | `Int32` |
| `F`(Flutuar) | `Double` |
| `D`(Data) | `String` |
| `T`(Horário) | `String` |
| `P`(BCD embalado, moeda, decimal, qty) | `Decimal` |
| `N`(Numérico) | `String` |
| `X`(Binário e Cru) | `String` |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados suportados como fontes e afundamentos pela atividade de cópia na Fábrica de Dados Do Azure, consulte [armazenamentode dados suportado](copy-activity-overview.md#supported-data-stores-and-formats).
