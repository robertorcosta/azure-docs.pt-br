---
title: Ler réplicas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de réplica de leitura no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f093d9b1a67d5e6836fc7f760b0336c9923f5186
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902079"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Ler réplicas no banco de dados do Azure para PostgreSQL-servidor único

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para PostgreSQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. Réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativa do mecanismo PostgreSQL.

As réplicas são novos servidores que você gerencia de modo similar ao Banco de Dados do Azure para PostgreSQL normal. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do PostgreSQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente da do servidor mestre. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

>[!NOTE]
> Os servidores de camada básica oferecem suporte apenas à replicação de mesma região.

Você pode ter um servidor mestre em qualquer [região do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região mestra.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Ler regiões de réplica":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre está localizado. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA, oeste dos EUA 2, Oeste EUA Central.

### <a name="paired-regions"></a>Regiões emparelhadas
Você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre além das regiões de réplica universal. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para o planejamento de recuperação de desastres, recomendamos criar a réplica na região emparelhada, e não em uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

Há limitações a serem consideradas: 

* Disponibilidade regional: o banco de dados do Azure para PostgreSQL está disponível na França central, Norte dos EAU e na Alemanha central. Entretanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Índia ocidental, sul do Brasil. 
   Isso significa que um servidor mestre no Oeste da Índia pode criar uma réplica no Sul da Índia. Entretanto, um servidor mestre no Sul da Índia não pode criar uma réplica no Oeste da Índia. Isso ocorre porque a região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia não é o Oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica
Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para armazenamento de [aumento automático](concepts-pricing-tiers.md#storage-auto-grow). O recurso de aumento automático permite que a réplica acompanhe os dados replicados para ele e evite uma interrupção na replicação causada por erros de armazenamento insuficiente.

O recurso de réplica de leitura usa a replicação física do PostgreSQL, e não a replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Para um servidor chamado **minha réplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
O banco de dados do Azure para PostgreSQL fornece duas métricas para monitorar a replicação. As duas métricas são o **retardo máximo entre réplicas** e **atraso de réplica**. Para saber como exibir essas métricas, consulte a seção **monitorar uma réplica** do artigo de [instruções de leitura de réplica](howto-read-replicas-portal.md).

A métrica de **atraso máximo entre réplicas** mostra o atraso em bytes entre o mestre e a réplica mais deatrasante. Essa métrica está disponível apenas no servidor mestre.

A métrica de **atraso de réplica** mostra o tempo desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse retardo. Essa métrica está disponível somente para servidores de réplica. A latência de réplica é calculada a partir da `pg_stat_wal_receiver` exibição:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Defina um alerta para informá-lo quando o retardo de réplica atinge um valor que não é aceitável para sua carga de trabalho. 

Para obter mais informações, consulte diretamente o servidor mestre para conhecer o retardo de replicação em bytes em todas as réplicas.

No PostgreSQL versão 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

No PostgreSQL versão 9.6 e versões anteriores:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Se um servidor mestre ou a réplica de leitura for reiniciado, o tempo necessário para reiniciar e pegar o ritmo será refletido na métrica de Retardo de Réplica.

## <a name="stop-replication"></a>Parar replicação
Você pode interromper a replicação entre um mestre e uma réplica. A ação de interrupção faz com que a réplica seja reinicia e remova suas configurações de replicação. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Quando você interrompe a replicação, a réplica perde todos os links para o mestre anterior e outras réplicas.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Não há nenhum failover automatizado entre servidores mestre e de réplica. 

Como a replicação é assíncrona, há um atraso entre o mestre e a réplica. A quantidade de latência pode ser influenciada por vários fatores, como a intensidade da carga de trabalho em execução no servidor mestre e a latência entre os data centers. Na maioria dos casos, o atraso da réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica*de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

> [!Tip]
> Se você realizar o failover para a réplica, o retardo no momento em que você desvincular a réplica do mestre indicará a quantidade de dados perdidos.

Depois que você decidir que deseja fazer failover para uma réplica, 

1. Parar a replicação na réplica<br/>
   Essa etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será reiniciado e será desvinculado do mestre. Depois que você inicia a interrupção da replicação, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção [parar replicação](#stop-replication) deste artigo para entender as implicações dessa ação.
    
2. Aponte seu aplicativo para a réplica (antiga)<br/>
   Cada servidor tem uma cadeia de conexão exclusiva. Atualize seu aplicativo para apontar para a réplica (antiga) em vez do mestre.
    
Depois que o aplicativo processar leituras e gravações com êxito, você terá concluído o failover. A quantidade de tempo de inatividade com a qual suas experiências de aplicativo dependerão quando você detectar um problema e concluir as etapas 1 e 2 acima.


## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
As réplicas de leitura e a [decodificação lógica](concepts-logical.md) dependem do postgres write ahead log (WAL) para obter informações. Esses dois recursos precisam de diferentes níveis de registro em log do Postgres. A decodificação lógica precisa de um nível mais alto de log do que as réplicas de leitura.

Para configurar o nível certo de registro em log, use o parâmetro de suporte de replicação do Azure. O suporte à replicação do Azure tem três opções de configuração:

* **Off** -coloca as informações mínimas no Wal. Essa configuração não está disponível na maioria dos servidores do banco de dados do Azure para PostgreSQL.  
* **Réplica** -mais detalhada do que **desativado**. Esse é o nível mínimo de log necessário para que as [réplicas de leitura](concepts-read-replicas.md) funcionem. Essa configuração é o padrão na maioria dos servidores.
* **Logical** -mais detalhado que a **réplica**. Este é o nível mínimo de registro em log para que a decodificação lógica funcione. As réplicas de leitura também funcionam nessa configuração.

O servidor precisa ser reiniciado após uma alteração desse parâmetro. Internamente, esse parâmetro define os parâmetros postgres `wal_level` , `max_replication_slots` e `max_wal_senders` .

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas, incluindo o período de retenção do armazenamento e do backup.

As regras de firewall, as regras de rede virtual e as configurações de parâmetros não são herdadas do servidor mestre para a réplica quando a réplica é criada ou posteriormente.

### <a name="scaling"></a>Scaling
Dimensionamento de vCores ou entre Uso Geral e com otimização de memória:
* O PostgreSQL requer que a `max_connections` configuração em um servidor secundário seja [maior ou igual à configuração no primário](https://www.postgresql.org/docs/current/hot-standby.html), caso contrário, o secundário não será iniciado.
* No banco de dados do Azure para PostgreSQL, o máximo permitido de conexões para cada servidor é corrigido para o SKU de computação, já que as conexões ocupam memória. Você pode saber mais sobre o [mapeamento entre max_connections e SKUs de computação](concepts-limits.md).
* **Escalar verticalmente**: primeiro escalar verticalmente a computação de uma réplica e, em seguida, escalar verticalmente o primário. Essa ordem impedirá que erros violem o `max_connections` requisito.
* **Reduzir verticalmente**: primeiro reduza horizontalmente a computação do primário e reduza a réplica. Se você tentar dimensionar a réplica abaixo do primário, haverá um erro, pois isso viola o `max_connections` requisito.

Armazenamento em escala:
* Todas as réplicas têm o aumento automático de armazenamento habilitado para evitar problemas de replicação de uma réplica de armazenamento completo. Essa configuração não pode ser desabilitada.
* Você também pode escalar verticalmente o armazenamento manualmente, como faria em qualquer outro servidor


### <a name="basic-tier"></a>Camada Básica
Os servidores de camada básica oferecem suporte apenas à replicação de mesma região.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL requer](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o valor do `max_prepared_transactions` parâmetro na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. Se você quiser alterar `max_prepared_transactions` o mestre, primeiro altere-o nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).
