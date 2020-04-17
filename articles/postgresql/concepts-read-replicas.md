---
title: Ler réplicas - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve o recurso de réplica de leitura no Banco de Dados Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 545d04bdede76a6ce25c9e4665f39c01ff6caa73
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531976"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Leia réplicas no Banco de Dados Do Azure para PostgreSQL - Servidor Único

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para PostgreSQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. Réplicas são atualizadas de forma assíncrona com a tecnologia de replicação nativa do mecanismo PostgreSQL.

As réplicas são novos servidores que você gerencia de modo similar ao Banco de Dados do Azure para PostgreSQL normal. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Saiba como [criar e gerenciar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura
O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do PostgreSQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do seu servidor mestre. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou a aproximação de dados com seus usuários.

Você pode ter um servidor mestre em qualquer [banco de dados Do Azure para região PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A imagem abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região mestre.

[![Leia as regiões das réplicas](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplicas universais
Você sempre pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde seu servidor mestre está localizado. Estas são as regiões de réplicauniversal:

Austrália Leste, Austrália Sudeste, Central dos EUA, Leste da Ásia, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul, Norte dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Oeste dos EUA.

*West US 2 está temporariamente indisponível como um local de réplica de região transversal.


### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não conhece o par da sua região, você pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas transfronteiriças para o planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

Há limitações a considerar: 

* Disponibilidade regional: O Banco de Dados Azure para PostgreSQL está disponível no West US 2, France Central, Eau North e Germany Central. No entanto, suas regiões pareadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azure são emparelhadas em uma única direção. Essas regiões incluem a Índia Ocidental, O Sul do Brasil. 
   Isso significa que um servidor mestre na Índia Ocidental pode criar uma réplica no sul da Índia. No entanto, um servidor mestre no sul da Índia não pode criar uma réplica na Índia Ocidental. Isso porque a região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do sul da Índia não é a Índia Ocidental.


## <a name="create-a-replica"></a>Criar uma réplica
Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para PostgreSQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

Cada réplica é habilitada para [o armazenamento de crescimento automático](concepts-pricing-tiers.md#storage-auto-grow). O recurso de crescimento automático permite que a réplica acompanhe os dados replicados e evite uma quebra na replicação causada por erros de armazenamento.

O recurso de réplica de leitura usa a replicação física do PostgreSQL, e não a replicação lógica. A replicação de streaming usando slots de replicação é o modo de operação padrão. Quando necessário, o envio de logs é usado para recuperar o atraso.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica
Quando você cria uma réplica, ela não herda as regras de firewall nem o ponto de extremidade de serviço de rede virtual do servidor mestre. Essas regras precisam ser configuradas independentemente da réplica.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para PostgreSQL. Para um servidor chamado **my replica** with the **admin useradmin**, você pode se conectar à réplica usando psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação
O Banco de Dados Azure para PostgreSQL fornece duas métricas para monitorar a replicação. As duas métricas são **Max Lag através de réplicas** e replica **lag**. Para saber como visualizar essas métricas, consulte o **Monitor de uma réplica** do artigo como ler [réplica](howto-read-replicas-portal.md).

A métrica **Max Lag Across Replicas** mostra o lag em bytes entre o mestre e a réplica mais atrasada. Essa métrica está disponível apenas no servidor mestre.

A métrica **Replica Lag** mostra o tempo desde a última transação reproduzida. Se não houver nenhuma transação ocorrendo no servidor mestre, a métrica refletirá esse retardo. Esta métrica está disponível apenas para servidores de réplica. O Defasagem `pg_stat_wal_receiver` de réplica é calculado a partir da exibição:

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

Quando você para a replicação, a réplica perde todos os links para o seu mestre anterior e outras réplicas.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover
Não há failover automatizado entre servidores mestre e réplica. 

Como a replicação é assíncrona, há um atraso entre o mestre e a réplica. A quantidade de lag pode ser influenciada por uma série de fatores, como o quão pesada é a carga de trabalho em execução no servidor mestre e a latência entre data centers. Na maioria dos casos, o lag de réplicavaria entre alguns segundos e alguns minutos. Você pode acompanhar seu atraso de replicação real usando a métrica *Replica Lag*, que está disponível para cada réplica. Esta métrica mostra o tempo desde a última transação reproduzida. Recomendamos que você identifique qual é o seu atraso médio observando sua réplica de lag durante um período de tempo. Você pode definir um alerta sobre o set lag de réplica, de modo que se ele for fora do seu alcance esperado, você pode tomar medidas.

> [!Tip]
> Se você falhar na réplica, o lag no momento em que você desvincular a réplica do mestre indicará quantos dados são perdidos.

Uma vez que você decidiu que quer failover para uma réplica, 

1. Parar a replicação para a réplica<br/>
   Esta etapa é necessária para tornar o servidor de réplica capaz de aceitar gravações. Como parte desse processo, o servidor de réplica será reiniciado e desvinculado do mestre. Uma vez iniciada a replicação stop, o processo de back-end normalmente leva cerca de 2 minutos para ser concluído. Consulte a seção de [replicação](#stop-replication) stop deste artigo para entender as implicações desta ação.
    
2. Aponte sua aplicação para a (antiga) réplica<br/>
   Cada servidor tem uma seqüência de conexão única. Atualize seu aplicativo para apontar para a réplica (anterior) em vez do mestre.
    
Uma vez que seu aplicativo esteja processando com sucesso leituras e gravações, você completou o failover. A quantidade de tempo de inatividade que suas experiências de aplicação dependerá quando você detectar um problema e completar as etapas 1 e 2 acima.


## <a name="considerations"></a>Considerações

Esta seção resume as considerações sobre o recurso de réplica de leitura.

### <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma réplica de leitura, o parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro é alterado, uma reinicialização do servidor é necessária para que a alteração entre em vigor. O parâmetro `azure.replication_support` se aplica somente às camadas de Uso Geral e Otimizada para Memória.

### <a name="new-replicas"></a>Novas réplicas
Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para PostgreSQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica
Uma réplica é criada usando as mesmas configurações de computação e armazenamento que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes que uma configuração mestre seja atualizada para um novo valor, atualize a configuração da réplica para um valor igual ou maior. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

PostgreSQL requer que o valor do parâmetro `max_connections` na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não será iniciada. No Banco de Dados do Azure para PostgreSQL, o valor do parâmetro `max_connections` se baseia na SKU. Para obter mais informações, confira [Limites no Banco de Dados do Azure para PostgreSQL](concepts-limits.md). 

Se você tentar atualizar os valores do servidor descritos acima, mas não aderir aos limites, você receberá um erro.

Regras de firewall, regras de rede virtual e configurações de parâmetros não são herdadas do servidor mestre para a réplica quando a réplica é criada ou posteriormente.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL exige que](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) `max_prepared_transactions` o valor do parâmetro na réplica de leitura seja maior ou igual ao valor mestre; caso contrário, a réplica não vai começar. Se você quiser `max_prepared_transactions` mudar no mestre, primeiro altere-o nas réplicas.

### <a name="stopped-replicas"></a>Réplicas paradas
Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica será reiniciada para aplicar a alteração. A réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos
Quando um servidor mestre é excluído, todas as suas réplicas de leitura se tornam servidores autônomos. As réplicas são reiniciadas para refletir essa alteração.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar e gerenciar réplicas de leitura no portal do Azure](howto-read-replicas-portal.md).
* Aprenda a [criar e gerenciar réplicas de leitura na Azure CLI e na API REST](howto-read-replicas-cli.md).
