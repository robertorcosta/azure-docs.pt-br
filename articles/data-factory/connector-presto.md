---
title: Copiar dados do Presto usando o Azure Data Factory (visualização)
description: Saiba como copiar dados do Presto para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
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
ms.openlocfilehash: 71bff5e3761d72236e6896733b96bd6e01460e52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927796"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Copiar dados do Presto usando o Azure Data Factory (visualização)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Presto. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!IMPORTANT]
> Atualmente, esse conector está em versão prévia. Você pode experimentá-lo e oferecer comentários. Se você quiser uma dependência de conectores em versão prévia em sua solução, entre em contato com [suporte do Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Presto é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Presto para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Presto.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Presto:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Presto** | Sim |
| host | O endereço IP ou nome do host do servidor Presto. (ou seja, 192.168.222.160)  | Sim |
| Versão do servidor | A versão do servidor Presto. (ou seja, 0.148-t)  | Sim |
| catálogo | O contexto de catálogo para todas as solicitações no servidor.  | Sim |
| porta | A porta TCP usada pelo servidor Presto para ouvir conexões de cliente. O valor padrão é 8080.  | Não |
| authenticationType | O mecanismo de autenticação usado para se conectar ao servidor do Presto. <br/>Os valores permitidos são: **Anônimo**, **LDAP** | Sim |
| Nome de Usuário | O nome de usuário usado para se conectar ao servidor do Presto.  | Não |
| password | A senha correspondente ao nome de usuário. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | Não |
| enableSsl | Especifica se as conexões com o servidor são criptografadas usando SSL. O valor padrão é false.  | Não |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis para verificar o servidor ao se conectar via SSL. Essa propriedade só pode ser definida ao usar o SSL em IR auto-hospedado. O valor padrão é o arquivo de cacerts.pem instalado com o IR.  | Não |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo PEM especificado. O valor padrão é false.  | Não |
| allowHostNameCNMismatch | Especifica se é necessário o nome do certificado SSL emitido pela autoridade de certificação para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é false.  | Não |
| allowSelfSignedServerCert | Especifica se deve permitir os certificados autoassinados do servidor. O valor padrão é false.  | Não |
| timeZoneID | O fuso horário local usado pela conexão. Os valores válidos para essa opção são especificados no banco de dados de fuso horário IANA. O valor padrão é o fuso horário do sistema.  | Não |

**Exemplo:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa de seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Presto.

Para copiar dados do Presto, defina a propriedade type do conjunto de dados como **PrestoObject**. Há suporte para as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo do conjunto de dados deve ser definida como: **PrestoObject** | Sim |
| esquema | Nome do esquema. |Não (se "query" na fonte da atividade for especificada)  |
| tabela | Nome da tabela. |Não (se "query" na fonte da atividade for especificada)  |
| tableName | Nome da mesa com esquema. Esta propriedade é suportada para compatibilidade retrógrada. Use `schema` `table` e para nova carga de trabalho. | Não (se "query" na fonte da atividade for especificada) |

**Exemplo**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Presto.

### <a name="presto-as-source"></a>Presto como fonte

Para copiar dados do Presto, defina o tipo de fonte na atividade de cópia como **PrestoSource**. As seguintes propriedades são suportadas na seção **de origem da** atividade de cópia:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como: **PrestoSource** | Sim |
| Consulta | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM MyTable"`. | Não (se "tableName" no conjunto de dados for especificado) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
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
