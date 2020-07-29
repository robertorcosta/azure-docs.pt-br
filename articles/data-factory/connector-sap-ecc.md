---
title: Copiar dados do SAP ECC
description: Saiba como copiar dados do SAP ECC para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/12/2020
ms.openlocfilehash: 4bdcb2b4008f54ff0d84594e6f3b5a7b76944e65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987007"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC usando o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados do SAP ECC (Enterprise Central Component). Para obter mais informações, confira [Visão geral da atividade de cópia](copy-activity-overview.md).

>[!TIP]
>Para saber mais sobre o suporte geral do ADF no cenário de integração de dados do SAP, confira o [white paper Integração de dados do SAP usando o Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, comparação e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Há suporte para este conector do SAP ECC nas seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/coletor com suporte](copy-activity-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um SAP ECC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP ECC dá suporte à:

- Copiar dados do SAP ECC no SAP NetWeaver versão 7.0 e posterior.
- Copiar dados de qualquer objeto exposto pelos serviços OData do SAP ECC, como:

  - Tabelas ou exibições do SAP.
  - Objetos de BAPI [interface de programação de aplicativo de negócios].
  - Extratores de dados.
  - Dados ou documentos intermediários (IDOCs) enviados ao SAP PI (Process Integration) que podem ser recebidos como OData por meio de adaptadores relativos.

- Cópia de dados por meio da autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC por meio de uma tabela ou uma exibição do SAP, use o conector de [tabela do SAP](connector-sap-table.md), que é mais rápido e mais escalonável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, o SAP ECC expõe entidades por meio de serviços OData através do Gateway do SAP. Para usar esse conector do SAP ECC, é necessário:

- **Configurar o Gateway do SAP**. Para os servidores com versões do SAP NetWeaver posteriores a 7.4, o SAP Gateway já está instalado. Para obter as versões anteriores, você precisará instalar o SAP Gateway inserido ou o sistema de hub do SAP Gateway antes de expor os dados do SAP ECC por meio de serviços OData. Para configurar o SAP Gateway, confira o [guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço OData do SAP**. Ative o serviço OData por meio do TCODE SICF em poucos segundos. Configure também quais objetos precisam ser expostos. Para obter mais informações, confira as [diretrizes passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir as entidades do Data Factory específicas ao conector do SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

Há suporte para as seguintes propriedades no serviço vinculado do SAP ECC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` precisa ser definida como `SapEcc`. | Sim |
| `url` | A URL do serviço OData do SAP ECC. | Sim |
| `username` | O nome de usuário usado para se conectar ao SAP ECC. | Não |
| `password` | A senha de texto não criptografado usada para se conectar ao SAP ECC. | Não |
| `connectVia` | O [runtime de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos](#prerequisites). Se você não especificar um runtime, o Azure Integration Runtime padrão será usado. | Não |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e das propriedades disponíveis para definir os conjuntos de dados, confira [Conjuntos de dados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades compatíveis com o conjunto de dados do SAP ECC.

Para copiar dados do SAP ECC, defina a propriedade `type` do conjunto de dados como `SapEccResource`.

Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `path` | Caminho da entidade OData do SAP ECC. | Sim |

### <a name="example"></a>Exemplo

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e das propriedades disponíveis para definir as atividades, confira [Pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades compatíveis com a fonte do SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como fonte

Para copiar dados do SAP ECC, defina a propriedade `type` na seção `source` da atividade de cópia como `SapEccSource`.

Há suporte para as seguintes propriedades na seção `source` da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` da seção `source` da atividade de cópia precisa ser definida como `SapEccSource`. | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>o conector do SAP ECC copia os dados da URL combinada:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para saber mais, confira as [Componentes da URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |
| `httpRequestTimeout` | O tempo limite (o valor **TimeSpan**) para a solicitação HTTP para obter uma resposta. Esse valor é o tempo limite para obter uma resposta, não o tempo limite para ler os dados da resposta. Se não for especificado, o valor padrão será **00:30:00** (30 minutos). | Não |

### <a name="example"></a>Exemplo

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamentos de tipo de dados do SAP ECC

Quando você copia dados do SAP ECC, os mapeamentos a seguir são usados dos tipos de dados OData para os dados do SAP ECC para os tipos de dados provisórios do Azure Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados OData | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> Atualmente, não há suporte para tipos de dados complexos.

## <a name="lookup-activity-properties"></a>Pesquisar propriedades de atividade

Para saber detalhes sobre as propriedades, verifique [Pesquisar atividade](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados compatíveis como fontes e coletores com a atividade de cópia no Azure Data Factory, confira [Armazenamentos de dados compatíveis](copy-activity-overview.md#supported-data-stores-and-formats).
