---
title: Configurar e ler logs com Azure Functions gatilho para Cosmos DB
description: Saiba como expor os logs ao pipeline de log do Azure Functions ao usar o gatilho do Azure Functions para o Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441839"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Como configurar e ler logs ao usar o gatilho do Azure Functions para Cosmos DB

Este artigo descreve como é possível configurar seu ambiente do Azure Functions para enviar os logs de gatilho do Azure Functions para Cosmos DB para a [solução de monitoramento](../azure-functions/functions-monitoring.md) configurada.

## <a name="included-logs"></a>Logs incluídos

O gatilho do Azure Functions para Cosmos DB usa a [Biblioteca do Processador do Feed de Alterações](./change-feed-processor.md) internamente e a biblioteca gera um conjunto de logs de integridade que podem ser usados para monitorar operações internas para [fins de solução de problemas](./troubleshoot-changefeed-functions.md).

Os logs de integridade descrevem como o gatilho do Azure Functions para Cosmos DB se comporta ao tentar executar operações durante cenários de balanceamento de carga ou de inicialização.

## <a name="enabling-logging"></a>Como habilitar o registro em log

Para habilitar o registro em log ao usar o gatilho do Azure Functions para Cosmos DB, localize o arquivo `host.json` no projeto do Azure Functions ou no aplicativo do Azure Functions e [configure o nível de registro em log necessário](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). É necessário habilitar os rastreamentos de `Host.Triggers.CosmosDB` conforme mostrado no exemplo a seguir:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Após a implantação da Função do Azure com a configuração atualizada, você verá os logs do gatilho do Azure Functions para Cosmos DB como parte dos seus rastreamentos. Você pode exibir os logs em seu provedor de log configurado na *categoria* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Consultar os logs

Execute a seguinte consulta para consultar os logs gerados pelo gatilho do Azure Functions para Cosmos DB na [Análise do Azure Application Insights](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Próximos passos

* [Habilitar o monitoramento](../azure-functions/functions-monitoring.md) em seus aplicativos do Azure Functions.
* Saiba como [Diagnosticar e solucionar problemas comuns](./troubleshoot-changefeed-functions.md) ao usar o gatilho do Azure Functions para Cosmos DB.