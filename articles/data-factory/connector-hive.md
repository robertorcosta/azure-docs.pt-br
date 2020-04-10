---
title: Copiar dados do Hive usando o Azure Data Factory
description: Saiba como copiar dados do Hive para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 1854465c463d78999674080048207cfa0916da3b
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992157"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiar dados do Hive usando o Azure Data Factory 

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Hive. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Colmeia é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de um Hive para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector de Hive.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado de Hive:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Hive** | Sim |
| host | Endereço IP ou nome de host do servidor Hive, separado por ';' para vários hosts (somente quando o serviçoDiscoveryMode está ativado).  | Sim |
| porta | A porta TCP usada pelo servidor Hive para ouvir conexões de cliente. Se você conectar ao Microsoft Azure HDInsights, especifique a porta como 443. | Sim |
| serverType | O tipo do servidor Hive. <br/>Os valores permitidos são: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Não |
| thriftTransportProtocol | O protocolo de transporte a ser usado na camada de Thrift. <br/>Os valores permitidos são: **Binário**, **SASL**, **HTTP** | Não |
| authenticationType | O método de autenticação usado para acessar o servidor do Hive. <br/>Valores permitidos são: **Anônimo**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sim |
| serviceDiscoveryMode | verdadeiro para indicar o uso do serviço ZooKeeper, falso não.  | Não |
| zooKeeperNameSpace | O namespace no ZooKeeper no qual os2 nós do Hive Server são adicionados.  | Não |
| useNativeQuery | Especifica se o driver usa consultas nativas do HiveQL ou as converte em uma forma equivalente no HiveQL.  | Não |
| Nome de Usuário | O nome de usuário que você usa para acessar o servidor do Hive.  | Não |
| password | A senha correspondente ao usuário. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| httpPath | A URL parcial correspondente ao servidor do Hive.  | Não |
| enableSsl | Especifica se as conexões ao servidor são criptografadas usando TLS. O valor padrão é false.  | Não |
| trustedCertPath | O caminho completo do arquivo .pem contendo certificados CA confiáveis para verificar o servidor ao se conectar através do TLS. Esta propriedade só pode ser definida ao usar TLS em IR auto-hospedado. O valor padrão é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é false.  | Não |
| allowHostNameCNMismatch | Especifica se é necessário exigir um nome de certificado TLS/SSL emitido pela CA para corresponder ao nome de host do servidor ao se conectar pelo TLS. O valor padrão é false.  | Não |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é false.  | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados de HTTP.

Para copiar dados de Hive, defina a propriedade type do conjunto de dados como **HiveObject**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **HiveObject** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da mesa, incluindo parte do esquema. Esta propriedade é suportada para compatibilidade retrógrada. Para nova carga `schema` `table`de trabalho, use e . | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte Hive.

### <a name="hivesource-as-source"></a>HiveSource como fonte

Para copiar dados de Hive, defina o tipo de fonte na atividade de cópia como **HiveSource**. As seguintes propriedades são suportadas na seção **de origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **HiveSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
