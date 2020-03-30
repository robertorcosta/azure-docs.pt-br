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
ms.date: 2/10/2020
ms.openlocfilehash: 6d87d3373711d12df3f2cced26ef35ae951ad41e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269829"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Use grupos de failover automático para habilitar o failover transparente e coordenado de vários bancos de dados

Os grupos de failover automático são um recurso de banco de dados SQL que permite gerenciar a replicação e o failover de um grupo de bancos de dados em um servidor de banco de dados SQL ou em todos os bancos de dados em uma instância gerenciada para outra região. É uma abstração declarativa em cima do recurso de [georeplicação ativa](sql-database-active-geo-replication.md) existente, projetado para simplificar a implantação e o gerenciamento de bancos de dados geo-replicados em escala. Você pode iniciar o failover manualmente ou pode delegá-lo para o serviço de Banco de Dados SQL com base em uma política definida pelo usuário. A última opção permite que você recupere automaticamente vários bancos de dados relacionados em uma região secundária após uma falha catastrófica ou outro evento não planejado que resulte em perda total ou parcial de disponibilidade do serviço de Banco de Dados SQL na região primária. Um grupo de failover pode incluir um ou vários bancos de dados, normalmente usados pelo mesmo aplicativo. Além disso, eles podem usar os bancos de dados secundários legíveis para descarregar cargas de trabalho de consulta somente leitura. Como os grupos de failover automático incluem vários bancos de dados, esses bancos de dados devem ser configurados no servidor primário. Os grupos de failover automático oferecem suporte à replicação de todos os bancos de dados no grupo para apenas um servidor secundário em uma região diferente.

> [!NOTE]
> Ao trabalhar com bancos de dados individuais ou em pool em um servidor do Banco de Dados SQL, se quiser vários secundários nas mesmas regiões ou em regiões diferentes, use a [replicação geográfica ativa](sql-database-active-geo-replication.md). 

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete um ou vários bancos de dados no grupo resultar em failover automático. Normalmente, estes são incidentes que não podem ser auto-mitigados pelas operações automáticas de alta disponibilidade incorporadas. Os exemplos de gatilhos de failover incluem um incidente causado por um anel de inquilino SQL ou anel de controle sendo desligado devido a um vazamento de memória do kernel do SISTEMA OPERACIONAL em vários nós de computação, ou um incidente causado por um ou mais anéis de inquilino saem porque um cabo de rede errado foi cortado durante desativação de hardware de rotina.  Para obter mais informações, consulte [SQL Database High Availability](sql-database-high-availability.md).

Além disso, os grupos de failover automático fornecem pontos de extremidade de ouvinte de leitura/gravação e somente leitura que permanecem inalterados durante failovers. Não importa se você usa a ativação de failover manual ou automática, o failover alterna todos os bancos de dados secundários no grupo para primário. Após o failover de banco de dados ser concluído, o registro DNS é atualizado automaticamente para redirecionar os pontos de extremidade para a nova região. Para os dados específicos de RPO e RTO, confira [Visão geral da continuidade de negócios](sql-database-business-continuity.md).

Ao usar grupos de failover automático com uma política de failover automático, qualquer interrupção que afete bancos de dados no servidor do Banco de Dados SQL ou na instância gerenciada resulta em um failover automático. Você pode gerenciar o grupo de failover automático usando:

