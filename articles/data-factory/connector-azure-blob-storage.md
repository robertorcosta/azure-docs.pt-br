---
title: Copiar dados para ou do Armazenamento de Blobs do Azure usando o Data Factory.| Microsoft Docs
description: Saiba como copiar dados de armazenamentos de dados de origem com suporte para o Armazenamento de Blobs do Azure ou do Armazenamento de Blobs para armazenamentos de dados de coletor com suporte, usando o Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: da8b4ebd5cf1e7a57842a116e5d9e21e3c3f7874
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387296"
---
# <a name="copy-data-to-or-from-azure-blob-storage-by-using-azure-data-factory"></a>Copiar dados de ou para o Armazenamento de Blobs do Azure usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-azure-blob-connector.md)
> * [Versão atual](connector-azure-blob-storage.md)

Este artigo descreve como copiar dados de e para o armazenamento de BLOBs do Azure. Para saber mais sobre o Azure Data Factory, leia as [artigo introdutório](introduction.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Este conector de blob do Azure tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Mapeando fluxo de dados](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Excluir atividade](delete-activity.md)

Especificamente, este conector de armazenamento de Blob dá suporte a:

- Copiar blobs de e para contas de Armazenamento do Azure de uso geral e para armazenamento de blobs Quente/Frio. 
- Copiar blobs usando chave de conta, assinatura de acesso compartilhado de serviço, entidade de serviço ou identidades gerenciadas para autenticações de recursos do Azure.
- Copiar blobs de blocos, acréscimos ou de páginas e copiar dados somente para blobs de blocos.
- Copiar blobs no estado em que se encontram ou analisando ou gerando blobs com os [formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md).

>[!NOTE]
>Se você habilitar a opção _"permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento"_ nas configurações do firewall do armazenamento do Azure, o uso de Azure Integration Runtime para se conectar ao armazenamento de BLOBs falhará com um erro proibido, pois o ADF não será tratado como confiável Serviço da Microsoft. Conecte-se por meio de um Integration Runtime hospedado internamente.

## <a name="get-started"></a>Comece agora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas para o Armazenamento de Blobs.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

O conector de Blob do Azure suporta os seguintes tipos de autenticação, consulte a seção correspondente sobre detalhes:

