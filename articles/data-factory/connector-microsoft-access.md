---
title: Copiar dados de fontes do Microsoft Access
description: Saiba como copiar dados de fontes do Microsoft Access para armazenamentos de dados sink suportados usando uma atividade de cópia em um pipeline da Fábrica de Dados Do Azure.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: fc2179efcda4ee11dda3b424b16a072a2bb2c26e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418177"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Copiar dados de e para os armazenamentos de dados do Microsoft Access usando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados de um armazenamento de dados do Microsoft Access. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Microsoft Access é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados da fonte do Microsoft Access para qualquer armazenamento de dados do sink suportado. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector Microsoft Access, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte [o artigo 'Executtime de integração'.](create-self-hosted-integration-runtime.md)
- Instale o driver Microsoft Access ODBC para o armazenamento de dados na máquina Integration Runtime.

>[!NOTE]
>A versão microsoft access 2016 do driver ODBC não funciona com este conector. Use a versão do driver 2013 ou 2010 em vez disso.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades da Fábrica de Dados específicas do conector Microsoft Access.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado ao Microsoft Access:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como: **MicrosoftAccess** | Sim |
| connectionString | A seqüência de conexão ODBC excluindo a porção de credencial. Você pode especificar a seqüência de conexão ou usar o sistema DSN (Nome da Fonte de Dados) configurado na máquina Integration Runtime (você ainda precisa especificar a parte de credencial no serviço vinculado de acordo com o serviço).<br> Você também pode colocar uma senha no Azure Key Vault e puxar a `password` configuração para fora da seqüência de conexão.Consulte as [credenciais da Loja no Azure Key Vault](store-credentials-in-key-vault.md) com mais detalhes.| Sim |
| authenticationType | Tipo de autenticação usada para se conectar ao armazenamento de dados do Microsoft Access.<br/>Os valores permitidos são: **Básica** e **Anônima**. | Sim |
| userName | Especifique o nome de usuário se você estiver usando a autenticação Básica. | Não |
| password | Especifique a senha da conta de usuário que você especificou para userName. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| credencial | A parte da credencial de acesso da cadeia de conexão especificada no formato propriedade-valor específico do driver. Marque esse campo como uma SecureString. | Não |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;",
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

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Microsoft Access.

Para copiar dados do Microsoft Access, as seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **MicrosoftAccessTable** | Sim |
| tableName | Nome da tabela no Microsoft Access. | Não para fonte (se "query" na fonte da atividade for especificada);<br/>Sim para coletor |

**Exemplo**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte do Microsoft Access.

### <a name="microsoft-access-as-source"></a>Microsoft Access como fonte

Para copiar dados do armazenamento de dados compatível com o Microsoft Access, as seguintes propriedades são suportadas na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como: **MicrosoftAccessSource** | Sim |
| Consulta | Utiliza a consulta personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
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
