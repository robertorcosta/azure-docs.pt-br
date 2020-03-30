---
title: Copiar dados para ou do Azure Data Explorer
description: Saiba como copiar dados de ou para o Azure Data Explorer usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 4c265cb0cdc665ef52f4dc6e69440e83c22db449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460964"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Copiar dados para ou do Azure Data Explorer usando a Fábrica de Dados do Azure

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados do Azure para copiar dados para ou do [Azure Data Explorer](../data-explorer/data-explorer-overview.md). Ele se baseia no artigo de visão geral da [atividade de cópia,](copy-activity-overview.md) que oferece uma visão geral da atividade de cópia.

>[!TIP]
>Para a integração a azure Data Factory e o Azure Data Explorer em geral, saiba mais com [o Integrate Azure Data Explorer com o Azure Data Factory](../data-explorer/data-factory-integration.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Azure Data Explorer é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de qualquer armazenamento de dados de origem com suporte para o Azure Data Explorer. Você também pode copiar dados do Azure Data Explorer para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que a atividade de cópia suporta como fontes ou afundantes, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Copiar dados para ou do Azure Data Explorer através de um armazenamento de dados no local usando tempo de execução de integração auto-hospedado é suportado na versão 3.14 e posterior.

Com o conector Azure Data Explorer, você pode fazer o seguinte:

* Copiar dados usando a autenticação de token de aplicativo do Azure AD (Azure Active Directory) com uma **entidade de serviço**.
* Como uma fonte, recupere dados usando uma consulta KQL (Kusto).
* Como um coletor, acrescente dados a uma tabela de destino.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter um passo a passo do conector Azure Data Explorer, consulte [Copiar dados para/do Azure Data Explorer usando a fábrica de dados do Azure](../data-explorer/data-factory-load-data.md) e a cópia em massa de um banco de dados para o [Azure Data Explorer](../data-explorer/data-factory-template.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Azure Data Explorer.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector Azure Data Explorer usa autenticação principal do serviço. Siga estas etapas para obter um diretor de serviço e para conceder permissões:

1. Registre uma entidade de aplicativo no Azure Active Directory seguindo as etapas em [Registre seu aplicativo com um inquilino Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda ao diretor de serviço as permissões corretas no Azure Data Explorer. Consulte Gerenciar as permissões do [banco de dados do Azure Data Explorer](../data-explorer/manage-database-permissions.md) para obter informações detalhadas sobre funções e permissões e sobre o gerenciamento de permissões. Em geral, você deve:

    - **Como fonte,** conceda pelo menos a função **de visualizador de banco de dados** ao seu banco de dados
    - **Como sink**, conceda pelo menos a função **de ingestor de banco de dados** ao seu banco de dados

>[!NOTE]
>Quando você usa a UI da Fábrica de Dados para ser autora, sua conta de usuário de login é usada para listar clusters, bancos de dados e tabelas do Azure Data Explorer. Digite manualmente o nome se você não tiver permissão para essas operações.

As seguintes propriedades são suportadas para o serviço vinculado ao Azure Data Explorer:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **do tipo** deve ser definida **como AzureDataExplorer**. | Sim |
| endpoint | URL de ponto de extremidade do cluster do Azure Data Explorer com o formato `https://<clusterName>.<regionName>.kusto.windows.net`. | Sim |
| Banco de Dados | Nome do banco de dados. | Sim |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Isso é conhecido como "ID de autoridade" na [seqüência de conexões Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Recupere-o pairando o ponteiro do mouse no canto superior direito do portal Azure. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. Isso é conhecido como "ID cliente de aplicativo AAD" na [seqüência de conexões Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Isso é conhecido como "chave de aplicativo AAD" na [seqüência de conexões Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Marque este campo como um **SecureString** para armazená-lo com segurança na Fábrica de Dados ou [referenciar dados seguros armazenados no Azure Key Vault](store-credentials-in-key-vault.md). | Sim |

**Exemplo de propriedades de serviço vinculadas:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados na Fábrica de Dados do Azure](concepts-datasets-linked-services.md). Esta seção lista propriedades que o conjunto de dados do Azure Data Explorer suporta.

Para copiar dados para o Azure Data Explorer, defina a propriedade type do conjunto de dados como **AzureDataExplorerTable**.

Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade **do tipo** deve ser definida **como AzureDataExplorerTable**. | Sim |
| tabela | O nome da tabela à qual o serviço vinculado se refere. | Não para coletor; não para fonte |

**Exemplo de propriedades de conjunto de dados:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte [Pipelines e atividades na Fábrica de Dados Do Azure](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades que o Azure Data Explorer origina e afunda o suporte.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer como fonte

Para copiar dados do Azure Data Explorer, defina a propriedade **type** na fonte da atividade de Cópia como **AzureDataExplorerSource**. As seguintes propriedades são suportadas na seção **de origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A **type** propriedade tipo da fonte de atividade de cópia deve ser definida como: **AzureDataExplorerSource** | Sim |
| Consulta | Uma solicitação somente leitura fornecida em um [formato KQL](/azure/kusto/query/). Use a consulta KQL personalizada como referência. | Sim |
| queryTimeout | O tempo de espera antes da solicitação de consulta é esgotado. O valor padrão é de 10 min (00:10:00); o valor máximo permitido é de 1 hora (01:00:00). | Não |
| noTruncation | Indica se deve truncar o conjunto de resultados retornado. Por padrão, o resultado é truncado após 500.000 registros ou 64 megabytes (MB). A truncação é fortemente recomendada para garantir o comportamento correto da atividade. |Não |

>[!NOTE]
>Por padrão, a fonte do Azure Data Explorer tem um limite de tamanho de 500.000 registros ou 64 MB. Para recuperar todos os registros sem truncação, você pode especificar `set notruncation;` no início de sua consulta. Para obter mais informações, consulte [os limites de consulta](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer como coletor

Para copiar dados do Azure Data Explorer, defina a propriedade type no coletor da atividade de cópia como **AzureDataExplorerSink**. As seguintes propriedades são suportadas na seção **de socedção de** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A **type** propriedade tipo do dissipador de atividade de cópia deve ser definida como: **AzureDataExplorerSink**. | Sim |
| ingestionMappingName | Nome de um [mapeamento](/azure/kusto/management/mappings#csv-mapping) pré-criado em uma tabela Kusto. Para mapear as colunas da origem para o Azure Data Explorer (que se aplica a [todas as lojas e formatos de origem suportados,](copy-activity-overview.md#supported-data-stores-and-formats)incluindo formatos CSV/JSON/Avro), você pode usar o mapeamento da coluna [de](copy-activity-schema-and-type-mapping.md) atividade de cópia (implicitamente pelo nome ou explicitamente configurado) e/ou mapeamentos do Azure Data Explorer. | Não |
| Additionalproperties | Um saco de propriedade que pode ser usado para especificar qualquer uma das propriedades de ingestão que ainda não estão sendo definidas pelo Azure Data Explorer Sink. Especificamente, pode ser útil para especificar tags de ingestão. Saiba mais [no Azure Data Explore data ingestion doc](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html). | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para obter mais informações sobre as propriedades, consulte [A atividade de procurar](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista de armazenamentos de dados que a atividade de cópia na Fábrica de Dados do Azure suporta como fontes e sumidouros, consulte armazenamentos de [dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).

* Saiba mais sobre como [copiar dados da Fábrica de Dados do Azure para o Azure Data Explorer](/azure/data-explorer/data-factory-load-data).
