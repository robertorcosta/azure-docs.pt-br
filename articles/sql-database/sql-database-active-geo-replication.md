---
title: Replicação geográfica ativa
description: Use a replicação geográfica ativa para criar bancos de dados secundários legíveis com base em bancos de dados individuais no mesmo data center (região) ou em data centers diferentes.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/06/2020
ms.openlocfilehash: 1f339d987d67047f5857679b440e93e6c3730059
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810459"
---
# <a name="creating-and-using-active-geo-replication"></a>Criação e uso de georeplicação ativa

A replicação geográfica ativa é um recurso de banco de dados SQL do Azure que permite criar bancos de dados secundários legíveis de bancos de dados individuais em um servidor de banco de dados SQL no mesmo ou diferente data center (região).

> [!NOTE]
> A replicação geográfica ativa não é compatível com a instância gerenciada. Para fazer failover geográfico de instâncias gerenciadas, use [grupos de failover automático](sql-database-auto-failover-group.md).

A replicação geográfica ativa foi projetada como uma solução de continuidade de negócios que permite que o aplicativo execute a recuperação de desastres rápida de bancos de dados individuais no caso de um desastre regional ou de uma interrupção em grande escala. Se a replicação geográfica estiver habilitada, o aplicativo poderá iniciar o failover para um banco de dados secundário em uma região do Azure diferente. Há suporte para até quatro secundários na mesma região ou em regiões diferentes, e os secundários também podem ser usados para consultas de acesso somente leitura. O failover deve ser iniciado manualmente pelo aplicativo ou pelo usuário. Após o failover, o novo banco de dados primário terá um ponto de extremidade de conexão diferente. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando a replicação geográfica ativa.

![replicação geográfica ativa](./media/sql-database-active-geo-replication/geo-replication.png )

> [!IMPORTANT]
> O Banco de Dados SQL do Azure também é compatível com grupos de failover automático. Para obter mais informações, confira [Usando grupos de failover automático](sql-database-auto-failover-group.md). Além disso, a replicação geográfica ativa não é compatível com bancos de dados criados dentro de uma Instância Gerenciada. Considere a possibilidade de usar [grupos de failover](sql-database-auto-failover-group.md) com Instâncias Gerenciadas.

Se, por qualquer motivo, o seu banco de dados primário falhar ou simplesmente precisar ser colocado offline, você poderá iniciar o failover para qualquer um dos seus bancos de dados secundários. Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são vinculados automaticamente ao novo primário.

Você pode gerenciar a replicação e o failover de um banco de dados individual ou de um conjunto de bancos de dados em um servidor ou em um pool elástico usando a replicação geográfica ativa. É possível fazer isso usando:

- O [portal Azure](sql-database-geo-replication-portal.md)
- [PowerShell: banco de dados único](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
- [PowerShell: pool elástico](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- [Transact-SQL: banco de dados único ou pool elástico](/sql/t-sql/statements/alter-database-azure-sql-database)
- [API REST: banco de dados único](https://docs.microsoft.com/rest/api/sql/replicationlinks)


A replicação geográfica ativa aproveita a tecnologia [Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) do SQL Server para replicar de maneira assíncrona transações confirmadas no banco de dados primário para um banco de dados secundário usando isolamento de instantâneo. Os grupos de failover automático fornecem a semântica de grupo além da replicação geográfica ativa, mas o mesmo mecanismo de replicação assíncrona é usado. Embora, a qualquer momento, o banco de dados secundário possa estar um pouco atrás do banco de dados primário, os dados secundários têm a garantia de nunca terem transações parciais. A redundância entre regiões permite que os aplicativos se recuperem rapidamente de uma perda permanente de um datacenter inteiro ou de partes de um datacenter, causada por desastres naturais, falhas humanas catastróficas ou crimes. Os dados específicos de RPO podem ser encontrados em [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

> [!NOTE]
> Se houver uma falha de rede entre as duas regiões, haverá uma repetição a cada 10 segundos para restabelecer as conexões.
> [!IMPORTANT]
> Para garantir que uma alteração crítica no banco de dados primário seja replicada para um secundário antes do failover, você pode forçar a sincronização para garantir a replicação das alterações críticas (por exemplo, atualizações de senha). A sincronização forçada afeta o desempenho, já que bloqueia o thread de chamada até que todas as transações confirmadas sejam replicadas. Para obter detalhes, veja [sp_wait_for_database_copy_sync](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Para monitorar o atraso de replicação entre o banco de dados primário e o geo-secundário, confira [sys.dm_geo_replication_link_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

A figura a seguir mostra que um exemplo de replicação geográfica ativa configurada com um banco de dados primário na região Centro-Norte dos EUA e um secundário na região Centro-Sul dos EUA.

![relacionamento de replicação geográfica](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Como os bancos de dados secundários são legíveis, eles podem ser usados para descarregar cargas de trabalho somente leitura, como trabalhos de relatórios. Se você estiver usando replicação geográfica ativa, é possível criar o banco de dados secundário na mesma região que o primário, mas isso não aumenta a resiliência a falhas catastróficas do aplicativo. Se estiver usando grupos de failover automático, o banco de dados secundário sempre será criado em uma região diferente.

Além da recuperação de desastres, a replicação geográfica ativa pode ser usada nos seguintes cenários:

- **Migração de banco de dados**: você pode usar a replicação geográfica ativa para migrar um banco de dados de um servidor para outro online com tempo de inatividade mínimo.
- **Atualizações de aplicativos**: você pode criar um secundário extra como uma cópia de failback durante as atualizações de aplicativos.

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como projetar soluções para recuperação de desastres, consulte [Projetando soluções em nuvem para recuperação de desastres usando a geo-replicação ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Funcionalidades e terminologia de replicação geográfica ativa

- **Replicação assíncrona automática**

  Você só pode criar um banco de dados secundário adicionando a um banco de dados existente. O banco de dados secundário só pode ser criado em qualquer servidor do Banco de Dados SQL do Azure. Depois de criado, o banco de dados secundário é preenchido com os dados copiados do banco de dados primário. Este processo é conhecido como propagação. Depois que o banco de dados secundário for criado e propagado, as atualizações para o banco de dados primário serão replicadas de forma assíncrona para o banco de dados secundário automaticamente. A replicação assíncrona significa que as transações são confirmadas no banco de dados primário antes de serem replicadas para o banco de dados secundário.

- **Bancos de dados secundários legíveis**

  Um aplicativo pode acessar um banco de dados secundário para operações somente leitura usando as mesmas ou diferentes entidades de segurança usadas para acessar o banco de dados primário. Os bancos de dados secundários operam no modo de isolamento de instantâneo para garantir que a replicação das atualizações do primário (repetição de log) não seja atrasada por consultas executadas no secundário.

> [!NOTE]
> A repetição do log será atrasada no banco de dados secundário se houver atualizações de esquema no primário. Este último requer um bloqueio de esquema no banco de dados secundário.
> [!IMPORTANT]
> Você pode usar a replicação geográfica para criar um banco de dados secundário na mesma região que o principal. Você pode usar este secundário para equilibrar a carga de cargas de trabalho somente leitura na mesma região. No entanto, um banco de dados secundário na mesma região não fornece resiliência adicional de falhas e, portanto, não é um alvo de failover adequado para a recuperação de desastres. Também não garantirá o isolamento da zona de disponibilidade. Use o nível de serviço Business critical ou Premium com [configuração redundante de zona](sql-database-high-availability.md#zone-redundant-configuration) para alcançar o isolamento da zona de disponibilidade.   
>

- **Failover planejado**

  O failover planejado alterna as funções dos bancos de dados primários e secundários após a sincronização completa ser concluída. É uma operação online que não resulta em perda de dados. O tempo da operação depende do tamanho do registro da transação no principal que precisa ser sincronizado. O failover planejado é projetado para os seguintes cenários: (a) para realizar brocas DR na produção quando a perda de dados não é aceitável; b Realocar o banco de dados para outra região; e (c) devolver o banco de dados à região primária após a interrupção ter sido atenuada (failback).

- **Failover não planejado**

  Um failover forçado ou não planejado mudará imediatamente o secundário para a função primária, sem nenhuma sincronização com o primário. Quaisquer transações comprometidas com o principal, mas não replicadas para o secundário, serão perdidas. Esta operação é projetada como um método de recuperação durante paralisações quando a primária não está acessível, mas a disponibilidade do banco de dados deve ser rapidamente restaurada. Quando a primária original estiver novamente on-line, ela se reconectará automaticamente e se tornará uma nova secundária. Todas as transações não sincronizadas antes do failover serão preservadas no arquivo de backup, mas não serão sincronizadas com as novas primárias para evitar conflitos. Essas transações terão que ser mescladas manualmente com a versão mais recente do banco de dados principal.
 
- **Vários secundários legíveis**

  Até quatro bancos de dados secundários podem ser criado para cada primário. Se houver apenas um banco de dados secundário e ele falhar, o aplicativo será exposto a um risco maior até que um novo banco de dados secundário seja criado. Se existirem vários bancos de dados secundários, o aplicativo permanecerá protegido mesmo se houver uma falha em um dos bancos de dados secundários. Os secundários adicionais também podem ser usados para expandir as cargas de trabalho somente leitura

  > [!NOTE]
  > Se você estiver usando replicação geográfica ativa para compilar um aplicativo distribuído globalmente e precisa fornecer acesso somente leitura aos dados em mais de quatro regiões, poderá criar um banco de dados secundário de outro banco de dados secundário (um processo conhecido como encadeamento). Dessa forma, que você pode obter uma escala praticamente ilimitada de replicação de banco de dados. Além disso, o encadeamento reduz a sobrecarga de replicação do banco de dados primário. A desvantagem é uma maior latência de replicação nos bancos de dados secundários filhos.

- **Replicação geográfica de bancos de dados em um pool elástico**

  Cada banco de dados secundário pode participar separadamente de um pool elástico ou não estar em nenhum pool elástico. A escolha de pool para cada banco de dados secundário é separada e não depende da configuração de nenhum outro banco de dados secundário (seja ele primário ou secundário). Cada pool elástico está dentro de uma única região, portanto, vários bancos de dados secundários na mesma topologia nunca podem compartilhar um pool elástico.


- **Failover e failback controlados pelo usuário**

  Um banco de dados secundário pode ser explicitamente alternado para a função primária a qualquer momento pelo aplicativo ou pelo usuário. Durante uma paralisação real, deve ser usada a opção "não planejada", que imediatamente promove um secundário para ser o principal. Quando o primário com falha se recuperar e estiver disponível novamente, o sistema o marcará automaticamente como um secundário e o atualizará de acordo com o novo primário. Devido à natureza assíncrona da replicação, uma pequena quantidade de dados poderá ser perdida durante failovers não planejados se o primário falhar antes de replicar as alterações mais recentes para o secundário. Quando um primário com vários secundários passar por failover, o sistema automaticamente reconfigurará as relações de replicação e vinculará os secundários restantes para o primário recém-promovido, sem a necessidade de intervenção do usuário. Depois que a interrupção que causou o failover for reduzida, poderá ser desejável retornar o aplicativo para a região primária. Para isso, o comando failover deve ser invocado com a opção "planejada".

## <a name="preparing-secondary-database-for-failover"></a>Preparando banco de dados secundário para failover

Para garantir que seu aplicativo possa acessar imediatamente o novo principal após o failover, certifique-se de que os requisitos de autenticação para seu servidor secundário e banco de dados estejam configurados corretamente. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md). Para garantir a conformidade após o failover, certifique-se de que a política de retenção de backup no banco de dados secundário corresponda à do principal. Essas configurações não fazem parte do banco de dados e não são replicadas. Por padrão, o secundário será configurado com um período de retenção PITR padrão de sete dias. Para obter detalhes, consulte [Backups automáticos do Banco de Dados SQL](sql-database-automated-backups.md).

> [!IMPORTANT]
> Se o seu banco de dados for membro de um grupo de failover, você não poderá iniciar seu failover usando o comando faiover de replicação geográfica. Considere usar o comando failover para o grupo. Se você precisar reprovar um banco de dados individual, você deve removê-lo do grupo failover primeiro. Consulte [grupos failover](sql-database-auto-failover-group.md) para obter detalhes. 


## <a name="configuring-secondary-database"></a>Configuração do banco de dados secundário

Os bancos de dados primário e secundário devem ter a mesma camada de serviço. Também é altamente recomendável que o banco de dados secundário seja criado com o mesmo tamanho da computação (DTUs ou vCores) que o primário. Se o banco de dados principal estiver experimentando uma carga de trabalho de gravação pesada, um secundário com menor tamanho de computação pode não ser capaz de acompanhá-lo. Isso causará defasagem no secundário e potencial indisponibilidade do secundário. Um banco de dados secundário que está atrasado em relação ao primário também corre o risco de uma grande perda de dados, caso seja necessário um failover forçado. Para mitigar esses riscos, a georeplicação ativa reduzirá a taxa de registro do primário, se necessário, para permitir que seus secundários se atualizem. 

A outra consequência de uma configuração secundária desequilibrada é que, após o failover, o desempenho do aplicativo pode sofrer devido à capacidade computacional insuficiente do novo primário. Nesse caso, será necessário ampliar o objetivo de serviço de banco de dados para o nível necessário, o que pode levar tempo significativo e computar recursos, e exigirá um [failover](sql-database-high-availability.md) de alta disponibilidade no final do processo de scale-up.

> [!IMPORTANT]
> O RPO SLA publicado de 5 segundos não pode ser garantido a menos que o banco de dados secundário esteja configurado com o mesmo ou maior tamanho de computação que o principal. 

Se você decidir criar o secundário com menor tamanho de computação, o gráfico percentual de IO de log no portal Azure fornece uma boa maneira de estimar o tamanho mínimo do cálculo do secundário necessário para sustentar a carga de replicação. Por exemplo, se o seu banco de dados principal é P6 (1000 DTU) e sua porcentagem de gravação de log é de 50%, o secundário precisa ser pelo menos P4 (500 DTU). Para recuperar dados históricos de IO de registro, use a exibição [sys.resource_stats.](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) Para recuperar dados recentes de gravação de log com maior granularidade que melhor reflete picos de curto prazo na taxa de log, use [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) view. 

O estrangulamento da taxa de registro de transações no principal devido ao menor tamanho de computação em um secundário é relatado usando o tipo de espera HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO, visível nas visualizações do banco de dados [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_os_wait_stats.](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) 

> [!NOTE]
> A taxa de registro de transações na principal pode ser estrangulada por razões não relacionadas ao menor tamanho de computação em um secundário. Esse tipo de estrangulamento pode ocorrer mesmo que o secundário tenha o mesmo tamanho de computação ou maior que o principal. Para obter detalhes, incluindo tipos de espera para diferentes tipos de estrangulamento da taxa de log, consulte [O controle da taxa de log de transações](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).

Para obter mais informações sobre os tamanhos da computação do Banco de Dados SQL, confira [Quais são as Camadas de Serviço do Banco de Dados SQL](sql-database-purchase-models.md).

## <a name="cross-subscription-geo-replication"></a>Geo-replicação por assinatura cruzada

Para configurar a georeplicação ativa entre dois bancos de dados pertencentes a assinaturas diferentes (sob o mesmo inquilino ou não), você deve seguir o procedimento especial descrito nesta seção.  O procedimento é baseado em comandos SQL e requer: 

- Criando um login privilegiado em ambos os servidores
- Adicionando o endereço IP à lista de permitir que o cliente execute a alteração em ambos os servidores (como o endereço IP do host executando o SQL Server Management Studio). 

O cliente que realiza as alterações precisa de acesso de rede ao servidor principal. Embora o mesmo endereço IP do cliente deva ser adicionado à lista de permissão no servidor secundário, a conectividade de rede com o servidor secundário não é estritamente necessária. 

### <a name="on-the-master-of-the-primary-server"></a>Sobre o mestre do servidor principal

1. Adicione o endereço IP à lista de permitir que o cliente realize as alterações (para ver mais informações, [Configure firewall](sql-database-firewall-configure.md)). 
1. Crie um login dedicado à configuração de geo-replicação ativa (e ajuste as credenciais conforme necessário):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Criar um usuário correspondente e atribuí-lo à função dbmanager: 

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Tome nota do SID do novo login usando esta consulta: 

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>No banco de dados de origem do servidor principal

1. Criar um usuário para o mesmo login:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Adicione o usuário à função db_owner:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>No mestre do servidor secundário 

1. Adicione o endereço IP à lista de permitir que o cliente realize as alterações. Ele deve ter o mesmo endereço IP exato do servidor principal. 
1. Crie o mesmo login do servidor principal, usando a mesma senha de nome de usuário e SID: 

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Criar um usuário correspondente e atribuí-lo à função dbmanager:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Sobre o mestre do servidor principal

1. Faça login no mestre do servidor principal usando o novo login. 
1. Criar uma réplica secundária do banco de dados de origem no servidor secundário (ajuste o nome do banco de dados e o nome do servidor conforme necessário):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Após a configuração inicial, as regras de usuários, logins e firewall criadas podem ser removidas. 


## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Manter credenciais e regras de firewall em sincronização

Recomendamos o uso [de regras de firewall IP em nível de banco de dados](sql-database-firewall-configure.md) para bancos de dados geo-replicados para que essas regras possam ser replicadas com o banco de dados para garantir que todos os bancos de dados secundários tenham as mesmas regras de firewall IP que as primárias. Essa abordagem elimina a necessidade de os clientes configurarem manualmente e manterem as regras de firewall nos servidores que hospedam os bancos de dados primários e secundários. Da mesma forma, usar [usuários de banco de dados independente](sql-database-manage-logins.md) para o acesso a dados garante que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário para que, durante failovers, não haja interrupções devido à incompatibilidade nos logons e senhas. Com a adição de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), os clientes podem gerenciar o acesso do usuário aos bancos de dados primários e secundários, eliminando a necessidade de gerenciamento de credenciais em todos os bancos de dados juntos.

## <a name="upgrading-or-downgrading-primary-database"></a>Atualizando ou rebaixando o banco de dados principal

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho da computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize primeiro o banco de dados secundário e, depois, atualize o primário. Ao fazer downgrade, inverta a ordem: faça primeiro o downgrade do banco de dados primário e, depois, faça do secundário. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta.

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado.

> [!IMPORTANT]
> O banco de dados principal em um grupo de failover não pode ser dimensionado para um nível mais alto, a menos que o banco de dados secundário seja dimensionado primeiro para o nível mais alto. Se você tentar dimensionar o banco de dados principal antes que o banco de dados secundário seja dimensionado, você pode receber o seguinte erro:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. Chamar **sp_wait_for_database_copy_sync** bloqueia o thread de chamada até que a última transação confirmada seja transmitida para o banco de dados secundário. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. **sp_wait_for_database_copy_sync** é atribuído a um vínculo de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> **sp_wait_for_database_copy_sync** impede a perda de dados depois de um failover, mas não garante a sincronização completa para acesso de leitura. A demora causada por uma chamada de procedimento **sp_wait_for_database_copy_sync** pode ser significativa e depende do tamanho do log de transações no momento da chamada.

## <a name="monitoring-geo-replication-lag"></a>Monitoramento de defasagem de georeplicação

Para monitorar o lag em relação ao RPO, use *replication_lag_sec* coluna [de sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) no banco de dados principal. Mostra defasagem em segundos entre as transações cometidas no primário e persistiu no secundário. Por ex.: se o valor do lag for de 1 segundo, significa que se o primário for impactado por uma paralisação neste momento e o failover for iniciado, 1 segundo das transições mais recentes não será salvo. 

Para medir o atraso em relação às alterações na base de dados primária que foram aplicadas no secundário, ou seja, disponíveis para leitura a partir do secundário, compare *last_commit* tempo no banco de dados secundário com o mesmo valor no banco de dados primário.

> [!NOTE]
> Às *vezes, replication_lag_sec* no banco de dados primário tem um valor NULL, o que significa que o principal não sabe atualmente quão longe o secundário está.   Isso normalmente acontece após a reinicialização do processo e deve ser uma condição transitória. Considere alertar o aplicativo se o *replication_lag_sec* retornar NULL por um longo período de tempo. Isso indicaria que o banco de dados secundário não pode se comunicar com o primário devido a uma falha de conectividade permanente. Há também condições que podem fazer com que a diferença entre *last_commit* tempo no secundário e no banco de dados primário se torne grande. Por ex.: se um compromisso for feito no primário após um longo período sem alterações, a diferença saltará para um grande valor antes de retornar rapidamente para 0. Considere-o uma condição de erro quando a diferença entre esses dois valores permanece grande por um longo tempo.


## <a name="programmatically-managing-active-geo-replication"></a>Gerenciando a replicação geográfica ativa programaticamente

Conforme discutido anteriormente, a replicação geográfica ativa pode ser gerenciada programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, consulte [O Controle de Acesso Baseado em Função do Azure](../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: Gerenciar failover de bancos de dados únicos e agrupados

> [!IMPORTANT]
> Esses comandos Transact-SQL só se aplicam à replicação geográfica ativa e não se aplicam a grupos de failover. Como tal, eles também não se aplicam para instâncias gerenciadas, pois eles são compatíveis somente com grupos de failover.

| Comando | Descrição |
| --- | --- |
| [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Use o argumento ADD SECONDARY ON SERVER para criar um banco de dados secundário para um banco de dados existente e inicie a replicação de dados |
| [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Usar o FAILOVER ou FORCE_FAILOVER_ALLOW_DATA_LOSS para alternar um banco de dados secundário para primário a fim de iniciar o failover |
| [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Use REMOVE SECONDARY ON SERVER para encerrar uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retorna informações sobre todos os links de replicação existentes para cada banco de dados no servidor do Banco de Dados SQL do Azure. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Obtém a hora da última replicação, latência da última replicação e outras informações sobre o link de replicação para um determinado Banco de Dados SQL. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Mostra o status de todas as operações de banco de dados, incluindo o status dos links de replicação. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |faz com que o aplicativo espere até que todas as transações confirmadas sejam replicadas e reconhecidas pelo banco de dados secundário ativo. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: Gerenciar failover de bancos de dados únicos e agrupados

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

| Cmdlet | Descrição |
| --- | --- |
| [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase) |Obtém um ou mais bancos de dados. |
| [New-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabasesecondary) |Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados. |
| [Set-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesecondary) |Alterna um banco de dados secundário para primário a fim de iniciar o failover. |
| [Remove-AzSqlDatabaseSecondary](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesecondary) |Encerra uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado. |
| [Get-AzSqlDatabaseReplicationLink](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Obtém os links de replicação geográfica entre um Banco de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
|  | |

> [!IMPORTANT]
> Para scripts de exemplo, confira [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) e [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>API REST: Gerencie failover de bancos de dados únicos e agrupados

| API | Descrição |
| --- | --- |
| [Criar ou atualizar banco de dados (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Cria, atualiza ou restaura um banco de dados primário ou secundário. |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) |Retorna o status durante uma operação de criação. |
| [Definir o banco de dados secundário como primário (Failover planejado)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failover) |Define qual banco de dados secundário é primário ao realizar failover do banco de dados primário atual. **Esta opção não é compatível com a Instância Gerenciada.**|
| [Definir o banco de dados secundário como primário r (Failover não planejado)](https://docs.microsoft.com/rest/api/sql/replicationlinks/failoverallowdataloss) |Define qual banco de dados secundário é primário ao realizar failover do banco de dados primário atual. Esta operação pode resultar em perda de dados. **Esta opção não é compatível com a Instância Gerenciada.**|
| [Obter link de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/get) |Obtém um link de replicação específico para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links. **Esta opção não é compatível com a Instância Gerenciada.**|
| [Links de Replicação - Listar pelo Banco de Dados](https://docs.microsoft.com/rest/api/sql/replicationlinks/listbydatabase) | Obtém todos os links de replicação para um determinado Banco de Dados SQL em uma parceria de replicação geográfica. Recupera as informações visíveis no modo de exibição de catálogo sys.geo_replication_links. |
| [Excluir links de replicação](https://docs.microsoft.com/rest/api/sql/replicationlinks/delete) | Exclui um link de replicação do banco de dados. Não pode ser feito durante o failover. |
|  | |

## <a name="next-steps"></a>Próximas etapas

- Para exemplos de scripts, consulte:
  - [Configurar e falhar em um único banco de dados usando geo-replicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Configurar e falhar em um banco de dados agrupado usando geo-replicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
- O Banco de Dados SQL do Azure também é compatível com grupos de failover automático. Para obter mais informações, confira [Usando grupos de failover automático](sql-database-auto-failover-group.md).
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre o uso de backups automatizados para recuperação, consulte [Restaurar um banco de dados a partir dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).
