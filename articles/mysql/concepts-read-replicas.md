---
title: Réplicas de leitura - Banco de Dados do Azure para MySQL.
description: 'Saiba mais sobre réplicas de leitura no Banco de Dados do Azure para MySQL: como escolher regiões, criar réplicas, conectar réplicas, monitorar replicação e parar replicação.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/4/2020
ms.openlocfilehash: d9d600b4ac34e4608b7747bee0e0a704ad2ab3be
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846045"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Leia réplicas no Banco de Dados do Azure para MySQL

O recurso de réplica de leitura permite replicar dados de um servidor de Banco de Dados do Azure para MySQL para um servidor somente leitura. Você pode replicar do servidor mestre para até cinco réplicas. As réplicas são atualizadas de forma assíncrona usando a tecnologia de replicação baseada em posição do arquivo binário nativo (log binário) do mecanismo MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

As réplicas são novos servidores que você gerencia de modo semelhante aos servidores normais do Banco de Dados do Azure para PostgreSQL. Para cada réplica de leitura, você será cobrado pela computação provisionada em vCores e pelo armazenamento em GB/mês.

Para saber mais sobre recursos e problemas de replicação do MySQL, consulte a [documentação de replicação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quando usar uma réplica de leitura

O recurso de réplica de leitura ajuda a melhorar o desempenho e o dimensionamento de cargas de trabalho com uso intenso de leitura. As cargas de trabalho de leitura podem ser isoladas para as réplicas, enquanto as cargas de trabalho de gravação podem ser direcionadas para o mestre.

Um cenário comum é ter cargas de trabalho analíticas e de BI usando a réplica de leitura como a fonte de dados para relatório.

Como réplicas são somente leitura, elas não reduzem diretamente os encargos de capacidade de gravação no mestre. Esse recurso não se destina a cargas de trabalho com uso intenso de gravação.

O recurso de réplica de leitura usa replicação assíncrona do MySQL. O recurso não se destina a cenários de replicação síncrona. Haverá um atraso mensurável entre o mestre e a réplica. Os dados na réplica acabarão se tornando consistentes com os dados no mestre. Use este recurso para cargas de trabalho que podem acomodar esse atraso.

## <a name="cross-region-replication"></a>Replicação entre regiões
Você pode criar uma réplica de leitura em uma região diferente da do servidor mestre. A replicação entre regiões pode ser útil para cenários como o planejamento de recuperação de desastres ou para trazer dados mais próximos dos seus usuários.

Você pode ter um servidor mestre em qualquer [região do Banco de Dados do Azure para MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Um servidor mestre pode ter uma réplica em sua região emparelhada ou nas regiões de réplica universal. A figura abaixo mostra quais regiões de réplica estão disponíveis dependendo da sua região mestra.

[ ![Ler regiões de réplica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiões de réplica universal
Você pode criar uma réplica de leitura em qualquer uma das seguintes regiões, independentemente de onde o servidor mestre estiver localizado. As regiões de réplica universal compatíveis incluem:

Leste da Austrália, Sudeste da Austrália, EUA Central, Leste da Ásia, Leste dos EUA, Leste dos EUA 2, Leste do Japão, Oeste do Japão, Coreia Central, Sul da Coreia, Centro-Norte dos EUA, Norte da Europa, Centro-Sul dos EUA, Sudeste da Ásia, Sul do Reino Unido, Oeste do Reino Unido, Oeste da Europa, Oeste dos EUA.

*Oeste dos EUA 2 está temporariamente indisponível como um local de réplica entre regiões.

### <a name="paired-regions"></a>Regiões emparelhadas
Você pode criar uma réplica de leitura na região emparelhada do Azure do seu servidor mestre além das regiões de réplica universal. Se não souber o par da sua região, você pode encontrar essa informação no [artigo Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md).

Se você estiver usando réplicas entre regiões para o planejamento de recuperação de desastres, recomendamos criar a réplica na região emparelhada, e não em uma das outras regiões. Regiões emparelhadas evitam atualizações simultâneas e priorizam isolamento físico e residência de dados.  

No entanto, há certas limitações a serem consideradas: 

* Disponibilidade regional: O Banco de Dados do Azure para MySQL está disponível no Oeste dos EUA 2, França Central, Norte dos EAU e Alemanha Central. Entretanto, suas regiões emparelhadas não estão disponíveis.
    
* Pares unidirecionais: Algumas regiões do Azure são emparelhadas em uma direção apenas. Essas regiões incluem Oeste da Índia, Sul do Brasil e US Gov - Virgínia. 
   Isso significa que um servidor mestre no Oeste da Índia pode criar uma réplica no Sul da Índia. Entretanto, um servidor mestre no Sul da Índia não pode criar uma réplica no Oeste da Índia. Isso ocorre porque a região secundária do Oeste da Índia é o Sul da Índia, mas a região secundária do Sul da Índia não é o Oeste da Índia.

## <a name="create-a-replica"></a>Criar uma réplica

Se um servidor mestre não tiver servidores de réplica, o mestre será reiniciado primeiro para se preparar para a replicação.

Quando você inicia o fluxo de trabalho de criação de réplica, um servidor do Banco de Dados do Azure para MySQL é criado. O novo servidor é preenchido com os dados que estavam no servidor mestre. A hora de criação depende da quantidade de dados no mestre e do tempo decorrido desde o último backup completo semanal. O tempo pode variar de alguns minutos a várias horas. O servidor de réplica é sempre criado no mesmo grupo de recursos e na mesma assinatura do servidor mestre. Se você quiser criar um servidor de réplica para um grupo de recursos diferente ou uma assinatura diferente, você pode [mover o servidor de réplica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) após a criação.

Cada réplica é habilitada para armazenamento de [aumento automático](concepts-pricing-tiers.md#storage-auto-grow). O recurso de aumento automático permite que a réplica acompanhe os dados replicados e evita uma interrupção na replicação causada por erros de falta de armazenamento.

Saiba como [criar uma réplica de leitura no portal do Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conectar-se a uma réplica

Uma réplica herda as regras de firewall do servidor mestre na criação. Essas regras são independentes do servidor mestre posteriormente.

A réplica herda a conta do administrador do servidor mestre. Todas as contas de usuário no servidor mestre são replicadas para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

Você pode se conectar à réplica usando seu nome de host e uma conta de usuário válida, assim como faria em um servidor regular do Banco de Dados do Azure para MySQL. Para um servidor chamado **myreplica** com o nome de usuário admin **myadmin**, você pode se conectar à réplica usando a CLI do mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

No prompt, insira a senha da conta de usuário.

## <a name="monitor-replication"></a>Monitorar a replicação

O Banco de Dados do Azure para MySQL fornece a métrica **Atraso da replicação em segundos** no Azure Monitor. Essa métrica está disponível apenas para réplicas.

Essa métrica é calculada com o uso da métrica `seconds_behind_master` disponível no comando `SHOW SLAVE STATUS` do MySQL.

Defina um alerta para informá-lo quando o retardo da replicação atinge um valor que não é aceitável para sua carga de trabalho.

## <a name="stop-replication"></a>Parar replicação

Você pode interromper a replicação entre um mestre e uma réplica. Após a replicação ser interrompida entre um servidor mestre e uma réplica de leitura, a réplica se torna um servidor autônomo. Os dados no servidor autônomo são os dados que estavam disponíveis na réplica no momento em que o comando de parar a replicação foi iniciado. O servidor autônomo não alcança o servidor mestre.

Quando você escolhe interromper a replicação em uma réplica, ela perde todos os links para o mestre anterior e outras réplicas. Não há failover automatizado entre um mestre e sua réplica.

> [!IMPORTANT]
> O servidor autônomo não pode se tornar uma réplica novamente.
> Antes de interromper a replicação em uma réplica de leitura, verifique se a réplica tem todos os dados de que você precisa.

Saiba como [interromper a replicação para uma réplica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerações e limitações

### <a name="pricing-tiers"></a>Tipos de preço

No momento, as réplicas de leitura estão disponíveis apenas nas camadas de preços de uso geral e de otimização de memória.

### <a name="master-server-restart"></a>Reinicialização do servidor mestre

Ao criar uma réplica para um mestre que não tem nenhuma réplica existente, primeiro o mestre será reiniciado para se preparar para a replicação. Leve isso em consideração e realize essas operações durante um período de pouca atividade.

### <a name="new-replicas"></a>Novas réplicas

Uma réplica de leitura é criada como um novo servidor de Banco de Dados do Azure para MySQL. Um servidor existente não pode se tornar uma réplica. Você não pode criar uma réplica de outra réplica de leitura.

### <a name="replica-configuration"></a>Configuração da réplica

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

Regras de firewall e configurações de parâmetros são herdadas do servidor mestre para a réplica quando a réplica é criada. As regras da réplica são independentes posteriormente.

### <a name="stopped-replicas"></a>Réplicas paradas

Se você interromper a replicação entre um servidor mestre e uma réplica de leitura, a réplica interrompida se tornará um servidor autônomo que aceita leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

### <a name="deleted-master-and-standalone-servers"></a>Servidores mestre e autônomo excluídos

Quando um servidor mestre é excluído, a replicação é interrompida para todas as réplicas de leitura. Essas réplicas se tornam servidores autônomos automaticamente e podem aceitar leituras e gravações. O próprio servidor mestre é excluído.

### <a name="user-accounts"></a>Contas de usuário

Os usuários no servidor mestre são replicados para as réplicas de leitura. Você só pode se conectar a uma réplica de leitura usando as contas de usuário disponíveis no servidor mestre.

### <a name="server-parameters"></a>Parâmetros do Servidor

Para impedir que os dados fiquem fora de sincronia e evitar possíveis perdas de dados ou danos, alguns parâmetros de servidor estão bloqueados para serem atualizados ao usar réplicas de leitura.

Os seguintes parâmetros do servidor estão bloqueados nos servidores mestre e de réplica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

O parâmetro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) está bloqueado nos servidores de réplica. 

Para atualizar um dos parâmetros acima no servidor mestre, exclua os servidores de réplica, atualize o valor do parâmetro no mestre e recrie as réplicas.

### <a name="other"></a>Outros

- O GTID (identificadores de transação globais) não são compatíveis.
- A criação de uma réplica de uma réplica não é suportada.
- Tabelas na memória podem fazer com que as réplicas fiquem fora de sincronia. Esta é uma limitação da tecnologia de replicação do MySQL. Leia mais na [documentação de referência do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) para mais informações.
- Verifique se as tabelas de servidor mestre contêm chaves primárias. A falta de chaves primárias pode resultar em latência de replicação entre o mestre e as réplicas.
- Analise a lista completa das limitações de replicação do MySQL no [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar e gerenciar réplicas de leitura usando o portal do Azure](howto-read-replicas-portal.md)
- Saiba como [criar e gerenciar réplicas de leitura usando a CLI do Azure e o API REST](howto-read-replicas-cli.md)
