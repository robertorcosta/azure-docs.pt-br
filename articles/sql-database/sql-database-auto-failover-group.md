---
title: Grupos de failover
description: Os grupos de failover automático é um recurso do Banco de Dados SQL que permite que você gerencie a replicação e failover automático/coordenado de um grupo de bancos de dados em um servidor do Banco de Dados SQL ou todos os bancos de dados na instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 11/07/2019
ms.openlocfilehash: 470e9a9c36b6b4ec2e40db5dfc47ae03fb6b5aa8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421377"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Use grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados

Auto-failover groups is a SQL Database feature that allows you to manage replication and failover of a group of databases on a SQL Database server or all databases in a managed instance to another region. It is a declarative abstraction on top of the existing [active geo-replication](sql-database-active-geo-replication.md) feature, designed to simplify deployment and management of geo-replicated databases at scale. Você pode iniciar o failover manualmente ou pode delegá-lo para o serviço de Banco de Dados SQL com base em uma política definida pelo usuário. A última opção permite que você recupere automaticamente vários bancos de dados relacionados em uma região secundária após uma falha catastrófica ou outro evento não planejado que resulte em perda total ou parcial de disponibilidade do serviço de Banco de Dados SQL na região primária. A failover group can include one or multiple databases, typically used by the same application. Além disso, eles podem usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de consulta somente leitura. Como os grupos de failover automático incluem vários bancos de dados, esses bancos de dados devem ser configurados no servidor primário. Os grupos de failover automático oferecem suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou em pool em um servidor do Banco de Dados SQL, se quiser vários secundários nas mesmas regiões ou em regiões diferentes, use a [replicação geográfica ativa](sql-database-active-geo-replication.md). 

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete um ou vários bancos de dados no grupo resultar em failover automático. Typically these are incidents that cannot be self-mitigated by the built-in automatic high availability operations. The examples of failover triggers include an incident caused by a SQL tenant ring or control ring being down due to an OS kernel memory leak on several compute nodes, or an incident caused by one or more tenant rings being down because a wrong network cable was cut during routine hardware decommissioning.  For more information, see [SQL Database High Availability](sql-database-high-availability.md).

