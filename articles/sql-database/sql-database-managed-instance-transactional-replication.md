---
title: Replicação transacional
description: Saiba mais sobre o uso da replicação transacional do SQL Server com bancos de dados individuais, em pool e de instâncias no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: 2a048ddefbcd76193436da13cd3ba68b8b6ffb0a
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607588"
---
# <a name="transactional-replication-with-single-pooled-and-instance-databases-in-azure-sql-database"></a>Replicação transacional do SQL Server com bancos de dados individuais, em pool e de instâncias no Banco de Dados SQL do Azure

A replicação transacional é um recurso do Banco de Dados SQL do Azure e SQL Server que permite que você replique dados de uma tabela no Banco de Dados SQL do Azure ou un SQL Server para as tabelas localizadas em bancos de dados remotos. Esse recurso permite que você sincronize várias tabelas em bancos de dados diferentes.

## <a name="when-to-use-transactional-replication"></a>Quando usar replicação transacional

A replicação transacional é útil nos seguintes cenários:
- Publique as alterações feitas em uma ou mais tabelas em um banco de dados e distribua-as para um ou vários Bancos de Dados SQL do Azure ou do SQL Server que se inscreveram para as alterações.
- Mantenha vários bancos de dados distribuídos em estado sincronizado.
- Migre os bancos de dados de um Servidor SQL ou instância gerenciada para outro banco de dados, publicando continuamente as alterações.

## <a name="overview"></a>Visão geral

Os principais componentes na replicação transacional são mostrados na figura a seguir:  

![replicação com um Banco de Dados SQL](media/replication-to-sql-database/replication-to-sql-database.png)

O **publicador** é uma instância ou um servidor que publica as alterações feitas em algumas tabelas (artigos), enviando as atualizações para o distribuidor. A publicação de um banco de dados SQL do Azure de um SQL Server local é uma ação compatível com as seguintes versões do SQL Server:

- SQL Server 2019 (versão prévia)
- SQL Server 2016 para SQL 2017
- SQL Server 2014 SP1 CU3 ou posterior (12.00.4427)
- SQL Server 2014 RTM CU10 (12.00.2556)
- SQL Server 2012 SP3 ou posterior (11.0.6020)
- SQL Server 2012 SP2 CU8 (11.0.5634.0)
- Para outras versões do SQL Server que não dão suporte à publicação para objetos no Azure, é possível utilizar o método de [republicação de dados](https://docs.microsoft.com/sql/relational-databases/replication/republish-data) para mover dados para versões mais recentes do SQL Server. 

O **distribuidor** é uma instância ou um servidor que coleta as alterações nos artigos de um publicador e as distribui aos assinantes. O Distribuidor pode ser a instância gerenciada do Azure SQL Database ou o SQL Server (qualquer versão, desde que seja igual ou superior à versão do Publisher). 

O **assinante** é uma instância ou um servidor que está recebendo as alterações feitas no publicador. Os assinantes podem ser bancos de dados individuais, em pool e de instâncias do Banco de Dados SQL do Azure ou de bancos de dados do SQL Server. Um assinante em um banco de dados individuais ou em pool precisa ser configurado como um assinante push. 

| Função | Bancos de dados individuais e em pool | Bancos de dados de instâncias |
| :----| :------------- | :--------------- |
| **Publicador** | Não | Sim | 
| **Distribuidor** | Não | Sim|
| **Assinante de pull** | Não | Sim|
| **Assinante push**| Sim | Sim|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > Uma assinatura pull não é suportada quando o distribuidor é um banco de dados Instance e o assinante não. 

Existem diferentes [tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication):


| Replicação | Bancos de dados individuais e em pool | Bancos de dados em instâncias|
| :----| :------------- | :--------------- |
| [**Transacional Padrão**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) | Sim (somente como assinante) | Sim | 
| [**Instantâneo**](https://docs.microsoft.com/sql/relational-databases/replication/snapshot-replication) | Sim (somente como assinante) | Sim|
| [**Replicação de mesclagem**](https://docs.microsoft.com/sql/relational-databases/replication/merge/merge-replication) | Não | Não|
| [**Ponto a ponto**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Não | Não|
| [**Bidirecional**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Não | Sim|
| [**Assinaturas updatable**](https://docs.microsoft.com/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Não | Não|
| &nbsp; | &nbsp; | &nbsp; |

  >[!NOTE]
  > - Tentativa de configurar a replicação usando uma versão mais antiga pode resultar em erro número MSSQL_REPL20084 (o processo não pôde se conectar ao Assinante.) e MSSQL_REPL40532 (não é possível abrir o servidor \<nome> solicitado pelo logon. Houve falha no logon).
  > - Para usar todos os recursos do Banco de Dados SQL do Azure, você deve estar usando as versões mais recentes do [SSMS (SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e do [SSDT (SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).
  
  ### <a name="supportability-matrix-for-instance-databases-and-on-premises-systems"></a>Matriz de suporte para bancos de dados de instâncias e sistemas on-premises
  A matriz de suporte à replicação, por exemplo, é a mesma do SQL Server no local. 
  
| **Publicador**   | **Distribuidor** | **Subscriber** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| Microsoft SQL Server 2017 | SQL Server 2019 <br/>Microsoft SQL Server 2017 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>Microsoft SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 | 
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> Microsoft SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="requirements"></a>Requisitos

- A conectividade usa Autenticação SQL entre os participantes da replicação. 
- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho usado pela replicação. 
- A porta 445 (saída tcp) precisa estar aberta nas regras de segurança da sub-rede de instância gerenciada para acessar o compartilhamento de arquivos do Azure. 
- A porta 1433 (saída tcp) precisa ser aberta se o Editor/Distribuidor estiver em uma instância gerenciada e o assinante não estiver. Você também pode precisar alterar a regra de segurança `allow_linkedserver_outbound` de saída do NSG de `internet`instância gerenciada para a tag serviço de **destino** da porta 1433 de `virtualnetwork` para . 
- Todos os tipos de participantes de replicação (Publisher, Distributor, Pull Subscriber e Push Subscriber) podem ser colocados em instâncias gerenciadas, mas o editor e o distribuidor devem estar tanto na nuvem quanto no local.
- Se o editor, o distribuidor e/ou o assinante existirem em diferentes redes virtuais, então o peering vpn deve ser estabelecido entre cada entidade, de tal forma que haja peering vpn entre o editor e o distribuidor, e/ou haja VPN olhando entre o distribuidor e o assinante. 


>[!NOTE]
> - Você pode encontrar o erro 53 ao se conectar a um Arquivo de Armazenamento Azure se a porta 445 do grupo de segurança de rede de saída (NSG) estiver bloqueada quando o distribuidor for um banco de dados de instâncias e o assinante estiver no local. [Atualize o vNet NSG](/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) para resolver esse problema. 


### <a name="compare-data-sync-with-transactional-replication"></a>Comparar a sincronização de dados com a replicação transacional

| | Sincronização de Dados | Replicação transacional |
|---|---|---|
| Vantagens | – Suporte ativo-ativo<br/>– Bidirecional entre o Banco de Dados SQL do Azure e o local | – Menor latência<br/>– Consistência transacional<br/>– Reutilização da topologia existente após a migração |
| Desvantagens | – Latência de 5 minutos ou mais<br/>– Não há consistência transacional<br/>– Maior impacto do desempenho | – Não pode publicar do banco de dados individual ou em pool do Banco de Dados SQL do Azure<br/>– Alto custo de manutenção |
| | | |

## <a name="common-configurations"></a>Configurações comuns

De modo geral, o publicador e o distribuidor devem ambos estar na nuvem ou ser locais. Há suporte para as seguintes configurações: 

### <a name="publisher-with-local-distributor-on-a-managed-instance"></a>Editor com distribuidor local em uma instância gerenciada

![Instância única como publicador e distribuidor](media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

O publisher e o distribuidor são configurados dentro de uma única instância gerenciada e distribuem alterações para outra instância gerenciada, banco de dados único, banco de dados pooled ou SQL Server no local. 

### <a name="publisher-with-remote-distributor-on-a-managed-instance"></a>Editor com distribuidor remoto em uma instância gerenciada

Nesta configuração, uma instância gerenciada publica alterações no distribuidor colocadas em outra instância gerenciada que podem atender a muitas instâncias gerenciadas por fonte e distribuir alterações em um ou muitos alvos em instância gerenciada, banco de dados único, banco de dados pooled ou SQL Server.

![Instâncias separadas para publicador e distribuidor](media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

O publicador e o distribuidor são configurados em duas instâncias gerenciadas. Existem algumas restrições com esta configuração: 

- Ambas as instâncias gerenciadas estão no mesmo vNet.
- As duas instâncias gerenciadas estão no mesmo local.


### <a name="publisher-and-distributor-on-premises-with-a-subscriber-on-a-single-pooled-and-instance-database"></a>Publicador e distribuidor locais com um assinante em um banco de dados individual, em pool e de instâncias 

![BD SQL do Azure como assinante](media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)
 
Nessa configuração, um Banco de Dados SQL do Azure (banco de dados individual, em pool e de instâncias) é um assinante. Essa configuração dá suporte à migração do local para o Azure. Se for um assinante está em um banco de dados individual ou um em pool, ele precisa estar no modo de envio por push.  

## <a name="with-failover-groups"></a>Com grupos failover

Se a replicação geográfica estiver habilitada em uma instância **de editor** ou **distribuidor** em um grupo [de failover,](sql-database-auto-failover-group.md)o administrador de instância gerenciada deve limpar todas as publicações na antiga primária e reconfigurá-las na nova primária após o failover ocorrer. Neste cenário são necessárias as seguintes atividades:

1. Pare todos os trabalhos de replicação em execução no banco de dados, se houver algum.
2. Solte os metadados de assinatura do editor executando o seguinte script no banco de dados do publisher:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```             
 
1. Solte metadados de assinatura do assinante. Execute o seguinte script no banco de dados de assinatura na instância do assinante:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>', 
      @publisher_db = N'<publisher database>', 
      @publication = N'<name of publication>'; 
   ```                

1. Retire à força todos os objetos de replicação do editor executando o seguinte script no banco de dados publicado:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Derrube com força o antigo distribuidor da instância primária original (se falhar de volta a uma antiga primária que costumava ter um distribuidor). Execute o seguinte script no banco de dados mestre na instância gerenciada do distribuidor antigo:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Se a replicação geográfica estiver ativada em uma instância **de assinante** em um grupo de failover, a publicação deve ser configurada para se conectar ao ponto final do grupo failover para a instância gerenciada pelo assinante. No caso de um failover, a ação subsequente do administrador de instância gerenciada depende do tipo de failover que ocorreu: 

- Para um failover sem perda de dados, a replicação continuará funcionando após o failover. 
- Para um failover com perda de dados, a replicação também funcionará. Ele vai replicar as mudanças perdidas novamente. 
- Para um failover com perda de dados, mas a perda de dados está fora do período de retenção do banco de dados de distribuição, o administrador de instância gerenciada precisará reinicializar o banco de dados de assinatura. 

## <a name="next-steps"></a>Próximas etapas

- [Configure a replicação entre um editor de MI e um assinante](replication-with-sql-database-managed-instance.md)
- [Configure a replicação entre um editor de MI, um distribuidor de MI e um assinante do SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [Crie uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication).
- [Crie uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription) usando o nome do servidor do Banco de Dados SQL do Azure como o assinante (por exemplo, `N'azuresqldbdns.database.windows.net` e o nome do Banco de Dados SQL do Azure como o banco de dados de destino (por exemplo, **AdventureWorks**. )


Para obter mais informações sobre como configurar a replicação transacional, consulte os seguintes tutoriais:



## <a name="see-also"></a>Consulte Também  

- [Replicação com um MI e um grupo de failover](sql-database-managed-instance-transact-sql-information.md#replication)
- [Replicação para um Banco de Dados SQL](replication-to-sql-database.md)
- [Replicação para instância gerenciada](replication-with-sql-database-managed-instance.md)
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitorando (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma assinatura](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