- [Autenticação de chave de conta](#account-key-authentication)
- [Autenticação de assinatura de acesso compartilhado](#shared-access-signature-authentication)
- [Autenticação de entidade de serviço](#service-principal-authentication)
- [Identidades gerenciadas para autenticação de recursos do Azure](#managed-identity)

>[!NOTE]
>Ao usar o polybase para carregar dados em SQL Data Warehouse, se o armazenamento de blob de origem ou de preparo estiver configurado com o ponto de extremidade de rede virtual, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase e usar o Integration Runtime de hospedagem interna com a versão 3,18 ou superior. Consulte a seção [autenticação de identidade gerenciada](#managed-identity) com mais pré-requisitos de configuração.

>[!NOTE]
>As atividades HDInsights e Azure Machine Learning só dão suporte à autenticação de chave de conta de armazenamento de BLOBs do Azure

### <a name="account-key-authentication"></a>Autenticação de chave de conta

Para usar a autenticação de chave de conta de armazenamento, há suporte para as seguintes propriedades:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerido) ou **AzureStorage** (consulte as observações abaixo). |SIM |
| connectionString | Especifique as informações necessárias para se conectar ao Armazenamento para a propriedade connectionString. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar a chave de conta no Azure Key Vault e efetuar pull da configuração `accountKey` da cadeia de conexão. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que oferece mais detalhes. |SIM |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>Não há suporte para o ponto de extremidade do serviço blob secundário ao usar a autenticação de chave de conta. Você pode usar outros tipos de autenticação.

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave da conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>Autenticação de assinatura de acesso compartilhado

Uma assinatura de acesso compartilhado fornece acesso delegado aos recursos da sua conta de armazenamento. Você pode usar uma assinatura de acesso compartilhado para conceder a um cliente permissões limitadas para objetos em sua conta de armazenamento por determinado tempo. Não é preciso compartilhar as chaves de acesso da conta. A assinatura de acesso compartilhado é um URI que engloba em seus parâmetros de consulta todas as informações necessárias para o acesso autenticado a um recurso de armazenamento. Para acessar recursos de armazenamento com a assinatura de acesso compartilhado, o cliente só precisa passar a assinatura de acesso compartilhado ao construtor ou método apropriado. Para saber mais sobre assinaturas de acesso compartilhado, veja [Assinaturas de Acesso Compartilhado: entendendo o modelo de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
>- O Data Factory agora dá suporte para **assinaturas de acesso compartilhado de serviço** e **assinaturas de acesso compartilhado de conta**. Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-sas-overview.md).
>- Na configuração do conjunto de dados anterior, o caminho da pasta é o caminho absoluto começando do nível de contêiner. Você precisa configurar um alinhado com o caminho em seu URI SAS.

> [!TIP]
> Para gerar uma assinatura de acesso compartilhado de serviço para a conta de armazenamento, você pode executar os comandos a seguir do PowerShell. Substitua os espaços reservados e conceda a permissão necessária.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar a autenticação de assinatura de acesso compartilhado, há suporte para as seguintes propriedades:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage** (sugerido) ou **AzureStorage** (consulte as observações abaixo). |SIM |
| sasUri | Especifique o URI de assinatura de acesso compartilhado para os recursos de Armazenamento, como blob/contêiner. <br/>Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. Você também pode colocar o token SAS em Azure Key Vault para aproveitar a rotação automática e remover a parte do token. Confira os exemplos a seguir e o artigo [Armazenar credenciais no Azure Key Vault](store-credentials-in-key-vault.md) que oferece mais detalhes. |SIM |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>Se você estiver usando o serviço vinculado do tipo "AzureStorage", ele ainda terá suporte no estado em que se encontra, enquanto você recebe a sugestão de usar esse novo tipo de serviço vinculado "AzureBlobStorage" no futuro.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<container>.blob.core.windows.net/?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exemplo: armazenar a chave da conta no Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<container>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ao criar um URI de assinatura de acesso compartilhado, considere os seguintes pontos:

- Defina as permissões apropriadas de leitura/gravação em objetos com base em como o serviço vinculado (leitura, gravação, leitura/gravação) será usado no data factory.
- Defina o **Tempo de expiração** adequadamente. Certifique-se de que o acesso aos objetos de Armazenamento não expira dentro do período ativo do pipeline.
- O URI deve ser criado no contêiner/blob correto com base na necessidade. Um URI de assinatura de acesso compartilhado a um blob permite que o Data Factory acesse o blob específico. Um URI de assinatura de acesso compartilhado para um contêiner de armazenamento de Blob permite que o Data Factory se reitere pelos blobs naquele contêiner. Para fornecer acesso a mais/menos objetos posteriormente, ou para atualizar o URI de assinatura de acesso compartilhado, lembre-se de atualizar o serviço vinculado ao novo URI.

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Para autenticação de entidade de serviço de Armazenamento do Microsoft Azure em geral, consulte [Autenticar o acesso ao Armazenamento do Microsoft Azure usando o Azure Active Directory](../storage/common/storage-auth-aad.md).

Para usar a autenticação de entidade de serviço, siga estas etapas:

1. Registre uma entidade de aplicativo no Azure Active Directory (Azure AD) seguindo [Registre o aplicativo com um locatário do Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Anote os seguintes valores, que são usados para definir o serviço vinculado:

    - ID do aplicativo
    - Chave do aplicativo
    - ID do locatário

2. Conceda a permissão apropriada à entidade de serviço no armazenamento de Blob do Azure. Consulte [Gerenciar os direitos de acesso aos dados do Armazenamento do Microsoft Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no Controle de acesso (IAM), conceda pelo menos a função **Leitor de Dados do Blob de Armazenamento**.
    - **Como coletor**, no Controle de acesso (IAM), conceda pelo menos a função **Colaborador de Dados do Blob de Armazenamento**.

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |SIM |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |SIM |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | SIM |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como  **SecureString** para armazená-lo com segurança no Data Factory ou [referencie um segredo armazenado no Cofre de Chaves do Azure](store-credentials-in-key-vault.md). | SIM |
| locatário | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. Para recuperá-lo, passe o mouse no canto superior direito do portal do Azure. | SIM |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

>[!NOTE]
>A autenticação da entidade de serviço só é suportada pelo serviço vinculado do tipo "AzureBlobStorage", mas não pelo serviço vinculado do tipo "AzureStorage" anterior.

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades gerenciadas para autenticação de recursos do Azure

Um data factory pode ser associado a uma [identidade gerenciada para recursos do Azure](data-factory-service-identity.md), que representa esse data factory específico. Você pode usar essa identidade gerenciada diretamente para autenticação de armazenamento de BLOBs semelhante ao uso de sua própria entidade de serviço. Ele permite que este alocador designado acesse e copie dados de/para o seu armazenamento de Blob.

Consulte [autenticar o acesso ao armazenamento do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md) para a autenticação de armazenamento do Azure em geral. Para usar identidades gerenciadas para autenticação de recursos do Azure, siga estas etapas:

1. [Recupere data Factory informações de identidade gerenciadas](data-factory-service-identity.md#retrieve-managed-identity) copiando o valor de "ID do aplicativo de identidade de serviço" gerado junto com sua fábrica.

2. Conceda a permissão adequada de identidade gerenciada no armazenamento do Azure Blob. Consulte [Gerenciar os direitos de acesso aos dados do Armazenamento do Microsoft Azure com o RBAC](../storage/common/storage-auth-aad-rbac.md) com mais detalhes sobre as funções.

    - **Como fonte**, no Controle de acesso (IAM), conceda pelo menos a função **Leitor de Dados do Blob de Armazenamento**.
    - **Como coletor**, no Controle de acesso (IAM), conceda pelo menos a função **Colaborador de Dados do Blob de Armazenamento**.

>[!IMPORTANT]
>Se você usar o polybase para carregar dados do blob (como origem ou como preparo) em SQL Data Warehouse, ao usar a autenticação de identidade gerenciada para o blob, certifique-se de também seguir as etapas 1 e 2 nesta [orientação](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) como 1) registrar seu servidor de banco de dados SQL com o Azure active Directory (Azure AD) e 2) atribuir a função de colaborador de dados de blob de armazenamento ao servidor do banco de dados SQL; o restante são tratados por Data Factory. Se o armazenamento de BLOBs estiver configurado com um ponto de extremidade de rede virtual do Azure, para usar o polybase para carregar dados dele, você deverá usar a autenticação de identidade gerenciada conforme exigido pelo polybase.

Estas propriedades são suportadas por um serviço vinculado de armazenamento de Blob do Azure:

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade de tipo deve ser definida como **AzureBlobStorage**. |SIM |
| serviceEndpoint | Especifique o ponto de extremidade do serviço de armazenamento de Blob do Azure com o padrão de `https://<accountName>.blob.core.windows.net/`. |SIM |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime do Azure ou o Integration Runtime auto-hospedado (se o armazenamento de dados estiver em uma rede privada). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não |

> [!NOTE]
> As identidades gerenciadas para a autenticação de recursos do Azure t~e suporte apenas pelo serviço vinculado do tipo "AzureBlobStorage", mas não pelo serviço vinculado do tipo "AzureStorage" anterior. 

**Exemplo:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de dados](concepts-datasets-linked-services.md). 

- Para **parquet, texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e DataSet de formato binário](#format-based-dataset) .
- Para outros formatos como o **formato Orc/JSON**, consulte [outra](#other-format-dataset) seção do conjunto de exemplo.

### <a name="format-based-dataset"></a>Parquet, texto delimitado, JSON, Avro e conjunto de DataSet de formato binário

Para copiar dados de e para o armazenamento de BLOBs em parquet, texto delimitado, formato Avro ou binário, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) em conjunto de dados com base em formato e configurações com suporte. As propriedades a seguir têm suporte para o blob do Azure em configurações `location` no conjunto de base de formato:

| Propriedade   | Descrição                                                  | obrigatórios |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | A propriedade Type do local no DataSet deve ser definida como **AzureBlobStorageLocation**. | SIM      |
| contêiner  | O contêiner de BLOB.                                          | SIM      |
| folderPath | O caminho para a pasta sob o contêiner fornecido. Se você quiser usar curinga para filtrar a pasta, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |
| fileName   | O nome do arquivo sob o contêiner especificado + folderPath. Se você quiser usar curinga para filtrar arquivos, ignore essa configuração e especifique nas configurações de origem da atividade. | Não       |

> [!NOTE]
>
> **AzureBlob** tipo de conjunto de dados com parquet/formato de texto mencionado na próxima seção ainda tem suporte como é para a atividade de cópia/pesquisa/GetMetadata para compatibilidade com versões anteriores, mas não funciona com o fluxo de dados de mapeamento. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Outro conjunto de DataSet de formato

Para copiar dados de e para o armazenamento de BLOBs no formato ORC/JSON, defina a propriedade Type do conjunto de dados como **AzureBlob**. Há suporte para as seguintes propriedades.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como **AzureBlob**. |SIM |
| folderPath | Caminho para o contêiner e a pasta no armazenamento de blob. <br/><br/>O filtro curinga é permitido para o caminho, excluindo o nome do contêiner. Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br/><br/>Exemplos: myblobcontainer/myblobfolder/; veja mais exemplos em [Exemplos de filtro de pasta e arquivo](#folder-and-file-filter-examples). |Sim para atividade de cópia/pesquisa, não para a atividade GetMetadata |
| fileName | **Filtro de nome ou curinga** para o blob (s) sob o "folderPath" especificado. Se você não especificar algum valor para essa propriedade, o conjunto de dados apontará para todos os blobs na pasta. <br/><br/>Para filtro, os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único).<br/>– Exemplo 1: `"fileName": "*.csv"`<br/>– Exemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` para se seu nome de arquivo real curinga ou esse caractere de escape dentro de escape.<br/><br/>Quando fileName não é especificado para um conjunto de dados de saída e **preserveHierarchy** não é especificado no coletor de atividade, a atividade de cópia gera automaticamente o nome do blob com o seguinte padrão: "*Data. [ GUID de ID de execução de atividade]. [GUID If FlattenHierarchy]. [Formatar se configurado]. [compactação se configurada]* ", por exemplo," Data. 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. txt. gz "; Se você copiar da fonte de tabela usando o nome da tabela em vez da consulta, o padrão de nome será " *[nome da tabela]. [ formato]. [compactação se configurada]* ", por exemplo," MyTable. csv ". |Não |
| modifiedDatetimeStart | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não |
| modifiedDatetimeEnd | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br/><br/> Lembre-se de que o desempenho geral da movimentação de dados será afetado ao habilitar essa configuração quando você desejar fazer o filtro de arquivo de grandes quantidades de arquivos. <br/><br/> As propriedades podem ser nulas, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de os.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora.| Não |
| format | Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.<br/><br/>Se você quiser analisar ou gerar arquivos com um formato específico, haverá suporte para os seguintes tipos de formatos de arquivo: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Defina a propriedade **type** sob **format** para um desses valores. Para saber mais, veja as seções [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Não (somente para o cenário de cópia binária) |
| compactação | Especifique o tipo e o nível de compactação para os dados. Para obter mais informações, consulte [Formatos de arquivo e codecs de compactação com suporte](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Os tipos com suporte são: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Os níveis de suporte são **Ideal** e **Mais rápido**. |Não |

>[!TIP]
>Para copiar todos os blobs em uma pasta, especifique **folderPath** somente.<br>Para copiar um único objeto binário grande com um determinado nome, especifique **folderPath** com parte da pasta e **fileName** com nome de arquivo.<br>Para copiar um subconjunto de blobs em uma pasta, especifique **folderPath** com parte da pasta e **fileName** com filtro curinga. 

**Exemplo:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção apresenta uma lista das propriedades com suporte na origem e no coletor do armazenamento de blob.

### <a name="blob-storage-as-a-source-type"></a>Armazenamento de blob como um tipo de fonte

- Para copiar de **parquet, de texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos, como o **formato Orc**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, JSON, Avro e fonte de formato binário

Para copiar dados de e para o armazenamento de BLOBs em **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) no conjunto de dados baseado em formato e configurações com suporte. As propriedades a seguir têm suporte para o blob do Azure em configurações `storeSettings` em fonte de cópia baseada em formato:

| Propriedade                 | Descrição                                                  | obrigatórios                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageReadSetting**. | SIM                                           |
| recursive                | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor. Os valores permitidos são **true** (padrão) e **false**. | Não                                            |
| wildcardFolderPath       | O caminho da pasta com caracteres curinga no contêiner fornecido configurado no conjunto de dados para filtrar as pastas de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape. <br>Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Não                                            |
| wildcardFileName         | O nome do arquivo com caracteres curinga no contêiner especificado + folderPath/wildcardFolderPath para filtrar os arquivos de origem. <br>Os curingas permitidos são: `*` (corresponde a zero ou mais caracteres) e `?` (corresponde a zero ou caractere único); use `^` para escape se o nome de pasta atual tiver curinga ou esse caractere interno de escape.  Veja mais exemplos em [Exemplos de filtro de pastas e arquivos](#folder-and-file-filter-examples). | Sim se `fileName` não for especificado no DataSet |
| modifiedDatetimeStart    | Filtro de arquivos com base no atributo: última modificação. Os arquivos serão selecionados se a hora da última alteração estiver dentro do intervalo de tempo entre `modifiedDatetimeStart` e `modifiedDatetimeEnd`. A hora é aplicada ao fuso horário de UTC no formato "2018-12-01T05:00:00Z". <br> As propriedades podem ser NULL, o que significa que nenhum filtro de atributo de arquivo será aplicado ao conjunto de dados.  Quando `modifiedDatetimeStart` tem o valor de data e hora, mas `modifiedDatetimeEnd` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é maior ou igual ao valor de data e hora.  Quando `modifiedDatetimeEnd` tem o valor de data e hora, mas `modifiedDatetimeStart` for NULL, isso significa que serão selecionados os arquivos cujo último atributo modificado é menor que o valor de data e hora. | Não                                            |
| modifiedDatetimeEnd      | Mesmo que acima.                                               | Não                                            |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não                                            |

> [!NOTE]
> Para o formato de texto parquet/delimitado, a origem da atividade de cópia do tipo **blobry** mencionada na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Outra fonte de formato

Para copiar dados do armazenamento de BLOBs no **formato Orc**, defina o tipo de fonte na atividade de cópia como **blobname**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type da fonte da atividade de cópia deve ser definida como **BlobSource**. |SIM |
| recursive | Indica se os dados são lidos recursivamente das subpastas ou somente da pasta especificada. Observe que quando recursiva é definida como true e o coletor é um armazenamento baseado em arquivo, uma pasta vazia ou subpasta não é copiada ou criada no coletor.<br/>Os valores permitidos são **true** (padrão) e **false**. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="blob-storage-as-a-sink-type"></a>Armazenamento de blob como um tipo de coletor

- Para copiar de **parquet, de texto delimitado, JSON, Avro e formato binário**, consulte a seção [parquet, texto delimitado, JSON, Avro e fonte de formato binário](#format-based-source) .
- Para copiar de outros formatos, como o **formato Orc**, consulte [outra seção fonte de formato](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, texto delimitado, JSON, Avro e fonte de formato binário

Para copiar dados do armazenamento de BLOBs em **parquet, texto delimitado, JSON, Avro e formato binário**, consulte [formato parquet](format-parquet.md), [formato de texto delimitado](format-delimited-text.md), [formato de Avro](format-avro.md) e artigo de [formato binário](format-binary.md) na fonte da atividade de cópia baseada em formato e configurações com suporte. As propriedades a seguir têm suporte para o blob do Azure em configurações `storeSettings` no coletor de cópia com base em formato:

| Propriedade                 | Descrição                                                  | obrigatórios |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | A propriedade Type em `storeSettings` deve ser definida como **AzureBlobStorageWriteSetting**. | SIM      |
| copyBehavior             | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não       |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não       |

> [!NOTE]
> Para o formato de texto parquet/delimitado, o coletor de atividade de cópia do tipo **BlobSink** mencionado na próxima seção ainda tem suporte como está para compatibilidade com versões anteriores. Você é sugerido para usar esse novo modelo no futuro, e a interface do usuário de criação do ADF mudou para gerar esses novos tipos.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobStorageWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Outro coletor de formato

Para copiar dados para o armazenamento de blob no **formato Orc**, defina o tipo de coletor na atividade de cópia como **BlobSink**. As propriedades a seguir têm suporte na seção **coletor**.

| Propriedade | Descrição | obrigatórios |
|:--- |:--- |:--- |
| type | A propriedade type do coletor da atividade de cópia deve ser definida como **BlobSink**. |SIM |
| copyBehavior | Define o comportamento de cópia quando a fonte for de arquivos de um armazenamento de dados baseado em arquivo.<br/><br/>Valores permitidos são:<br/><b>- PreserveHierarchy (padrão)</b>: Preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><b>- FlattenHierarchy</b>: Todos os arquivos da pasta de origem estão no primeiro nível da pasta de destino. Os arquivos de destino têm os nomes gerados automaticamente. <br/><b>- MergeFiles</b>: Mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo ou do blob for especificado, o nome do arquivo mesclado será o nome especificado. Caso contrário, ele será um nome de arquivo gerado automaticamente. | Não |
| maxConcurrentConnections | O número de conexões a serem conectadas ao repositório de armazenamento simultaneamente. Especifique somente quando quiser limitar a conexão simultânea com o armazenamento de dados. | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Exemplos de filtro de pasta e arquivo

Esta seção descreve o comportamento resultante do caminho da pasta e do nome de arquivo com filtros curinga.

| folderPath | fileName | recursive | Estrutura da pasta de origem e resultado do filtro (os arquivos em **negrito** são recuperados)|
|:--- |:--- |:--- |:--- |
| `container/Folder*` | (vazio, usar padrão) | falso | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | (vazio, usar padrão) | true | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | falso | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `container/Folder*` | `*.csv` | true | contêiner<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Arquivo5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;OutraPastaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Alguns exemplos de recursive e copyBehavior

Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

| recursive | copyBehavior | Estrutura de pasta de origem | Destino resultante |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a mesma estrutura da origem:<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 |
| true |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo5 |
| true |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A Pasta1 de destino é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. |
| falso |preserveHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |flattenHierarchy | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura: <br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para o Arquivo2<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |
| falso |mergeFiles | Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5 | A pasta de destino Pasta1 é criada com a seguinte estrutura<br/><br/>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos de Arquivo1 + Arquivo2 são mesclados em um arquivo com um nome de arquivo gerado automaticamente. nome gerado automaticamente para o Arquivo1<br/><br/>A Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não é selecionada. |

## <a name="mapping-data-flow-properties"></a>Mapeando Propriedades de fluxo de dados

Aprenda detalhes da [transformação de origem](data-flow-source.md) e da [transformação de coletor](data-flow-sink.md) no fluxo de dados de mapeamento.

## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Propriedades da atividade GetMetadata

Para saber detalhes sobre as propriedades, verifique a [atividade GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Excluir propriedades da atividade

Para obter detalhes sobre as propriedades, marque a [atividade de exclusão](delete-activity.md)

## <a name="next-steps"></a>Próximos passos

Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).
