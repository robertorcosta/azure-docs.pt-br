---
title: Registro de auditoria - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Conceitos para logon de auditoria pgAudit no Banco de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 45490e398abd8b5bd3c10adb95b56e1019d2bb94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842462"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Registro de auditoria no Banco de Dados Azure para PostgreSQL - Servidor Único

O registro de auditoria das atividades do banco de dados no Banco de Dados Do Azure para PostgreSQL - Single Server está disponível através da Extensão de Auditoria PostgreSQL: [pgAudit](https://www.pgaudit.org/). pgAudit fornece registro detalhado de auditoria de sessão e/ou objeto.

> [!NOTE]
> pgAudit está em pré-visualização no Banco de Dados Azure para PostgreSQL.
> A extensão pode ser habilitada apenas em servidores de propósito geral e otimizado de memória.

Se você quiser registros de nível de recurso do Azure para operações como dimensionamento de computação e armazenamento, consulte o Registro de Atividades do [Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="usage-considerations"></a>Considerações sobre o uso
Por padrão, as instruções de log pgAudit são emitidas junto com suas instruções de log regulares usando o recurso de log padrão do Postgres. No banco de dados do Azure para PostgreSQL, esses arquivos. log podem ser baixados por meio do portal do Azure ou da CLI. O armazenamento máximo para a coleta de arquivos é de 1 GB, e cada arquivo está disponível por um máximo de sete dias (o padrão é de três dias). Este serviço é uma opção de armazenamento de curto prazo.

Alternativamente, você pode configurar todos os logs a serem emitidos para o serviço de registro de diagnóstico do Azure Monitor. Se você habilitar o registro de diagnóstico do Azure Monitor, seus registros serão enviados automaticamente (em formato JSON) para registros do Azure Storage, Event Hubs e/ou Azure Monitor, dependendo da sua escolha.

A habilitação de pgAudit gera um grande volume de log em um servidor, que tem um impacto no desempenho e no armazenamento de log. Recomendamos que você use o serviço log de diagnóstico do Azure, que oferece opções de armazenamento de longo prazo, bem como recursos de análise e alerta. Recomendamos que você desative o registro em log padrão para reduzir o impacto no desempenho de log adicional:

   1. Defina o `logging_collector` parâmetro para OFF. 
   2. Reinicie o servidor para aplicar essa alteração.

Para saber como configurar o registro nos registros do Azure Storage, Event Hubs ou Azure Monitor, visite a seção de registros de diagnóstico do [artigo logs](concepts-server-logs.md)do servidor .

## <a name="installing-pgaudit"></a>Instalação de pgAudit

Para instalar o pgAudit, você precisa incluí-lo nas bibliotecas de pré-carga compartilhadas do servidor. Uma alteração no parâmetro `shared_preload_libraries` do Postgres requer uma reinicialização do servidor para fazer efeito. Você pode alterar parâmetros usando o [portal Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [Rest API](/rest/api/postgresql/configurations/createorupdate).

Usando o [portal Azure:](https://portal.azure.com)

   1. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.
   2. Na barra lateral, selecione **Parâmetros do servidor**.
   3. Pesquise o parâmetro `shared_preload_libraries`.
   4. Selecione **pgaudit**.
   5. Reinicie o servidor para aplicar a alteração.

   6. Conecte-se ao seu servidor usando um cliente (como psql) e habilite a extensão pgAudit
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Se você vir um erro, confirme se `shared_preload_libraries`reiniciou seu servidor após a salvação .

## <a name="pgaudit-settings"></a>configurações de pgAudit

pgAudit permite configurar o registro de sessão ou auditoria de objetos. [O registro de auditoria de sessão](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) emite registros detalhados de declarações executadas. [O registro de auditoria de objetos](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) é uma auditoria escopo para relações específicas. Você pode optar por configurar um ou ambos os tipos de registro. 

> [!NOTE]
> as configurações pgAudit são especificadas gloabally e não podem ser especificadas em um banco de dados ou nível de função.

Depois de [instalar o pgAudit,](#installing-pgaudit)você pode configurar seus parâmetros para iniciar o registro. A [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) fornece a definição de cada parâmetro. Teste os parâmetros primeiro e confirme se você está recebendo o comportamento esperado.

> [!NOTE]
> A `pgaudit.log_client` configuração para ON redirecionará os logs para um processo cliente (como psql) em vez de ser gravado para arquivo. Essa configuração deve ser deixada desabilitada. <br> <br>
> `pgaudit.log_level`só está ativado `pgaudit.log_client` quando está ligado.

> [!NOTE]
> No Banco de Dados Azure `pgaudit.log` para PostgreSQL, não pode ser definido usando um `-` atalho de sinal (menos) conforme descrito na documentação pgAudit. Todas as classes de instrução necessárias (LEITURA, GRAVAÇÃO etc.) devem ser especificadas individualmente.

### <a name="audit-log-format"></a>Formato de log de auditoria
Cada entrada de auditoria `AUDIT:` é indicada perto do início da linha de log. O formato do resto da entrada está detalhado na [documentação pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Se você precisar de outros campos para satisfazer seus requisitos de auditoria, use o parâmetro `log_line_prefix`Postgres . `log_line_prefix`é uma string que é saída no início de cada linha de log do Postgres. Por exemplo, `log_line_prefix` a configuração a seguir fornece carimbo de tempo, nome de usuário, nome do banco de dados e ID do processo:

```
t=%m u=%u db=%d pid=[%p]:
```

Para saber `log_line_prefix`mais sobre , visite a [documentação PostgreSQL](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Introdução
Para começar rapidamente, `pgaudit.log` `WRITE`defina e abra seus registros para revisar a saída. 

## <a name="viewing-audit-logs"></a>Visualização de registros de auditoria
Se você estiver usando arquivos .log, seus logs de auditoria serão incluídos no mesmo arquivo que seus registros de erro PostgreSQL. Você pode baixar arquivos de log do [portal](howto-configure-server-logs-in-portal.md) Azure ou [CLI](howto-configure-server-logs-using-cli.md). 

Se você estiver usando o registro de diagnóstico do Azure, a maneira como você acessa os registros depende de qual ponto final você escolher. Para O Armazenamento Azure, consulte o artigo da [conta de armazenamento de logs.](../azure-monitor/platform/resource-logs-collect-storage.md) Para Centros de Eventos, consulte o [artigo de logs do Azure.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

Para registros do Monitor do Azure, os logs são enviados para o espaço de trabalho selecionado. Os registros do Postgres usam o modo de coleta **Do AzureDiagnostics,** para que possam ser consultados a partir da tabela AzureDiagnostics. Os campos na tabela estão descritos abaixo. Saiba mais sobre consulta e alerta na visão geral da [consulta Do Monitor do Azure.](../azure-monitor/log-query/log-query-overview.md)

Você pode usar esta consulta para começar. Você pode configurar alertas com base em consultas.

Procure por todos os registros do Postgres para um servidor específico no último dia
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre o login no Banco de Dados Do Azure para PostgreSQL](concepts-server-logs.md)
- Saiba como definir parâmetros usando o [portal Azure,](howto-configure-server-parameters-using-portal.md) [Azure CLI](howto-configure-server-parameters-using-cli.md)ou [Rest API](/rest/api/postgresql/configurations/createorupdate).
