---
title: Ler réplicas-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso de réplica de leitura no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769034"
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
Você pode criar uma réplica de leitura em uma região diferente do servidor mestre. A replicação entre regiões pode ser útil para cenários como planejamento de recuperação de desastres ou trazer dados mais próximos aos seus usuários.

Você pode ter um servidor mestre em qualquer [região do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo de sua região mestra.

[![ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre está localizado. Estas são as regiões de réplica universal:

Leste da Austrália, sudeste da Austrália, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, leste do Japão, oeste do Japão, Coreia central, sul da Coreia, norte EUA Central, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, oeste dos EUA.

\* Oeste dos EUA 2 está temporariamente indisponível como um local de réplica entre regiões.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não souber o par de sua região, poderá aprender mais no [artigo regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Há limitações a serem consideradas: 

* Disponibilidade regional: o banco de dados do Azure para PostgreSQL está disponível no oeste dos EUA 2, na França central, Norte dos EAU e na Alemanha central. No entanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: algumas regiões do Azure são emparelhadas apenas em uma direção. Essas regiões incluem Índia ocidental, sul do Brasil. 
   Isso significa que um servidor mestre na Índia ocidental pode criar uma réplica na Índia Sul. No entanto, um servidor mestre na Índia Sul não pode criar uma réplica na Índia ocidental. Isso ocorre porque a região secundária da Índia ocidental é sul da Índia, mas a região secundária do Sul da Índia não é oeste da Índia.


## <a name="create-a-replica"></a>Criar uma réplica
Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para [crescimento automático](concepts-pricing-tiers.md#storage-auto-grow)do armazenamento. O recurso de aumento automático permite que a réplica acompanhe os dados replicados para ele e evite uma interrupção na replicação causada por erros de armazenamento insuficiente.

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

A métrica de **atraso de réplica** mostra o tempo desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse retardo. Essa métrica está disponível somente para servidores de réplica. A latência de réplica é calculada a partir da exibição de `pg_stat_wal_receiver`:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Defina um alerta para informá-lo quando o retardo de réplica atinge um valor que não é aceitável para sua carga de trabalho. 

Para obter mais informações, consulte diretamente o servidor mestre para conhecer o retardo de replicação em bytes em todas as réplicas.

No PostgreSQL versão 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

No PostgreSQL versão 9.6 e versões anteriores:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
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

Como a replicação é assíncrona, há um atraso entre o mestre e a réplica. A quantidade de latência pode ser influenciada por vários fatores, como a intensidade da carga de trabalho em execução no servidor mestre e a latência entre os data centers. Na maioria dos casos, a latência de réplica varia entre alguns segundos e alguns minutos. Você pode acompanhar o retardo de replicação real usando o *retardo de réplica*de métrica, que está disponível para cada réplica. Essa métrica mostra o tempo desde a última transação reproduzida. É recomendável que você identifique o que é o retardo médio, observando o atraso da réplica em um período de tempo. Você pode definir um alerta na latência de réplica, de modo que, se ele ficar fora do intervalo esperado, você poderá executar uma ação.

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
Antes de criar uma réplica de leitura, o parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes que uma configuração mestre seja atualizada para um novo valor, atualize a configuração de réplica para um valor igual ou maior. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

PostgreSQL requer que o valor do parâmetro `max_connections` na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. No Banco de Dados do Azure para PostgreSQL, o valor do parâmetro `max_connections` se baseia na SKU. Para obter mais informações, confira [Limites no Banco de Dados do Azure para PostgreSQL](concepts-limits.md). 

Se você tentar atualizar os valores de servidor descritos acima, mas não atender aos limites, você receberá um erro.

As regras de firewall, as regras de rede virtual e as configurações de parâmetros não são herdadas do servidor mestre para a réplica quando a réplica é criada ou posteriormente.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
O [PostgreSQL requer](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que o valor do parâmetro `max_prepared_transactions` na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. Se você quiser alterar `max_prepared_transactions` no mestre, primeiro altere-o nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximos passos
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
* Saiba como [criar e gerenciar réplicas de leitura no CLI do Azure e na API REST](howto-read-replicas-cli.md).
