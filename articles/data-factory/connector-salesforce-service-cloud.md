---
title: Copiar dados de e para Salesforce Service Cloud
description: Saiba como copiar dados do Salesforce Service Cloud para armazenamentos de dados sink suportados ou de armazenamentos de dados de origem suportados para salesforce service cloud usando uma atividade de cópia em um pipeline de fábrica de dados.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: ec2aa5b1492534908adb55544623110242717609
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416670"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Copiar dados de e para salesforce service cloud usando a fábrica de dados do Azure
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados do Azure para copiar dados da Salesforce Service Cloud. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector Salesforce Service Cloud é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com [matriz de origem/pia suportada](copy-activity-overview.md)
- [Atividade de procurar](control-flow-lookup-activity.md)

Você pode copiar dados do Salesforce Service Cloud para qualquer armazenamento de dados de sink suportado. Você também pode copiar dados de qualquer armazenamento de dados de origem suportada para salesforce Service Cloud. Para obter uma lista de armazenamentos de dados que são suportados como fontes ou afundantes pela atividade Copiar, consulte a tabela [Armazenamento de dados suportado.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector salesforce service cloud suporta:

- Edições de Desenvolvedor, Professional, Enterprise ou Ilimitada do Salesforce.
- Copiar dados de e para a produção, da área restrita e do domínio personalizado do Salesforce.

O conector Salesforce é construído em cima da API Salesforce REST/Bulk. Por padrão, o conector usa [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados do Salesforce e usa [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para o Salesforce. Você também pode definir explicitamente a versão de API usada para ler/gravar dados via [ `apiVersion` propriedade](#linked-service-properties) em serviço vinculado.

## <a name="prerequisites"></a>Pré-requisitos

A permissão de API deve estar habilitada no Salesforce. Para mais informações, consulte [Habilitar o acesso à API no Salesforce por conjunto de permissões](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limites da solicitação Salesforce

O Salesforce tem limites para o total de solicitações de API e as solicitações simultâneas de API. Observe os seguintes pontos:

- Se o número de solicitações simultâneas exceder o limite, a limitação será atingida e você verá falhas aleatórias.
- Se o número total de solicitações exceder o limite, a conta do Salesforce será bloqueada por 24 horas.

Você também pode receber a mensagem de erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Para obter mais informações, veja a seção "Limites de Solicitações da API" na seção [Salesforce Developer Limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limites do Desenvolvedor Salesforce).

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre propriedades usadas para definir entidades da Fábrica de Dados específicas do conector Salesforce Service Cloud.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Salesforce.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type |A propriedade do tipo deve ser definida como **SalesforceServiceCloud**. |Sim |
| environmentUrl | Especifique a URL da instância da Nuvem de Serviço do Salesforce. <br> – O padrão é `"https://login.salesforce.com"`. <br> – Para copiar dados da área restrita, especifique `"https://test.salesforce.com"`. <br> – Para copiar dados do domínio personalizado, especifique, por exemplo, `"https://[domain].my.salesforce.com"`. |Não |
| Nome de Usuário |Especifique um nome de usuário para a conta de usuário. |Sim |
| password |Especifique um senha para a conta de usuário.<br/><br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Sim |
| securityToken |Especifique um token de segurança para a conta de usuário. <br/><br/>Para saber mais sobre os tokens de segurança em geral, veja [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) (Segurança e a API). O token de segurança só pode ser ignorado se você adicionar o IP do Integration Runtime à [lista de endereços IP confiáveis](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) no Salesforce. Ao usar o Azure IR, consulte [endereços IP do Azure Integration Runtime](azure-integration-runtime-ip-addresses.md).<br/><br/>Para obter instruções sobre como obter e redefinir um token de segurança, consulte [Obter um token de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Marque esse campo como SecureString para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). |Não |
| apiVersion | Especifique a versão de API Salesforce `48.0`REST/Bulk para usar, por exemplo. Por padrão, o conector usa [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) para copiar dados do Salesforce e usa [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) para copiar dados para o Salesforce. | Não |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não para a fonte, Sim para o coletor se o serviço vinculado à fonte não possuir integration runtime |

>[!IMPORTANT]
>Quando você copia dados na Nuvem de Serviço suscola do Salesforce, o Tempo de Execução de Integração do Azure padrão não pode ser usado para executar a cópia. Em outras palavras, se o serviço vinculado à fonte não tiver um tempo de execução de integração especificado, crie explicitamente [um Runtime de integração do Azure](create-azure-integration-runtime.md#create-azure-ir) com um local próximo à instância da Nuvem de Serviço suslo do Salesforce. Associe o serviço vinculado ao Salesforce Service Cloud como no exemplo a seguir.

**Exemplo: Credenciais de armazenamento no Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: Credenciais de armazenamento no Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados Salesforce Service Cloud.

Para copiar dados de e para salesforce Service Cloud, as seguintes propriedades são suportadas.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade do tipo deve ser definida como **SalesforceServiceCloudObject**.  | Sim |
| objectApiName | O nome do objeto de Salesforce para recuperar dados. | Não para fonte, Sim para o coletor |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Exemplo:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **RelationalTable**. | Sim |
| tableName | Nome da tabela em Salesforce Service Cloud. | Não (se "query" na fonte da atividade for especificada) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista de propriedades suportadas pela fonte e sink do Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud como um tipo de origem

Para copiar dados do Salesforce Service Cloud, as seguintes propriedades são suportadas na seção **de origem de** atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo da fonte de atividade de cópia deve ser definida como **SalesforceServiceCloudSource**. | Sim |
| Consulta |Utiliza a consulta personalizada para ler os dados. É possível usar a consulta [SOQL (Salesforce Object Query Language)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) ou a consulta SQL-92. Veja mais dicas na seção [dicas de consulta](#query-tips). Se a consulta não for especificada, todos os dados do objeto Salesforce Service Cloud especificados em "objectApiName" no conjunto de dados serão recuperados. | Não (se "objectApiName" no conjunto de dados for especificado) |
| readBehavior | Indica se deve consultar os registros existentes, ou consultar todos os registros, incluindo o que foi excluído. Se não for especificado, o comportamento padrão é o primeiro. <br>Valores permitidos: **query** (padrão), **queryAll**.  | Não |

> [!IMPORTANT]
> A parte "__c" do **Nome da API** é necessária para qualquer objeto personalizado.

![Lista Nome da API de conexão do Salesforce ao Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud como um tipo de pia

Para copiar dados no Salesforce Service Cloud, as seguintes propriedades são suportadas na seção **de sumidouro de** atividade de cópia.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade tipo do dissipador de atividadede cópia deve ser definida como **SalesforceServiceCloudSink**. | Sim |
| writeBehavior | O comportamento da operação de gravação.<br/>Valores permitidos são **Insert** e **Upsert**. | Não (o padrão é Insert) |
| externalIdFieldName | O nome do campo de ID externo para a operação upsert. O campo especificado deve ser definido como "Campo de dide externo" no objeto Salesforce Service Cloud. Ele não pode ter valores nulos nos dados de entrada correspondentes. | Sim para "Upsert" |
| writeBatchSize | A contagem de linhas de dados gravados no Salesforce Service Cloud em cada lote. | Não (o padrão é 5.000) |
| ignoreNullValues | Indica se deve ignorar valores NULL de dados de entrada durante a operação de gravação.<br/>Os valores permitidos são **True** e **False**.<br>- **True**: Deixa os dados no objeto de destino inalterados quando você faz uma operação upsert ou atualização. Insira um valor padrão definido quando você faz uma operação insert.<br/>- **False**: atualiza os dados no objeto de destino como NULL quando você faz uma operação upsert ou atualização. Insira um valor NULL quando você faz uma operação insert. | Não (padrão é falso) |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Dicas de consulta

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Recuperar dados de um relatório da Salesforce Service Cloud

Você pode recuperar dados dos relatórios do Salesforce `{call "<report name>"}`Service Cloud especificando uma consulta como . Um exemplo é `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Recuperar registros excluídos da lixeira de nuvem de serviço de salesforce

Para consultar os registros excluídos suavemente da Caixa de Reciclagem `readBehavior` `queryAll`de Nuvem de Serviço de Serviço saqueado, você pode especificar como . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Diferença entre a sintaxe de consulta SOQL e SQL

Ao copiar dados do Salesforce Service Cloud, você pode usar a consulta SOQL ou a consulta SQL. Note que as duas têm suporte diferente de sintaxe e funcionalidade, não combine-as. Você é sugerido a usar a consulta SOQL que é suportada nativamente pela Salesforce Service Cloud. A tabela a seguir lista as principais diferenças:

| Sintaxe | Modo SOQL | Modo SQL |
|:--- |:--- |:--- |
| Seleção de coluna | Precisa enumerar os campos a serem copiados na consulta, por exemplo.`SELECT field1, filed2 FROM objectname` | `SELECT *` tem suporte além da seleção de colunas. |
| Aspas | Nomes de campos/objetos não podem ser entre aspas. | Nomes de campos/objetos podem ser entre aspas, p. ex. `SELECT "id" FROM "Account"` |
| Formato de data/hora |  Consulte os detalhes [aqui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) e exemplos na próxima seção. | Consulte os detalhes [aqui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) e exemplos na próxima seção. |
| Valores boolianos | Representado como `False` e `True`, p. ex. `SELECT … WHERE IsDeleted=True`. | Representado como 0 ou 1, p. ex. `SELECT … WHERE IsDeleted=1`. |
| Renomeação de coluna | Sem suporte. | Com suporte, p. ex.: `SELECT a AS b FROM …`. |
| Relação | Com suporte, p. ex. `Account_vod__r.nvs_Country__c`. | Sem suporte. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperar dados usando um onde cláusula na coluna DateTime

Ao especificar a consulta SQL ou SOQL, preste atenção à diferença de formato DateTime. Por exemplo:

* **Amostra de SOQL:**`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Amostra de SQL:**`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Erro de MALFORMED_QUERY:Truncado

Se você acertar o erro de "MALFORMED_QUERY: Truncado", normalmente é devido a você ter coluna do tipo JunctionIdList em dados e salesforce tem limitação no suporte a esses dados com grande número de linhas. Para mitigar, tente excluir a coluna JunctionIdList ou limitar o número de linhas a serem copiadas (você pode particionar para várias executações de atividade de cópia).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapeamento de tipo de dados para salesforce service cloud

Quando você copia dados do Salesforce Service Cloud, os seguintes mapeamentos são usados dos tipos de dados da Salesforce Service Cloud para os tipos de dados provisórios da Fábrica de Dados. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados da Nuvem de Serviço de Salesforce | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| Numeração automática |String |
| Caixa de seleção |Boolean |
| Moeda |Decimal |
| Data |Datetime |
| Data/hora |Datetime |
| Email |String |
| ID |String |
| Relação de pesquisa |String |
| Lista de seleção múltipla |String |
| Número |Decimal |
| Porcentagem |Decimal |
| Telefone |String |
| Lista de seleção |String |
| Texto |String |
| Área de texto |String |
| Área de texto (longo) |String |
| Área de texto (Rich) |String |
| Texto (criptografado) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Propriedades de atividade de procurar

Para saber detalhes sobre as propriedades, verifique a [atividade do Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
