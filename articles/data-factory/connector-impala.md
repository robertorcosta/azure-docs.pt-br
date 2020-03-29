---
title: Copiar dados do Impala usando o Azure Data Factory
description: Saiba como copiar dados do Impala para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Data Factory.
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
ms.openlocfilehash: f465fe4bb69bc5ae81db6c78df51bf5133de1b60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929298"
---
# <a name="copy-data-from-impala-by-using-azure-data-factory"></a>Copiar dados do Impala usando o Azure Data Factory

Este artigo descreve como usar atividade de cópia no Azure Data Factory para copiar dados de e para o Impala. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Impala é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Impala para qualquer repositório de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou afundantes pela atividade de cópia, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Impala.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Impala.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **Impala**. | Sim |
| host | O endereço IP ou nome do host do servidor Impala (que é 192.168.222.160).  | Sim |
| porta | A porta TCP usada pelo servidor Impala para ouvir conexões de cliente. O valor padrão é 21050.  | Não |
| authenticationType | O tipo de autenticação a ser usado. <br/>Valores permitidos são: **Anônimo**, **SASLUsername** e **UsernameAndPassword**. | Sim |
| Nome de Usuário | O nome de usuário usado para acessar o servidor Impala. O valor padrão é anônimo quando você usa SASLUsername.  | Não |
| password | A senha que corresponde ao nome de usuário quando você usa UsernameAndPassword. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é **false**.  | Não |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis usados para verificar o servidor ao se conectar via SSL. Essa propriedade pode ser definida somente quando você usa o SSL em Integration Runtime auto-hospedada. O valor padrão é o arquivo de cacerts.pem instalado com o runtime de integração.  | Não |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é **false**.  | Não |
| allowHostNameCNMismatch | Especifica se é necessário o nome do certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é **false**.  | Não |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é **false**.  | Não |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

**Exemplo:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Impala.

Para copiar dados do Impala, defina a propriedade type do conjunto de dados como **ApacheImpalaObject**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **ImpalaObject** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Use `schema` `table` e para nova carga de trabalho. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo tipo de fonte do Impala.

### <a name="impala-as-a-source-type"></a>Impala como um tipo de fonte

Para copiar dados do Impala, defina o tipo de fonte na atividade de cópia como **ImpalaSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como **ImpalaSource**. | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
