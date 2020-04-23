---
title: Leia réplicas - Banco de dados Azure para MariaDB
description: 'Saiba mais sobre as réplicas de leitura no Banco de Dados Do Azure para MariaDB: escolhendo regiões, criando réplicas, conectando-se a réplicas, monitorando a replicação e parando a replicação.'
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 9129cb308a364a3ed0654055f8afe9dd8c89010a
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024618"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas de leitura no Banco de Dados do Azure para MariaDB

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para MariaDB para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição de arquivo binário (binlog) do mecanismo MariaDB com iD de transação global (GTID). Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog](https://mariadb.com/kb/en/library/replication-overview/).

As réplicas são novos servidores que você gerencia semelhantes ao Banco de Dados Azure regular para servidores MariaDB. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre a replicação do GTID, consulte a [documentação de replicação do MariaDB](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente do seu servidor mestre. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou a aproximação de dados com seus usuários.

Você pode ter um servidor mestre em qualquer [banco de dados do Azure para a região do MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A imagem abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região mestre.

[![Leia as regiões das réplicas](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplicas universais
Você pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde seu servidor mestre está localizado. As regiões de réplica universal suportadas incluem:

Austrália Leste, Austrália Sudeste, Central dos EUA, Leste da Ásia, Leste dos EUA, Leste dos EUA 2, Japão Leste, Japão Oeste, Coréia Central, Coréia do Sul, Norte dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Reino Unido Oeste, Europa Ocidental, Oeste dos EUA.

*West US 2 está temporariamente indisponível como um local de réplica de região transversal.

### <a name="paired-regions"></a>Regiões emparelhadas
Além das regiões de réplica universal, você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre. Se você não conhece o par da sua região, você pode aprender mais com o [artigo Azure Paired Regions](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas transfronteiriças para o planejamento de recuperação de desastres, recomendamos que você crie a réplica na região emparelhada em vez de uma das outras regiões. As regiões emparelhadas evitam atualizações simultâneas e priorizam o isolamento físico e a residência de dados.  

No entanto, há limitações a considerar: 

* Disponibilidade regional: O Banco de Dados Azure para MariaDB está disponível no Oeste dos EUA 2, França Central, Emirados Norte e Alemanha Central. No entanto, suas regiões pareadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões azure são emparelhadas em uma única direção. Essas regiões incluem A Índia Ocidental, Brasil Sul e Us Gov Virginia. 
   Isso significa que um servidor mestre na Índia Ocidental pode criar uma réplica no sul da Índia. No entanto, um servidor mestre no sul da Índia não pode criar uma réplica na Índia Ocidental. Isso porque a região secundária da Índia Ocidental é o sul da Índia, mas a região secundária do sul da Índia não é a Índia Ocidental.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor mestre não tiver servidores de réplica existentes, o mestre primeiro reiniciará para se preparar para a replicação.

Quando você inicia o fluxo de trabalho de criação de réplicas, um banco de dados Azure em branco para servidor MariaDB é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas.

> [!NOTE]
> Se você não tem alerta de armazenamento configurado em seus servidores, recomendamos que você faça isso. O alerta informa quando um servidor está se aproximando de seu limite de armazenamento, o que afetará a replicação.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Na criação, uma réplica herda as regras de firewall do servidor mestre. Depois, essas regras são independentes do servidor mestre.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, como faria em um banco de dados Azure regular para servidor MariaDB. Para um servidor chamado **myreplica** com o **myadmin**de nome de usuário admin, você pode se conectar à réplica usando o mysql CLI:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O Banco de Dados Do Azure para MariaDB fornece o **lag de replicação em segundos** métricos no Monitor Azure. Essa métrica está disponível apenas para réplicas.

Esta métrica é `seconds_behind_master` calculada usando a métrica `SHOW SLAVE STATUS` disponível no comando do MariaDB.

Defina um alerta para informá-lo quando o lag de replicação atingir um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

Quando você opta por parar a replicação em uma réplica, ela perde todos os links para seu mestre anterior e outras réplicas. Não há failover automatizado entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Quando você cria uma réplica para um mestre que não tem réplicas existentes, o mestre primeiro reiniciará para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período fora do pico.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo banco de dados Azure para servidor MariaDB. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: geração de computação, vCores, armazenamento, período de retenção de backup e versão do mecanismo MariaDB. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

As regras de firewall e as configurações dos parâmetros são herdadas do servidor mestre para a réplica quando a réplica é criada. Depois, as regras da réplica são independentes.

### <a name="stopped-replicas"></a>Réplicas paradas

Se você parar a replicação entre um servidor mestre e uma réplica de leitura, a réplica parada se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam automaticamente servidores autônomos e podem aceitar leituras e gravações. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="server-parameters"></a>Parâmetros do Servidor

Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados nos servidores mestre e réplica:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

O [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) parâmetro está bloqueado nos servidores de réplica.

### <a name="other"></a>Outros

- A criação de uma réplica de uma réplica não é suportada.
- As tabelas de memória podem fazer com que as réplicas fiquem fora de sincronia. Esta é uma limitação da tecnologia de replicação MariaDB.
- Verifique se as tabelas de servidor mestre contêm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Aprenda a [criar e gerenciar réplicas de leitura usando a API Azure CLI e REST](howto-read-replicas-cli.md)
