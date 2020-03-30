---
title: Logs - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Descreve configuração de registro, armazenamento e análise no Banco de Dados Azure para PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 2636e9a225002148e4cd79bb2176e0883aed623a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280489"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Logs no Banco de Dados Azure para PostgreSQL - Servidor Único
O Banco de Dados Do Azure para PostgreSQL permite configurar e acessar os registros padrão do Postgres. Os logs podem ser usados para identificar, solucionar problemas e reparar erros de configuração e desempenho abaixo do ideal. O registro de informações que você pode configurar e acessar inclui erros, informações de consulta, registros de vácuo automático, conexões e pontos de verificação. (O acesso aos logs de transação não está disponível).

O registro de auditoria é disponibilizado através de uma extensão postgres, pgaudit. Para saber mais, visite o artigo [de conceitos de auditoria.](concepts-audit.md)


## <a name="configure-logging"></a>Configurar o registro em log 
Você pode configurar o registro padrão postgres em seu servidor usando os parâmetros do servidor de registro. Em cada banco de dados do Azure para servidor PostgreSQL, `log_checkpoints` e `log_connections` estão ligados por padrão. Há parâmetros adicionais que você pode ajustar para atender às suas necessidades de registro em log: 

![Banco de Dados do Azure para PostgreSQL – parâmetros de registro em log](./media/concepts-server-logs/log-parameters.png)

Para saber mais sobre os parâmetros de log do Postgres, visite as seções [Quando Registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) e O [Que Registrar](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) da documentação do Postgres. A maioria, mas não todos, os parâmetros de registro do Postgres estão disponíveis para configurar no Banco de Dados Azure para PostgreSQL.

Para saber como configurar parâmetros no Banco de Dados Azure para PostgreSQL, consulte a [documentação](howto-configure-server-parameters-using-portal.md) do portal ou a [documentação da CLI](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Configurar um alto volume de logs, por exemplo, o registro de declarações, pode adicionar sobrecarga de desempenho significativa. 

## <a name="access-log-files"></a>Acesse arquivos .log
O formato de log padrão no Banco de Dados Azure para PostgreSQL é .log. Uma linha de amostra deste registro parece:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

O Banco de Dados Azure para PostgreSQL fornece um local de armazenamento de curto prazo para os arquivos .log. Um novo arquivo começa a cada 1 hora ou 100 MB, o que vier primeiro. Os logs são anexados ao arquivo atual à medida que são emitidos do Postgres.  

Você pode definir o período de retenção `log_retention_period` para este armazenamento de log de curto prazo usando o parâmetro. O valor padrão é de 3 dias; o valor máximo é de 7 dias. O local de armazenamento de curto prazo pode conter até 1 GB de arquivos de log. Após 1 GB, os arquivos mais antigos, independentemente do período de retenção, serão excluídos para abrir espaço para novos logs. 

Para retenção de logs e análise de log suscetia a longo prazo, você pode baixar os arquivos .log e movê-los para um serviço de terceiros. Você pode baixar os arquivos usando o [portal Azure](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Alternativamente, você pode configurar as configurações de diagnóstico do Azure Monitor que emitem automaticamente seus logs (em formato JSON) para locais de longo prazo. Saiba mais sobre essa opção na seção abaixo. 

Você pode parar de gerar arquivos .log definindo o parâmetro `logging_collector` como OFF. A desatação da geração de arquivos .log é recomendada se você estiver usando as configurações de diagnóstico do Azure Monitor. Essa configuração reduzirá o impacto de desempenho de registro adicional.

## <a name="diagnostic-logs"></a>Logs de diagnóstico
O Banco de Dados Azure para PostgreSQL é integrado às configurações de diagnóstico do Azure Monitor. As configurações de diagnóstico permitem que você envie seus registros do Postgres no formato JSON para o Azure Monitor Logs para análise e alerta, Hubs de eventos para streaming e Armazenamento Azure para arquivamento. 

> [!IMPORTANT]
> Este recurso de diagnóstico para logs de servidor só está disponível nos níveis de preços otimizados para propósito [geral](concepts-pricing-tiers.md)e memória otimizada .


### <a name="configure-diagnostic-settings"></a>Definir as configurações de diagnóstico
Você pode habilitar as configurações de diagnóstico para o servidor Postgres usando o portal Azure, CLI, Rest API e Powershell. A categoria de log a ser selecionada é **PostgreSQLLogs**. (Existem outros registros que você pode configurar se estiver usando [o Query Store](concepts-query-store.md).)

Para habilitar registros de diagnóstico usando o portal Azure:

   1. No portal, vá para *Configurações de Diagnóstico* no menu de navegação do servidor Postgres.
   2. Selecione *Adicionar configuração de diagnóstico*.
   3. Nomeie esta configuração. 
   4. Selecione seu ponto final preferido (conta de armazenamento, hub de eventos, análise de log). 
   5. Selecione o tipo de log **PostgreSQLLogs**.
   7. Salve sua configuração.

Para habilitar registros de diagnóstico usando Powershell, CLI ou API REST, visite o artigo [de configurações de diagnóstico.](../azure-monitor/platform/diagnostic-settings.md)

### <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

A maneira como você acessa os registros depende de qual ponto final você escolher. Para O Armazenamento Azure, consulte o artigo da [conta de armazenamento de logs.](../azure-monitor/platform/resource-logs-collect-storage.md) Para Centros de Eventos, consulte o [artigo de logs do Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Para registros do Monitor do Azure, os logs são enviados para o espaço de trabalho selecionado. Os registros do Postgres usam o modo de coleta **Do AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela estão descritos abaixo. Saiba mais sobre consulta e alerta na visão geral da [consulta Do Monitor do Azure.](../azure-monitor/log-query/log-query-overview.md)

A seguir, consultas que você pode tentar começar. Você pode configurar alertas com base em consultas.

Procure por todos os registros do Postgres para um servidor específico no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
```

Procure todas as tentativas de conexão não-localhost
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
A consulta acima mostrará resultados nas últimas 6 horas para qualquer login de servidor Postgres neste espaço de trabalho.

### <a name="log-format"></a>Formato de log

A tabela a seguir descreve os campos para o tipo **PostgreSQLLogs.** Dependendo do ponto de extremidade de saída escolhido, os campos incluídos e a ordem em que aparecem podem variar. 

|**Campo** | **Descrição** |
|---|---|
| TenantId | Sua ID de locatário |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Carimbo de data/hora quando o log foi gravado, em UTC |
| Type | Tipo do log. Sempre `AzureDiagnostics` |
| SubscriptionId | GUID para a assinatura a que o servidor pertence |
| ResourceGroup | Nome do grupo de recursos ao qual o servidor pertence |
| ResourceProvider | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | URI de recurso |
| Recurso | Nome do servidor |
| Categoria | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | Nível de log, exemplo: LOG, ERROR, NOTICE |
| Mensagem | Mensagem de log primária | 
| Domínio | Versão do servidor, o exemplo: postgres-10 |
| Detalhes | Mensagem de log secundária (se aplicável) |
| ColumnName | Nome da coluna (se aplicável) |
| SchemaName | Nome do esquema (se aplicável) |
| DatatypeName | Nome do tipo de dados (se aplicável) |
| LogicalServerName | Nome do servidor | 
| _ResourceId | URI de recurso |
| Prefixo | Prefixo da linha de log |


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como acessar logs no [portal do Azure](howto-configure-server-logs-in-portal.md) ou na [CLI do Azure](howto-configure-server-logs-using-cli.md).
- Saiba mais sobre o [preço do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
- Saiba mais sobre [registros de auditoria](concepts-audit.md)
