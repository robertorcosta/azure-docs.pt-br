---
title: Como configurar o banco de dados SQL do Azure
description: Saiba como configurar e gerenciar o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ccdeb883dc9cf2cba499e45e25ff4706bb120463
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689863"
---
# <a name="how-to-use-azure-sql-database"></a>Como usar o Banco de Dados SQL do Azure

Nesta seção, é possível encontrar vários guias, scripts e explicações que podem ajudar você a gerenciar e configurar seu Banco de Dados SQL do Azure. Você também pode encontrar guias de instruções específicos para [banco de dados individual](sql-database-howto-single-database.md) e [Instância Gerenciada](sql-database-howto-managed-instance.md).

## <a name="load-data"></a>Carregar dados

- [Copiar um banco de dados individual ou em pool dentro do Azure](sql-database-copy.md)
- [Importar um BD de um BACPAC](sql-database-import.md)
- [Exportar um BD para um BACPAC](sql-database-export.md)
- [Carregar dados com o BCP](sql-database-load-from-csv-with-bcp.md)
- [Carregar dados com o ADF](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>Sincronização de dados

- [Sincronização de Dados SQL](sql-database-sync-data.md)
- [Agente de Sincronização de Dados](sql-database-data-sync-agent.md)
- [Replicar alterações de esquema](sql-database-update-sync-schema.md)
- [Monitorar com o OMS](sql-database-sync-monitor-oms.md)
- [Melhores práticas para a sincronização de dados](sql-database-best-practices-data-sync.md)
- [Solucionar problemas da sincronização de dados](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>Monitoramento e ajuste

- [Ajuste manual](sql-database-performance-guidance.md)
- [Utilizar DMVs para monitorar o desempenho](sql-database-monitoring-with-dmvs.md)
- [Usar o Repositório de Consultas para monitorar o desempenho](sql-database-operate-query-store.md)
- [Solucionar problemas de desempenho com o Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md)
- [Usar o log de diagnóstico do Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md)
- [Monitorar o espaço OLTP in-memory](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>Eventos estendidos

- [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md)
- [Armazenar eventos estendidos no arquivo de evento](sql-database-xevent-code-event-file.md)
- [Armazenar eventos estendidos no buffer de anel](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>Configurar recursos

- [Configurar a autenticação do Azure AD](sql-database-aad-authentication-configure.md)
- [Configurar o acesso condicional](sql-database-conditional-access.md)
- [Autenticação multifator do AAD](sql-database-ssms-mfa-authentication.md)
- [Configurar autenticação multifator](sql-database-ssms-mfa-authentication-configure.md)
- [Configurar a política de retenção temporal](sql-database-temporal-tables-retention-policy.md)
- [Configurar TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [Girar chaves BYOK da TDE](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [Remover protetor da TDE](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [Configurar OLTP na memória](sql-database-in-memory-oltp-migration.md)
- [Configurar Automação do Azure](sql-database-manage-automation.md)

## <a name="develop-applications"></a>Desenvolver aplicativos

- [Conectividade](sql-database-libraries.md)
- [Usar o Conector do Spark](sql-database-spark-connector.md)
- [Autenticar aplicativo](sql-database-client-id-keys.md)
- [Mensagens de erro](sql-database-develop-error-messages.md)
- [Usar o envio em lote para obter melhor desempenho](sql-database-use-batching-to-improve-performance.md)
- [Diretrizes de conectividade](sql-database-connectivity-issues.md)
- [Aliases de DNS](dns-alias-overview.md)
- [Configurar PowerShell de alias do DNS](dns-alias-powershell.md)
- [Portas - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C e C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>Design de aplicativos

- [Design para a recuperação de desastres](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Design para pools elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Design para atualizações do aplicativo](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Design de aplicativos SaaS multilocatários

- [Padrões de design do SaaS](saas-tenancy-app-design-patterns.md)
- [Video Indexer de SaaS](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Segurança de aplicativo SaaS](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Guias de instruções para Instâncias Gerenciadas](sql-database-howto-managed-instance.md).
- Saiba mais sobre [Guias de instruções para bancos de dados individuais](sql-database-howto-single-database.md).
