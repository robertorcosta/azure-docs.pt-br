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
ms.date: 09/02/2019
ms.openlocfilehash: f875d8f4603a8f51b8b8fed2438e6f3a30c87aeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931167"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiar dados do SAP ECC usando a fábrica de dados do Azure

Este artigo descreve como usar a atividade de cópia na Fábrica de Dados Do Azure para copiar dados do SAP Enterprise Central Component (ECC). Para obter mais informações, consulte [A visão geral da atividade do Copy](copy-activity-overview.md).

>[!TIP]
>Para obter o suporte geral da ADF no cenário de integração de dados SAP, consulte [a integração de dados SAP usando o whitepaper da Fábrica de Dados Do Azure](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector SAP ECC é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados de um SAP ECC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou afundantes pela atividade de cópia, consulte a tabela [armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector do SAP ECC dá suporte à:

- Copiando dados do SAP ECC na versão 7.0 do SAP NetWeaver e posterior.
- Copiando dados de quaisquer objetos expostos pelos serviços SAP ECC OData, tais como:

  - Tabelas ou visualizações SAP.
  - Objetos da Interface de Programação de Aplicativos de Negócios (BAPI).
  - Extratores de dados.
  - Dados ou documentos intermediários (IDOCs) enviados ao SAP Process Integration (PI) que podem ser recebidos como OData através de adaptadores relativos.

- Copiando dados usando autenticação básica.

>[!TIP]
>Para copiar dados do SAP ECC através de uma tabela ou exibição SAP, use o conector de [tabela SAP,](connector-sap-table.md) que é mais rápido e escalável.

## <a name="prerequisites"></a>Pré-requisitos

Geralmente, o SAP ECC expõe entidades por meio de serviços OData através do Gateway do SAP. Para usar esse conector do SAP ECC, é necessário:

- **Configurar o Gateway do SAP**. Para servidores com versões SAP NetWeaver mais tarde que 7.4, o SAP Gateway já está instalado. Para versões anteriores, você deve instalar o SAP Gateway incorporado ou o sistema de hub SAP Gateway antes de expor os dados Do SAP ECC através dos serviços OData. Para configurar o SAP Gateway, consulte o [guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço SAP OData**. Você pode ativar o serviço OData através do TCODE SICF em segundos. Você também pode configurar quais objetos precisam ser expostos. Para obter mais informações, consulte a [orientação passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir as entidades da Fábrica de Dados específicas do conector SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As seguintes propriedades são suportadas para o serviço vinculado ao SAP ECC:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A propriedade `type` deve ser definida como `SapEcc`. | Sim |
| `url` | A URL do serviço SAP ECC OData. | Sim |
| `username` | O nome de usuário usado para se conectar ao SAP ECC. | Não |
| `password` | A senha de texto simples usada para se conectar ao SAP ECC. | Não |
| `connectVia` | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Saiba mais na seção [Pré-requisitos.](#prerequisites) Se você não especificar um tempo de execução, o tempo de execução de integração padrão do Azure será usado. | Não |

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

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte [Conjuntos de dados](concepts-datasets-linked-services.md). A seção a seguir fornece uma lista das propriedades suportadas pelo conjunto de dados SAP ECC.

Para copiar dados do SAP `type` ECC, defina `SapEccResource`a propriedade do conjunto de dados como .

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

Para obter uma lista completa das seções e propriedades disponíveis para definir as atividades, consulte [Pipelines](concepts-pipelines-activities.md). A seção a seguir fornece uma lista das propriedades suportadas pela fonte SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC como fonte

Para copiar dados do SAP `type` ECC, `source` defina a `SapEccSource`propriedade na seção da atividade de cópia para .

As seguintes propriedades são suportadas `source` na seção da atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| `type` | A `type` propriedade da seção `source` de atividade `SapEccSource`de cópia deve ser definida como . | Sim |
| `query` | As opções de consulta OData para filtrar os dados. Por exemplo: <br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>O conector SAP ECC copia dados da URL combinada:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Para saber mais, confira as [Componentes da URL do OData](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Não |

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

## <a name="data-type-mappings-for-sap-ecc"></a>Mapeamentos de tipo de dados para SAP ECC

Quando você está copiando dados do SAP ECC, os seguintes mapeamentos são usados a partir de tipos de dados OData para dados SAP ECC para tipos de dados provisórios da Fábrica de Dados Do Azure. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

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
> Os tipos de dados complexos não são suportados no momento.

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Próximas etapas

Para obter uma lista dos armazenamentos de dados suportados como fontes e afundamentos pela atividade de cópia na Fábrica de Dados Do Azure, consulte [armazenamentode dados suportado](copy-activity-overview.md#supported-data-stores-and-formats).
