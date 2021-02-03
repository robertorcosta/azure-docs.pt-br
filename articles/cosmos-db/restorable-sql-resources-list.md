---
title: Listar recursos de API do SQL restauráveis no Azure Cosmos DB usando a API REST
description: Retorna uma lista de combinação de banco de dados e contêiner que existe na conta no carimbo de data e hora e no local fornecidos. Isso ajuda em cenários para validar quais recursos existem em determinado carimbo de data/hora e local.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dbee87098dcc712669c332deac656cf5656ef4c4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527227"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Listar recursos de API do SQL restauráveis no Azure Cosmos DB usando a API REST

Retorna uma lista de combinação de banco de dados e contêiner que existe na conta no carimbo de data e hora e no local fornecidos. Isso ajuda em cenários para validar quais recursos existem em determinado carimbo de data/hora e local. Esta API requer `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` permissão.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Com parâmetros opcionais:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Em | Obrigatório | Type | Descrição |
| --- | --- | --- | --- | --- |
| **instanceId** | caminho | True |string| O GUID de instanceId de uma conta de banco de dados restaurável. |
| **local** | caminho | True | string| Azure Cosmos DB região, com espaços entre palavras e cada palavra em maiúsculas. |
| **subscriptionId** | caminho | True | string| A ID da assinatura de destino. |
| **versão da API** | Consulta | True | string | A versão da API a ser usada para esta operação. |
| **restoreLocation** | Consulta | |string| O local onde os recursos restauráveis estão localizados. |
| **restoreTimestampInUtc** | Consulta | |string| O carimbo de data/hora em que os recursos restauráveis existiam. |

## <a name="responses"></a>Respostas

| Nome | Tipo | Descrição |
| --- | --- | --- |
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| A operação foi concluída com sucesso. |
| Outros códigos de status | [DefaultErrorResponse](#defaulterrorresponse)| Resposta de erro que descreve por que a operação falhou. |

## <a name="examples"></a>Exemplos

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Exemplo de solicitação**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Resposta de exemplo**

Código de status: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definições

| Definição | Descrição | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | Bancos de dados específicos a serem restaurados. | | [DefaultErrorResponse](#defaulterrorresponse) | Uma resposta de erro do serviço. | | [ErrorResponse](#errorresponse) | Resposta de erro. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | A resposta de operação de lista, que contém os recursos SQL restauráveis. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

Bancos de dados específicos a serem restaurados.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| coleções de |string[]| Os nomes das coleções disponíveis para restauração. |
| databaseName |string| O nome do banco de dados disponível para restauração. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

A resposta de operação de lista, que contém os recursos SQL restauráveis.

| **Nome** | **Tipo** | **Descrição** |
| --- | --- | --- |
| valor |[DatabaseRestoreResource](#databaserestoreresource)[]| Lista de recursos SQL restauráveis, incluindo os nomes do banco de dados e da coleção. |

## <a name="next-steps"></a>Próximas etapas

* [Listar bancos de dados restauráveis](restorable-sql-databases-list.md) na API do SQL Cosmos do Azure usando a API REST.
* [Modelo de recurso](continuous-backup-restore-resource-model.md) do modo de backup contínuo.