---
title: Referência de dados de monitoramento do armazenamento do Azure | Microsoft Docs
description: Referência de registro e métricas para monitoramento de dados do armazenamento do Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: fa8838dd5eca03d9dd85e424f0163eb9ca8ed5e2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077848"
---
# <a name="azure-storage-monitoring-data-reference"></a>Referência de dados de monitoramento do Azure Storage

Consulte [Monitoramento do armazenamento do Azure](monitor-storage.md) para obter detalhes sobre como coletar e analisar dados de monitoramento para o armazenamento do Azure.

## <a name="metrics"></a>Métricas

As tabelas a seguir listam as métricas da plataforma coletadas para o armazenamento do Azure. 

### <a name="capacity-metrics"></a>Métricas de capacidade

Os valores de métricas de capacidade são enviados para o Azure Monitor a cada hora. Os valores são atualizados diariamente. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

O Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

#### <a name="account-level"></a>Nível de conta

Esta tabela mostra as [métricas de nível de conta](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts).

| Métrica | Descrição |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento utilizada pela conta de armazenamento. Para contas de armazenamento Standard, é a soma da capacidade usada por blob, tabela, arquivo e fila. Para contas de armazenamento Premium e contas de armazenamento de Blob, é o mesmo que Capacidade de Blob. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="blob-storage"></a>Armazenamento de blob