- [Portal Azure](sql-database-implement-geo-distributed-database.md)
- [Azure CLI: Grupo Failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)
- [PowerShell: grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- [API REST: grupo de failover](/rest/api/sql/failovergroups).

Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Para garantir a continuidade de negócios real, a adição de redundância de banco de dados entre datacenters é apenas parte da solução. A recuperação de um aplicativo (serviço) de ponta a ponta após uma falha catastrófica exige a recuperação de todos os componentes que constituem o serviço e quaisquer serviços dependentes. O software cliente (por exemplo, um navegador com um JavaScript personalizado), front-ends da Web, armazenamento e DNS são exemplos desses componentes. É fundamental que todos os componentes sejam resilientes às mesmas falhas e fiquem disponíveis dentro do RTO (objetivo de tempo de recuperação) de seu aplicativo. Portanto, você precisa identificar todos os serviços dependentes e entender as garantias e os recursos que eles fornecem. Em seguida, você deve tomar as medidas necessárias para garantir que seu serviço funcione durante o failover dos serviços dos quais ele depende. Para obter mais informações sobre como projetar soluções para recuperação de desastres, consulte [Projetando soluções em nuvem para recuperação de desastres usando a geo-replicação ativa](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="auto-failover-group-terminology-and-capabilities"></a>Funcionalidades e terminologia de grupo de failover automático

- **Grupo failover (FOG)**

  Um grupo de failover é um grupo nomeado de bancos de dados gerenciados por um único servidor de banco de dados SQL ou dentro de uma única instância gerenciada que pode falhar como uma unidade para outra região no caso de todos ou alguns bancos de dados primários ficarem indisponíveis devido a uma paralisação na região primária. Quando criado para instâncias gerenciadas, um grupo de failover contém todos os bancos de dados de usuários na instância e, portanto, apenas um grupo de failover pode ser configurado em uma instância.
  
  > [!IMPORTANT]
  > O nome do grupo failover deve ser `.database.windows.net` globalmente único dentro do domínio.

- **Servidores de banco de dados SQL**

     Com servidores do Banco de Dados SQL, alguns ou todos os bancos de dados do usuário em um único servidor podem ser colocados em um grupo de failover. Além disso, um servidor do Banco de Dados SQL dá suporte a vários grupos de failover em um único servidor do Banco de Dados SQL.

- **Primária**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos de dados principais no grupo de failover.

- **Secundário**

  O servidor de banco de dados SQL ou a instância gerenciada que hospeda os bancos de dados secundários no grupo de failover. O secundário não pode estar na mesma região do primário.

- **Adicionar bancos de dados individuais ao grupo de failover**

  É possível colocar vários bancos de dados individuais no mesmo servidor do Banco de Dados SQL no mesmo grupo de failover. Se você adicionar um banco de dados individual ao grupo de failover, ele criará automaticamente um banco de dados secundário usando a mesma edição e tamanho da computação no servidor secundário.  Você especificou esse servidor ao criar o grupo de failover. Se você adicionar um banco de dados que já possui um banco de dados secundário no servidor secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no servidor secundário.

  > [!IMPORTANT]
  > Certifique-se de que o servidor secundário não tenha um banco de dados com o mesmo nome, a menos que seja um banco de dados secundário existente. Em grupos failover para instância gerenciada, todos os bancos de dados do usuário são replicados. Você não pode escolher um subconjunto de bancos de dados de usuário para replicação no grupo de failover.

- **Adicionar bancos de dados no pool elástico para o grupo de failover**

  É possível colocar todos ou vários bancos de dados dentro de um pool elástico no mesmo grupo de failover. Se o banco de dados primário estiver em um pool elástico, o banco de dados secundário é criado automaticamente no pool elástico com o mesmo nome (pool secundário). Você deve garantir que o servidor secundário contém um pool elástico com exatamente o mesmo nome e capacidade livre suficiente para hospedar os bancos de dados secundários que serão criados pelo grupo de failover. Se você adicionar um banco de dados no pool que já possui um banco de dados secundário no pool secundário, esse vínculo de replicação geográfica é herdado pelo grupo. Quando você adiciona um banco de dados que já tem um banco de dados secundário em um servidor que não faz parte do grupo de failover, um novo banco de dados secundário é criado no pool secundário.
  
- **Semeada Inicial** 

  Ao adicionar bancos de dados, piscinas elásticas ou instâncias gerenciadas a um grupo de failover, há uma fase inicial de semeação antes da replicação de dados ser iniciada. A fase inicial de semeação é a operação mais longa e cara. Uma vez que a semeada inicial é concluída, os dados são sincronizados e, em seguida, apenas as alterações subseqüentes de dados são replicadas. O tempo necessário para a semente inicial ser concluída depende do tamanho de seus dados, do número de bancos de dados replicados e da velocidade do link entre as entidades no grupo failover. Em circunstâncias normais, a velocidade típica de semeamento é de 50-500 GB por hora para um único banco de dados ou piscina elástica, e 18-35 GB por hora para uma instância gerenciada. A semeação é realizada para todas as bases de dados em paralelo. Você pode usar a velocidade de semeada indicada, juntamente com o número de bancos de dados e o tamanho total dos dados para estimar quanto tempo a fase inicial de semeada levará antes da replicação dos dados começar.

  Para instâncias gerenciadas, a velocidade do link de Rota Expressa entre as duas instâncias também precisa ser considerada ao estimar o tempo da fase inicial de semeadura. Se a velocidade do elo entre as duas instâncias for mais lenta do que o necessário, o tempo de semente provavelmente será notavelmente impactado. Você pode usar a velocidade de semeadura declarada, o número de bancos de dados, o tamanho total dos dados e a velocidade de link para estimar quanto tempo a fase inicial de semeadura levará antes da replicação dos dados começar. Por exemplo, para um único banco de dados de 100 GB, a fase inicial de sementes levaria de 2,8 a 5,5 horas se o link fosse capaz de empurrar 35 GB por hora. Se o link só pode transferir 10 GB por hora, então a semeadura de um banco de dados de 100 GB levará cerca de 10 horas. Se houver vários bancos de dados para replicar, a semeadura será executada em paralelo, e, quando combinada com uma velocidade de ligação lenta, a fase inicial de semeadura pode levar consideravelmente mais tempo, especialmente se a semeadura paralela de dados de todas as bases de dados exceder o disponível largura de banda de link. Se a largura de banda da rede entre duas instâncias for limitada e você estiver adicionando várias instâncias gerenciadas a um grupo de failover, considere adicionar várias instâncias gerenciadas ao grupo de failover sequencialmente, uma a uma.

  
- **Zona DNS**

  Um ID único que é gerado automaticamente quando uma nova instância é criada. Um certificado san (san) de vários domínios, para este caso, é provisionado para autenticar as conexões do cliente em qualquer instância na mesma região de DNS. As duas instâncias gerenciadas no mesmo grupo de failover devem compartilhar a zona DNS.
  
  > [!NOTE]
  > Um ID de zona DNS não é necessário para grupos de failover criados para servidores sql database.

- **Ouvinte de leitura/gravação do grupo de failover**

  Um registro CNAME DNS que aponta para a URL da primária atual. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL de leitura-gravação se reconecte de forma transparente ao banco de dados principal quando as alterações primárias após o failover. Quando o grupo failover é criado em um servidor sql database, o registro `<fog-name>.database.windows.net`DNS CNAME para a URL do ouvinte é formado como . Quando o grupo failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.database.windows.net`.

- **Ouvinte de somente leitura do grupo de failover**

  Foi formado um registro CNAME de DNS que aponta ao ouvinte somente leitura que aponta à URL do secundário. Ele é criado automaticamente quando o grupo de failover é criado e permite que a carga de trabalho SQL somente leitura se conecte de forma transparente ao secundário usando as regras de balanceamento de carga especificadas. Quando o grupo failover é criado em um servidor sql database, o registro `<fog-name>.secondary.database.windows.net`DNS CNAME para a URL do ouvinte é formado como . Quando o grupo failover é criado em uma instância gerenciada, o registro DNS CNAME para a URL do ouvinte é formado como `<fog-name>.zone_id.secondary.database.windows.net`.

- **Política de failover automático**

  Por padrão, um grupo de failover é configurado com uma política de failover automático. O serviço de Banco de Dados SQL dispara o failover após a falha ser detectada e o período de cortesia expirar. O sistema deve verificar se a interrupção não pode ser mitigada pela [infraestrutura de alta disponibilidade interna do serviço do Banco de Dados SQL](sql-database-high-availability.md) devido à escala do impacto. Se você deseja controlar o fluxo de trabalho de failover do aplicativo, pode desligar o failover automático.
  
  > [!NOTE]
  > Como a verificação da escala da paralisação e a rapidez com que ela pode ser mitigada envolve ações humanas da equipe de operações, o período de carência não pode ser definido abaixo de uma hora.  Essa limitação se aplica a todas as bases de dados do grupo failover, independentemente do estado de sincronização de dados. 

- **Política de failover somente leitura**

  Por padrão, o failover do ouvinte somente leitura é desabilitado. Isso garante que o desempenho do primário não seja afetado quando o secundário estiver offline. No entanto, isso também significa que as sessões somente leitura não poderão conectar-se até que o secundário seja recuperado. Se você não puder tolerar o tempo de inatividade para as sessões somente de leitura e estiver ok em usar temporariamente o tráfego principal para tráfego somente leitura e `AllowReadOnlyFailoverToPrimary` leitura-gravação em detrimento da potencial degradação de desempenho do principal, você pode habilitar failover para o ouvinte somente leitura configurando a propriedade. Nesse caso, o tráfego somente leitura será automaticamente redirecionado para o principal se o secundário não estiver disponível.

- **Failover planejado**

   O failover planejado executa uma sincronização completa entre o banco de dados primário e o secundário antes de o secundário mudar para a função de primário. Isso assegura que não ocorra nenhuma perda de dados. O failover planejado é usado nos seguintes cenários:

  - Executar a recuperação de desastre em produção quando a perda de dados não é aceitável
  - Relocar os bancos de dados para uma região diferente
  - Retorne os bancos de dados para a região primária após a interrupção ter sido atenuada (failback).

- **Failover não planejado**

   Um failover forçado ou não planejado mudará imediatamente o secundário para a função primária, sem nenhuma sincronização com o primário. Esta operação pode resultar em perda de dados. Um failover não planejado é usado como um método de recuperação durante as interrupções quando o primário não está acessível. Quando a primária original estiver novamente on-line, ela se reconectará automaticamente sem sincronização e se tornará uma nova secundária.

- **Failover manual**

  Você pode iniciar o failover manualmente a qualquer momento, independentemente da configuração de failover automático. Se a política de failover automático não for configurada, será necessário fazer o failover manual para recuperar os bancos de dados no grupo de failover para o secundário. Você pode iniciar um failover forçado ou amigável (com sincronização total de dados). O failover manual pode ser usado para relocar o primário para a região secundária. Quando o failover estiver concluído os registros DNS são atualizados automaticamente para garantir a conectividade com o novo primário

- **Período de carência com perda de dados**

  Como os bancos de dados primário e secundário são sincronizados usando replicação assíncrona, o failover pode resultar em perda de dados. Você pode personalizar a política de failover automático para refletir a tolerância do seu aplicativo à perda de dados. Ao `GracePeriodWithDataLossHours`configurar, você pode controlar quanto tempo o sistema espera antes de começar o failover que provavelmente resultará na perda de dados.

- **Vários grupos de failover**

  Você pode configurar vários grupos de failover para o mesmo par de servidores para controlar a escala de failovers. Cada grupo sofre failover de forma independente. Se seu aplicativo multilocatário usa pools elásticos, você pode usar esse recurso para misturar os bancos de dados primários e secundários em cada pool. Dessa forma, você pode reduzir o impacto de uma interrupção a somente metade dos locatários.

  > [!NOTE]
  > A Instância Gerenciada não dá suporte a vários grupos de failover.
  
## <a name="permissions"></a>Permissões

As permissões para um grupo failover são gerenciadas via [RBAC (Role-Based Access Control, controle de acesso baseado em função).](../role-based-access-control/overview.md) A função [SQL Server Contributor](../role-based-access-control/built-in-roles.md#sql-server-contributor) tem todas as permissões necessárias para gerenciar grupos de failover.

### <a name="create-failover-group"></a>Criar grupo de failover

Para criar um grupo de failover, você precisa de acesso à gravação RBAC aos servidores primários e secundários e a todos os bancos de dados do grupo failover. Para uma instância gerenciada, você precisa que o RBAC escreva acesso à instância gerenciada primária e secundária, mas as permissões em bancos de dados individuais não são relevantes, uma vez que os bancos de dados de instâncias gerenciadas individuais não podem ser adicionados ou removidos de um grupo de failover. 

### <a name="update-a-failover-group"></a>Atualize um grupo de failover

Para atualizar um grupo de failover, você precisa de acesso à gravação RBAC ao grupo de failover e a todos os bancos de dados no servidor principal atual ou instância gerenciada.  

### <a name="failover-a-failover-group"></a>Failover um grupo de failover

Para falhar sobre um grupo de failover, você precisa de acesso à gravação RBAC ao grupo failover no novo servidor principal ou instância gerenciada.

## <a name="best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools"></a>Práticas recomendadas de como usar grupos de failover com bancos de dados individuais e pools elásticos

O grupo de failover automático precisa ser configurado no servidor do Banco de Dados SQL primário e o conectará ao servidor do Banco de Dados SQL secundário em outra região do Azure. Os grupos podem incluir alguns ou todos os bancos de dados nesses servidores. O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando vários bancos de dados e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group.png)

> [!NOTE]
> Consulte [Adicionar um banco de dados único a um grupo de failover](sql-database-single-database-failover-group-tutorial.md) para um tutorial passo-a-passo detalhado adicionando um único banco de dados a um grupo de failover.

Ao projetar um serviço pensando em continuidade de negócios, siga estas diretrizes gerais:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Usando um ou vários grupos failover para gerenciar failover de vários bancos de dados

Um ou mais grupos de failover podem ser criados entre dois servidores em diferentes regiões (servidores primário e secundário). Cada grupo pode conter um ou vários bancos de dados que são recuperados como uma unidade no caso de alguns ou todos os bancos de dados primários ficarem indisponíveis devido a uma interrupção na região primária. O grupo de failover cria um banco de dados geograficamente secundário com o mesmo objetivo de serviço do primário. Se você adicionar uma relação de replicação geográfica existente ao grupo de failover, certifique-se de que o geograficamente secundário esteja configurado com o mesmo nível de serviço e tamanho da computação do primário.
  
> [!IMPORTANT]
> A criação de grupos de failover entre dois servidores em diferentes assinaturas não é suportada atualmente para bancos de dados únicos e pools elásticos. Se você mover o servidor principal ou secundário para uma assinatura diferente após a criação do grupo failover, isso pode resultar em falhas nas solicitações de failover e outras operações.

### <a name="using-read-write-listener-for-oltp-workload"></a>Usando o ouvinte de leitura para carga de trabalho OLTP

Ao executar operações de OLTP, use `<fog-name>.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. Observe que o failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS.

### <a name="using-read-only-listener-for-read-only-workload"></a>Usando o ouvinte somente leitura para carga de trabalho somente leitura

Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para sessões somente leitura, use `<fog-name>.secondary.database.windows.net` como a URL do servidor e a conexão é direcionada automaticamente para o secundário. Também é recomendável que você indique `ApplicationIntent=ReadOnly`na intenção de leitura de seqüência de conexão usando . Se você quiser garantir que a carga de trabalho somente leitura possa se reconectar após o `AllowReadOnlyFailoverToPrimary` failover ou no caso de o servidor secundário ficar offline, certifique-se de configurar a propriedade da diretiva failover.

### <a name="preparing-for-performance-degradation"></a>Preparando-se para a degradação do desempenho

Um aplicativo típico do Azure usa vários serviços do Azure e consiste em vários componentes. O failover automatizado do grupo failover é acionado com base no estado em que os componentes Do Azure SQL são apenas. Outros serviços do Azure na região primária podem não ser afetados pela paralisação e seus componentes ainda podem estar disponíveis nessa região. Uma vez que os bancos de dados primários mudem para a região dr, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto da maior latência no desempenho do aplicativo, garanta a redundância de todos os componentes do aplicativo na região DR e siga essas [diretrizes de segurança](#failover-groups-and-network-security)de rede.

### <a name="preparing-for-data-loss"></a>Preparando-se para a perda de dados

Se uma paralisação for detectada, o SQL aguarda `GracePeriodWithDataLossHours`o período especificado por . O valor padrão é de 1 hora. Se você não puder suportar a `GracePeriodWithDataLossHours` perda de dados, certifique-se de definir para um número suficientemente grande, como 24 horas. Use o failover manual do grupo para fazer failback do secundário para primário.

> [!IMPORTANT]
> Os pools elásticos com 800 ou menos DTUs e mais de 250 bancos de dados usando a replicação geográfica podem encontrar problemas, incluindo failovers planejados mais longos e diminuição do desempenho.  A ocorrência desses problemas é mais provável para cargas de trabalho com uso intensivo de gravação, quando os pontos de extremidade de replicação geográfica são separados por uma grande extensão geográfica ou quando vários pontos de extremidade secundários são usados para cada banco de dados.  Os sintomas desses problemas são indicados quando o retardo da replicação geográfica aumenta ao longo do tempo.  Esse retardo pode ser monitorado usando [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Se esses problemas ocorrerem, considere mitigações como aumentar o número de DTUs do pool ou reduzir o número de bancos de dados replicados geograficamente no mesmo pool.

### <a name="changing-secondary-region-of-the-failover-group"></a>Mudança da região secundária do grupo failover

Para ilustrar a seqüência de alterações, assumiremos que o servidor A é o servidor principal, o servidor B é o servidor secundário existente e o servidor C é o novo secundário na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie secundários adicionais de cada banco de dados no servidor A para o servidor C usando [a geo-replicação ativa](sql-database-active-geo-replication.md). Cada banco de dados no servidor A terá dois secundários, um no servidor B e outro no servidor C. Isso garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
2.  Exclua o grupo failover. Neste momento, os logins estarão falhando. Isso porque os pseudônimos SQL para os ouvintes do grupo failover foram excluídos e o gateway não reconhecerá o nome do grupo failover.
3.  Recrie o grupo failover com o mesmo nome entre os servidores A e C. Neste ponto, os logins pararão de falhar.
4.  Adicione todos os bancos de dados principais no servidor A ao novo grupo de failover.
5.  Soltar servidor B. Todos os bancos de dados em B serão excluídos automaticamente. 


### <a name="changing-primary-region-of-the-failover-group"></a>Mudança da região primária do grupo failover

Para ilustrar a seqüência de alterações, assumiremos que o servidor A é o servidor principal, o servidor B é o servidor secundário existente e o servidor C é o novo principal na terceira região.  Para fazer a transição, siga estas etapas:

1.  Execute um failover planejado para mudar o servidor principal para B. O servidor A se tornará o novo servidor secundário. O failover pode resultar em vários minutos de inatividade. O tempo real dependerá do tamanho do grupo failover.
2.  Crie secundários adicionais de cada banco de dados no servidor B para o servidor C usando [geo-replicação ativa](sql-database-active-geo-replication.md). Cada banco de dados no servidor B terá dois secundários, um no servidor A e outro no servidor C. Isso garantirá que as bases de dados primárias permaneçam protegidas durante a transição.
3.  Exclua o grupo failover. Neste momento, os logins estarão falhando. Isso porque os pseudônimos SQL para os ouvintes do grupo failover foram excluídos e o gateway não reconhecerá o nome do grupo failover.
4.  Recrie o grupo failover com o mesmo nome entre os servidores A e C. Neste ponto, os logins pararão de falhar.
5.  Adicione todos os bancos de dados principais em B ao novo grupo de failover. 
6.  Execute um failover planejado do grupo failover para alternar B e C. Agora o servidor C se tornará o principal e o B - o secundário. Todos os bancos de dados secundários no servidor A serão automaticamente ligados às primárias em C. Como na etapa 1, o failover pode resultar em vários minutos de inatividade.
6.  Solte o servidor A. Todos os bancos de dados em A serão excluídos automaticamente.

> [!IMPORTANT]
> Quando o grupo failover é excluído, os registros de DNS para os pontos finais do ouvinte também são excluídos. Nesse ponto, há uma probabilidade não zero de outra pessoa criar um grupo de failover ou alias de servidor com o mesmo nome, o que impedirá que você o use novamente. Para minimizar o risco, não use nomes genéricos de grupo de failover.

## <a name="best-practices-of-using-failover-groups-with-managed-instances"></a>Melhores práticas de uso de grupos de failover com instâncias gerenciadas

O grupo de failover automático precisa ser configurado na instância primária e a conectará à instância secundária em uma região do Azure diferente.  Todos os bancos de dados na instância serão replicados para a instância secundária.

O diagrama a seguir ilustra uma configuração típica de um aplicativo de nuvem com redundância geográfica usando uma instância gerenciada e um grupo de failover automático.

![failover automático](./media/sql-database-auto-failover-group/auto-failover-group-mi.png)

> [!NOTE]
> Veja [Adicionar instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md) para um tutorial passo-a-passo detalhado adicionando uma instância gerenciada para usar o grupo failover.

Se o aplicativo usar a instância gerenciada como o nível de dados, siga essas diretrizes gerais ao projetar para a continuidade de negócios:

### <a name="creating-the-secondary-instance"></a>Criando a instância secundária 

Para garantir conectividade ininterrupta à instância primária após o failover, ambas as instâncias primária e secundária precisam estar na mesma zona DNS. Ele garantirá que o mesmo certificado de vários domínios (SAN) possa ser usado para autenticar as conexões do cliente em qualquer uma das duas instâncias do grupo failover. Quando seu aplicativo está pronto para implantação em produção, crie uma instância do secundário em uma região diferente e assegure que ela compartilhe a zona DNS com a instância do primário. Você pode fazê-lo `DNS Zone Partner` especificando um parâmetro opcional usando o portal Azure, PowerShell ou a API REST.

> [!IMPORTANT]
> A primeira instância criada na sub-rede determina a região de DNS para todas as instâncias subseqüentes na mesma sub-rede. Isso significa que duas instâncias da mesma sub-rede não podem pertencer a diferentes regiões DNS.

Para obter mais informações sobre a criação da instância secundária na mesma região de DNS da instância primária, consulte [Criar uma instância gerenciada secundária](sql-database-managed-instance-failover-group-tutorial.md#3---create-a-secondary-managed-instance).

### <a name="enabling-replication-traffic-between-two-instances"></a>Habilitando o tráfego de replicação entre duas instâncias

Já que cada instância é isolada em sua própria rede virtual, o tráfego em duas vias entre essas redes virtuais deve ser permitido. Confira [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Criação de um grupo de failover entre instâncias gerenciadas em diferentes assinaturas

Você pode criar um grupo de failover entre instâncias gerenciadas em duas assinaturas diferentes. Ao usar a API powershell, você `PartnerSubscriptionId` pode fazê-lo especificando o parâmetro para a instância secundária. Ao usar a API REST, cada `properties.managedInstancePairs` ID de instância incluído no parâmetro pode ter sua própria assinaturaID.
  
> [!IMPORTANT]
> O portal Azure não suporta a criação de grupos de failover em diferentes assinaturas. Além disso, para os grupos de failover existentes em diferentes assinaturas e/ou grupos de recursos, o failover não pode ser iniciado manualmente via portal a partir da instância primária. Iniciá-lo a partir da instância geo-secundária em vez disso.

### <a name="managing-failover-to-secondary-instance"></a>Gerenciamento do failover para instância secundária

O grupo de failover gerenciará o failover de todos os bancos de dados na instância. Quando um grupo é criado, cada banco de dados na instância será replicado geograficamente de modo automático para a instância do secundário. Não é possível usar grupos de failover para iniciar um failover parcial de um subconjunto dos bancos de dados.

> [!IMPORTANT]
> Se um banco de dados for removido da instância do primário, ele será também removido automaticamente na instância geográfica do secundário.

### <a name="using-read-write-listener-for-oltp-workload"></a>Usando o ouvinte de leitura para carga de trabalho OLTP

Ao executar operações de OLTP, use `<fog-name>.zone_id.database.windows.net` como a URL do servidor e as conexões são direcionadas automaticamente para o primário. Essa URL não é alterada após o failover. O failover envolve a atualização do registro DNS, para que conexões de cliente sejam redirecionadas ao novo primário somente após a atualização do cliente do cache DNS. Como a instância secundária compartilha a zona DNS com a principal, o aplicativo cliente poderá se reconectar a ela usando o mesmo certificado de SAN.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Usando ouvinte somente leitura para se conectar à instância secundária

Se houver uma carga de trabalho somente leitura logicamente isolada que seja tolerante a determinadas desatualizações de dados, você poderá usar o banco de dados secundário no aplicativo. Para se conectar diretamente ao secundário com replicação geográfica, use `server.secondary.zone_id.database.windows.net` como a URL do servidor.

> [!NOTE]
> Em determinadas camadas de serviço, o Banco de Dados SQL do Azure é compatível com o uso de [réplicas somente leitura](sql-database-read-scale-out.md) para realizar o balanceamento de cargas de trabalho de consulta somente leitura usando a capacidade de uma réplica somente leitura e usando o parâmetro `ApplicationIntent=ReadOnly` na cadeia de conexão. Quando você tiver configurado um secundário replicado geograficamente, você pode usar essa funcionalidade para se conectar a uma réplica somente leitura na localização do primário ou na localização com replicação geográfica.
> - Para se conectar a uma réplica somente leitura na localização do primário, use `<fog-name>.zone_id.database.windows.net`.
> - Para conectar-se a uma réplica somente leitura `<fog-name>.secondary.zone_id.database.windows.net`no local secundário, use .

### <a name="preparing-for-performance-degradation"></a>Preparando-se para a degradação do desempenho

Um aplicativo típico do Azure usa vários serviços do Azure e consiste em vários componentes. O failover automatizado do grupo failover é acionado com base no estado em que os componentes Do Azure SQL são apenas. Outros serviços do Azure na região primária podem não ser afetados pela paralisação e seus componentes ainda podem estar disponíveis nessa região. Uma vez que os bancos de dados primários mudem para a região dr, a latência entre os componentes dependentes pode aumentar. Para evitar o impacto da maior latência no desempenho do aplicativo, garanta a redundância de todos os componentes do aplicativo na região DR e siga essas [diretrizes de segurança](#failover-groups-and-network-security)de rede.

### <a name="preparing-for-data-loss"></a>Preparando-se para a perda de dados

Quando uma falha for detectada, o SQL disparará o failover de leitura-gravação se não houver perda de dados, até onde nós sabemos. Caso contrário, ele aguardará o período especificado por você em `GracePeriodWithDataLossHours`. Se você especificou `GracePeriodWithDataLossHours`, esteja preparado para perda de dados. Em geral, durante interrupções, o Azure favorece a disponibilidade. Se você não puder perder dados, defina GracePeriodWithDataLossHours com um número grande o suficiente, como 24 horas.

A atualização do DNS do ouvinte de leitura-gravação ocorrerá imediatamente após o início do failover. Esta operação não resultará em perda de dados. No entanto, o processo de mudar as funções de bancos de dados pode levar até 5 minutos em condições normais. Até que ele seja concluído, alguns bancos de dados na nova instância do primário ainda serão somente leitura. Se o failover for iniciado usando o PowerShell, toda a operação será síncrona. Se for iniciado usando o portal Azure, a ui indicará o status de conclusão. Se ele é iniciado usando a API REST, use o mecanismo de sondagem padrão do Azure Resource Manager para monitorar quanto à conclusão.

> [!IMPORTANT]
> Use o failover manual de grupo para mover os primários de volta para a localização original. Quando a interrupção que causou o failover for atenuada, você poderá mover seus bancos de dados primários para a localização original. Para fazer isso, você deve iniciar o failover manual do grupo.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Mudança da região secundária do grupo failover

Vamos supor que a instância A é a instância primária, a instância B é a instância secundária existente, e a instância C é a nova instância secundária na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie a instância C com o mesmo tamanho de A e na mesma zona De DNS. 
2.  Exclua o grupo failover entre as instâncias A e B. Neste momento, os logins falharão porque os pseudônimos SQL para os ouvintes do grupo failover foram excluídos e o gateway não reconhecerá o nome do grupo failover. Os bancos de dados secundários serão desconectados das primárias e se tornarão bancos de dados de leitura e gravação. 
3.  Crie um grupo de failover com o mesmo nome entre as instâncias A e C. Siga as instruções no [grupo failover com tutorial de instância gerenciada](sql-database-managed-instance-failover-group-tutorial.md). Esta é uma operação de tamanho de dados e será concluída quando todos os bancos de dados da instância A forem semeados e sincronizados.
4.  Exclua a instância B se não for necessário para evitar cobranças desnecessárias.

> [!NOTE]
> Após a etapa 2 e até que a etapa 3 seja concluída, os bancos de dados na instância A permanecerão desprotegidos de uma falha catastrófica da instância A.

### <a name="changing-primary-region-of-the-failover-group"></a>Mudança da região primária do grupo failover

Vamos assumir a instância A é a instância primária, a instância B é a instância secundária existente, e a instância C é a nova instância primária na terceira região.  Para fazer a transição, siga estas etapas:

1.  Crie a instância C com o mesmo tamanho de B e na mesma zona De DNS. 
2.  Conecte-se à instância B e failover manualmente para mudar a instância primária para B. A instância A se tornará a nova instância secundária automaticamente.
3.  Exclua o grupo failover entre as instâncias A e B. Neste momento, os logins falharão porque os pseudônimos SQL para os ouvintes do grupo failover foram excluídos e o gateway não reconhecerá o nome do grupo failover. Os bancos de dados secundários serão desconectados das primárias e se tornarão bancos de dados de leitura e gravação. 
4.  Crie um grupo de failover com o mesmo nome entre as instâncias A e C. Siga as instruções no [grupo failover com tutorial de instância gerenciada](sql-database-managed-instance-failover-group-tutorial.md). Esta é uma operação de tamanho de dados e será concluída quando todos os bancos de dados da instância A forem semeados e sincronizados.
5.  Exclua a instância A se não for necessário para evitar cobranças desnecessárias.

> [!NOTE] 
> Após a etapa 3 e até que a etapa 4 seja concluída, os bancos de dados na instância A permanecerão desprotegidos de uma falha catastrófica da instância A.

> [!IMPORTANT]
> Quando o grupo failover é excluído, os registros de DNS para os pontos finais do ouvinte também são excluídos. Nesse ponto, há uma probabilidade não zero de outra pessoa criar um grupo de failover ou alias de servidor com o mesmo nome, o que impedirá que você o use novamente. Para minimizar o risco, não use nomes genéricos de grupo de failover.

## <a name="failover-groups-and-network-security"></a>Grupos de failover e a segurança de rede

Para alguns aplicativos, as regras de segurança exigem que o acesso da rede ao nível de dados seja restrito a um componente ou componente específico, como uma VM, serviço web etc. Essa exigência apresenta alguns desafios para o design de continuidade de negócios e o uso dos grupos de failover. Considere as seguintes opções ao implementar esse acesso restrito.

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
2. [Crie um balanceador de carga público](../load-balancer/quickstart-load-balancer-standard-public-portal.md) e atribua o IP público a ele.
3. [Crie uma rede virtual e as máquinas virtuais](../load-balancer/quickstart-load-balancer-standard-public-portal.md) para os componentes de front-end
4. [Crie um grupo de segurança de rede](../virtual-network/security-overview.md) e configure conexões de entrada.
5. Verifique se as conexões de saída estão abertas para o banco de dados SQL do Azure usando a [marca de serviço](../virtual-network/security-overview.md#service-tags) 'Sql'.
6. Crie uma [regra de firewall de banco de dados SQL](sql-database-firewall-configure.md) para permitir o tráfego de entrada do endereço IP público criado na etapa 1.

Para obter mais informações sobre como configurar o acesso de saída e qual IP usar nas regras de firewall, veja [conexões de saída do balanceador de carga](../load-balancer/load-balancer-outbound-connections.md).

A configuração acima garantirá que o failover automático não bloqueie conexões dos componentes front-end e pressupõe que o aplicativo pode tolerar a latência mais longa entre o front-end e a camada de dados.

> [!IMPORTANT]
> Para garantir a continuidade dos negócios para interrupções regionais, garanta redundância geográfica para bancos de dados e componentes de front-end.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Habilitando a replicação geográfica entre instâncias gerenciadas e seus VNets

Quando você configura um grupo de failover entre instâncias gerenciadas primárias e secundárias em duas regiões diferentes, cada instância é isolada usando uma rede virtual independente. Para permitir o tráfego de replicação entre esses VNets, certifique-se de que esses pré-requisitos sejam atendidos:

1. As duas instâncias gerenciadas precisam estar em diferentes regiões do Azure.
2. As duas instâncias gerenciadas precisam ser o mesmo nível de serviço e ter o mesmo tamanho de armazenamento.
3. Sua instância gerenciada secundária deve estar vazia (sem bancos de dados de usuários).
4. As redes virtuais usadas pelas instâncias gerenciadas precisam ser conectadas através de um [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [Express Route](../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Quando duas redes virtuais se conectarem através de uma rede local, certifique-se de que não há nenhuma regra de firewall bloqueando as portas 5022 e 11000-11999. O Emparelhamento VNET Global não é compatível.
5. Os dois VNets de instância gerenciada não podem ter endereços IP sobrepostos.
6. Você precisa configurar seus Grupos de Segurança de Rede (NSG) de forma que as portas 5022 e o intervalo 11000~12000 estejam abertos de entrada e saída para conexões da sub-rede da outra instância gerenciada. Isso é para permitir o tráfego de replicação entre as instâncias.

   > [!IMPORTANT]
   > Regras de segurança de NSG mal configuradas resultam em operações de cópia de banco de dados paralisadas.

7. A instância secundária é configurada com o ID da região DNS correto. A região DNS é uma propriedade de uma instância gerenciada e de um cluster virtual, e seu ID está incluído no endereço de nome do host. O ID de região é gerado como uma seqüência aleatória quando a primeira instância gerenciada é criada em cada VNet e o mesmo ID é atribuído a todas as outras instâncias na mesma sub-rede. Uma vez atribuída, a zona DNS não pode ser modificada. As instâncias gerenciadas incluídas no mesmo grupo de failover devem compartilhar a região DNS. Você consegue isso passando o ID de região da instância primária como o valor do parâmetro DnsZonePartner ao criar a instância secundária. 

   > [!NOTE]
   > Para obter um tutorial detalhado sobre a configuração de grupos de failover com instância gerenciada, consulte [adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md).

## <a name="upgrading-or-downgrading-a-primary-database"></a>Atualizar ou fazer downgrade de um banco de dados primário

Você pode atualizar ou fazer downgrade de um banco de dados primário para um tamanho da computação diferente (dentro da mesma camada de serviço, não entre Uso Geral e Comercialmente Crítico) sem desconectar nenhum banco de dados secundário. Ao atualizar, recomendamos que você atualize todos os bancos de dados secundários primeiro e, em seguida, atualize o principal. Ao desvalorizar, inverta a ordem: rebaixe o primeiro primário e, em seguida, desbaixe todos os bancos de dados secundários. Quando você atualiza ou faz downgrade do banco de dados para uma camada de serviço diferente essa recomendação é imposta.

Esta seqüência é recomendada especificamente para evitar o problema em que o secundário em um SKU inferior fica sobrecarregado e deve ser ressemeado durante um processo de upgrade ou downgrade. Você também pode evitar o problema fazendo a leitura primária somente, em detrimento de impactar todas as cargas de trabalho de leitura-gravação contra as primárias.

> [!NOTE]
> Se você tiver criado um banco de dados secundário como parte da configuração do grupo de failover não é recomendável fazer o downgrade do banco de dados secundário. Isso é para garantir que sua camada de dados tenha capacidade suficiente para processar sua carga de trabalho normal após o failover ser ativado.

## <a name="preventing-the-loss-of-critical-data"></a>Evitando a perda de dados críticos

Devido à alta latência das redes de longa distância, a cópia contínua usa um mecanismo de replicação assíncrona. A replicação assíncrona tornará a perda de alguns dados inevitável se ocorrer uma falha. No entanto, alguns aplicativos podem exigir nenhuma perda de dados. Para proteger essas atualizações críticas, um desenvolvedor de aplicativo pode chamar o procedimento de sistema [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) imediatamente após a confirmação da transação. A `sp_wait_for_database_copy_sync` chamada bloqueia o segmento de chamada até que a última transação comprometida tenha sido transmitida para o banco de dados secundário. Contudo, a chamada não aguarda as transações transmitidas serem reproduzidas e confirmadas no banco de dados secundário. `sp_wait_for_database_copy_sync`é escopo de um link de cópia contínua específico. Qualquer usuário com os direitos de conexão para o banco de dados primário pode chamar este procedimento.

> [!NOTE]
> `sp_wait_for_database_copy_sync`evita a perda de dados após o failover, mas não garante a sincronização total para acesso à leitura. O atraso causado `sp_wait_for_database_copy_sync` por uma chamada de procedimento pode ser significativo e depende do tamanho do registro da transação no momento da chamada.

## <a name="failover-groups-and-point-in-time-restore"></a>Grupos de failover e restauração pontual

Para obter informações sobre como usar a restauração pontual com grupos de failover, confira a [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Limitações de grupos de failover

Esteja ciente das seguintes limitações:

- O grupo failover não pode ser criado entre dois servidores ou instâncias nas mesmas regiões do Azure.
- O grupo failover não pode ser renomeado. Você precisará excluir o grupo e recriá-lo com um nome diferente. 
- O renome de banco de dados não é suportado para instâncias em grupo failover. Você precisará excluir temporariamente o grupo failover para poder renomear um banco de dados.

## <a name="programmatically-managing-failover-groups"></a>Gerenciando os grupos de failover programaticamente

Conforme discutido anteriormente, os grupos de failover automático e a replicação geográfica ativa podem ser gerenciados programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis. A replicação geográfica ativa inclui um conjunto de APIs do Azure Resource Manager para gerenciamento, incluindo a [API REST do Banco de Dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/) e [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). Essas APIs exigem o uso de grupos de recursos e dão suporte a RBAC (segurança baseada em funções). Para obter mais informações sobre como implementar funções de acesso, consulte [O Controle de Acesso Baseado em Função do Azure](../role-based-access-control/overview.md).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Remove um grupo de failover do servidor |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Recupera a configuração de um grupo de failover |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Modifica a configuração de um grupo de failover |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Aciona failover de um grupo failover para o servidor secundário |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Adiciona um ou mais bancos de dados a um grupo de failover|

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerenciar grupos de failover de banco de dados SQL com instâncias gerenciadas

| Cmdlet | Descrição |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Este comando cria um grupo de failover e registra-o em instâncias primárias e secundárias|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Modifica a configuração de um grupo de failover|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Recupera a configuração de um grupo de failover|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Aciona failover de um grupo failover para a instância secundária|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Remove um grupo de failover|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="manage-sql-database-failover-with-single-databases-and-elastic-pools"></a>Gerenciar failover do Banco de Dados SQL com pools elásticos e bancos de dados individuais

| Comando | Descrição |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Esse comando cria um grupo de failover e registra-o nos servidores primário e secundário|
| [az sql failover-group excluir](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Remove um grupo de failover do servidor |
| [az sql failover-group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Recupera uma configuração de grupo failover |
| [az sql failover-group atualização](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Modifica a configuração de um grupo de failover e/ou adiciona um ou mais bancos de dados a um grupo de failover|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Aciona failover de um grupo failover para o servidor secundário |

### <a name="manage-sql-database-failover-groups-with-managed-instances"></a>Gerenciar grupos de failover de banco de dados SQL com instâncias gerenciadas

| Comando | Descrição |
| --- | --- |
| [az sql instância-failover-group criar](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-create) | Este comando cria um grupo de failover e registra-o em instâncias primárias e secundárias |
| [az sql instância-failover-group atualização](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-update) | Modifica a configuração de um grupo de failover|
| [az sql instância-failover-show grupo](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-show) | Recupera a configuração de um grupo de failover|
| [az sql instância-failover-grupo set-principal](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-set-primary) | Aciona failover de um grupo failover para a instância secundária|
| [az sql instância-failover-group excluir](/cli/azure/sql/instance-failover-group#az-sql-instance-failover-group-delete) | Remove um grupo de failover |

* * *

> [!IMPORTANT]
> Para um script de exemplo, confira [Configurar e realizar o failover de um grupo de failover para um banco de dados individual](scripts/sql-database-add-single-db-to-failover-group-powershell.md).

### <a name="rest-api-manage-sql-database-failover-groups-with-single-and-pooled-databases"></a>API REST: Gerencie grupos de failover de banco de dados SQL com bancos de dados únicos e agrupados

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/createorupdate) | Criar ou atualizar grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/delete) | Remove um grupo de failover do servidor |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/failovergroups/failover) | Aciona failover do servidor primário atual para o servidor secundário com sincronização completa de dados.|
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Aciona failover do servidor principal atual para o servidor secundário sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/get) | Recupera a configuração de um grupo de failover. |
| [Listar grupos de failover pelo servidor](https://docs.microsoft.com/rest/api/sql/failovergroups/listbyserver) | Lista os grupos de failover em um servidor. |
| [Atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/failovergroups/update) | Atualiza a configuração de um grupo de failover. |

### <a name="rest-api-manage-failover-groups-with-managed-instances"></a>API REST: Gerencie grupos de failover com instâncias gerenciadas

| API | Descrição |
| --- | --- |
| [Criar ou atualizar grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/createorupdate) | Cria ou atualiza a configuração de um grupo de failover |
| [Excluir grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/delete) | Remove um grupo de failover da instância |
| [Failover (planejado)](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/failover) | Aciona failover da instância primária atual para esta instância com sincronização completa de dados. |
| [O Failover forçado permite a perda de dados](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Aciona failover da instância primária atual para a instância secundária sem sincronizar dados. Esta operação pode resultar em perda de dados. |
| [Obter grupo de failover](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/get) | recupera a configuração de um grupo de failover. |
| [Listar grupos de failover – listar por localização](https://docs.microsoft.com/rest/api/sql/instancefailovergroups/listbylocation) | Lista os grupos de failover em uma localização. |

## <a name="next-steps"></a>Próximas etapas

- Para tutoriais detalhados, consulte
    - [Adicionar banco de dados único a um grupo de failover](sql-database-single-database-failover-group-tutorial.md)
    - [Adicionar pool elástico a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
    - [Adicionar uma instância gerenciada a um grupo de failover](sql-database-managed-instance-failover-group-tutorial.md)
- Para exemplos de scripts, consulte:
  - [Use o PowerShell para configurar a replicação geográfica ativa de um banco de dados individual no Banco de Dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)
  - [Use o PowerShell para configurar a replicação geográfica ativa de um banco de dados em pool no Banco de Dados SQL do Azure](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md)
  - [Usar o PowerShell para adicionar um banco de dados individual do Banco de Dados SQL do Azure a um grupo de failover](scripts/sql-database-add-single-db-to-failover-group-powershell.md)
- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre o uso de backups automatizados para recuperação, consulte [Restaurar um banco de dados a partir dos backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre requisitos de autenticação para um novo servidor primário e banco de dados, consulte [Segurança do Banco de Dados SQL após a recuperação de desastres](sql-database-geo-replication-security-config.md).
