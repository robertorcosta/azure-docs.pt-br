---
title: Listar contêineres de API SQL restauráveis no Azure Cosmos DB usando a API REST
description: Mostrar o feed de eventos de todas as mutações feitas em todos os contêineres de Azure Cosmos DB SQL em um banco de dados específico. Isso ajuda no cenário em que o contêiner foi acidentalmente excluído.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 90018d3e1b793575830ba34756ad685927612006
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527275"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Listar contêineres de API SQL restauráveis no Azure Cosmos DB usando a API REST

Mostrar o feed de eventos de todas as mutações feitas em todos os contêineres de Azure Cosmos DB SQL em um banco de dados específico. Isso ajuda no cenário em que o contêiner foi acidentalmente excluído. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Em | Obrigatório | Type | Descrição |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | True |string| O GUID de instanceId de uma conta de banco de dados restaurável. |
| **local** | caminho | True | string| Azure Cosmos DB região, com espaços entre palavras e cada palavra em maiúsculas. |
| **subscriptionId** | caminho | True | string| A ID da assinatura de destino. |
| **versão da API** | Consulta | True | string | A versão da API a ser usada para esta operação. |
| **restorableSqlDatabaseRid** | Consulta | |string| A ID de recurso do banco de dados SQL. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Descrição |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição | | --- || --- | | [Contêiner](#container) | Objeto de recurso de contêiner do SQL Azure Cosmos DB | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [ErrorResponse](#errorresponse) | Resposta de erro. | | [OperationType](#operationtype) | Enum para indicar o tipo de operação do evento. | | [Recurso](#resource) | O recurso de um evento de contêiner do SQL Azure Cosmos DB | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Um evento de contêiner do SQL Azure Cosmos DB | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | As propriedades de um evento de contêiner Azure Cosmos DB SQL | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | A resposta de operação de lista, que contém os eventos de contêiner SQL e suas propriedades. |

### <a name="container"></a><a id="container"></a>Contêiner

Objeto de recurso de contêiner do SQL Azure Cosmos DB

| **Nome**  |  do **Tipo**  |  de **Descrição** | | --- || --- | ---| | _etag | cadeia de caracteres | Uma propriedade gerada pelo sistema que representa a ETag de recurso necessária para o controle de simultaneidade otimista. | | _rid | cadeia de caracteres | Uma propriedade gerada pelo sistema. Um identificador exclusivo. | | _self | cadeia de caracteres | Uma propriedade gerada pelo sistema que especifica o caminho endereçável do recurso de contêiner. | | _ts | | Uma propriedade gerada pelo sistema que denota o último carimbo de data/hora atualizado do recurso. | | ID | cadeia de caracteres | Nome do contêiner de Azure Cosmos DB SQL |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Uma resposta de erro do serviço.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| erro | [ErrorResponse](#errorresponse)| Resposta de erro. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Resposta de erro.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| code |string| Código do erro. |
| message |string| Mensagem de erro indicando por que a operação falhou. |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enum para indicar o tipo de operação do evento.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| Criar |string|evento de criação de contêiner|
| Excluir |string|evento de exclusão de contêiner|
| Substitua |string|evento de modificação do contêiner|
| SystemOperation |string|evento de modificação de contêiner disparado pelo sistema. Este evento não é iniciado pelo usuário|

### <a name="resource"></a><a id="resource"></a>Recurso

O recurso de um evento de contêiner de Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| _rid |string| Uma propriedade gerada pelo sistema. Um identificador exclusivo. |
| contêiner |[Contêiner](#container)| Objeto de recurso de contêiner do SQL Azure Cosmos DB |
| eventTimestamp |string| A hora em que esse evento de contêiner ocorreu. |
| operationType |[OperationType](#operationtype)| O tipo de operação deste evento de contêiner. |
| ownerId |string| O nome do contêiner SQL. |
| ownerResourceId |string| A ID de recurso do contêiner SQL. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Um evento de contêiner de Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | ---
| ID |string| O identificador de recurso exclusivo do recurso de Azure Resource Manager. |
| name |string| O nome do recurso de Azure Resource Manager. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| As propriedades de um evento de contêiner do SQL. |
| tipo |string| O tipo de recurso do Azure. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

As propriedades de um evento de contêiner de Azure Cosmos DB SQL

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| recurso |[Recurso](#resource)| O recurso de um evento de contêiner de Azure Cosmos DB SQL |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

A resposta de operação de lista, que contém os eventos de contêiner SQL e suas propriedades.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Lista de eventos de contêiner SQL e suas propriedades. |

## <a name="next-steps"></a>Próximas etapas

* [Listar bancos de dados restauráveis](restorable-mongodb-databases-list.md)  na API Azure Cosmos DB para MongoDB usando a API REST.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.