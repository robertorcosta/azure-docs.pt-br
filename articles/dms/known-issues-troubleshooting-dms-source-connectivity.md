---
title: Problemas de conexão de bancos de dados de origem
titleSuffix: Azure Database Migration Service
description: Saiba como solucionar problemas/erros conhecidos associados à conexão do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de origem.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297093"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Solucionar problemas de erros de DMS ao se conectar a bancos de dados de origem

O artigo a seguir fornece detalhes sobre como abordar possíveis problemas que você pode encontrar ao conectar o DMS (Azure Database Migration Service, serviço de migração de banco de dados do Azure) ao seu banco de dados de origem. Cada seção abaixo diz respeito a um tipo específico de banco de dados de origem, listando o erro que você pode encontrar juntamente com detalhes e links para informações sobre como solucionar problemas da conectividade.

## <a name="sql-server"></a>SQL Server

Os problemas potenciais associados à conexão a um banco de dados SQL Server de origem e como abordá-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| Falha na conexão SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da ocorrência está correto e se o SQL Server está configurado para permitir conexões remotas.<br> | Esse erro ocorre se o serviço não conseguir localizar o servidor de origem. Para resolver o problema, consulte o [artigo Erro de conexão ao SQL Server de origem ao usar a porta dinâmica ou a instância nomeada](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Erro 53** - Falha na conexão SQL. (Também, para os códigos de erro 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Esse erro ocorre se o serviço não puder se conectar ao servidor de origem. Para resolver o problema, consulte os seguintes recursos e tente novamente. <br><br>  [Guia interativo do usuário para solucionar problemas de conectividade](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Pré-requisitos para migração do SQL Server para o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Pré-requisitos para migrar o SQL Server para uma instância gerenciada do Azure SQL Database](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **Erro 18456** - Falha de login.<br> | Esse erro ocorre se o serviço não puder se conectar ao banco de dados de origem usando as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Você também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou aos documentos de solução de problemas listados na nota abaixo desta tabela e, em seguida, tentar novamente. |
| Valor de conta{0}malformado ' fornecido. O formato esperado para AccountName é DomainName\UserName<br> | Esse erro ocorre se o usuário selecionar a autenticação do Windows, mas fornecer o nome de usuário em um formato inválido. Para resolver o problema, forneça o nome de usuário no formato correto para autenticação do Windows ou selecione **autenticação SQL**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Os problemas potenciais associados à conexão a um banco de dados AWS RDS MySQL de origem e como abordá-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro [2003]**[HY000] - falha na conexão. ERRO [HY000] [MySQL][Driver ODBC x.x(w) Não pode se conectar ao servidor MySQL em '{server}' (10060) | Esse erro ocorre se o driver MySQL ODBC não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente.<br> |
| **Erro [2005]**[HY000] - falha na conexão. ERRO [HY000] [MySQL][Driver ODBC x.x(w) Host de servidor MySQL desconhecido '{servidor}' | Esse erro ocorre se o serviço não conseguir encontrar o host de origem no RDS. O problema pode ser porque a fonte listada não existe ou há um problema com a infra-estrutura RDS. Para resolver o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente.<br> |
| **Erro [1045]**[HY000] - falha na conexão. ERRO [HY000] [MySQL][Driver ODBC x.x(w) negado para usuário '{usuário}'@'{server}' (usando senha: SIM) | Esse erro ocorre se o driver MySQL ODBC não puder se conectar ao servidor de origem devido a credenciais inválidas. Verifique as credenciais inseridas. Se o problema continuar, verifique se o computador de origem tem as credenciais corretas. Talvez seja necessário redefinir a senha no console. Se você ainda encontrar o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente.<br> |
| **Erro [9002]**[HY000] - falha na conexão. ERRO [HY000] [MySQL][Driver ODBC x.x(w) A seqüência de conexão pode não estar certa. Visite o portal para referências.| Esse erro ocorre se a conexão estiver falhando devido a um problema com a seqüência de conexões. Verifique se a seqüência de conexões fornecida é válida. Para resolver o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente.<br> |
| **Erro no registro binário. Variável binlog_format tem valor '{valor}'. Por favor, mude para 'linha'.** | Esse erro ocorre se houver um erro no registro binário; a variável binlog_format tem o valor errado. Para resolver o problema, altere o binlog_format no grupo de parâmetros para 'ROW' e, em seguida, reinicie a instância. Para obter mais informações, consulte [opções e variáveis de registro binário ou](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) [documentação de arquivos de registro de banco de dados Do AWS RDS MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Para obter mais informações sobre problemas de solução de problemas relacionados à conexão a um banco de dados AWS RDS MySQL de origem, consulte os seguintes recursos:
> * [Solução de problemas para problemas de conectividade do Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolver problemas de conexão à minha instância de banco de dados RDS do Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

Os problemas potenciais associados à conexão a um banco de dados AWS RDS PostgreSQL de origem e como abordá-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro [101]**[08001] - falha na conexão. Erro [08001] tempo expirou. | Esse erro ocorre se o driver Postgres não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente. |
| **Erro: Parâmetro wal_level tem valor '{valor}'. Por favor, altere-o para 'lógico' para permitir a replicação.** | Este erro ocorre se o parâmetro wal_level tiver o valor errado. Para resolver o problema, altere o rds.logical_replication no grupo de parâmetros para 1 e, em seguida, reinicie a instância. Para obter mais informações, consulte [os pré-requisitos para migrar para o Azure PostgreSQL usando DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) ou [PostgreSQL no Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Para obter mais informações sobre problemas de solução de problemas relacionados à conexão a um banco de dados AWS RDS PostgreSQL de origem, consulte os seguintes recursos:
> * [Solução de problemas para problemas de conectividade do Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Como resolver problemas de conexão à minha instância de banco de dados RDS do Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>Servidor AWS RDS SQL

Os problemas potenciais associados à conexão a um banco de dados AWS RDS SQL Server de origem e como abordá-los são fornecidos na tabela a seguir.

| Erro         | Detalhes de causa e solução de problemas |
| ------------- | ------------- |
| **Erro 53** - Falha na conexão SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da ocorrência está correto e se o SQL Server está configurado para permitir conexões remotas. (provedor: chamado Provedor de tubos, erro: 40 - Não foi possível abrir uma conexão com o SQL Server | Esse erro ocorre se o serviço não puder se conectar ao servidor de origem. Para resolver o problema, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente. |
| **Erro 18456** - Falha de login. Falha de login para usuário '{usuário}' | Esse erro ocorre se o serviço não puder se conectar ao banco de dados de origem com as credenciais T-SQL fornecidas. Para resolver o problema, verifique as credenciais inseridas. Você também pode consultar [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) ou aos documentos de solução de problemas listados na nota abaixo desta tabela e tentar novamente. |
| **Erro 87** - A seqüência de conexão não é válida. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da ocorrência está correto e se o SQL Server está configurado para permitir conexões remotas. (provedor: SQL Network Interfaces, erro: 25 - String connection não é válido) | Esse erro ocorre se o serviço não puder se conectar ao servidor de origem por causa de uma seqüência de conexão inválida. Para resolver o problema, verifique a seqüência de conexões fornecida. Se o problema persistir, consulte os documentos de solução de problemas listados na nota abaixo desta tabela e tente novamente. |
| **Erro - Certificado do servidor não é confiável.** Uma conexão com o servidor foi estabelecida com êxito, mas ocorreu um erro durante o processo de logon. (provedor: Provedor SSL, erro: 0 - A cadeia de certificados foi emitida por uma autoridade que não é confiável.) | Esse erro ocorre se o certificado usado não for confiável. Para resolver o problema, você precisa encontrar um certificado confiável e, em seguida, atiça-lo no servidor. Alternativamente, você pode selecionar a opção Certificado de Confiança durante a conexão. Tome esta ação somente se você estiver familiarizado com o certificado usado e confiar nele. <br> As conexões TLS que são criptografadas usando um certificado auto-assinado não fornecem segurança forte - eles são suscetíveis a ataques man-in-the-middle. Não confie em TLS usando certificados auto-assinados em um ambiente de produção ou em servidores conectados à internet. <br> Para obter mais informações, consulte [O Uso do SSL com uma instância](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) ou tutorial do Microsoft SQL Server DB ou [tutorial: Migre o RDS SQL Server para o Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites). |
| **Erro 300** - O usuário não tem permissões necessárias. A permissão DO ESTADO DO SERVIDOR FOI negada no objeto '{servidor}', banco de dados '{banco de dados}' | Esse erro ocorre se o usuário não tiver permissão para realizar a migração. Para resolver o problema, consulte [grant server permissions - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) ou [Tutorial: Migrate RDS SQL Server to Azure usando DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) para obter mais detalhes. |

> [!NOTE]
> Para obter mais informações sobre problemas de solução de problemas relacionados à conexão a um servidor SQL RDS RDS de origem, consulte os seguintes recursos:
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) (Resolvendo erros de conectividade com o SQL Server)
> * [Como resolver problemas de conexão à minha instância de banco de dados RDS do Amazon?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Problemas conhecidos

* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados Do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados Do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Próximas etapas

* Exibir o artigo [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Veja o artigo Como configurar parâmetros de [servidor no Banco de Dados Azure para MySQL usando o portal Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Exibir a visão geral do artigo [dos pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte o [FAQ sobre o uso do Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/faq).