Além disso, os grupos de failover automático fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante failovers. Não importa se você usa a ativação de failover manual ou automática, o failover alterna todos os bancos de dados secundários no grupo para primário. Após o failover de banco de dados ser concluído, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região. Para os dados específicos de RPO e RTO, confira [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete bancos de dados no servidor do Banco de Dados SQL ou na instância gerenciada resulta em um failover automático. Você pode gerenciar o grupo de failover automático usando:

- O [Portal do Azure](sql-database-implement-geo-distributed-database.md)
- [PowerShell: grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups).

Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como criar soluções para a recuperação de desastre, veja [Criando soluções de nuvem para a recuperação de desastre usando a replicação geográfica ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funcionalidades e terminologia de grupo de failover automático

- **Failover group (FOG)**

  A failover group is a named group of databases managed by a single SQL Database server or within a single managed instance that can fail over as a unit to another region in case all or some primary databases become unavailable due to an outage in the primary region. When created for managed instances, a failover group contains all user databases in the instance and therefore only one failover group can be configured on an instance.
  
  > [!IMPORTANT]
  > The name of the failover group must be globally unique within the `.database.windows.net` domain.

- **Servidores do Banco de Dados SQL**

     Com servidores do Banco de Dados SQL, alguns ou todos os bancos de dados do usuário em um único servidor podem ser colocados em um grupo de failover. Além disso, um servidor do Banco de Dados SQL dá suporte a vários grupos de failover em um único servidor do Banco de Dados SQL.

- **Primário**

  The SQL Database server or managed instance that hosts the primary databases in the failover group.

- **Secundário**

  The SQL Database server or managed instance that hosts the secondary databases in the failover group. O secundário não pode estar na mesma região do primário.

- **Adicionar bancos de dados individuais ao grupo de failover**

  É possível colocar vários bancos de dados individuais no mesmo servidor do Banco de Dados SQL no mesmo grupo de failover. Se você adicionar um banco de dados individual ao grupo de failover, ele criará automaticamente um banco de dados secundário usando a mesma edição e tamanho da computação no servidor secundário.  Você especificou esse servidor ao criar o grupo de failover. Se você adicionar um banco de dados que já possui um banco de dados secundário no servidor secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no servidor secundário.

  > [!IMPORTANT]
  > Make sure that the secondary server doesn't have a database with the same name unless it is an existing secondary database. In failover groups for managed instance all user databases are replicated. Você não pode escolher um subconjunto de bancos de dados de usuário para replicação no grupo de failover.

- **Adicionar bancos de dados no pool elástico para o grupo de failover**

  É possível colocar todos ou vários bancos de dados dentro de um pool elástico no mesmo grupo de failover. Se o banco de dados primário estiver em um pool elástico, o banco de dados secundário é criado automaticamente no pool elástico com o mesmo nome (pool secundário). Você deve garantir que o servidor secundário contém um pool elástico com exatamente o mesmo nome e capacidade livre suficiente para hospedar os bancos de dados secundários que serão criados pelo grupo de failover. Se você adicionar um banco de dados no pool que já possui um banco de dados secundário no pool secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no pool secundário.
  
- **Zona DNS**

  A unique ID that is automatically generated when a new instance is created. A multi-domain (SAN) certificate for this instance is provisioned to authenticate the client connections to any instance in the same DNS zone. The two managed instances in the same failover group must share the DNS zone.
  
  > [!NOTE]
  > A DNS zone ID is not required for failover groups created for SQL Database servers.

- **Ouvinte de leitura/gravação do grupo de failover**

  A DNS CNAME record that points to the current primary's URL. It is created automatically when the failover group is created and allows the read-write SQL workload to transparently reconnect to the primary database when the primary changes after failover. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.database.windows.net`.

- **Ouvinte de somente leitura do grupo de failover**

  Foi formado um registro CNAME de DNS que aponta ao ouvinte somente leitura que aponta à URL do secundário. It is created automatically when the failover group is created and allows the read-only SQL workload to transparently connect to the secondary using the specified load-balancing rules. When the failover group is created on a SQL Database server, the DNS CNAME record for the listener URL is formed as `<fog-name>.secondary.database.windows.net`. When the failover group is created on a managed instance, the DNS CNAME record for the listener URL is formed as `<fog-name>.zone_id.secondary.database.windows.net`.

- **Política de failover automático**

  Por padrão, um grupo de failover é configurado com uma política de failover automático. O serviço de Banco de Dados SQL dispara o failover após a falha ser detectada e o período de cortesia expirar. O sistema deve verificar se a interrupção não pode ser mitigada pela [infraestrutura de alta disponibilidade interna do serviço do Banco de Dados SQL](sql-database-high-availability.md) devido à escala do impacto. Se você deseja controlar o fluxo de trabalho de failover do aplicativo, pode desligar o failover automático.
  
  > [!NOTE]
  > Because verification of the scale of the outage and how quickly it can be mitigated involves human actions by the operations team, the grace period cannot be set below one hour.  This  limitation applies to all databases in the failover group regardless of their data synchronization state. 

- **Política de failover somente leitura**

  Por padrão, o failover do ouvinte somente leitura é desabilitado. Isso garante que o desempenho do primário não seja afetado quando o secundário estiver offline. No entanto, isso também significa que as sessões somente leitura não poderão conectar-se até que o secundário seja recuperado. If you cannot tolerate downtime for the read-only sessions and are OK to temporarily use the primary for both read-only and read-write traffic at the expense of the potential performance degradation of the primary, you can enable failover for the read-only listener by configuring the `AllowReadOnlyFailoverToPrimary` property. In that case, the read-only traffic will be automatically redirected to the primary if the secondary is not available.

- **Failover planejado**

   O failover planejado executa uma sincronização completa entre o banco de dados primário e o secundário antes de o secundário mudar para a função de primário. Isso assegura que não ocorra nenhuma perda de dados. O failover planejado é usado nos seguintes cenários:

  - Executar a recuperação de desastre em produção quando a perda de dados não é aceitável
  - Relocar os bancos de dados para uma região diferente
  - Retorne os bancos de dados para a região primária após a interrupção ter sido atenuada (failback).

- **Failover não planejado**

   Um failover forçado ou não planejado mudará imediatamente o secundário para a função primária, sem nenhuma sincronização com o primário. Esta operação pode resultar em perda de dados. Um failover não planejado é usado como um método de recuperação durante as interrupções quando o primário não está acessível. When the original primary is back online, it will automatically reconnect without synchronization and become a new secondary.

- **Failover manual**

  Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não for configurada, será necessário fazer o failover manual para recuperar os bancos de dados no grupo de failover para o secundário. Você pode iniciar um failover forçado ou amigável (com sincronização total de dados). O failover manual pode ser usado para relocar o primário para a região secundária. Quando o failover estiver concluído os registros DNS são atualizados automaticamente para garantir a conectividade com o novo primário

- **Período de carência com perda de dados**

  Como os bancos de dados primário e secundário são sincronizados usando replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. By configuring `GracePeriodWithDataLossHours`, you can control how long the system waits before initiating the failover that is likely to result data loss.

- **Vários grupos de failover**

  Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo sofre failover de forma independente. Se seu aplicativo multilocatário usa pools elásticos, você pode usar esse recurso para misturar os bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção a somente metade dos locatários.

  > [!NOTE]
  > A Instância Gerenciada não dá suporte a vários grupos de failover.
  
## <a name="permissions"></a>Permissões

Permissions for a failover group are managed via [role-based access control (RBAC)](../role-based-access-control/overview.md). The [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) role has all the necessary permissions to manage failover groups.

### <a name="create-failover-group"></a>Create failover group

To create a failover group, you need RBAC write access to both the primary and secondary servers, and to all databases in the failover group. For a managed instance, you need RBAC write access to both the primary and secondary managed instance, but permissions on individual databases are not relevant since individual managed instance databases cannot be added to or removed from a failover group. 

### <a name="update-a-failover-group"></a>Update a failover group

To update a failover group, you need RBAC write access to the failover group, and all databases on the current primary server or managed instance.  

### <a name="failover-a-failover-group"></a>Failover a failover group

To fail over a failover group, you need RBAC write access to the failover group on the new primary server or managed instance.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Práticas recomendadas de como usar grupos de failover com bancos de dados individuais e pools elásticos

O grupo de failover automático precisa ser configurado no servidor do Banco de Dados SQL primário e o conectará ao servidor do Banco de Dados SQL secundário em outra região do Azure. Os grupos podem incluir alguns ou todos os bancos de dados nesses servidores. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando vários bancos de dados e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> See [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a single database to a failover group.

Ao projetar um serviço pensando em continuidade de negócios, siga estas diretrizes gerais:

- **Use um ou vários grupos de failover para gerenciar failover de vários bancos de dados**

  Um ou mais grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geograficamente secundário com o mesmo objetivo de serviço do primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, certifique-se de que o geograficamente secundário esteja configurado com o mesmo nível de serviço e tamanho da computação do primário.
  
  > [!IMPORTANT]
  > Creating failover groups between two servers in different subscriptions is not currently supported for single databases and elastic pools. If you move the primary or secondary server to a different subscription after the failover group has been created, it could result in failures of the failover requests and other operations.

- **Use ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações de OLTP, use `<fog-name>.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. Observe que o failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS.

- **Use o ouvinte somente leitura para a carga de trabalho somente leitura**

  Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para sessões somente leitura, use `<fog-name>.secondary.database.windows.net` como a URL do servidor e a conexão é direcionada automaticamente para o secundário. It is also recommended that you indicate in connection string read intent by using `ApplicationIntent=ReadOnly`. If you want to ensure that the read-only workload can reconnect after failover or in case the secondary server goes offline, make sure to configure the `AllowReadOnlyFailoverToPrimary` property of the failover policy.

- **Prepare-se para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode estar "misturado", com alguns componentes em uma região e outros em outra. Para evitar a degradação, garanta a implantação do aplicativo redundante na região de recuperação de desastre e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

  > [!NOTE]
  > O aplicativo na área de recuperação de desastres não precisa usar uma cadeia de conexão diferente.  

- **Prepare-se para a perda de dados**

  If an outage is detected, SQL waits for the period you specified by `GracePeriodWithDataLossHours`. O valor padrão é de 1 hora. If you cannot afford data loss, make sure to set `GracePeriodWithDataLossHours` to a sufficiently large number, such as 24 hours. Use o failover manual do grupo para fazer failback do secundário para primário.

  > [!IMPORTANT]
  > Os pools elásticos com 800 ou menos DTUs e mais de 250 bancos de dados usando a replicação geográfica podem encontrar problemas, incluindo failovers planejados mais longos e diminuição do desempenho.  A ocorrência desses problemas é mais provável para cargas de trabalho com uso intensivo de gravação, quando os pontos de extremidade de replicação geográfica são separados por uma grande extensão geográfica ou quando vários pontos de extremidade secundários são usados para cada banco de dados.  Os sintomas desses problemas são indicados quando o retardo da replicação geográfica aumenta ao longo do tempo.  Esse retardo pode ser monitorado usando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se esses problemas ocorrerem, considere mitigações como aumentar o número de DTUs do pool ou reduzir o número de bancos de dados replicados geograficamente no mesmo pool.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Best practices of using failover groups with managed instances

O grupo de failover automático precisa ser configurado na instância primária e a conectará à instância secundária em uma região do Azure diferente.  Todos os bancos de dados na instância serão replicados para a instância secundária.

O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando uma instância gerenciada e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> See [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md) for a detailed step-by-step tutorial adding a managed instance to use failover group.

If your application uses managed instance as the data tier, follow these general guidelines when designing for business continuity:

- **Criar a instância secundária na mesma zona DNS que a instância primária**

  Para garantir conectividade ininterrupta à instância primária após o failover, ambas as instâncias primária e secundária precisam estar na mesma zona DNS. It will guarantee that the same multi-domain (SAN) certificate can be used to authenticate the client connections to either of the two instances in the failover group. Quando seu aplicativo está pronto para implantação em produção, crie uma instância do secundário em uma região diferente e assegure que ela compartilhe a zona DNS com a instância do primário. You can do it by specifying a `DNS Zone Partner` optional parameter using the Azure portal, PowerShell, or the REST API.

> [!IMPORTANT]
> First instance created in the subnet determines DNS zone for all subsequent instances in the same subnet. This means that two instances from the same subnet cannot belong to different DNS zones.

  For more information about creating the secondary instance in the same DNS zone as the primary instance, see [Create a secondary managed instance](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

- **Permitir o tráfego de replicação entre duas instâncias**

  Já que cada instância é isolada em sua própria rede virtual, o tráfego em duas vias entre essas redes virtuais deve ser permitido. Confira [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- **Create a failover group between managed instances in different subscriptions**

  You can create a failover group between managed instances in two different subscriptions. When using PowerShell API you can do it by  specifying the `PartnerSubscriptionId` parameter for the secondary instance. When using REST API, each instance ID included in the `properties.managedInstancePairs` parameter can have its own subscriptionID.
  
  > [!IMPORTANT]
  > Azure Portal does not support failover groups across different subscriptions.

- **Configurar um grupo de failover para gerenciar o failover da instância inteira**

  O grupo de failover gerenciará o failover de todos os bancos de dados na instância. Quando um grupo é criado, cada banco de dados na instância será replicado geograficamente de modo automático para a instância do secundário. Não é possível usar grupos de failover para iniciar um failover parcial de um subconjunto dos bancos de dados.

  > [!IMPORTANT]
  > Se um banco de dados for removido da instância do primário, ele será também removido automaticamente na instância geográfica do secundário.

- **Use ouvinte de leitura/gravação para carga de trabalho OLTP**

  Ao executar operações de OLTP, use `<fog-name>.zone_id.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. O failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS. Because the secondary instance shares the DNS zone with the primary, the client application will be able to reconnect to it using the same SAN certificate.

- **Conectar-se diretamente a um secundário replicado geograficamente para consultas somente leitura**

  Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para se conectar diretamente ao secundário com replicação geográfica, use `server.secondary.zone_id.database.windows.net` como a URL do servidor.

  > [!NOTE]
  > Em determinadas camadas de serviço, o Banco de Dados SQL do Azure é compatível com o uso de [réplicas somente leitura](sql-database-read-scale-out.md) para realizar o balanceamento de cargas de trabalho de consulta somente leitura usando a capacidade de uma réplica somente leitura e usando o parâmetro `ApplicationIntent=ReadOnly` na cadeia de conexão. Quando você tiver configurado um secundário replicado geograficamente, você pode usar essa funcionalidade para se conectar a uma réplica somente leitura na localização do primário ou na localização com replicação geográfica.
  > - Para se conectar a uma réplica somente leitura na localização do primário, use `<fog-name>.zone_id.database.windows.net`.
  > - To connect to a read-only replica in the secondary location, use `<fog-name>.secondary.zone_id.database.windows.net`.

- **Prepare-se para degradação de desempenho**

  A decisão de failover do SQL é independente do restante do aplicativo ou de outros serviços usados. O aplicativo pode estar "misturado", com alguns componentes em uma região e outros em outra. Para evitar a degradação, garanta a implantação do aplicativo redundante na região de recuperação de desastre e siga essas [diretrizes de segurança de rede](#failover-groups-and-network-security).

- **Prepare-se para a perda de dados**

  Quando uma falha for detectada, o SQL disparará o failover de leitura-gravação se não houver perda de dados, até onde nós sabemos. Caso contrário, ele aguardará o período especificado por você em `GracePeriodWithDataLossHours`. Se você especificou `GracePeriodWithDataLossHours`, esteja preparado para perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, defina GracePeriodWithDataLossHours com um número grande o suficiente, como 24 horas.

  A atualização do DNS do ouvinte de leitura-gravação ocorrerá imediatamente após o início do failover. Esta operação não resultará em perda de dados. No entanto, o processo de mudar as funções de bancos de dados pode levar até 5 minutos em condições normais. Até que ele seja concluído, alguns bancos de dados na nova instância do primário ainda serão somente leitura. If failover is initiated using PowerShell, the entire operation is synchronous. If it is initiated using the Azure portal, the UI will indicate completion status. Se ele é iniciado usando a API REST, use o mecanismo de sondagem padrão do Azure Resource Manager para monitorar quanto à conclusão.

  > [!IMPORTANT]
  > Use o failover manual de grupo para mover os primários de volta para a localização original. Quando a interrupção que causou o failover for atenuada, você poderá mover seus bancos de dados primários para a localização original. Para fazer isso, você deve iniciar o failover manual do grupo.

- **Acknowledge known limitations of failover groups**

  Database rename is not supported for instances in failover group. You will need to temporarily delete failover group to be able to rename a database.

## <a name="failover-groups-and-network-security"></a>Grupos de failover e a segurança de rede

For some applications the security rules require that the network access to the data tier is restricted to a specific component or components such as a VM, web service etc. This requirement presents some challenges for business continuity design and the use of the failover groups. Consider the following options when implementing such restricted access.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Como usar grupos de failover e regras da rede virtual

Se você está usando [Pontos de extremidade e regras de serviço de Rede Virtual](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso ao seu banco de dados SQL, lembre-se de que cada ponto de extremidade de serviço de Rede Virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede. Portanto, apenas os aplicativos implantados na mesma região do cliente podem se conectar ao banco de dados primário. Uma vez que o failover resulta em sessões de cliente SQL serem redirecionadas para um servidor em uma região diferente (secundária), essas sessões falham se originadas de um cliente fora dessa região. Por esse motivo, a política de failover automático não poderá ser habilitada se os servidores participantes estiverem incluídos nas regras de Rede Virtual. Para dar suporte a failover manual, siga estas etapas:

1. Provisione as cópias redundantes dos componentes front-end do seu aplicativo (serviço Web, máquinas virtuais etc.) na região secundária
2. Configurar as [regras da rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) individualmente para os servidores primário e secundário
3. Habilitar o [failover front-end usando uma configuração do Gerenciador de tráfego](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Iniciar o failover manual quando a interrupção for detectada. Essa opção é otimizada para os aplicativos que precisam de latência consistente entre o front-end e a camada de dados e oferece suporte à recuperação quando o front-end, a camada de dados ou ambos são afetados pela interrupção.

> [!NOTE]
> Se você estiver usando o **ouvinte somente leitura** para balanceamento de uma carga de trabalho somente leitura, essa carga de trabalho deverá ser executada em uma VM ou outro recurso na região secundária para que possa se conectar ao banco de dados secundário.

### <a name="using-failover-groups-and-sql-database-firewall-rules"></a>Usando grupos de failover e regras de firewall de banco de dados SQL

Se o seu plano de continuidade de negócios exigir failover usando grupos com failover automático, você poderá restringir o acesso ao banco de dados SQL usando as regras de firewall tradicional. Para dar suporte a failover automático, siga estas etapas:

1. [Criar um IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Crie um balanceador de carga público](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-a-basic-load-balancer) e atribua o IP público a ele.
3. [Crie uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-back-end-servers) para os componentes de front-end
4. [Crie um grupo de segurança de rede](../virtual-network/security-overview.md) e configure conexões de entrada.
5. Verifique se as conexões de saída estão abertas para o banco de dados SQL do Azure usando a [marca de serviço](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Crie uma [regra de firewall de banco de dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada do endereço IP público criado na etapa 1.

Para obter mais informações sobre como configurar o acesso de saída e qual IP usar nas regras de firewall, veja [conexões de saída do balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima garantirá que o failover automático não bloqueie conexões dos componentes front-end e pressupõe que o aplicativo pode tolerar a latência mais longa entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade dos negócios para interrupções regionais, garanta redundância geográfica para bancos de dados e componentes de front-end.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Enabling geo-replication between managed instances and their VNets

When you set up a failover group between primary and secondary managed instances in two different regions, each instance is isolated using an independent virtual network. To allow replication traffic between these VNets ensure these prerequisites are met:

1. The two managed instances need to be in different Azure regions.
1. The two managed instances need to be the same service tier, and have the same storage size.
1. Your secondary managed instance must be empty (no user databases).
1. The virtual networks used by the managed instances need to be connected through a [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) or Express Route. When two virtual networks connect through an on-premises network, ensure there is no firewall rule blocking ports 5022, and 11000-11999. O Emparelhamento VNET Global não é compatível.
1. The two managed instance VNets cannot have overlapping IP addresses.
1. You need to set up your Network Security Groups (NSG) such that ports 5022 and the range 11000~12000 are open inbound and outbound for connections from the other managed instanced subnet. Isso é para permitir o tráfego de replicação entre as instâncias

   > [!IMPORTANT]
   > Regras de segurança de NSG mal configuradas resultam em operações de cópia de banco de dados paralisadas.

1. The secondary instance is configured with the correct DNS zone ID. DNS zone is a property of a managed instance and virtual cluster, and its ID is included in the host name address. The zone ID is generated as a random string when the first managed instance is created in each VNet and the same ID is assigned to all other instances in the same subnet. Once assigned, the DNS zone cannot be modified. Managed instances included in the same failover group must share the DNS zone. You accomplish this by passing the primary instance's zone ID as the value of DnsZonePartner parameter when creating the secondary instance. 

   > [!NOTE]
   > For a detailed tutorial on configuring failover groups with managed instance, see [add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho da computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. When upgrading, we recommend that you upgrade all of the secondary databases first, and then upgrade the primary. When downgrading, reverse the order: downgrade the primary first, and then downgrade all of the secondary databases. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta.

This sequence is recommended specifically to avoid the problem where the secondary at a lower SKU gets overloaded and must be re-seeded during an upgrade or downgrade process. You could also avoid the problem by making the primary read-only, at the expense of impacting all read-write workloads against the primary.

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado.

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. Calling `sp_wait_for_database_copy_sync` blocks the calling thread until the last committed transaction has been transmitted to the secondary database. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. `sp_wait_for_database_copy_sync` is scoped to a specific continuous copy link. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync` prevents data loss after failover, but does not guarantee full synchronization for read access. The delay caused by a `sp_wait_for_database_copy_sync` procedure call can be significant and depends on the size of the transaction log at the time of the call.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauração pontual

Para obter informações sobre como usar a restauração pontual com grupos de failover, confira a [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="programmatically-managing-failover-groups"></a>Gerenciando os grupos de failover programaticamente

Conforme discutido anteriormente, os grupos de failover automático e a replicação geográfica ativa podem ser gerenciados programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, confira [Controle de Acesso Baseado em Funções do Azure](../role-based-access-control/overview.md).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove o grupo de failover do servidor e exclui todos os bancos de dados secundários incluídos no grupo |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração do grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração do grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Dispara o failover do grupo de failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bancos de dados a um grupo de failover do Banco de Dados SQL do Azure|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração do grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração do grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Dispara o failover do grupo de failover para o servidor secundário|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Command | Descrição |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Remove o grupo de failover do servidor e exclui todos os bancos de dados secundários incluídos no grupo |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera a configuração do grupo de failover |
| [az sql failover-group update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifies the configuration of the failover group and/or adds one or more databases to an Azure SQL Database failover group|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Dispara o failover do grupo de failover para o servidor secundário |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Manage SQL database failover groups with managed instances

| Command | Descrição |
| --- | --- |
| [az sql instance-failover-group create](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [az sql instance-failover-group update](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica a configuração do grupo de failover|
| [az sql instance-failover-group show](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera a configuração do grupo de failover|
| [az sql instance-failover-group set-primary](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Dispara o failover do grupo de failover para o servidor secundário|
| [az sql instance-failover-group delete](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Remove um grupo de failover |

* * *

> [!IMPORTANT]
> Para um script de exemplo, confira [Configurar e realizar o failover de um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>REST API: Manage SQL database failover groups with single and pooled databases

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Failover do servidor principal atual para este servidor. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) |Failover do servidor principal atual para este servidor. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover pelo servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista grupos de failover em um servidor. |
| [Atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza um grupo de failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>REST API: Manage failover groups with Managed Instances

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove o grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Failover do servidor principal atual para este servidor. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) |Failover do servidor principal atual para este servidor. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | Obtém um grupo de failover. |
| [Listar grupos de failover – listar por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover em uma localização. |

## <a name="next-steps"></a>Próximos passos

- For detailed tutorials, see
    - [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
    - [Add elastic pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Add a managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
- Para exemplos de scripts, consulte:
  - [Use PowerShell to configure active geo-replication for a single database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use PowerShell to configure active geo-replication for a pooled database in Azure SQL Database](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Use PowerShell to add an Azure SQL Database single database to a failover group](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).