Esta tabela mostra as [métricas de armazenamento de BLOBs](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsblobservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de Blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType** e **BlobTier** ([Definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType** e **BlobTier** ([Definição](#metrics-dimensions)) |
| BlobProvisionedSize | A quantidade de armazenamento provisionada na conta de armazenamento. Essa métrica é aplicável somente a contas de armazenamento Premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |
| ContainerCount    | O número de contêineres na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento usado pelo Índice ADLS Gen2 Hierárquico <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="table-storage"></a>Armazenamento de tabela

Esta tabela mostra as [métricas de armazenamento de tabela](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de Tabelas usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades de tabela na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="queue-storage"></a>Armazenamento de filas

Esta tabela mostra as [métricas de armazenamento de filas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de fila usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número aproximado de mensagens de fila na serviço Fila da conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

#### <a name="file-storage"></a>Armazenamento de arquivos

Esta tabela mostra as [métricas de armazenamento de arquivos](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices).

| Métrica | Descrição |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de Arquivos usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de compartilhamentos de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareProvisionedIOPS | O número de IOPS provisionados em um compartilhamento de arquivos. Essa métrica é aplicável somente ao armazenamento de arquivos premium. <br/><br/> Unidade: bytes <br/> Tipo de agregação: Média |

### <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas, do Armazenamento do Azure para o Azure Monitor, em cada solicitação a uma conta de armazenamento. Caso não haja nenhuma atividade em sua conta de armazenamento, não haverá nenhum dado nas métricas de transações no período. Todas as métricas de transação estão disponíveis no nível de conta e de serviço (armazenamento de Blob, armazenamento de Tabelas, Azure Files e armazenamento de fila). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

O Armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Métrica | Descrição |
| ------------------- | ----------------- |
| Transactions | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e autenticação ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor total de solicitações faturáveis e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. <br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99,99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Armazenamento do Azure oferece suporte às seguintes dimensões para métricas no Azure Monitor.

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de blob somente para métricas de Blob. Os valores com suporte são **BlockBlob**, **PageBlob** e **Azure Data Lake Storage**. Acrescentar Blob está incluído no BlockBlob. |
| **BlobTier** | O Armazenamento do Azure oferece diferentes camadas de acesso que permitem armazenar dados de objeto Blob da maneira mais econômica. Confira mais informações em [Camada de blob de armazenamento do Microsoft Azure](../blobs/storage-blob-storage-tiers.md). Os valores com suporte incluem: <br/> <li>**Frequente**: Camada frequente</li> <li>**Esporádico**: Camada esporádica</li> <li>**Arquivar**: Camada de arquivo</li> <li>**Premium**: Camada Premium para blob de blocos</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de camada para o Blob de páginas premium</li> <li>**Standard**: Tipo de camada para o Blob de páginas padrão</li> <li>**Sem camadas**: Tipo de camada de conta de armazenamento v1 de uso geral</li> |
| **GeoType** | Transação de cluster primário ou secundário. Os valores disponíveis incluem **Primário** e **Secundário**. Aplica-se ao acesso de leitura ao armazenamento com redundância geográfica (RA-GRS) ao ler objetos de um locatário secundário. |
| **ResponseType** | Tipo de resposta da transação. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: Todos os outros erros do lado do servidor, exceto aqueles descritos </li> <li>**ServerBusyError**: Solicitação autenticada que retornou um código de status HTTP 503. </li> <li>**ServerTimeoutError**: Solicitação autenticada de tempo limite que retornou um código de status HTTP 500. O tempo limite foi ultrapassado devido a um erro no servidor. </li> <li>**AuthorizationError**: Solicitação autenticada que falhou devido a um acesso não autorizado de dados ou a uma falha de autorização. </li> <li>**NetworkError**: Solicitação autenticada que falhou devido a erros de rede. Geralmente ocorre quando um cliente fecha prematuramente uma conexão antes da expiração do tempo limite. </li><li>**ClientAccountBandwidthThrottlingError**: A solicitação é limitada na largura de banda por exceder os [limites de escalabilidade da conta de armazenamento](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: A solicitação é limitada na taxa de solicitação por exceder os [limites de escalabilidade da conta de armazenamento](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Outro erro de limitação do cliente. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError são excluídos.</li> <li>**ClientTimeoutError**: Solicitação autenticada de tempo limite que retornou um código de status HTTP 500. Se o tempo limite de rede do cliente ou o tempo limite da solicitação for definido como um valor menor do que o esperado pelo serviço de armazenamento, ele é considerado um tempo limite esperado. Caso contrário, ele será relatado como um ServerTimeoutError. </li> <li>**ClientOtherError**: Todos os outros erros do lado do cliente, exceto aqueles descritos. </li> <li>**Êxito**: Solicitação bem-sucedida</li> <li> **SuccessWithThrottling**: Solicitação bem-sucedida quando um cliente SMB é limitado na primeira tentativa, mas é bem-sucedido após novas tentativas.</li> |
| **ApiName** | O nome da operação. Por exemplo: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Para saber todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autenticação** | Tipo de autenticação usado em transações. Os valores disponíveis incluem: <br/> <li>**AccountKey**: A transação é autenticada com a chave de conta de armazenamento.</li> <li>**SAS**: A transação é autenticada com assinaturas de acesso compartilhado.</li> <li>**OAuth**: A transação é autenticada com tokens de acesso OAuth.</li> <li>**Anonymous**: A transação é solicitada anonimamente. Ele não inclui solicitações de simulação.</li> <li>**AnonymousPreflight**: A transação é a solicitação de simulação.</li> |

Para saber as dimensões de métricas com suporte, você precisa especificar o valor da dimensão para exibir os valores correspondentes das métricas. Por exemplo, se você pesquisar o valor de **Transações** para respostas com sucesso, você precisa filtrar a dimensão **ResponseType** por **Sucesso**. Ou se você pesquisar o valor de **BlobCount** para o Blob de Blocos, você precisa filtrar a dimensão **BlobType** por **BlockBlob**.

## <a name="resource-logs-preview"></a>Logs de recurso (versão prévia)

> [!NOTE]
> Os logs do Armazenamento do Microsoft Azure no Azure Monitor estão em versão preliminar pública e disponíveis para teste de versão preliminar em todas as regiões de nuvem pública. Para se inscrever na versão preliminar, veja [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Essa versão preliminar habilita logs para blobs (incluindo o Azure Data Lake Storage Gen2), arquivos, filas, tabelas, contas de armazenamento Premium nas contas de armazenamento GPv1 e GPv2. Não há suporte para contas de armazenamento clássicas.

A tabela a seguir lista as propriedades dos logs de recursos do armazenamento do Azure quando eles são coletados nos logs de Azure Monitor ou no armazenamento do Azure. As propriedades descrevem a operação, o serviço e o tipo de autorização que foi usado para executar a operação.

### <a name="fields-that-describe-the-operation"></a>Campos que descrevem a operação

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Propriedade | Descrição |
|:--- |:---|
|**time** | A hora universal coordenada (UTC) quando a solicitação foi recebida pelo armazenamento. Por exemplo: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | A ID do recurso da conta de armazenamento. Por exemplo: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | A categoria da operação solicitada. Por exemplo: `StorageRead`, `StorageWrite` ou `StorageDelete`|
|**operationName** | O tipo de operação REST executado. <br> Para obter uma lista completa de operações, consulte o tópico [Análise de Armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | A versão do serviço de armazenamento especificada quando a solicitação foi feita. Isso é equivalente ao valor do cabeçalho **x-ms-version**. Por exemplo: `2017-04-17`.|
|**schemaVersion** | A versão do esquema do registro. Por exemplo: `1.0`.|
|**statusCode** | O código de status HTTP da solicitação. Se a solicitação for interrompida, esse valor poderá ser definido como `Unknown`. <br> Por exemplo: `206` |
|**statusText** | O status da operação solicitada.  Para obter uma lista completa de mensagens de status, consulte o tópico [Análise de Armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Na versão 2017-04-17 e posterior, a mensagem de status `ClientOtherError` não é usada. Em vez disso, esse campo contém um código de erro. Por exemplo: `SASSuccess`  |
|**durationMs** | O tempo total, expressado em milissegundos para executar a operação solicitada. Isso inclui o tempo de leitura da solicitação de entrada e envio da resposta ao solicitante. Por exemplo: `12`.|
|**callerIpAddress** | O endereço IP do solicitante, incluindo o número da porta. Por exemplo: `192.100.0.102:4362`. |
|**correlationId** | A ID usada para correlacionar os logs entre os recursos. Por exemplo: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**local** | O local da conta de armazenamento. Por exemplo: `North Europe`. |
|**protocol**|O protocolo usado na operação. Por exemplo: `HTTP`, `HTTPS`, `SMB` ou `NFS`|
| **uri** | Identificador de recurso uniforme que é solicitado. Por exemplo: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Campos que descrevem como a operação foi autenticada

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Propriedade | Descrição |
|:--- |:---|
|**identity / type** | O tipo de autenticação que foi usado para fazer a solicitação. Por exemplo: `OAuth`, `SAS Key`, `Account Key` ou `Anonymous` |
|**identity / tokenHash**|Este campo é reservado somente para uso interno. |
|**authorization / action** | A ação que é atribuída à solicitação. Por exemplo: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization / roleAssignmentId** | ID de atribuição de função. Por exemplo: `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | A ID de definição de função. Por exemplo: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | A ID da entidade de segurança. Por exemplo: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | O tipo de entidade de segurança. Por exemplo: `ServicePrincipal`. |
|**requester / appID** | A ID do aplicativo de Open Authorization (OAuth) que é usada como solicitante. <br> Por exemplo: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | O público OAuth da solicitação. Por exemplo: `https://storage.azure.com`. |
|**requester / objectId** | A ID do objeto OAuth da solicitação. No caso da autenticação Kerberos, representa o identificador de objeto do usuário autenticado do Kerberos. Por exemplo: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | A ID de locatário OAuth de identidade. Por exemplo: `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | O emissor do token OAuth. Por exemplo: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | O UPN (nome UPN) do solicitante. Por exemplo: `someone@contoso.com`. |
|**requester / userName** | Este campo é reservado somente para uso interno.|

### <a name="fields-that-describe-the-service"></a>Campos que descrevem o serviço

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Propriedade | Descrição |
|:--- |:---|
|**accountName** | O nome da conta de armazenamento. Por exemplo: `mystorageaccount`.  |
|**requestUrl** | A URL que é solicitada. Por exemplo: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | O valor de cabeçalho de **User-Agent**, entre aspas. Por exemplo: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | O valor do cabeçalho de **Referrer**. Por exemplo: `http://contoso.com/about.html`.|
|**clientRequestId** | O valor do cabeçalho **x-ms-client-request-id** da solicitação. Por exemplo: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | O identificador ETag para o objeto retornado, entre aspas. Por exemplo: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | O tempo total, expressado em milissegundos para executar a operação solicitada. Esse valor não inclui a latência de rede (o tempo de leitura da solicitação de entrada e envio da resposta ao solicitante). Por exemplo: `22`. |
|**serviceType** | O serviço associado a essa solicitação. Por exemplo: `blob`, `table`, `files` ou `queue`. |
|**operationCount** | O número de cada operação registrada que está envolvida na solicitação. Essa contagem começa com um índice de `0`. Algumas solicitações exigem mais de uma operação, como uma solicitação para copiar um blob. A maioria das solicitações executa apenas uma operação. Por exemplo: `1`. |
|**requestHeaderSize** | O tamanho do cabeçalho da solicitação, expresso em bytes. Por exemplo: `578`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia. |
|**requestBodySize** | O tamanho dos pacotes de solicitação, expresso em bytes, que são lidos pelo serviço de armazenamento. <br> Por exemplo: `0`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia.  |
|**responseHeaderSize** | O tamanho do cabeçalho da solicitação, expresso em bytes. Por exemplo: `216`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia.  |
|**responseBodySize** | O tamanho dos pacotes de resposta gravados pelo serviço de armazenamento, em bytes. Se uma solicitação for malsucedida, esse valor poderá estar vazia. Por exemplo: `216`.  |
|**requestMd5** | O valor do cabeçalho **Content-MD5** ou do cabeçalho **x-ms-content-md5** na solicitação. O valor de hash MD5 especificado nesse campo representa o conteúdo na solicitação. Por exemplo: `788815fd0198be0d275ad329cafd1830`. <br>Este campo pode estar vazio.  |
|**serverMd5** | O valor de hash MD5 calculado pelo serviço de armazenamento. Por exemplo: `3228b3cf1069a5489b298446321f8521`. <br>Este campo pode estar vazio.  |
|**lastModifiedTime** | A LMT (Hora da Última Modificação) do objeto retornado.  Por exemplo: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Esse campo fica vazio para operações que podem retornar vários objetos. |
|**conditionsUsed** | Uma lista separada por ponto e vírgula de pares chave-valor que representam uma condição. As condições podem ser as seguintes: <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Por exemplo: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | O valor do cabeçalho Content-Length para a solicitação enviada ao serviço de armazenamento. Se a solicitação foi bem-sucedida, esse valor será igual a requestBodySize. Se uma solicitação for malsucedida, esse valor talvez não seja igual a requestBodySize ou pode estar vazio. |
|**tlsVersion** | A versão de TLS usada na conexão da solicitação. Por exemplo: `TLS 1.2`. |
|**smbTreeConnectID** | O **treeConnectId** de Server Message Block (SMB) estabelecido na hora de conexão da árvore. Por exemplo: `0x3` |
|**smbPersistentHandleID** | ID de identificador persistente de uma solicitação SMB2 CREATE que sobrevive a reconexões de rede.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID.Persistent**. Por exemplo: `0x6003f` |
|**smbVolatileHandleID** | ID de identificador volátil de uma solicitação SMB2 CREATE que é reciclado em reconexões de rede.  Referenciado em [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID.Volatile**. Por exemplo: `0xFFFFFFFF00000065` |
|**smbMessageID** | A conexão relativa a **MessageId**. Por exemplo: `0x3b165` |
|**smbCreditsConsumed** | A entrada ou saída consumida pela solicitação, em unidades de 64K. Por exemplo: `0x3` |
|**smbCommandDetail** | Mais informações sobre essa solicitação específica, em vez do tipo geral de solicitação. Por exemplo: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | A **FileID** associada ao arquivo ou ao diretório.  Aproximadamente análogo a NTFS FileID. Por exemplo: `0x9223442405598953` |
|**smbSessionID** | O SMB2 **SessionId** estabelecido no momento da configuração da sessão. Por exemplo: `0x8530280128000049` |
|**smbCommandMajor  uint32** | Valor no **SMB2_HEADER.Command**. Atualmente, esse é um número entre 0 e 18, inclusive. Por exemplo: `0x6` |
|**smbCommandMinor** | A subclasse de **SmbCommandMajor**, quando apropriado. Por exemplo: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Confira também

- Consulte [Monitoramento do armazenamento do Azure](monitor-storage.md) para obter uma descrição do monitoramento do armazenamento do Azure.
- Confira [Como monitorar os recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre o monitoramento de recursos do Azure.