---
title: Monitorar funções do Azure com logs de monitor do Azure
description: Aprenda a usar o Azure Monitor Logs com funções do Azure para monitorar execuções de funções.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cshoe
ms.openlocfilehash: 13c72a1cf8a0dd4a1124e51b9ceee04ae04bf261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649867"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Monitorar funções do Azure com logs de monitor do Azure

O Azure Functions oferece uma integração com [o Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md) para monitorar funções. Este artigo mostra como configurar funções do Azure para enviar logs gerados pelo sistema e gerados pelo usuário para logs do Monitor do Azure.

O Azure Monitor Logs oferece a capacidade de consolidar logs de diferentes recursos no mesmo espaço de trabalho, onde ele pode ser analisado com [consultas](../azure-monitor/log-query/log-query-overview.md) para recuperar, consolidar e analisar rapidamente os dados coletados.  É possível criar e testar consultas usando o [Log Analytics](../azure-monitor/log-query/portals.md) no portal do Azure e, em seguida, analisar diretamente os dados usando essas ferramentas ou salvar consultas para uso com [visualizações](../azure-monitor/visualizations.md) ou [regras de alerta](../azure-monitor/platform/alerts-overview.md).

O Azure Monitor usa uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) usada pelo Azure Data Explorer que é adequada para consultas de log simples, mas também inclui funcionalidades avançadas como agregações, junções e análises inteligentes. É possível aprender a linguagem de consulta rapidamente por meio de [várias lições](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> A integração com o Azure Monitor Logs está atualmente em visualização pública para aplicativos de função em execução em planos de hospedagem windows consumption, premium e dedicado.

## <a name="setting-up"></a>Configurando

Na seção **Monitoramento,** selecione **Configurações de diagnóstico** e clique em **Adicionar configuração de diagnóstico**.

![Adicionar uma configuração de diagnóstico](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

Na página **configurações diagnósticos,** escolha **Enviar para o Log Analytics**e, em seguida, selecione o espaço de trabalho do Log Analytics. Em **log** choose **FunctionAppLogs**, esta tabela contém os logs desejados.

![Adicionar uma configuração de diagnóstico](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Logs gerados pelo usuário

Para gerar logs personalizados, você pode usar a instrução de registro específica dependendo do seu idioma, aqui estão os trechos do código de exemplo:


# <a name="c"></a>[C #](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Consultando os logs

Para consultar os logs gerados, vá para o espaço de trabalho do Log Analytics que você configurou para enviar os logs de função e clique **em Logs**.

![Janela de consulta no espaço de trabalho de LA](media/functions-monitor-log-analytics/querying.png)

Funções do Azure grava todos os logs na tabela **FunctionAppLogs,** aqui estão algumas consultas de exemplo.

### <a name="all-logs"></a>Todos os registros

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>Um logde de função específico

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Exceções

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Próximas etapas

- Revise a [visão geral das funções do Azure](functions-overview.md)
- Saiba mais sobre [os registros do Monitor do Azure](../azure-monitor/platform/data-platform-logs.md)
- Saiba mais sobre o [idioma de consulta](../azure-monitor/log-query/get-started-queries.md).
