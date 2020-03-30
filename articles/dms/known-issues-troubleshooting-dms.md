---
title: Problemas comuns - Serviço de migração de banco de dados do Azure
description: Saiba como solucionar problemas/erros conhecidos comuns associados ao uso do Serviço de Migração de Banco de Dados do Azure.
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
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649100"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Solucionando problemas e erros comuns do Serviço de Migração do Banco de Dados do Azure

Este artigo descreve alguns problemas e erros comuns que os usuários do Azure Database Migration Service podem encontrar. O artigo também inclui informações sobre como resolver esses problemas e erros.

## <a name="migration-activity-in-queued-state"></a>Atividade de migração em estado enfileirado

Quando você cria novas atividades em um projeto do Serviço de Migração de Banco de Dados do Azure, as atividades permanecem em um estado enfileirado.

| Causa         | Resolução |
| ------------- | ------------- |
| Esse problema acontece quando a instância do Serviço de Migração de Banco de Dados do Azure atingiu a capacidade máxima para tarefas em andamento que são executadas simultaneamente. Qualquer nova atividade está na fila até que a capacidade esteja disponível. | Validar a instância do Serviço de Migração de Dados tem atividades em execução entre projetos. Você pode continuar a criar novas atividades que são adicionadas automaticamente à fila para execução. Assim que qualquer uma das atividades em execução existentes é concluída, a próxima atividade enfileirada começa a ser concluída e o status muda automaticamente para o estado em execução. Você não precisa tomar nenhuma ação adicional para iniciar a migração da atividade enfileirada.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Número máximo de bancos de dados selecionados para migração

O erro a seguir ocorre ao criar uma atividade para um projeto de migração de banco de dados para mover-se para o Banco de Dados SQL do Azure ou para uma instância gerenciada do Azure SQL Database:

* **Erro**: Erro de validação das configurações de migração", "erroDetalhe":"Mais do que o número máximo '4' objetos de 'Bancos de Dados' foram selecionados para migração."

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro é exibido quando você selecionou mais de quatro bancos de dados para uma única atividade de migração. Atualmente, cada atividade migratória está limitada a quatro bancos de dados. | Selecione quatro ou menos bancos de dados por atividade de migração. Se você precisar migrar mais de quatro bancos de dados em paralelo, provisão outra instância do Azure Database Migration Service. Atualmente, cada assinatura suporta até duas instâncias do Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Erros para migração do MySQL para o Azure MySQL com falhas de recuperação

Quando você migra do MySQL para o Azure Database para MySQL usando o Azure Database Migration Service, a atividade de migração falha com o seguinte erro:

* **Erro**: Erro de migração do banco de dados - A tarefa 'TaskID' foi suspensa devido a [n] sucessivas falhas de recuperação.

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro pode ocorrer quando o usuário que faz a migração estiver perdendo a função ReplicationAdmin e/ou privilégios do REPLICATION CLIENT, REPLICATION REPLICA e SUPER (versões anteriores ao MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Certifique-se de que os [privilégios pré-requisitos](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) para a conta de usuário estão configurados com precisão na instância Do Banco de Dados Azure para MySQL. Por exemplo, as seguintes etapas podem ser seguidas para criar um usuário chamado 'migrateuser' com privilégios necessários:<br>1. CRIAR MIGRATEUSER@'% DO USUÁRIO IDENTIFICADO POR 'segredo'; <br>2. Conceder todos os privilégios em db_name.* para 'migrar'@'%' identificados por 'segredo'; repetir esta etapa para conceder acesso em mais bancos de dados <br>3. Conceder escravo de replicação em *.* para 'migrar'@'%' identificado por 'segredo';<br>4. Conceder cliente de replicação em *.* para 'migrar'@'%' identificado por 'segredo';<br>5. Privilégios de descarga; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Erro ao tentar parar o Serviço de Migração de Banco de Dados do Azure

Você recebe o seguinte erro ao interromper a instância do Serviço de Migração do Banco de Dados do Azure:

* **Erro**: O serviço falhou em parar. Erro: {'error':{'code':'InvalidRequest','message':'One or more activities are currently running. Para interromper o serviço, aguarde até que as atividades tenham sido concluídas ou interrompa essas atividades manualmente e tente novamente.'}}

| Causa         | Resolução |
| ------------- | ------------- |
| Esse erro é exibido quando a instância de serviço que você está tentando parar inclui atividades que ainda estão em execução ou estão presentes em projetos de migração. <br><br><br><br><br><br> | Certifique-se de que não há atividades em execução no exemplo do Serviço de Migração de Banco de Dados Do Azure que você está tentando parar. Você também pode excluir as atividades ou projetos antes de tentar interromper o serviço. As etapas a seguir ilustram como remover projetos para limpar a instância do serviço de migração, excluindo todas as tarefas em execução:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription -SubscriptionName "\<subName>" <br> 4. Remove-AzureRmDataMigrationProjeto \<-Nome do projetoNome \<> -ResourceGroupName> -ServiceName do ServiceName \<> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Erro ao tentar iniciar o Serviço de Migração de Banco de Dados do Azure

Você recebe o seguinte erro ao iniciar a instância do Serviço de Migração do Banco de Dados do Azure:

* **Erro**: O serviço não é inicial. Erro: {'errorDetail':'O serviço falhou em iniciar, entre em contato com o suporte da Microsoft'}

| Causa         | Resolução |
| ------------- | ------------- |
| Este erro é exibido quando a instância anterior falhou internamente. Esse erro ocorre raramente, e a equipe de engenharia está ciente disso. <br> | Exclua a instância do serviço que você não pode iniciar e, em seguida, provisionar um novo para substituí-lo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Erro ao restaurar banco de dados ao migrar SQL para a instância gerenciada do Azure SQL DB

Quando você executa uma migração on-line do SQL Server para uma instância gerenciada do Azure SQL Database, a recorte falha com o seguinte erro:

* **Erro**: Falha na operação Falha na operação Id 'operationId'. Código 'AutorizaçãoFalha', Mensagem 'O cliente 'clientId' com id de objeto 'objectId' não tem autorização para executar a ação 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' sobre escopo '/assinaturas/subscriptionId'.'.

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro indica que o principal do aplicativo que está sendo usado para migração on-line do SQL Server para uma instância gerenciada do Azure SQL Database não tem permissão de contribuição na assinatura. Certas chamadas de API com Instância Gerenciada no momento exigem essa permissão na assinatura para a operação de restauração. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Use `Get-AzureADServicePrincipal` o cmdlet `-ObjectId` PowerShell com disponível na mensagem de erro para listar o nome de exibição do ID do aplicativo que está sendo usado.<br><br> Valide as permissões para este aplicativo e certifique-se de que ele tenha a [função de contribuinte](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) no nível de assinatura. <br><br> A equipe de engenharia do serviço de migração de banco de dados do Azure está trabalhando para restringir o acesso necessário da função de contribuição atual na assinatura. Se você tiver um requisito de negócios que não permita o uso da função de contribuição, entre em contato com o suporte do Azure para obter ajuda adicional. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Erro ao excluir NIC associado ao Serviço de Migração de Banco de Dados do Azure

Quando você tenta excluir um cartão de interface de rede associado ao Serviço de Migração de Banco de Dados do Azure, a tentativa de exclusão falha com esse erro:

* **Erro**: Não é possível excluir a NIC associada ao Serviço de Migração de Banco de Dados do Azure devido ao serviço DMS que utiliza a NIC

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema acontece quando a instância do Serviço de Migração de Banco de Dados do Azure ainda pode estar presente e consumindo a NIC. <br><br><br><br><br><br><br><br> | Para excluir essa NIC, exclua a instância de serviço DMS que exclui automaticamente a NIC usada pelo serviço.<br><br> **Importante**: Certifique-se de que a instância do Serviço de Migração do Banco de Dados do Azure que está sendo excluída não tenha atividades em execução.<br><br> Depois que todos os projetos e atividades associados à instância do Serviço de Migração de Banco de Dados do Azure forem excluídos, você pode excluir a instância do serviço. A NIC usada pela instância de serviço é automaticamente limpa como parte da exclusão do serviço. |

## <a name="connection-error-when-using-expressroute"></a>Erro de Conexão ao usar o ExpressRoute

Quando você tenta se conectar à fonte no assistente de projeto de serviço de Migração de Banco de dados do Azure, a conexão falha após o tempo limite prolongado se a fonte estiver usando o ExpressRoute para conectividade.

| Causa         | Resolução    |
| ------------- | ------------- |
| Ao usar [o ExpressRoute](https://azure.microsoft.com/services/expressroute/), o Azure Database Migration Service [requer](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) o provisionamento de três pontos finais de serviço na sub-rede virtual associada ao serviço:<br> -- Ponto final do Ônibus de Serviço<br> -- Ponto final de armazenamento<br> -- Ponto final do banco de dados de destino (por exemplo, ponto final sql, ponto final do Cosmos DB)<br><br><br><br><br> | [Habilite](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) os pontos finais de serviço necessários para a conectividade ExpressRoute entre a origem e o Serviço de Migração de Banco de Dados Do Azure. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Erro de tempo de espera de bloqueio ao migrar um banco de dados MySQL para OZure DB para MySQL

Quando você migra um banco de dados MySQL para uma ocorrência do Azure Database for MySQL via Azure Database Migration Service, a migração falha com o seguinte erro de tempo de espera de bloqueio:

* **Erro**: Erro de migração do banco de dados - Falha ao carregar arquivo - Falha ao iniciar processo de carga para arquivo 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Mensagem: [MySQL][Driver ODBC][mysqld] Tempo de espera de bloqueio excedido; tente reiniciar a transação

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse erro ocorre quando a migração falha devido ao tempo de espera de bloqueio durante a migração. | Considere aumentar o valor do parâmetro de servidor **'innodb_lock_wait_timeout'**. O maior valor permitido é 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Erro de conexão ao SQL Server de origem ao usar a porta dinâmica ou a instância nomeada

Quando você tenta conectar o Serviço de Migração do Banco de Dados do Azure à fonte do SQL Server que é executada em instância nomeada ou em uma porta dinâmica, a conexão falha com esse erro:

* **Erro**: -1 - Falha na conexão SQL. Ocorreu um erro relacionado à rede ou específico da instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome de instância está correto e se o SQL Server está configurado para permitir conexões remotas. (provedor: Adaptadores de Rede do SQL, erro: 26 – Erro ao Localizar Servidor/Instância Especificada)

| Causa         | Resolução    |
| ------------- | ------------- |
| Esse problema acontece quando a instância de origem do SQL Server que o Azure Database Migration Service tenta conectar tem uma porta dinâmica ou está usando uma instância nomeada. O serviço SQL Server Browser ouve a porta UDP 1434 para conexões recebidas em uma instância nomeada ou ao usar uma porta dinâmica. A porta dinâmica pode ser mudada cada vez que o serviço sql server for reiniciado. Você pode verificar a porta dinâmica atribuída a uma instância através da configuração de rede no SQL Server Configuration Manager.<br><br><br> |Verifique se o Serviço de Migração de Banco de Dados do Azure pode se conectar ao serviço de navegador sql server de origem na porta UDP 1434 e à instância do SQL Server através da porta TCP atribuída dinamicamente conforme aplicável. |

## <a name="additional-known-issues"></a>Outros problemas conhecidos

* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados Do Azure para MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Problemas/limitações de migração conhecidas com migrações on-line para o Banco de Dados Do Azure para PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Próximas etapas

* Exibir o artigo [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Veja o artigo Como configurar parâmetros de [servidor no Banco de Dados Azure para MySQL usando o portal Azure](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Exibir a visão geral do artigo [dos pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs).
* Consulte o [FAQ sobre o uso do Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/faq).
